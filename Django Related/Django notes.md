
### Using Serializer.py vs Models.py
How to decide whether rules such as 
`item_price = models.DecimalField(max_digits=10, decimal_places=2)`
and
`product_details = ListingSerializer(source='product', read_only=True)`
should be placed in models.py or serializers.py:

Put in models.py if it defines the shape of your DB or directly control your tables.
Put in serializers.py if it controls how data looks when being passed as JSON, or if it handles incoming client inputs.

E.g.

```python
#In models.py IF:

#Formatting Prices:
item_price = models.DecimalField(max_digits=10, decimal_places=2)
#Foreign Keys:
category = models.ForeignKey(Categories, on_delete=models.CASCADE)
#System Constraints:
item_name = models.CharField(max_length=255, null=False)

#In serializers.py IF:

#Nesting Full Objects: (You only got product ID, but want to get full product details from a serializer called ListingSerializer through the product ID)
product_details = ListingSerializer(source='product', read_only=True)

#Hiding sensitive output:
#Databse keep hashed vers. of password, but serializer ensure that when Django response with 201 Created status, the password string is not sent with the outgoing JSON. Hence the write_only = true.
password = serializers.CharField(write_only=True)

#Double-Entry Validation
#Don't need to have a password_confirm column on the DB so do validation in serializer.py.
password_confirm = serializers.CharField(write_only=True)
```

### models.py
```python
from django.db import models

class Listing(models.Model):
    # Django automatically creates an auto-incrementing 'id' primary key field.
    
    title = models.CharField(max_length=255)
    description = models.TextField(blank=True, null=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField(default=0)
    image_url = models.URLField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```
The Key Parts Broken Down:
- **Field Types:** Notice CharField, TextField, DecimalField. These tell Django what kind of PostgreSQL data types to use (VARCHAR, TEXT, NUMERIC, etc.).
- **Validation Restrictions:** Arguments like max_length=255 enforce limits at both the database level and in your admin panel forms.
- **blank vs null:** blank=True means the field is optional in forms (frontend). null=True means the database will store an empty value as NULL.
- The **str()** method: This tells Django how to display the object in text form (like in your Django Admin panel). Instead of showing <Listing (1) object>, it will show Wireless Headphones.

#### Managing relationships (Foreign Keys)
```python
class Category(models.Model):
    name = models.CharField(max_length=100)

class Listing(models.Model):
    title = models.CharField(max_length=255)
    # One-to-Many: One category can have many listings
    category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name='listings')
```
**on_delete=models.CASCADE** is vital. It tells PostgreSQL: "If this Category is deleted, automatically delete all the listings assigned to it."
#### Why related_name Matters

related_name allows you to do reverse searching. For example,
```python
#inside Listing's model.py
category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name='listings')
```

By having related_name = 'listings', I can access listings that are related to a specific category as such:

```python
# 1. Fetch the specific category row from the database 
my_category = Category.objects.get(id=id) 

# 2. Access the reverse relationship to get all connected listings 
all_category_listings = my_category.listings.all()
```

Even if there is no listings column in my category table.

---

When you define your ForeignKey, the related_name attribute determines the name of the Python property you use to initiate this search.

- If you set related_name='listings', you type: my_category.listings.all()
- If you set related_name='products', you type: my_category.products.all()
- If you **omit** related_name, Django creates a default one for you using the model name lowercase followed by _set: my_category.listing_set.all()


#### Updating DB (Migrations)
1. Inspects models.py and generates a "blueprint" file of the changes
	`python manage.py makemigrations

2. Runs that blueprint against PostgreSQL to actually create/alter the tables
	`python manage.py migrate

### serializers.py

Serializers is just how to convert JSON to object that Django can use! If the data is not being transported from backend to frontend or viceversa, no need to serialise it :D

```python
# serializers.py
from rest_framework import serializers
from .models import Listing

class ListingSerializer(serializers.ModelSerializer):
    class Meta:
        model = Listing
        # Specify exactly which database columns you want to expose to React
        fields = ['id', 'title', 'price', 'stock', 'image_url']
```

The above transform data from python to:
```json
[
  {
    "id": 101,
    "title": "Wireless Gaming Mouse",
    "price": "79.99",
    "stock": 25,
    "image_url": "https://example.com/mouse.jpg"
  }
]
```

