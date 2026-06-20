### Pulling repo from github
```bash
git remote set-url origin https://github.com/your-username/your-repo-name.git

git fetch origin

# Explicitly register the remote origin mapping 
git remote add origin https://github.com/your-username/your-repo-name.git 
# Run fetch to test the new mapping link 
git fetch origin
```

```bash
git init

git remote add origin https://github.com/your-username/your-repo-name.git

git pull origin main
```