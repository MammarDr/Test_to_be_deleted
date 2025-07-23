
```c
git config --global user.name "Mammar Dr"
git config --global user.email "your-email@example.com" // same github
```


```c

cd "/Your_Project"

git init

git remote add origin https://github.com/MammarDr/my_repository.git

git add .

git commit -m "initial commit"

// push to master directly

git push -u origin master

// Or

git branch -M main

git push -u origin main

```

### 🔹 `git config --global`

- Applies to **all repositories** for the current user.
    
- Saves the configuration in:  
    `~/.gitconfig` or `%USERPROFILE%\.gitconfig` on Windows.
    

✅ Use it for settings you want to apply everywhere:

```bash
git config --global user.name "Mammar Dr"
git config --global user.email "you@example.com"
```


---

### 🔸 `git config` (without `--global`)

- Applies **only to the current repository**.
    
- Saves the config in `.git/config` inside that repo.
    

✅ Use it if:

- You're using a different identity for one specific project

```bash
git config user.name "Mammar Dr"
git config user.email "you@example.com"
```