#### Deserialisation (JSON to python):
The serializer performs three steps here:
1. **Parses** the incoming JSON text.
2. **Validates** the data (e.g., checks if price is a valid number, if title isn't too long, or if required fields are missing).
3. **Converts** it into an object that Django can run .save() or .create() on.

```python
# Inside views.py handling a POST request
@api_view(['POST'])
def create_listing(request):
    # Pass the incoming JSON data (request.data) into the serializer
    serializer = ListingSerializer(data=request.data)
    
    # Run built-in validation checks based on your models.py rules
    if serializer.is_valid():
        # Save directly to PostgreSQL if everything is perfect
        serializer.save() 
        return Response(serializer.data, status=201)
        
    # If the user sent bad data (e.g. text where a price integer belongs), 
    # return the errors back to React so it can display them to the user.
    return Response(serializer.errors, status=400)
```

#### Nesting serializers with foreign keys:
```python
# serializers.py

class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']

class ListingSerializer(serializers.ModelSerializer):
    # Nesting the category serializer right here!
    category = CategorySerializer(read_only=True)

    class Meta:
        model = Listing
        fields = ['id', 'title', 'price', 'category'] 
        # React will now receive a fully complete category object inside each product!
```

This is to achieve:
```python
[
  {
    "id": 101,
    "title": "Wireless Gaming Mouse",
    "price": "79.99",
    "category": {
      "id": 1,
      "name": "Electronics",
      "slug": "electronics"
    }
  }
]
```

### views.py
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import CartItem
from .serializers import CartItemSerializer

class AddToCartView(APIView):
    # This class automatically knows it handles POST because of the name below
class AddToCartView(APIView):
    def post(self, request):
        serializer = CartItemSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    # If you wanted to support GET requests on the same URL later, you just add this:
# The Django view that responds to your React GET request:
	def get(self, request, format=None):
	    cart_items = CartItem.objects.all()
	    serializer = CartItemSerializer(cart_items, many=True)
	    return Response(serializer.data) # <-- This data drops directly into your React 'data' state!
```

in backend/urls.py file:
```python
from django.contrib import admin
from django.urls import path
from cart.views import AddToCartView

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/cart/add/', AddToCartView.as_view(), name='add-to-cart'),
]
```

In JSX file:
#### Post:
```javascript
import React, { useState } from 'react';

  

export function CartButton() {
  const [isAdding, setIsAdding] = useState(false);
  
  const addToCart = async (productId) => {
    setIsAdding(true);
    try {
      const response = await fetch('http://127.0.0.1:8000/api/cart/add/', {
        method: 'POST',
        //Fixed way to tell Django it is json
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          product_id: productId,
          quantity: 1, // hardcoded item increments
        }),
      });

      if (!response.ok) {
        throw new Error('Network response failure');
      }
      const data = await response.json();
      console.log('Database row generated:', data);
      alert('Item added to PostgreSQL database successfully!');
    } catch (error) {
      console.error('Transmission error:', error);
      alert('Failed to connect to backend database.');
    } finally {
      setIsAdding(false);
    }
  };
  const [productId, setProductId] = useState("");
  return (
    <form>
      <label htmlFor="add-to-cart">Item to add to Cart:</label>
      <input
        type="text"
        id="add-to-cart"
        value ={productId}
        onChange={(e) => setProductId(e.target.value)}
        placeholder="Enter item ID"
      />
      <button
        onClick={() => addToCart(productId)}
        disabled={isAdding}
        style={{ padding: '10px 20px', cursor: 'pointer' }}
      >
        {isAdding ? 'Processing...' : 'Add to Cart'}
      </button>
    </form>
  );
}
```

Alternatively if need to deal with images:
```jsx
    const handleSubmit = async (e) => {
        e.preventDefault();
        
        const formData = new FormData();
        formData.append("item_name", item_name);
        formData.append("item_price", item_price);
        formData.append("category", '1');

        if (imageFile) {
            formData.append("image", imageFile);
        }

        try {
            const response = await fetch(`${API_BASE_URL}/api/listings/`, {
                method: "POST",
                body: formData,
            });

            if (response.ok) {
                alert("Listing added locally successfully!");
            }

        } catch (error) {
            console.error("Error saving listing:", error);
        }
    };
```
#### Get:
```Javascript
import React, { useState, useEffect } from 'react';

