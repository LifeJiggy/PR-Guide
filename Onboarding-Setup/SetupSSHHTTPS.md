# Setup SSH/HTTPS for GitHub

This guide covers the essential setup for connecting to GitHub using SSH and HTTPS protocols, including authentication, key management, and best practices for secure development workflows.

## 🔐 **HTTPS Setup**

### **Basic HTTPS Authentication**
```bash
# Clone with HTTPS (requires token for private repos)
git clone https://github.com/username/repo.git

# When pushing, you'll be prompted for username and password/token
# Use Personal Access Token instead of password
```

### **Personal Access Token (PAT)**
1. Go to GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate new token with appropriate scopes:
   - `repo` - Full control of private repositories
   - `public_repo` - Access public repositories
   - `workflow` - Update GitHub Action workflows
3. Copy token (save it securely - you won't see it again)
4. Use token as password when authenticating

### **Credential Storage**
```bash
# Store credentials (not recommended for shared machines)
git config --global credential.helper store

# Use credential manager (Windows)
git config --global credential.helper manager

# Use keychain (macOS)
git config --global credential.helper osxkeychain

# Use credential cache (temporary storage)
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'  # 1 hour
```

## 🔑 **SSH Setup**

### **Generate SSH Key**
```bash
# Generate new SSH key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# Or for older systems
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Press Enter for default location (~/.ssh/id_ed25519)
# Optional: Add passphrase for extra security
```

### **Add SSH Key to GitHub**
```bash
# Copy public key to clipboard
cat ~/.ssh/id_ed25519.pub

# Or on Windows
type %USERPROFILE%\.ssh\id_ed25519.pub
```

1. Go to GitHub → Settings → SSH and GPG keys
2. Click "New SSH key"
3. Paste your public key
4. Add descriptive title (e.g., "Work Laptop" or "Personal MacBook")

### **SSH Agent Setup**
```bash
# Start SSH agent
eval "$(ssh-agent -s)"

# Add your SSH private key
ssh-add ~/.ssh/id_ed25519

# For automatic loading on startup (add to ~/.bashrc or ~/.zshrc)
echo 'eval "$(ssh-agent -s)"' >> ~/.bashrc
echo 'ssh-add ~/.ssh/id_ed25519' >> ~/.bashrc
```

### **Test SSH Connection**
```bash
# Test connection to GitHub
ssh -T git@github.com

# You should see:
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

## 🛠️ **Repository Setup**

### **Clone with SSH**
```bash
# Clone using SSH (recommended for frequent contributions)
git clone git@github.com:username/repo.git

# Clone using HTTPS
git clone https://github.com/username/repo.git
```

### **Convert Existing Repository**
```bash
# Check current remote URL
git remote -v

# Change to SSH
git remote set-url origin git@github.com:username/repo.git

# Change to HTTPS
git remote set-url origin https://github.com/username/repo.git
```

### **Multiple Remotes Setup**
```bash
# Add origin (your fork) - SSH
git remote add origin git@github.com:yourusername/repo.git

# Add upstream (original repo) - HTTPS for read-only
git remote add upstream https://github.com/originalowner/repo.git

# Verify remotes
git remote -v
```

## 🔒 **Security Best Practices**

### **SSH Key Security**
```bash
# Use strong passphrase
ssh-keygen -p -f ~/.ssh/id_ed25519

# Use different keys for different purposes
ssh-keygen -t ed25519 -C "work@example.com" -f ~/.ssh/id_work
ssh-keygen -t ed25519 -C "personal@example.com" -f ~/.ssh/id_personal

# Add to SSH config (~/.ssh/config)
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_work

Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_personal
```

### **Token Management**
- **Rotate tokens regularly** (GitHub recommends every 30 days)
- **Use fine-grained permissions** when possible
- **Delete unused tokens** immediately
- **Never commit tokens** to version control

### **Two-Factor Authentication (2FA)**
- **Enable 2FA** on your GitHub account
- **Use authenticator apps** over SMS
- **Add backup codes** and store securely
- **Set up recovery options**

## 🚀 **Workflow Optimization**

### **SSH Config for Multiple Accounts**
```bash
# ~/.ssh/config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519

Host work.github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_work

# Usage
git clone git@work.github.com:organization/repo.git
```

### **HTTPS with Token in URL (Not Recommended)**
```bash
# Avoid this - token in URL is visible in logs
git clone https://YOUR_TOKEN@github.com/username/repo.git

# Use credential helper instead
git config --global credential.helper store
# Enter username and token when prompted
```

### **Conditional Remotes**
```bash
# Use SSH for pushing, HTTPS for pulling
git remote set-url origin https://github.com/username/repo.git
git remote set-url --push origin git@github.com:username/repo.git
```

## 🔧 **Troubleshooting**

### **Common SSH Issues**
```bash
# Permission denied (publickey)
# 1. Check SSH key is added to agent
ssh-add -l

# 2. Verify public key on GitHub matches local key
ssh-keygen -lf ~/.ssh/id_ed25519.pub

# 3. Test connection
ssh -vT git@github.com

# 4. Check SSH config syntax
ssh -T -F ~/.ssh/config git@github.com
```

### **Common HTTPS Issues**
```bash
# Authentication failed
# 1. Verify token is correct and not expired
# 2. Check token has required scopes
# 3. Try regenerating token
# 4. Clear credential cache: git config --global --unset credential.helper
```

### **Mixed Protocol Issues**
```bash
# If you have both SSH and HTTPS remotes
# Check which protocol each remote uses
git remote -v

# Update as needed
git remote set-url origin git@github.com:username/repo.git
```

## 📊 **Performance Comparison**

| Aspect | SSH | HTTPS |
|--------|-----|-------|
| **Speed** | Faster for large repos | Slower due to encryption overhead |
| **Security** | Key-based auth | Token-based auth |
| **Setup** | More complex | Simpler |
| **Firewall Friendly** | May be blocked | Usually allowed |
| **Caching** | No | Credential caching available |

## 🎯 **Recommended Setup for Contributors**

### **For Individual Contributors**
1. **Use SSH** for personal projects and frequent contributions
2. **Use HTTPS** with PAT for CI/CD and automated systems
3. **Enable 2FA** on your GitHub account
4. **Use SSH agent** for key management

### **For Team/Organization Contributors**
1. **Use SSH** with organization-provided keys
2. **Implement key rotation policies**
3. **Use GitHub's organization features** for access management
4. **Consider SSO integration** for enterprise accounts

### **For Open Source Contributors**
1. **Fork repositories** to your account
2. **Use SSH** for pushing to your fork
3. **Use HTTPS** for cloning upstream repos
4. **Keep personal tokens** secure and rotate regularly

## 📚 **Additional Resources**

- [GitHub SSH Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitHub HTTPS Documentation](https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories)
- [Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [SSH Key Generation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

## ✅ **Quick Setup Checklist**

### **SSH Setup**
- [ ] Generate SSH key pair
- [ ] Add public key to GitHub
- [ ] Start SSH agent
- [ ] Add private key to agent
- [ ] Test connection

### **HTTPS Setup**
- [ ] Create Personal Access Token
- [ ] Configure credential helper
- [ ] Test authentication

### **Security**
- [ ] Enable 2FA on GitHub
- [ ] Set strong passphrase on SSH key
- [ ] Store backup codes securely
- [ ] Review token permissions regularly

Choose the protocol that best fits your workflow and security requirements! 🔐⚡