export default function SnippetList() {
  // 1. Initialize state to hold our array of snippets
  const [snippets, setSnippets] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // 2. useEffect ensures this code runs exactly ONCE when the component loads
  useEffect(() => {
    // By default, the browser fetch() method performs a GET request
    fetch('http://127.0.0.1:8000/api/snippets/')
      .then((response) => {
        // Check if Django responded with a successful status code (200 OK)
        if (!response.ok) {
          throw new Error('Failed to fetch data from backend');
        }
        return response.json(); // Convert the raw network text string into a JS array
      })
      .then((data) => {
        setSnippets(data); // Save the JSON data into our state variable
        setLoading(false); // Turn off the loading message
      })
      .catch((err) => {
        console.error('Error fetching data:', err);
        setError(err.message);
        setLoading(false);
      });
  }, []); // The empty array [] ensures this effect runs only on mount

  // 3. Conditional rendering based on the request state
  if (loading) return <p>Loading snippets...</p>;
  if (error) return <p style={{ color: 'red' }}>Error: {error}</p>;

  // 4. The JSX layout: Looping through our data using .map()
  return (
    <div style={{ padding: '20px' }}>
      <h2>Code Snippets</h2>
      {snippets.length === 0 ? (
        <p>No snippets found.</p>
      ) : (
        <ul style={{ listStyleType: 'none', padding: 0 }}>
          {snippets.map((snippet) => (
            <li 
              key={snippet.id} 
              style={{ border: '1px solid #ddd', padding: '15px', marginBottom: '10px', borderRadius: '5px' }}
            >
              <h3>{snippet.title || 'Untitled Snippet'}</h3>
              <pre style={{ background: '#f5f5f5', padding: '10px', borderRadius: '4px' }}>
                <code>{snippet.code}</code>
              </pre>
              <small>Language: {snippet.language}</small>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

### urls.py

```
# Django `urls.py` Cheat Sheet

## TL;DR Core Concept
`urlpatterns` is Django's traffic controller.
It matches incoming browser URLs (minus domain & leading slash) to specific backend views, scanning sequentially from top to bottom. 

* Connected via `ROOT_URLCONF` in `settings.py`.
```
---
#### Anatomy of a Path
```python
path('route/', views.function_name, name='unique-nickname')
```

- **Route:** The URL pattern string. Always include a trailing slash `/`.
- **View:** The code that runs when the URL is matched.
- **Name:** A unique nickname. Used for reverse URL lookups so changing the URL string won't break your app.

#### Elaboration on name:

Usually to use on HTML:
```HTML
<a href="{% url 'article-view' pk=article.id %}">Read Article</a>
```
This will tell Django to substitute this tag with:
```HTML
<a href="/articles/42/">Read Article</a>
```

If you require the URL inside Django's views.py:
- `reverse()`: Computes the routing table and returns a raw string (e.g., `"/path/to/page/"`).
- `redirect()`: A shortcut that calls `reverse()` internally and immediately sends an HTTP 302 redirect header back to the browser.

e.g
```Python
from django.urls import reverse 
def get_success_url(request): 
	# Returns the literal string: "/articles/42/" 
	my_url = reverse('article-view', kwargs={'pk': 42}) return my_url
```

```Python
from django.shortcuts import redirect 
def delete_article(request, pk): 
	article = Article.objects.get(pk=pk) 
	article.delete() 
	# Django calculates the path for 'home' and instantly issues a 302 redirect return redirect('home')
```

To use with React, need to create a hardcoded API Router instead of using the name parameter as React cannot access Django's python environment to do reverse().

E.g.
```Javascript
// src/apiRoutes.js 
const API_BASE = 'https://api.yourdomain.com'; 
export const apiRoutes = { 
login: `${API_BASE}/auth/login/`, // For dynamic URLs, use a function that returns a string 
userDetail: (userId) => `${API_BASE}/profiles/view/${userId}/`, 
blogPost: (slug) => `${API_BASE}/blog/posts/${slug}/`, };
```


#### Dynamic Routing (Path Converters)
Capture values from the URL using `<type:variable_name>`. These are passed directly as arguments to your view.
```python
path('user/<int:user_id>/', views.profile)  # Matches: /user/42/
path('post/<slug:post_slug>/', views.post)  # Matches: /post/my-first-blog/
```
- `<int:>` - Positive integers.
- `<str:>` - Any non-empty string (excluding `/`). _Default_.
- `<slug:>` - Letters, numbers, hyphens, and underscores.
- `<uuid:>` - Formatted UUIDs.
#### Modular Routing (`include`)

Keep apps clean by nesting URLs. The master router chops off its prefix and hands the rest down.
```python
#This hands off everything that starts with blog/
path('blog/', include('blog.urls')) 

# api/urls.py (App-specific)
path('users/', views.list_users)  # Final URL: /api/users/
```

Include will pass the result to this file instead and find where to go so that urls.py is not so crowded
```python
#In blog/urls.py

from django.urls import path from . 
import views 
urlpatterns = [ path('', views.blog_home), # Matches: /blog/ 
				path('latest/', views.blog_latest), # Matches: /blog/latest/ 
				]
```

### apps.py

Inside app.py have a method called `ready()` Django runs this function ONCE when starting up web server.

Import signals in here so python registers event decorators before any traffic hits the endpoints.

E.g.
```python
# accounts/apps.py
from django.apps import AppConfig

class AccountsConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'accounts' # Matches the folder name

    # This method runs the millisecond Django finishes initializing
    def ready(self):
        import accounts.signals  # 🌟 This forces Django to read the signals file!
```

### signals.py

signals.py is Django's built in notification network.
It allows certain actions in the app to broadcast a announcement to the rest of the project.

E.g.
```python
# accounts/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile

# 1. The @receiver decorator connects this function to the User model's post_save alarm.
#sender=User makes it so that the function only wakes up when a User table input is touched.
@receiver(post_save, sender=User)
#sender is which model sent the notification
#instance is the exact python object that was saved
#kwargs catch all dictionary for any extra parameters passed along with the signal that is not explicity named.
def create_user_profile(sender, instance, created, **kwargs):
    # 2. 'created' is a True/False flag. It's True only if a brand-new row was made.
    if created:
        # 3. 'instance' is the actual User object that was just saved.
        Profile.objects.create(user=instance)
```
