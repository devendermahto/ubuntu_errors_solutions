# **The Complete CI/CD Master Guide for Developers**
## **Table of Contents**

### **📚 PART 1: FOUNDATION & CORE CONCEPTS**
1.1 Understanding the Three Worlds (Your Computer, GitHub, Server)  
1.2 Why CI/CD Beats Manual SSH Editing  
1.3 Tools You'll Need and Why  

### **🏠 PART 2: SINGLE DEVELOPER, SINGLE COMPUTER**
2.1 Initial Setup: From Zero to Automated Deployment  
2.2 Your Daily Workflow with CI/CD  
2.3 Common Problems and Solutions  

### **💼 PART 3: WORKING FROM MULTIPLE COMPUTERS**
3.1 Setup Your Second Computer  
3.2 Sync Between Computers Seamlessly  
3.3 Managing SSH Keys Across Devices  

### **👥 PART 4: ADDING TEAM MEMBERS**
4.1 Adding Your First Collaborator  
4.2 Team Workflow: Branches, PRs, and Reviews  
4.3 Security and Permissions for Teams  

### **🤖 PART 5: ADVANCED AUTOMATION**
5.1 Using Cursor AI with CI/CD  
5.2 Automated Testing and Quality Gates  
5.3 Zero-Downtime Deployments  

### **🚨 PART 6: TROUBLESHOOTING & MAINTENANCE**
6.1 Fixing Common CI/CD Failures  
6.2 Rollback Procedures  
6.3 Monitoring and Alerts  

---

# **📚 PART 1: FOUNDATION & CORE CONCEPTS**

## **1.1 Understanding the Three Worlds**

### **World 1: YOUR COMPUTER (Local Development)**
**Location:** Your Mac, PC, or Laptop  
**What happens here:**
- You write code in VS Code/Cursor
- You test locally in your browser
- You commit changes to Git
- **Files are on your hard drive**

### **World 2: GITHUB (The Cloud Hub)**
**Location:** Internet cloud servers (github.com)  
**What happens here:**
- Stores all versions of your code
- Runs GitHub Actions (the automation robot)
- Hosts your repository for collaboration
- **Acts as the central source of truth**

### **World 3: YOUR NAMECHEAP SERVER (Production)**
**Location:** Namecheap hosting servers  
**What happens here:**
- Hosts your live website (khabribaba.com)
- Files live in `public_html/` folder
- Visitors access your site here
- **Only updated by automation**

## **1.2 Visualizing the Flow**
```
YOUR COMPUTER (Local)      GITHUB (Cloud)        NAMECHEAP (Production)
┌─────────────────┐       ┌─────────────────┐    ┌─────────────────┐
│ 1. Write Code   │──────▶│ 2. Push to Git  │    │                 │
│    - index.html │       │    - Store code │    │                 │
│    - style.css  │       │    - Run tests  │    │                 │
└─────────────────┘       └─────────────────┘    └─────────────────┘
        │                         │                       │
        │                         │   3. Trigger Actions  │
        │                         │───────▶🤖 Robot       │
        │                         │                       │
        │                         │          4. SSH Login │
        │                         │──────────────────────▶│
        │                         │                       │
        │                         │          5. Deploy    │
        │                         │──────────────────────▶│ 6. Live Site
        │                         │                       └─────────────▶
        │                         │                       khabribaba.com
        │                         │
        │                 7. Email you results
        │◀────────────────────────│
```

## **1.3 Why This Beats Manual SSH**

**Problem with Manual SSH (What you're doing now):**
```bash
ssh user@khabribaba.com
nano index.html  # Direct edit on server
# If you make typo → Site breaks
# No backup → Can't undo
# No history → Who changed what?
```

**Solution with CI/CD:**
```bash
# On your computer
code index.html  # Edit locally
git add .
git commit -m "Update homepage"
git push origin main
# Robot handles the rest → 2 mins later site updates
```

---

# **🏠 PART 2: SINGLE DEVELOPER, SINGLE COMPUTER**

## **2.1 Initial Setup: Complete Step-by-Step**

### **PHASE A: Setup on YOUR COMPUTER**

**Step 1: Open Terminal and Navigate**
```bash
# Mac: Cmd+Space, type "Terminal", Enter
# Windows: Win+R, type "cmd", Enter

# Go to Desktop (or wherever you want)
cd ~/Desktop

# Create project folder
mkdir khabribaba
cd khabribaba
```

**Step 2: Create Basic Website Files**
```bash
# Create HTML file
echo '<!DOCTYPE html>
<html>
<head>
    <title>KhabriBaba</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <h1>Welcome to KhabriBaba.com!</h1>
    <p>This site is now managed with CI/CD.</p>
</body>
</html>' > index.html

# Create CSS folder and file
mkdir css
echo 'body {
    font-family: Arial, sans-serif;
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}
h1 {
    color: #2c3e50;
}' > css/style.css
```

**Step 3: Initialize Git (Time Machine for Code)**
```bash
# Start tracking changes
git init

# Tell Git who you are
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Create .gitignore (VERY IMPORTANT)
echo '# System files
.DS_Store
Thumbs.db

# Environment files
.env
.env.local

# IDE files
.vscode/
.idea/

# Logs
*.log
npm-debug.log*

# Dependencies
node_modules/
vendor/' > .gitignore

# Save initial version
git add .
git commit -m "Initial website setup"
```

### **PHASE B: Setup GITHUB Account**

**Step 4: Create GitHub Repository**
1. Go to https://github.com (sign up if needed)
2. Click **+** (top right) → **New repository**
3. Repository name: `khabribaba`
4. **DO NOT** check "Initialize with README" (leave empty)
5. Click **Create repository**

**Step 5: Connect Your Computer to GitHub**
```bash
# Copy the commands GitHub shows you, or use:
git remote add origin https://github.com/YOUR-USERNAME/khabribaba.git
git branch -M main
git push -u origin main

# Refresh GitHub page - you should see your files!
```

### **PHASE C: Configure NAMECHEAP SERVER**

**Step 6: SSH into Your Server (One Last Manual Login)**
```bash
# Open NEW terminal tab
ssh yourusername@khabribaba.com
# Enter your password when asked

# You are now ON YOUR SERVER (not your computer!)
# Type this to see where you are:
pwd
# Should show: /home/yourusername
```

**Step 7: Create Deployment User on Server**
```bash
# Still on server, run these commands:

# Create a user just for deployments
sudo adduser deployer
# When asked for password, create a strong one
# Press Enter for other fields (full name, etc.)

# Find your web directory
ls -la ~/
# Look for: public_html/ or www/ or public/
# For Namecheap, it's usually: /home/yourusername/public_html/

# Give deployer permission to update website
sudo chown -R deployer:www-data /home/yourusername/public_html/
sudo chmod -R 755 /home/yourusername/public_html/
```

### **PHASE D: SSH Keys (Security Bridge)**

**Step 8: Generate SSH Key on YOUR COMPUTER**
```bash
# On YOUR COMPUTER (new terminal tab, NOT on server)
ssh-keygen -t ed25519 -C "github-action@khabribaba.com" -f ~/.ssh/khabribaba-deploy

# When asked for passphrase, just press Enter (empty)
# This creates two files:
# ~/.ssh/khabribaba-deploy      (PRIVATE KEY - KEEP SECRET!)
# ~/.ssh/khabribaba-deploy.pub  (PUBLIC KEY)

# View and copy the PUBLIC key
cat ~/.ssh/khabribaba-deploy.pub
# Select ALL text from "ssh-ed25519" to end
# Copy to clipboard
```

**Step 9: Add Public Key to SERVER**
```bash
# Back in the server terminal (where you SSH'd)
# Switch to deployer user
sudo su - deployer

# Create SSH directory
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# Add the public key
echo "PASTE_THE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# Test it works (from YOUR COMPUTER, new tab):
ssh -i ~/.ssh/khabribaba-deploy deployer@khabribaba.com
# Should login without password!

# Exit deployer user
exit
# Exit server
exit
```

### **PHASE E: GitHub Secrets (Safe Password Storage)**

**Step 10: Add Secrets to GitHub**
1. Go to: `https://github.com/YOUR-USERNAME/khabribaba`
2. Click **Settings** tab (top right, gear icon)
3. Click **Secrets and variables** → **Actions** (left sidebar)
4. Click **New repository secret**

**Add these 4 secrets:**

**Secret 1: SSH_PRIVATE_KEY**
```bash
# On YOUR COMPUTER, view and copy the PRIVATE key
cat ~/.ssh/khabribaba-deploy
# Copy ALL text including:
# -----BEGIN OPENSSH PRIVATE KEY-----
# [all lines]
# -----END OPENSSH PRIVATE KEY-----
```
Paste into GitHub as `SSH_PRIVATE_KEY`

**Secret 2: SSH_HOST**
```
khabribaba.com
```

**Secret 3: SSH_USERNAME**
```
deployer
```

**Secret 4: DEPLOY_PATH**
```
/home/yourusername/public_html/
# Replace "yourusername" with your actual Namecheap username
```

### **PHASE F: Create the Automation Robot**

**Step 11: Create GitHub Actions File on YOUR COMPUTER**
```bash
# On YOUR COMPUTER, in project folder
mkdir -p .github/workflows

# Create deployment file
echo 'name: Deploy Website

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: 📦 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔑 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
    
    - name: 🚀 Deploy to Server
      run: |
        echo "Starting deployment..."
        
        # Create backup of current site
        ssh khabribaba "mkdir -p /tmp/backups && cp -r ${{ secrets.DEPLOY_PATH }} /tmp/backups/backup_\$(date +%Y%m%d_%H%M%S)"
        
        # Sync files using rsync
        rsync -avz --delete \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="README.md" \
          ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
        
        # Set proper permissions
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type d -exec chmod 755 {} \;"
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type f -exec chmod 644 {} \;"
        
        echo "✅ Deployment complete!"' > .github/workflows/deploy.yml
```

**Step 12: Push Everything to GitHub**
```bash
# On YOUR COMPUTER
git add .github/workflows/deploy.yml
git commit -m "Add CI/CD automation"
git push origin main
```

## **2.2 Your First Automated Deployment**

**Step 13: Watch the Magic**
1. Go to: `https://github.com/YOUR-USERNAME/khabribaba/actions`
2. You'll see "Deploy Website" running with orange dot
3. Click on it to watch real-time logs
4. Wait for green checkmark ✅

**Step 14: Test It Works**
```bash
# On YOUR COMPUTER, make a change
echo '<p style="color: green;">✅ Successfully deployed via CI/CD!</p>' >> index.html

# Commit and push
git add index.html
git commit -m "Test CI/CD deployment"
git push origin main

# Wait 2 minutes, then visit: http://khabribaba.com
# You should see the green text!
```

## **2.3 Your New Daily Workflow**

**Before CI/CD (Old Way):**
1. SSH into server
2. Edit files directly on live site
3. Cross fingers and hope
4. If broken, panic and try to fix

**After CI/CD (New Way):**
```bash
# 1. Work locally (safe, can't break production)
code index.html

# 2. Test locally
open index.html  # View in browser

# 3. Save to Git
git add .
git commit -m "Add new feature"
git push origin main

# 4. Wait 2 minutes
# 5. Visit site - changes are live!
```

**Time Comparison:**
```
Manual SSH Method:
- SSH connect: 30 seconds
- Edit file: 2 minutes
- Test: 1 minute
- Total: 3.5 minutes per change

CI/CD Method:
- Edit locally: 2 minutes
- git push: 10 seconds
- Wait: 2 minutes (you can do other work)
- Total: 2 minutes active time
```

---

# **💼 PART 3: WORKING FROM MULTIPLE COMPUTERS**

## **3.1 Setup Your Second Computer**

**Scenario:** You have a Desktop (Home) and Laptop (Office)

### **COMPUTER 2 SETUP (e.g., Laptop)**

**Step 1: Install Git (if not installed)**
```bash
# Mac
brew install git

# Windows: Download from git-scm.com
# Ubuntu/Debian
sudo apt install git
```

**Step 2: Clone the Repository**
```bash
cd ~/Desktop
git clone https://github.com/YOUR-USERNAME/khabribaba.git
cd khabribaba
```

**Step 3: Configure Git Identity**
```bash
# Same as Computer 1
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Step 4: Get Latest Code**
```bash
git checkout main
git pull origin main
```

**That's it!** Your second computer is ready.

## **3.2 Workflow Between Computers**

### **Leaving Computer 1 (Desktop at Home)**
```bash
# Finish your work
git add .
git commit -m "Work from home - updated homepage"
git push origin main

# If you have unfinished work:
git stash  # Saves changes temporarily
git pull origin main  # Get latest
```

### **Starting Computer 2 (Laptop at Office)**
```bash
# Get latest code
git pull origin main

# If you stashed work:
git stash pop  # Restores your changes

# Continue working...
```

## **3.3 Managing SSH Keys Across Computers**

**Option A: Same Key on Both Computers**
```bash
# On Computer 1, copy the private key
cat ~/.ssh/khabribaba-deploy
# Copy the output

# On Computer 2, create the same key
mkdir -p ~/.ssh
echo "PASTE_PRIVATE_KEY_HERE" > ~/.ssh/khabribaba-deploy
chmod 600 ~/.ssh/khabribaba-deploy
```

**Option B: Different Key for Each Computer (More Secure)**
```bash
# On Computer 2, generate new key
ssh-keygen -t ed25519 -C "laptop@khabribaba.com" -f ~/.ssh/khabribaba-laptop

# Add public key to server (SSH in and add to deployer's authorized_keys)
# Update GitHub Secrets with BOTH private keys (comma separated)
```

**Pro Tip: Use SSH Agent**
```bash
# Add key to SSH agent (runs in background)
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/khabribaba-deploy

# Now git push works without entering key each time
```

---

# **👥 PART 4: ADDING TEAM MEMBERS**

## **4.1 Adding Your First Collaborator**

### **Step 1: Invite Collaborator on GitHub**
1. Go to: `https://github.com/YOUR-USERNAME/khabribaba/settings/access`
2. Click **Invite a collaborator**
3. Enter their GitHub username or email
4. They'll receive invitation email

### **Step 2: Team Member Setup (Their Computer)**
**They need to do this:**

```bash
# 1. Install Git
# 2. Generate SSH key
ssh-keygen -t ed25519 -C "teammate@example.com"

# 3. Add to their GitHub account
# Go to GitHub → Settings → SSH and GPG keys → New SSH key
# Paste ~/.ssh/id_ed25519.pub

# 4. Clone repository
git clone git@github.com:YOUR-USERNAME/khabribaba.git
cd khabribaba

# 5. Set their identity
git config --global user.name "Teammate Name"
git config --global user.email "teammate@example.com"
```

### **Step 3: Create .env.example for Team**
```bash
# On YOUR COMPUTER, create example config
echo '# Database Configuration
DB_HOST=localhost
DB_NAME=khabribaba
DB_USER=root
DB_PASS=

# API Keys
GOOGLE_MAPS_API_KEY=your_key_here
STRIPE_API_KEY=sk_test_...

# Application
DEBUG=true
APP_URL=http://localhost:3000' > .env.example

# Add to git
echo '
# Environment files
.env
.env.local
.env.production' >> .gitignore

git add .env.example .gitignore
git commit -m "Add environment template"
git push origin main
```

## **4.2 Team Development Workflow**

### **Branch Strategy for Teams**
```bash
# Main branches
main         # Production-ready code
develop      # Integration branch

# Feature branches (from develop)
git checkout -b feature/user-auth
git checkout -b feature/payment-gateway

# Hotfix branches (from main, for urgent fixes)
git checkout -b hotfix/urgent-bug
```

### **Pull Request Process**

**Developer Creates Feature:**
```bash
# 1. Get latest
git checkout main
git pull origin main
git checkout -b feature/contact-page

# 2. Make changes
# 3. Commit
git add .
git commit -m "Add contact page with form validation"

# 4. Push
git push origin feature/contact-page
```

**Create Pull Request on GitHub:**
1. Go to repository → Pull Requests → New Pull Request
2. Base: `main` ← Compare: `feature/contact-page`
3. Add description, assign reviewers
4. Click **Create Pull Request**

**Reviewer Process:**
1. Go to PR → **Files changed**
2. Click line numbers to add comments
3. Can **Approve** or **Request changes**
4. When approved, click **Merge pull request**

### **Code Review Checklist File**
```bash
# Create CODE_REVIEW.md
echo '# Code Review Guidelines

## Before Requesting Review
- [ ] Code follows project style guide
- [ ] No console.log/debug statements in production code
- [ ] All tests pass locally
- [ ] Added/updated tests for new features
- [ ] Updated documentation (README, comments)
- [ ] No sensitive data (API keys, passwords)
- [ ] Checked for security vulnerabilities

## During Review
1. Understand the change purpose
2. Check for edge cases
3. Verify error handling
4. Test performance impact
5. Ensure backwards compatibility
6. Check accessibility (a11y)

## Common Issues to Watch
- SQL injection vulnerabilities
- XSS (Cross-site scripting)
- CSRF (Cross-site request forgery)
- Hardcoded credentials
- Memory leaks
- Race conditions' > CODE_REVIEW.md

git add CODE_REVIEW.md
git commit -m "Add code review guidelines"
git push origin main
```

## **4.3 Enhanced GitHub Actions for Teams**

**Update `.github/workflows/deploy.yml`:**
```yaml
name: Deploy Website

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  # Test on PR, deploy on push to main
  test:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: 🧪 Run Tests
      run: |
        echo "Running tests..."
        # Add your test commands here
        # npm test
        # phpunit
        
    - name: 📏 Lint Code
      run: |
        echo "Checking code quality..."
        # npx eslint .
        # php-cs-fixer fix --dry-run
        
  deploy:
    needs: test
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    
    steps:
    - name: 📦 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔑 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
    
    - name: 🚀 Deploy to Server
      run: |
        echo "🚀 Starting deployment..."
        echo "👤 Deployed by: ${{ github.actor }}"
        echo "📝 Commit: ${{ github.event.head_commit.message }}"
        
        # Backup current site
        ssh khabribaba "mkdir -p /tmp/backups && cp -r ${{ secrets.DEPLOY_PATH }} /tmp/backups/backup_\$(date +%Y%m%d_%H%M%S)"
        
        # Deploy
        rsync -avz --delete \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="README.md" \
          --exclude="CODE_REVIEW.md" \
          ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
        
        # Permissions
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type d -exec chmod 755 {} \;"
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type f -exec chmod 644 {} \;"
        
        echo "✅ Deployment complete!"
        
    - name: 📢 Notify Team
      if: success()
      run: |
        # Send notification (Slack, Discord, Email)
        echo "Notifying team of successful deployment..."
        # curl -X POST -H 'Content-type: application/json' \
        #   --data '{"text":"✅ khabribaba.com deployed successfully!"}' \
        #   ${{ secrets.SLACK_WEBHOOK }}
```

## **4.4 Team Communication Setup**

### **GitHub Project Board**
1. Go to repository → **Projects** tab
2. Click **New project** → **Board**
3. Add columns: Backlog, To Do, In Progress, Review, Done
4. Create issues for tasks, drag between columns

### **Issue Templates**
```bash
# Create .github/ISSUE_TEMPLATE/bug_report.md
echo '---
name: Bug Report
about: Report a bug or issue
title: "[BUG] "
labels: bug
assignees: ""
---

## Bug Description
Clear and concise description of the bug.

## Steps to Reproduce
1. Go to "..."
2. Click on "..."
3. Scroll down to "..."
4. See error

## Expected Behavior
What should happen?

## Actual Behavior
What actually happens?

## Screenshots
If applicable, add screenshots.

## Environment
- Device: [e.g., iPhone 12]
- OS: [e.g., iOS 14.4]
- Browser: [e.g., Chrome 88]
- Version: [e.g., 1.0.0]

## Additional Context
Add any other context about the problem here."' > .github/ISSUE_TEMPLATE/bug_report.md

# Create feature request template similarly
```

---

# **🤖 PART 5: ADVANCED AUTOMATION**

## **5.1 Using Cursor AI with CI/CD**

### **Create .cursorrules File**
```bash
# On YOUR COMPUTER, in project root
echo '# Cursor Rules for khabribaba.com
# These rules help AI understand our project

## Project Structure
- Use src/ for source code
- Use public/ for publicly accessible files
- Follow existing code patterns

## Code Style
- Use 2-space indentation
- Single quotes for strings
- Semicolons required
- Comment complex logic

## Security Rules
- Never hardcode API keys
- Validate all user inputs
- Use prepared statements for SQL
- Sanitize output

## Deployment Notes
- CI/CD runs on push to main
- Tests must pass before deployment
- Check .github/workflows/deploy.yml for pipeline

## Common Commands
# Start local server: npm run dev
# Run tests: npm test
# Deploy: git push origin main' > .cursorrules

git add .cursorrules
git commit -m "Add Cursor AI rules"
git push origin main
```

### **Cursor AI Commands for CI/CD**
```
# In Cursor chat, you can ask:
"Show me the deployment workflow"
"Help me fix the CI/CD error"
"Create a test for this component"
"Check for security vulnerabilities"
```

## **5.2 Automated Testing**

### **Basic Test Setup for Node.js**
```bash
# On YOUR COMPUTER
npm init -y
npm install --save-dev jest

# Create package.json scripts
echo '{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "coverage": "jest --coverage"
  }
}' > package.json

# Create simple test
mkdir tests
echo '// tests/sample.test.js
test("adds 1 + 2 to equal 3", () => {
  expect(1 + 2).toBe(3);
});' > tests/sample.test.js

git add package.json tests/
git commit -m "Add test setup"
git push origin main
```

### **Update GitHub Actions with Tests**
```yaml
# Add to deploy.yml
- name: 🧪 Run Tests
  run: |
    if [ -f "package.json" ]; then
      npm install
      npm test
    fi
    if [ -f "composer.json" ]; then
      composer install
      vendor/bin/phpunit
    fi
```

## **5.3 Zero-Downtime Deployments**

### **Blue-Green Deployment on Single Server**
```yaml
# Advanced deployment strategy
- name: 🚀 Zero-Downtime Deploy
  run: |
    echo "Setting up zero-downtime deployment..."
    
    # Deploy to staging directory
    STAGING_DIR="/home/deployer/staging_${{ github.sha }}"
    PROD_DIR="${{ secrets.DEPLOY_PATH }}"
    
    # Copy files to staging
    ssh khabribaba "rm -rf $STAGING_DIR && mkdir -p $STAGING_DIR"
    rsync -avz ./ khabribaba:$STAGING_DIR/
    
    # Switch symlink atomically
    ssh khabribaba "ln -sfn $STAGING_DIR $PROD_DIR.tmp && mv -T $PROD_DIR.tmp $PROD_DIR"
    
    # Cleanup old deployments (keep last 5)
    ssh khabribaba "ls -dt /home/deployer/staging_* | tail -n +6 | xargs rm -rf"
    
    echo "✅ Zero-downtime deployment complete!"
```

---

# **🚨 PART 6: TROUBLESHOOTING & MAINTENANCE**

## **6.1 Common CI/CD Failures**

### **Failure 1: Permission Denied (Publickey)**
**Solution:**
```bash
# Test SSH connection manually
ssh -i ~/.ssh/khabribaba-deploy -v deployer@khabribaba.com

# Check server authorized_keys
ssh yourusername@khabribaba.com
sudo su - deployer
cat ~/.ssh/authorized_keys
# Should have your public key
```

### **Failure 2: Rsync Fails**
**Solution:**
```yaml
# Add debug to workflow
- name: Debug Rsync
  run: |
    rsync -avz --dry-run ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
    # Check output for errors
```

### **Failure 3: GitHub Actions Timeout**
**Solution:**
```yaml
# Increase timeout
timeout-minutes: 10
# Add to job
```

## **6.2 Rollback Procedures**

### **Quick Rollback Script**
```bash
# Create scripts/rollback.sh on YOUR COMPUTER
echo '#!/bin/bash
# Rollback to previous version
echo "🚨 Initiating rollback..."

# SSH into server and restore from backup
BACKUP=$(ssh khabribaba "ls -t /tmp/backups/ | head -1")
if [ -z "$BACKUP" ]; then
  echo "❌ No backups found!"
  exit 1
fi

echo "📦 Restoring from: $BACKUP"
ssh khabribaba "cp -r /tmp/backups/$BACKUP/* ${{ secrets.DEPLOY_PATH }}/"

echo "✅ Rollback complete!"
echo "🔍 Check: http://khabribaba.com"' > scripts/rollback.sh

chmod +x scripts/rollback.sh
```

### **Git Revert (Code Level)**
```bash
# On YOUR COMPUTER
# Find bad commit
git log --oneline

# Revert it
git revert BAD_COMMIT_HASH
git push origin main
# CI/CD will deploy the revert
```

## **6.3 Monitoring and Alerts**

### **Simple Uptime Check**
```yaml
# Add to deploy.yml
- name: 🏥 Health Check
  run: |
    echo "Checking if site is up..."
    if curl -s -f http://khabribaba.com > /dev/null; then
      echo "✅ Site is responding"
    else
      echo "❌ Site is down!"
      exit 1
    fi
```

### **Add Email Notifications**
```yaml
# Add to deploy.yml
- name: 📧 Send Notification
  if: failure()
  uses: dawidd6/action-send-mail@v3
  with:
    server_address: smtp.gmail.com
    server_port: 465
    username: ${{ secrets.EMAIL_USER }}
    password: ${{ secrets.EMAIL_PASS }}
    subject: "❌ khabribaba.com Deployment Failed"
    to: your-email@example.com
    from: "CI/CD Bot"
    body: |
      Deployment failed!
      Repository: ${{ github.repository }}
      Commit: ${{ github.sha }}
      Actor: ${{ github.actor }}
      Check: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
```

## **🔄 Maintenance Checklist**

### **Weekly Tasks**
```bash
# 1. Check GitHub Actions runs
# Go to: https://github.com/YOUR-USERNAME/khabribaba/actions

# 2. Clean up old backups on server
ssh khabribaba "find /tmp/backups/ -type d -mtime +7 -exec rm -rf {} \;"

# 3. Update dependencies
npm audit fix
composer update

# 4. Review logs
ssh khabribaba "tail -100 /var/log/apache2/error.log"
```

### **Monthly Tasks**
```bash
# 1. Rotate SSH keys
ssh-keygen -t ed25519 -C "new-key@khabribaba.com" -f ~/.ssh/khabribaba-deploy-new

# 2. Update .cursorrules
# 3. Review and update tests
# 4. Backup database (if any)
```

---

# **📊 Quick Reference Cheat Sheet**

## **Git Commands**
```bash
# Daily use
git status                    # What changed?
git add .                     # Stage changes
git commit -m "message"       # Save locally
git push origin main          # Send to GitHub

# Working with branches
git checkout -b feature/name  # Create new branch
git checkout main            # Switch to main
git merge feature/name       # Merge branch
git branch -d feature/name   # Delete branch

# Fixing mistakes
git reset --soft HEAD~1      # Undo commit, keep changes
git checkout -- file.txt     # Discard file changes
git revert COMMIT_HASH       # Undo specific commit
```

## **SSH Commands**
```bash
# Generate key
ssh-keygen -t ed25519 -C "name@project.com"

# Test connection
ssh -i ~/.ssh/key user@server.com

# Copy key to server
ssh-copy-id -i ~/.ssh/key.pub user@server.com
```

## **GitHub Actions Triggers**
```yaml
# When to run
on:
  push:                 # On any push
  push:
    branches: [main]    # Only on main branch
  pull_request:         # On PR creation/update
  schedule:
    - cron: '0 2 * * *' # Daily at 2 AM
  workflow_dispatch:    # Manual trigger
```

## **Emergency Contacts**
```
1. GitHub Status: https://www.githubstatus.com/
2. Namecheap Support: https://www.namecheap.com/support/
3. SSH Debug: ssh -v user@host
4. Test Website: curl -I http://yoursite.com
```

---

# **🎯 Final Checklist Before Going Live**

## **Setup Complete?**
- [ ] GitHub repository created
- [ ] SSH keys generated and configured
- [ ] GitHub Secrets added
- [ ] .github/workflows/deploy.yml created
- [ ] First successful deployment
- [ ] Team members added (if any)
- [ ] .cursorrules created
- [ ] Backup strategy in place

## **Daily Workflow Verified?**
- [ ] Can edit locally in VS Code/Cursor
- [ ] Can commit and push to GitHub
- [ ] GitHub Actions trigger automatically
- [ ] Deployment completes successfully
- [ ] Site updates within 2-3 minutes
- [ ] Rollback procedure tested

## **Team Ready?**
- [ ] All members have Git installed
- [ ] SSH keys added to GitHub accounts
- [ ] Repository cloned locally
- [ ] Understand branch/PR workflow
- [ ] Know emergency procedures

---

**Remember:** The first setup is the hardest. Once it's working, you'll wonder how you ever lived without it. Every successful deployment builds confidence. Every automated test catches bugs before users see them. Every team member can contribute without fear of breaking production.

**Your new superpower:** You can deploy from anywhere, at any time, with anyone, and know exactly what changed, who changed it, and how to fix it if something goes wrong.

**Need help?

# Here is Advanced Version of Directly Starting With Cursor Ai, if your starting point is cursor Ai
# **Complete Beginner's Guide: Starting with Cursor AI and Ending with Auto-Deployment**

## **📋 Table of Contents**
1. **Opening Cursor for the First Time** - What you'll see
2. **Creating Your First Project in Cursor** - No GitHub yet
3. **Building Your Website Locally** - HTML/CSS/JavaScript
4. **Turning Your Project into a Time Machine (Git)** 
5. **Creating Your GitHub Account** - The cloud storage
6. **Connecting Cursor to GitHub** - Your first push
7. **Setting Up Your Namecheap Server** - One-time setup
8. **Automating Everything** - No more manual uploads
9. **Your Daily Workflow** - How it works every day

---

# **1. Opening Cursor for the First Time**

## **Before You Start:**
- You have Cursor installed (from cursor.sh)
- You have a Namecheap hosting account (khabribaba.com)
- You have NO GitHub account yet (we'll create one)

## **Step 1: Launch Cursor**
1. **Double-click** the Cursor icon on your Desktop/Mac
2. You'll see a welcome screen with options:
   - **Open Folder** (choose this)
   - **Clone Git Repository** (ignore for now)
   - **New Project** (also ignore)

## **Step 2: Create Your Project Folder**
```
1. Click "Open Folder"
2. Navigate to your Desktop (or Documents)
3. Click "New Folder" (bottom left)
4. Name it: khabribaba-site
5. Click "Open"
```

**What you see now:**
- Left sidebar: File explorer (empty for now)
- Main area: Welcome message
- Bottom: Terminal (this is important!)

## **Step 3: Create Your First File**
```
1. In the file explorer (left sidebar), right-click on "khabribaba-site"
2. Select "New File"
3. Name it: index.html
4. Press Enter
```

**Now type this in the editor:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>KhabriBaba - News Portal</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        header {
            background: #2c3e50;
            color: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 30px;
        }
        .news-item {
            background: white;
            padding: 20px;
            margin-bottom: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body>
    <header>
        <h1>📰 KhabriBaba News Portal</h1>
        <p>Latest news from around the world</p>
    </header>
    
    <div class="news-item">
        <h2>Breaking News</h2>
        <p>This website is now being built with Cursor AI!</p>
        <p><strong>Status:</strong> Under development</p>
    </div>
    
    <div class="news-item">
        <h2>Technology Update</h2>
        <p>We're setting up automatic deployment. Soon changes will go live instantly!</p>
    </div>
    
    <footer style="margin-top: 40px; text-align: center; color: #666;">
        <p>© 2024 KhabriBaba.com | Built with ❤️ using Cursor AI</p>
    </footer>
</body>
</html>
```

## **Step 4: Preview Your Site in Cursor**
```
1. Right-click on index.html in the file explorer
2. Select "Open with Live Preview" 
3. OR: Look for a small "browser" icon in the top-right of the editor
4. Click it to see your website inside Cursor!
```

**🎉 Congratulations!** You've created your first file in Cursor and can see it live.

---

# **2. Using Cursor AI to Help You**

## **The AI Assistant in Cursor:**
Cursor has **two ways** to get AI help:

### **Method 1: Chat (Bottom Left Icon)**
- Click the chat bubble icon (bottom left)
- Type: "Create a CSS file for my news website"
- Press Enter
- Cursor will create and explain the CSS

### **Method 2: Code Actions (Cmd/Ctrl + K)**
1. Place cursor where you want changes
2. Press `Cmd + K` (Mac) or `Ctrl + K` (Windows)
3. Type what you want: "Add a navigation menu"
4. Press Enter
5. Cursor will add the code

## **Let's Create More Files with AI:**

### **Create CSS File with AI:**
```
1. Open Chat (bottom left)
2. Type: "Create a file called style.css with modern CSS for a news website"
3. Press Enter
4. Cursor creates the file automatically
```

### **Create JavaScript File:**
```
In Chat, type: "Create a file called script.js that adds a dark mode toggle"
```

### **Your Project Structure Now:**
```
khabribaba-site/
├── index.html
├── style.css
├── script.js
└── (Cursor might create other files)
```

## **Step 5: Link Your CSS and JS**
**Edit index.html** (add these lines inside `<head>` and before `</body>`):
```html
<!-- In <head> section, add: -->
<link rel="stylesheet" href="style.css">

<!-- Before </body>, add: -->
<script src="script.js"></script>
```

---

# **3. Turning Your Project into a Time Machine (Git)**

## **What is Git?**
Think of Git as a **time machine + backup system** for your code. Every time you save, Git remembers what changed.

## **Step 6: Initialize Git in Cursor**
```
1. Open Terminal in Cursor:
   - Click "Terminal" in top menu
   - OR: View → Terminal
   - OR: Ctrl + ` (backtick key)

2. You'll see terminal at bottom. Type:
   git init

3. You'll see: "Initialized empty Git repository"
```

## **Step 7: Tell Git Who You Are**
```bash
# In the same terminal, type:
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## **Step 8: Create .gitignore (IMPORTANT!)**
This file tells Git what NOT to save (like passwords, system files).

**Using Cursor AI:**
```
1. In Chat, type: "Create a .gitignore file for a web project"
2. Cursor creates the file
3. Check it has these at minimum:
```

**.gitignore content:**
```gitignore
# System files
.DS_Store
Thumbs.db

# Environment files (passwords, API keys)
.env
.env.local
.env.production

# IDE files
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log
npm-debug.log*

# Dependencies
node_modules/
vendor/
```

## **Step 9: Your First Git Save (Commit)**
```bash
# In terminal:
git add .                     # Stage all files
git commit -m "First version of KhabriBaba"  # Save with message

# You'll see: "5 files changed" (or similar)
```

**🎉 Now your code is saved locally!** You can:
- See changes: `git status`
- See history: `git log`
- Undo mistakes: `git checkout -- filename`

---

# **4. Creating Your GitHub Account**

## **Step 10: Sign Up for GitHub**
```
1. Open browser (Chrome/Firefox/Safari)
2. Go to: https://github.com
3. Click "Sign up" (top right)
4. Enter:
   - Email (use one you can access)
   - Password (strong one!)
   - Username (like: khabribaba-dev)
5. Verify email (check your inbox)
6. Complete setup
```

## **Step 11: Create Repository on GitHub**
```
1. After login, click "+" (top right)
2. Select "New repository"
3. Fill in:
   - Repository name: khabribaba-site
   - Description: "News portal website"
   - Choose: Private (for now)
   - DO NOT check "Initialize with README"
4. Click "Create repository"
```

## **Step 12: Get Your Repository URL**
After creation, you'll see a page with commands. **Save this URL:**
```
https://github.com/YOUR-USERNAME/khabribaba-site.git
```

---

# **5. Connecting Cursor to GitHub**

## **Step 13: Connect Local Project to GitHub**
**Back in Cursor Terminal:**
```bash
# Copy these EXACTLY (replace YOUR-USERNAME):
git remote add origin https://github.com/YOUR-USERNAME/khabribaba-site.git
git branch -M main
git push -u origin main
```

**If asked for username/password:**
- Username: Your GitHub username
- Password: Create a **Personal Access Token** (see below)

## **Step 14: Create GitHub Personal Access Token**
**Why:** GitHub no longer accepts passwords for Git operations.

```
1. Go to GitHub → Click profile picture → Settings
2. Scroll to bottom → Developer settings
3. Click "Personal access tokens" → "Tokens (classic)"
4. Click "Generate new token" → "Generate new token (classic)"
5. Fill in:
   - Note: "Cursor AI Access"
   - Expiration: 90 days (recommended)
   - Select scopes: ✅ repo (all)
6. Click "Generate token"
7. COPY THE TOKEN IMMEDIATELY (you won't see it again!)
8. Use this as password when Git asks
```

## **Step 15: Verify Push Worked**
```
1. Refresh your GitHub repository page
2. You should see:
   - index.html
   - style.css  
   - script.js
   - .gitignore
3. 🎉 Your code is now on GitHub!
```

---

# **6. Setting Up Your Namecheap Server (One Time)**

## **Step 16: Get Server Information from Namecheap**
```
1. Login to Namecheap
2. Go to "Hosting" → "Dashboard"
3. Find your khabribaba.com hosting
4. Look for:
   - Server IP address
   - cPanel username
   - cPanel password
5. Save these in a text file temporarily
```

## **Step 17: Connect to Server via Cursor Terminal**
**In Cursor Terminal:**
```bash
# Replace with your info:
ssh your-cpanel-username@khabribaba.com
# Example: ssh john123@khabribaba.com

# Enter password when asked (you won't see typing)
```

**First time connection:**
- Type `yes` to "Are you sure you want to continue connecting?"

## **Step 18: Find Your Website Folder**
```bash
# After login, you're on the SERVER now!
# Type:
pwd
# Shows: /home/yourusername

# List files:
ls -la

# Look for: public_html/ or www/ or public/
# Namecheap usually uses: /home/username/public_html/

# Go to that folder:
cd public_html
ls -la
# You'll see existing files if your site was live
```

## **Step 19: Create a Backup of Current Site**
```bash
# On server, in public_html folder:
cp -r . /home/yourusername/backup-old-site/
# This copies everything to backup folder
```

---

# **7. The Magic: Automating Deployment**

## **Step 20: Generate SSH Keys (The Secure Bridge)**
**Back in Cursor Terminal (on YOUR COMPUTER, not server):**
```bash
# Close server connection first (type: exit)
# Now on your computer's terminal:

ssh-keygen -t ed25519 -C "github-actions@khabribaba.com" -f ~/.ssh/khabribaba-key

# When asked for passphrase, press Enter (empty)
```

**This creates two files:**
- `~/.ssh/khabribaba-key` → **PRIVATE KEY** (never share!)
- `~/.ssh/khabribaba-key.pub` → **PUBLIC KEY** (goes to server)

## **Step 21: Copy Public Key to Server**
```bash
# View and copy public key:
cat ~/.ssh/khabribaba-key.pub
# Select ALL text (starts with ssh-ed25519...)
# Copy to clipboard

# SSH into server again:
ssh your-username@khabribaba.com

# On server, run:
mkdir -p ~/.ssh
echo "PASTE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# Test it works (back on YOUR computer):
ssh -i ~/.ssh/khabribaba-key your-username@khabribaba.com
# Should login without password!
```

## **Step 22: Add GitHub Secrets**
```
1. Go to your GitHub repository
2. Settings → Secrets and variables → Actions
3. Click "New repository secret"
```

**Add these 4 secrets:**

**Secret 1: SSH_PRIVATE_KEY**
```bash
# In Cursor Terminal (your computer):
cat ~/.ssh/khabribaba-key
# Copy ALL text including BEGIN/END lines
```
Paste into GitHub as: `SSH_PRIVATE_KEY`

**Secret 2: SSH_HOST**
```
khabribaba.com
```

**Secret 3: SSH_USERNAME**
```
your-cpanel-username
```

**Secret 4: DEPLOY_PATH**
```
/home/yourusername/public_html/
```

## **Step 23: Create the Automation File in Cursor**

**Using Cursor AI to create the workflow:**
```
1. In Cursor Chat, type:
   "Create a GitHub Actions workflow file that deploys my website to my Namecheap server using SSH"

2. Cursor will create .github/workflows/deploy.yml
3. Replace its content with this:
```

**.github/workflows/deploy.yml:**
```yaml
name: 🚀 Deploy to Namecheap

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: 📥 Get latest code
      uses: actions/checkout@v3
      
    - name: 🔐 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
        
    - name: 📤 Deploy files
      run: |
        echo "Starting deployment of khabribaba.com..."
        
        # Backup current site
        ssh khabribaba "mkdir -p /tmp/site-backups && cp -r ${{ secrets.DEPLOY_PATH }} /tmp/site-backups/backup_\$(date +%Y%m%d_%H%M%S)"
        
        # Deploy using rsync (efficient file copy)
        rsync -avz --delete \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="README.md" \
          ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
        
        # Fix permissions
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type d -exec chmod 755 {} \;"
        ssh khabribaba "find ${{ secrets.DEPLOY_PATH }} -type f -exec chmod 644 {} \;"
        
        echo "✅ Deployment complete! Visit: http://khabribaba.com"
```

## **Step 24: Save and Push the Automation**
```bash
# In Cursor Terminal:
git add .github/workflows/deploy.yml
git commit -m "Add automatic deployment"
git push origin main
```

---

# **8. The Moment of Truth: First Automated Deployment**

## **Step 25: Watch the Magic Happen**
```
1. Go to your GitHub repository
2. Click "Actions" tab (top menu)
3. You'll see "🚀 Deploy to Namecheap" running
4. Click on it to watch live logs
5. Wait for green checkmark ✅ (2-3 minutes)
```

## **Step 26: Make a Test Change**
**In Cursor:**
1. Edit `index.html`
2. Add this before `</footer>`:
```html
<div class="news-item" style="background: #e8f5e9;">
    <h2>🚀 Deployment Successful!</h2>
    <p>This update was deployed automatically via GitHub Actions!</p>
    <p><strong>Timestamp:</strong> <span id="current-time"></span></p>
</div>

<script>
    document.getElementById('current-time').textContent = new Date().toLocaleString();
</script>
```

## **Step 27: Deploy the Change**
```bash
# In Cursor Terminal:
git add index.html
git commit -m "Test automatic deployment"
git push origin main
```

## **Step 28: Verify**
```
1. Wait 2 minutes
2. Open browser
3. Go to: http://khabribaba.com
4. You should see the green "Deployment Successful" box!
```

**🎉 CONGRATULATIONS!** You've successfully:
1. Created a website in Cursor AI
2. Saved it with Git
3. Pushed to GitHub  
4. Set up automatic deployment
5. Made changes that auto-deploy to live site!

---

# **9. Your Daily Workflow from Now On**

## **Every time you want to update your site:**

### **Morning (Start Work):**
```bash
# 1. Open Cursor
# 2. Open your project folder
# 3. Get latest code (if working with others):
git pull origin main
```

### **Making Changes:**
```
1. Edit files in Cursor
2. Use AI help when needed (Cmd/Ctrl + K)
3. Preview locally (Live Preview feature)
4. Test everything works
```

### **Saving and Deploying:**
```bash
# In Cursor Terminal:
git add .
git commit -m "Describe your changes"
git push origin main

# That's it! Wait 2 minutes, site updates automatically.
```

## **Using Cursor AI Effectively:**

### **Common AI Commands:**
```
"Add a contact form to index.html"
"Fix the responsive design for mobile"
"Create an about.html page"
"Optimize images for web"
"Add Google Analytics tracking"
"Create a sitemap.xml file"
```

### **Working with Multiple Files:**
```
1. To see all files: Ctrl+P (Command Palette)
2. To search across files: Ctrl+Shift+F
3. To split screen: Right-click tab → Split Right
```

## **Troubleshooting Common Issues:**

### **Git Push Fails:**
```bash
# Get latest changes first:
git pull origin main
# Resolve conflicts if any, then:
git add .
git commit -m "Merge changes"
git push origin main
```

### **Deployment Failed in GitHub Actions:**
```
1. Go to GitHub → Actions
2. Click failed run
3. Read error message
4. Common fixes:
   - Wrong SSH key
   - Server out of space
   - Permission issues
```

### **Site Not Updating:**
```bash
# Check if deployment ran:
# Go to GitHub → Actions (look for latest run)

# Manual deploy if needed:
ssh your-username@khabribaba.com
cd public_html
git pull /path/to/backup
```

---

# **10. Advanced Cursor Features for Your Workflow**

## **Cursor Rules File (.cursorrules)**
Create a file called `.cursorrules` in your project root:

```bash
# In Cursor Chat, type:
"Create a .cursorrules file for my news website project with these guidelines:
- Use semantic HTML5
- CSS should be mobile-first
- JavaScript should be modern ES6+
- Follow accessibility standards
- Add comments for complex logic"
```

## **Using Cursor with GitHub Issues:**
```
1. Create issues on GitHub for bugs/features
2. In Cursor Chat: "Show me open GitHub issues"
3. Work on an issue: "Implement issue #1"
4. When done: "Create a pull request for my changes"
```

## **Keyboard Shortcuts for Speed:**
```
Cmd/Ctrl + S          - Save file
Cmd/Ctrl + K          - AI command
Cmd/Ctrl + /          - Toggle comment
Cmd/Ctrl + F          - Find in file
Cmd/Ctrl + Shift + F  - Find in all files
Cmd/Ctrl + `          - Toggle terminal
```

---

# **📊 Quick Reference Card**

## **When You Open Cursor Tomorrow:**
```
1. Open Cursor
2. Open Folder → khabribaba-site
3. Make changes
4. Terminal: git add . && git commit -m "..." && git push
5. Wait 2 minutes
6. Check khabribaba.com
```

## **Essential Git Commands:**
```bash
git status              # What changed?
git add filename        # Stage one file
git add .              # Stage all files
git commit -m "msg"    # Save locally
git push               # Send to GitHub
git pull               # Get updates
git log --oneline      # See history
```

## **Essential SSH Commands:**
```bash
# Manual deploy if automation breaks:
scp -r * your-username@khabribaba.com:/home/username/public_html/

# Check server space:
ssh your-username@khabribaba.com "df -h"

# View logs:
ssh your-username@khabribaba.com "tail -f /var/log/apache2/error.log"
```

---

# **🎯 Success Checklist**

**By now you should have:**
- ✅ Cursor project with HTML/CSS/JS
- ✅ Git repository initialized
- ✅ GitHub account and repository
- ✅ Code pushed to GitHub
- ✅ SSH keys generated
- ✅ GitHub Secrets configured
- ✅ GitHub Actions workflow created
- ✅ First successful automated deployment
- ✅ Made a change and seen it go live automatically

**If stuck at any step:**
1. Check error messages carefully
2. Google the exact error
3. Ask in Cursor Chat: "Help me fix [error]"
4. Check GitHub Actions logs

---

# **📞 Getting Help**

## **Common Problems and Solutions:**

### **"Permission denied (publickey)"**
```bash
# Fix: Regenerate and re-add SSH key
ssh-keygen -t ed25519 -f ~/.ssh/khabribaba-key-new
# Update GitHub Secrets with new key
# Add new public key to server
```

### **"Repository not found"**
```
# Fix: Check repository URL
git remote -v
# If wrong: git remote set-url origin correct-url
```

### **"Push rejected"**
```bash
# Fix: Pull first, then push
git pull origin main --rebase
git push origin main
```

## **Where to Ask for Help:**
1. **Cursor Chat:** "Help me with [problem]"
2. **GitHub Issues:** In your repository
3. **Stack Overflow:** Tag with [github-actions] [ssh]
4. **Namecheap Support:** For server issues

---

# **🚀 Next Steps After Mastery**

## **Week 2: Add More Features**
- Contact form with PHP
- Database for news articles
- User authentication
- Email newsletter

## **Month 2: Professionalize**
- Custom domain (if not already)
- SSL certificate (HTTPS)
- CDN for faster loading
- Analytics integration

## **Month 3: Scale Up**
- Staging environment
- Automated testing
- Performance monitoring
- Backup automation

---

**Remember:** The setup was the hard part. From now on, every change is just:
1. Edit in Cursor
2. `git add . && git commit -m "..." && git push`
3. Wait 2 minutes
4. Changes are live!

**Welcome to modern web development!** 🎉

# When You Are Building/Working With A PHP Project Do Follow All Steps from Here considering Cursor is your Starting Point
# **Complete PHP + Cursor AI + CI/CD Guide for khabribaba.com**

## **📋 PHP-Specific Table of Contents**
1. **Opening Cursor with Existing PHP Site** - If you already have PHP files
2. **Creating New PHP Site in Cursor** - If starting fresh
3. **Local PHP Development Setup** - XAMPP/MAMP/WAMP
4. **PHP-Specific Git Setup** - What to include/exclude
5. **Database Handling with CI/CD** - MySQL/phpMyAdmin
6. **PHP Security Considerations** - .env files, credentials
7. **PHP-Specific GitHub Actions** - Composer, dependencies
8. **Deploying PHP to Namecheap** - cPanel specifics
9. **Common PHP Issues & Solutions** - Permissions, sessions, errors
10. **Advanced PHP Features** - APIs, authentication, caching

---

# **1. Opening Cursor with Existing PHP Site**

## **Scenario A: You have PHP files on your computer**
```
1. Launch Cursor
2. Click "Open Folder"
3. Navigate to where your PHP files are:
   - Maybe: C:\xampp\htdocs\khabribaba\ (Windows)
   - Maybe: /Applications/MAMP/htdocs/khabribaba/ (Mac)
   - Maybe: /var/www/html/khabribaba/ (Linux)
4. Click "Open"
```

## **Scenario B: You need to download from Namecheap server first**
```bash
# Open Cursor Terminal
# Navigate to where you want the project
cd ~/Desktop

# Download your entire site from Namecheap
scp -r your-username@khabribaba.com:/home/yourusername/public_html/* ./khabribaba-php/

# Open this folder in Cursor
# Cursor → Open Folder → Desktop → khabribaba-php
```

## **What you should see in Cursor:**
```
khabribaba-php/
├── index.php
├── about.php
├── contact.php
├── includes/
│   ├── header.php
│   ├── footer.php
│   └── config.php
├── css/
│   └── style.css
├── js/
│   └── script.js
└── images/
```

---

# **2. Creating New PHP Site in Cursor**

## **Step 1: Create Project Structure**
**In Cursor Chat, type:**
```
"Create a PHP website structure for a news portal with these files:
- index.php (homepage)
- about.php
- contact.php
- news.php (listing)
- single-news.php (single article)
- includes/header.php
- includes/footer.php
- includes/database.php
- css/style.css
- js/script.js
- .htaccess file
- README.md"
```

## **Step 2: Create index.php with AI**
**In Cursor Chat:**
```
"Create an index.php file for a news website with:
1. PHP include for header/footer
2. Database connection
3. Latest news listing
4. Featured stories
5. Categories sidebar
6. Pagination
7. Search form"
```

**Example index.php:**
```php
<?php
// index.php - KhabriBaba News Portal
session_start();
require_once 'includes/database.php';
require_once 'includes/functions.php';

$page_title = "Latest News - KhabriBaba";
include 'includes/header.php';

// Get latest news
$news = get_latest_news(10);
?>

<div class="container">
    <div class="row">
        <div class="col-md-8">
            <h1 class="mb-4">Latest News</h1>
            
            <?php if (empty($news)): ?>
                <div class="alert alert-info">No news articles found.</div>
            <?php else: ?>
                <?php foreach ($news as $article): ?>
                <div class="card mb-4">
                    <?php if ($article['featured_image']): ?>
                    <img src="uploads/<?php echo htmlspecialchars($article['featured_image']); ?>" 
                         class="card-img-top" alt="<?php echo htmlspecialchars($article['title']); ?>">
                    <?php endif; ?>
                    
                    <div class="card-body">
                        <h2 class="card-title">
                            <a href="single-news.php?id=<?php echo $article['id']; ?>">
                                <?php echo htmlspecialchars($article['title']); ?>
                            </a>
                        </h2>
                        
                        <p class="text-muted">
                            <small>
                                By <?php echo htmlspecialchars($article['author']); ?> 
                                | <?php echo date('F j, Y', strtotime($article['published_at'])); ?>
                                | Category: <?php echo htmlspecialchars($article['category']); ?>
                            </small>
                        </p>
                        
                        <p class="card-text">
                            <?php echo truncate_text($article['content'], 200); ?>
                        </p>
                        
                        <a href="single-news.php?id=<?php echo $article['id']; ?>" 
                           class="btn btn-primary">Read More</a>
                    </div>
                </div>
                <?php endforeach; ?>
            <?php endif; ?>
            
            <!-- Pagination -->
            <nav aria-label="Page navigation">
                <ul class="pagination">
                    <li class="page-item"><a class="page-link" href="#">Previous</a></li>
                    <li class="page-item active"><a class="page-link" href="#">1</a></li>
                    <li class="page-item"><a class="page-link" href="#">2</a></li>
                    <li class="page-item"><a class="page-link" href="#">3</a></li>
                    <li class="page-item"><a class="page-link" href="#">Next</a></li>
                </ul>
            </nav>
        </div>
        
        <div class="col-md-4">
            <!-- Sidebar -->
            <?php include 'includes/sidebar.php'; ?>
        </div>
    </div>
</div>

<?php include 'includes/footer.php'; ?>
```

## **Step 3: Create Database Configuration**
**In Cursor Chat:**
```
"Create a secure database configuration file with:
1. Database connection using PDO
2. Error handling
3. Configuration from environment variables
4. Helper functions for queries"
```

**includes/database.php:**
```php
<?php
// database.php - Secure database connection

class Database {
    private $host;
    private $dbname;
    private $username;
    private $password;
    private $conn;
    
    public function __construct() {
        // Load from environment variables
        $this->host = getenv('DB_HOST') ?: 'localhost';
        $this->dbname = getenv('DB_NAME') ?: 'khabribaba';
        $this->username = getenv('DB_USER') ?: 'root';
        $this->password = getenv('DB_PASS') ?: '';
    }
    
    public function connect() {
        try {
            $this->conn = new PDO(
                "mysql:host={$this->host};dbname={$this->dbname};charset=utf8mb4",
                $this->username,
                $this->password,
                [
                    PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
                    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
                    PDO::ATTR_EMULATE_PREPARES => false
                ]
            );
            return $this->conn;
        } catch (PDOException $e) {
            // Log error but don't expose details
            error_log("Database connection failed: " . $e->getMessage());
            
            // Show user-friendly message
            if (getenv('APP_ENV') === 'development') {
                die("Database connection error: " . $e->getMessage());
            } else {
                die("Database connection failed. Please try again later.");
            }
        }
    }
    
    public function query($sql, $params = []) {
        $stmt = $this->conn->prepare($sql);
        $stmt->execute($params);
        return $stmt;
    }
}

// Global helper functions
function get_latest_news($limit = 10) {
    $db = new Database();
    $conn = $db->connect();
    
    $stmt = $conn->prepare("
        SELECT * FROM news 
        WHERE published = 1 AND published_at <= NOW()
        ORDER BY published_at DESC 
        LIMIT :limit
    ");
    $stmt->bindValue(':limit', $limit, PDO::PARAM_INT);
    $stmt->execute();
    
    return $stmt->fetchAll();
}

function get_news_by_id($id) {
    $db = new Database();
    $conn = $db->connect();
    
    $stmt = $conn->prepare("
        SELECT * FROM news 
        WHERE id = :id AND published = 1
    ");
    $stmt->execute([':id' => $id]);
    
    return $stmt->fetch();
}

// Other helper functions...
?>
```

---

# **3. Local PHP Development Setup**

## **For Windows (XAMPP):**
```
1. Install XAMPP: https://www.apachefriends.org/
2. During install, choose default location: C:\xampp\
3. Start XAMPP Control Panel
4. Start Apache and MySQL
5. Your PHP files go in: C:\xampp\htdocs\khabribaba\
6. Access via: http://localhost/khabribaba/
```

## **For Mac (MAMP):**
```
1. Install MAMP: https://www.mamp.info/
2. Open MAMP, click Preferences
3. Web Server: Set document root to your project folder
4. Start Servers
5. Access via: http://localhost:8888/
```

## **For Linux (LAMP):**
```bash
# Install LAMP stack
sudo apt update
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql

# Enable Apache rewrite module
sudo a2enmod rewrite

# Set permissions
sudo chown -R $USER:$USER /var/www/html/khabribaba

# Restart Apache
sudo systemctl restart apache2
```

## **Test PHP is Working:**
**Create test.php in Cursor:**
```php
<?php
phpinfo();
?>
```
**Save to:** `htdocs/khabribaba/test.php`
**Browse to:** `http://localhost/khabribaba/test.php`

---

# **4. PHP-Specific Git Setup**

## **.gitignore for PHP Projects**
**In Cursor Chat:**
```
"Create a comprehensive .gitignore file for a PHP project with:
- Environment files
- Uploaded files directory
- Session files
- Cache files
- Log files
- Composer dependencies
- IDE files
- Operating system files
- Database dumps"
```

**.gitignore:**
```gitignore
# Environment
.env
.env.local
.env.production
.env.development

# Uploads (user-generated content)
/uploads/*
!/uploads/.gitkeep

# Sessions
/sessions/*
!/sessions/.gitkeep

# Cache
/cache/*
!/cache/.gitkeep

# Logs
*.log
error_log
access_log

# Composer
/vendor/
composer.lock
composer.phar

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
Desktop.ini

# Database
*.sql
*.sqlite
/db-backups/

# Temporary files
*.tmp
*.temp

# XAMPP/MAMP specific
/xampp/
/mamp/
/htdocs/

# PHPUnit
/phpunit.xml
.phpunit.result.cache

# Development tools
/node_modules/
/npm-debug.log*
```

## **Create .gitkeep files for empty directories:**
```bash
# In Cursor Terminal:
touch uploads/.gitkeep
touch sessions/.gitkeep
touch cache/.gitkeep
touch logs/.gitkeep
```

## **Initialize Git for PHP Project:**
```bash
# In Cursor Terminal:
git init
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Add all files except ignored ones
git add .

# First commit
git commit -m "Initial PHP project setup"
```

---

# **5. Database Handling with CI/CD**

## **Local Development Database:**
```bash
# Using phpMyAdmin (comes with XAMPP/MAMP):
1. Go to: http://localhost/phpmyadmin
2. Create database: khabribaba
3. Create user: khabribaba_user
4. Import SQL if you have existing database
```

## **Create database.sql for setup:**
**In Cursor Chat:**
```
"Create an SQL file for a news website database with:
1. News table with title, content, author, category, featured_image, published_at
2. Categories table
3. Users table for admin
4. Comments table
5. Sample data insertion"
```

**sql/schema.sql:**
```sql
-- Database schema for khabribaba.com

CREATE DATABASE IF NOT EXISTS khabribaba;
USE khabribaba;

-- News table
CREATE TABLE news (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    content LONGTEXT NOT NULL,
    excerpt TEXT,
    author VARCHAR(100) NOT NULL,
    category_id INT,
    featured_image VARCHAR(255),
    views INT DEFAULT 0,
    published BOOLEAN DEFAULT FALSE,
    published_at DATETIME,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_published (published, published_at),
    INDEX idx_category (category_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Categories table
CREATE TABLE categories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Users table (for admin)
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(100),
    role ENUM('admin', 'editor', 'author') DEFAULT 'author',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login DATETIME
);

-- Comments table
CREATE TABLE comments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    news_id INT NOT NULL,
    user_name VARCHAR(100) NOT NULL,
    user_email VARCHAR(100),
    content TEXT NOT NULL,
    approved BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (news_id) REFERENCES news(id) ON DELETE CASCADE,
    INDEX idx_news_id (news_id),
    INDEX idx_approved (approved)
);

-- Sample data
INSERT INTO categories (name, slug) VALUES
('Technology', 'technology'),
('Politics', 'politics'),
('Sports', 'sports'),
('Entertainment', 'entertainment'),
('Business', 'business');

INSERT INTO news (title, slug, content, author, category_id, published, published_at) VALUES
('PHP 8.3 Released with New Features', 'php-83-released', 'PHP 8.3 brings exciting new features...', 'John Doe', 1, TRUE, NOW()),
('Local Elections Underway', 'local-elections-underway', 'Citizens are casting their votes...', 'Jane Smith', 2, TRUE, NOW());

-- Create admin user (password: admin123)
INSERT INTO users (username, email, password_hash, full_name, role) VALUES
('admin', 'admin@khabribaba.com', '$2y$10$YourHashedPasswordHere', 'Site Administrator', 'admin');
```

## **Database Migration Strategy for CI/CD:**

### **Create migration script:**
**scripts/migrate.php:**
```php
<?php
// migrate.php - Safe database migrations
require_once __DIR__ . '/../includes/database.php';

class Migrations {
    private $db;
    
    public function __construct() {
        $this->db = new Database();
        $this->conn = $this->db->connect();
    }
    
    public function run() {
        echo "Starting database migrations...\n";
        
        // Create migrations table if not exists
        $this->conn->exec("
            CREATE TABLE IF NOT EXISTS migrations (
                id INT PRIMARY KEY AUTO_INCREMENT,
                migration_name VARCHAR(255) UNIQUE NOT NULL,
                batch INT NOT NULL,
                applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            )
        ");
        
        // Get already applied migrations
        $stmt = $this->conn->query("SELECT migration_name FROM migrations");
        $applied = $stmt->fetchAll(PDO::FETCH_COLUMN);
        
        // Define migrations in order
        $migrations = [
            '001_create_news_table.sql',
            '002_create_categories_table.sql',
            '003_create_users_table.sql',
            '004_create_comments_table.sql',
            '005_add_sample_data.sql'
        ];
        
        foreach ($migrations as $migration) {
            if (!in_array($migration, $applied)) {
                echo "Applying: $migration\n";
                $this->applyMigration($migration);
            }
        }
        
        echo "Migrations completed!\n";
    }
    
    private function applyMigration($filename) {
        $path = __DIR__ . "/../sql/$filename";
        
        if (!file_exists($path)) {
            throw new Exception("Migration file not found: $filename");
        }
        
        $sql = file_get_contents($path);
        
        // Execute migration within transaction
        $this->conn->beginTransaction();
        try {
            $this->conn->exec($sql);
            
            // Record migration
            $stmt = $this->conn->prepare(
                "INSERT INTO migrations (migration_name, batch) VALUES (?, 1)"
            );
            $stmt->execute([$filename]);
            
            $this->conn->commit();
            echo "✓ Applied: $filename\n";
        } catch (Exception $e) {
            $this->conn->rollBack();
            echo "✗ Failed: $filename - " . $e->getMessage() . "\n";
            throw $e;
        }
    }
}

// Run migrations
if (php_sapi_name() === 'cli') {
    $migrations = new Migrations();
    $migrations->run();
} else {
    die("This script should be run from command line.");
}
```

---

# **6. PHP Security Considerations**

## **Environment Configuration:**
**Create .env.example:**
```env
# Database Configuration
DB_HOST=localhost
DB_NAME=khabribaba
DB_USER=root
DB_PASS=

# Application Configuration
APP_ENV=development  # development, staging, production
APP_URL=http://localhost/khabribaba
APP_DEBUG=true

# Security
APP_KEY=generate_secure_key_here
JWT_SECRET=your_jwt_secret_here

# Email Configuration
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password

# Third-party APIs
GOOGLE_MAPS_API_KEY=your_key_here
RECAPTCHA_SITE_KEY=your_site_key
RECAPTCHA_SECRET_KEY=your_secret_key
```

## **Create environment loader:**
**includes/config.php:**
```php
<?php
// config.php - Environment configuration

// Load .env file
function loadEnvironment() {
    $envPath = dirname(__DIR__) . '/.env';
    
    if (file_exists($envPath)) {
        $lines = file($envPath, FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
        
        foreach ($lines as $line) {
            if (strpos(trim($line), '#') === 0) {
                continue; // Skip comments
            }
            
            list($name, $value) = explode('=', $line, 2);
            $name = trim($name);
            $value = trim($value);
            
            if (!array_key_exists($name, $_ENV)) {
                $_ENV[$name] = $value;
                putenv("$name=$value");
            }
        }
    }
}

// Load environment variables
loadEnvironment();

// Define constants for easy access
define('DB_HOST', getenv('DB_HOST') ?: 'localhost');
define('DB_NAME', getenv('DB_NAME') ?: 'khabribaba');
define('DB_USER', getenv('DB_USER') ?: 'root');
define('DB_PASS', getenv('DB_PASS') ?: '');

define('APP_ENV', getenv('APP_ENV') ?: 'production');
define('APP_DEBUG', getenv('APP_DEBUG') === 'true');
define('APP_URL', getenv('APP_URL') ?: 'http://khabribaba.com');

// Security settings
ini_set('session.cookie_httponly', 1);
ini_set('session.use_only_cookies', 1);
ini_set('session.cookie_secure', APP_ENV === 'production' ? 1 : 0);

// Error reporting based on environment
if (APP_DEBUG) {
    error_reporting(E_ALL);
    ini_set('display_errors', 1);
} else {
    error_reporting(0);
    ini_set('display_errors', 0);
}

// Set timezone
date_default_timezone_set('Asia/Kolkata');
?>
```

## **.htaccess for Security:**
```apache
# .htaccess - Security and URL rewriting

# Enable rewrite engine
RewriteEngine On

# Security headers
Header set X-Content-Type-Options "nosniff"
Header set X-Frame-Options "SAMEORIGIN"
Header set X-XSS-Protection "1; mode=block"
Header set Referrer-Policy "strict-origin-when-cross-origin"

# Block access to sensitive files
<FilesMatch "\.(env|log|sql|ini|conf)$">
    Order allow,deny
    Deny from all
</FilesMatch>

<FilesMatch "^\.">
    Order allow,deny
    Deny from all
</FilesMatch>

# Protect directories
<Directory "includes">
    Order deny,allow
    Deny from all
</Directory>

<Directory "sql">
    Order deny,allow
    Deny from all
</Directory>

<Directory "vendor">
    Order deny,allow
    Deny from all
</Directory>

# URL rewriting for pretty URLs
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^news/([0-9]+)/?$ single-news.php?id=$1 [L,QSA]
RewriteRule ^category/([a-z0-9-]+)/?$ category.php?slug=$1 [L,QSA]
RewriteRule ^author/([a-z0-9-]+)/?$ author.php?username=$1 [L,QSA]

# Redirect to HTTPS in production
RewriteCond %{HTTPS} off
RewriteCond %{HTTP_HOST} ^khabribaba\.com$ [NC]
RewriteRule ^(.*)$ https://khabribaba.com/$1 [R=301,L]

# Prevent directory listing
Options -Indexes

# Custom error pages
ErrorDocument 404 /404.php
ErrorDocument 403 /403.php
ErrorDocument 500 /500.php

# Compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/css text/javascript application/javascript
</IfModule>

# Cache control
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
</IfModule>
```

---

# **7. PHP-Specific GitHub Actions**

## **Complete deploy.yml for PHP:**
```yaml
name: 🚀 Deploy PHP to Namecheap

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  PHP_VERSION: '8.1'

jobs:
  # Test PHP code before deployment
  test-php:
    runs-on: ubuntu-latest
    
    steps:
    - name: 📥 Checkout code
      uses: actions/checkout@v3
    
    - name: 🐘 Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: ${{ env.PHP_VERSION }}
        extensions: mbstring, xml, curl, gd, mysqli, pdo_mysql
        coverage: none
    
    - name: 📦 Install Composer dependencies
      run: |
        if [ -f "composer.json" ]; then
          composer install --no-progress --no-suggest
        fi
    
    - name: 🧪 Run PHP tests
      run: |
        # Check for syntax errors
        find . -name "*.php" -exec php -l {} \;
        
        # Run custom tests if exist
        if [ -f "phpunit.xml" ]; then
          vendor/bin/phpunit
        fi
    
    - name: 🔍 Security scan
      run: |
        # Check for common vulnerabilities
        echo "Checking for exposed credentials..."
        if grep -r "password\|secret\|key\|token" . --include="*.php" | grep -v ".env.example" | grep -v ".git"; then
          echo "⚠️  Possible secrets found in code"
          exit 1
        fi
        
        echo "Checking for SQL injection vulnerabilities..."
        # Add your security checks here
    
    - name: 📏 Code quality check
      run: |
        if [ -f "composer.json" ] && composer show | grep -q "squizlabs/php_codesniffer"; then
          vendor/bin/phpcs --standard=PSR12 .
        fi

  # Deploy to production
  deploy:
    needs: test-php
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    
    steps:
    - name: 📥 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔑 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
    
    - name: 🐘 Check server PHP version
      run: |
        echo "Checking server PHP version..."
        ssh khabribaba "php --version"
    
    - name: 🗃️ Backup database (optional)
      if: ${{ secrets.BACKUP_DATABASE == 'true' }}
      run: |
        echo "Creating database backup..."
        ssh khabribaba "mysqldump -u ${{ secrets.DB_USER }} -p${{ secrets.DB_PASS }} ${{ secrets.DB_NAME }} > /tmp/db_backup_$(date +%Y%m%d_%H%M%S).sql"
    
    - name: 📤 Deploy PHP files
      run: |
        echo "Deploying PHP application..."
        
        # Create environment file on server
        cat > .env.production << EOF
        # Auto-generated by GitHub Actions
        DB_HOST=${{ secrets.DB_HOST }}
        DB_NAME=${{ secrets.DB_NAME }}
        DB_USER=${{ secrets.DB_USER }}
        DB_PASS=${{ secrets.DB_PASS }}
        
        APP_ENV=production
        APP_DEBUG=false
        APP_URL=https://khabribaba.com
        
        # Add other environment variables from secrets
        SMTP_HOST=${{ secrets.SMTP_HOST }}
        SMTP_USER=${{ secrets.SMTP_USER }}
        SMTP_PASS=${{ secrets.SMTP_PASS }}
        EOF
        
        # Sync files (exclude development files)
        rsync -avz --delete \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="README.md" \
          --exclude="composer.json" \
          --exclude="composer.lock" \
          --exclude="vendor/" \
          --exclude="node_modules/" \
          --exclude="*.sql" \
          --exclude=".env.example" \
          --exclude=".env.local" \
          --exclude=".env.development" \
          --include=".env.production" \
          ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
        
        # Set proper permissions for PHP
        ssh khabribaba "
          # Set directory permissions
          find ${{ secrets.DEPLOY_PATH }} -type d -exec chmod 755 {} \;
          
          # Set file permissions
          find ${{ secrets.DEPLOY_PATH }} -type f -exec chmod 644 {} \;
          
          # Special permissions for uploads and cache
          chmod -R 775 ${{ secrets.DEPLOY_PATH }}/uploads 2>/dev/null || true
          chmod -R 775 ${{ secrets.DEPLOY_PATH }}/cache 2>/dev/null || true
          chmod -R 775 ${{ secrets.DEPLOY_PATH }}/sessions 2>/dev/null || true
          
          # Set ownership for PHP to write
          chown -R ${{ secrets.SSH_USERNAME }}:www-data ${{ secrets.DEPLOY_PATH }}
          
          # Protect .env file
          chmod 640 ${{ secrets.DEPLOY_PATH }}/.env.production
        "
    
    - name: 📦 Install Composer on server (if needed)
      run: |
        echo "Installing Composer dependencies on server..."
        ssh khabribaba "
          cd ${{ secrets.DEPLOY_PATH }}
          if [ -f \"composer.json\" ] && [ ! -d \"vendor\" ]; then
            curl -sS https://getcomposer.org/installer | php
            php composer.phar install --no-dev --optimize-autoloader
          fi
        "
    
    - name: 🗄️ Run database migrations
      if: ${{ secrets.RUN_MIGRATIONS == 'true' }}
      run: |
        echo "Running database migrations..."
        ssh khabribaba "
          cd ${{ secrets.DEPLOY_PATH }}
          if [ -f \"scripts/migrate.php\" ]; then
            php scripts/migrate.php
          fi
        "
    
    - name: 🧹 Clear cache
      run: |
        echo "Clearing application cache..."
        ssh khabribaba "
          cd ${{ secrets.DEPLOY_PATH }}
          rm -rf cache/* 2>/dev/null || true
          
          # Clear opcache if enabled
          if command -v php > /dev/null; then
            curl -s http://khabribaba.com/clear-cache.php 2>/dev/null || true
          fi
        "
    
    - name: 🏥 Health check
      run: |
        echo "Checking if site is healthy..."
        max_attempts=10
        attempt=1
        
        while [ $attempt -le $max_attempts ]; do
          if curl -s -f "https://khabribaba.com" > /dev/null; then
            echo "✅ Site is responding (attempt $attempt)"
            break
          else
            echo "⏳ Waiting for site... (attempt $attempt/$max_attempts)"
            sleep 10
            attempt=$((attempt + 1))
          fi
        done
        
        if [ $attempt -gt $max_attempts ]; then
          echo "❌ Site failed to respond after $max_attempts attempts"
          exit 1
        fi
    
    - name: 📢 Notify deployment
      if: success()
      run: |
        echo "🚀 Deployment successful!"
        echo "📅 Date: $(date)"
        echo "👤 Deployed by: ${{ github.actor }}"
        echo "📝 Commit: ${{ github.event.head_commit.message }}"
        echo "🔗 Site: https://khabribaba.com"
```

---

# **8. Deploying PHP to Namecheap (cPanel Specific)**

## **Namecheap cPanel PHP Requirements:**
1. **PHP Version:** Usually PHP 7.4 or 8.0+
2. **Extensions:** MySQLi, PDO, GD, cURL, mbstring
3. **Memory Limit:** 256M+ (check in cPanel)
4. **Upload Limits:** Adjust in php.ini

## **cPanel Setup Steps:**

### **Step 1: Check PHP Version in cPanel**
```
1. Login to cPanel
2. Search for "PHP Version"
3. Select PHP 8.0 or higher
4. Enable extensions: mysqli, pdo_mysql, gd, curl, mbstring
```

### **Step 2: Create Database in cPanel**
```
1. cPanel → MySQL Databases
2. Create New Database: khabribaba_db
3. Create User: khabribaba_user
4. Add User to Database
5. Set Privileges: ALL PRIVILEGES
6. Note: Database name will be prefixed (username_dbname)
```

### **Step 3: Update GitHub Secrets for cPanel:**
```
SSH_HOST: khabribaba.com
SSH_USERNAME: your_cpanel_username
DEPLOY_PATH: /home/username/public_html/
DB_HOST: localhost
DB_NAME: username_khabribaba
DB_USER: username_khabribaba_user
DB_PASS: your_database_password
```

### **Step 4: Special cPanel Considerations:**

**Create public_html/.htaccess for cPanel:**
```apache
# cPanel-specific settings
AddHandler application/x-httpd-php80 .php

# Increase PHP limits
php_value upload_max_filesize 64M
php_value post_max_size 64M
php_value memory_limit 256M
php_value max_execution_time 300
php_value max_input_time 300

# Disable directory browsing
Options -Indexes
```

## **Alternative: Using cPanel Git Version Control**
If SSH access is restricted, use cPanel's Git feature:

```
1. cPanel → Git Version Control
2. Create Repository: /home/username/repositories/khabribaba.git
3. Clone URL: Use cPanel's provided URL
4. Add post-receive hook:
```

**post-receive hook for cPanel:**
```bash
#!/bin/bash
TARGET="/home/username/public_html"
GIT_DIR="/home/username/repositories/khabribaba.git"

while read oldrev newrev ref
do
    if [[ $ref = refs/heads/main ]]; then
        echo "Deploying to $TARGET..."
        git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f main
        
        # Set permissions
        chmod -R 755 $TARGET
        chmod -R 644 $TARGET/*.php
        
        echo "Deployment complete!"
    fi
done
```

---

# **9. Common PHP Issues & Solutions**

## **Issue 1: Permission Denied for Uploads**
```bash
# On server after deployment:
ssh username@khabribaba.com
cd public_html
chmod -R 775 uploads
chown -R username:www-data uploads

# In PHP code, add:
if (!is_writable('uploads')) {
    die('Uploads directory is not writable');
}
```

## **Issue 2: Database Connection Fails**
```php
// Debug database connection:
try {
    $pdo = new PDO($dsn, $user, $pass, $options);
    echo "Connected successfully";
} catch (PDOException $e) {
    // Check error in logs
    error_log("DB Error: " . $e->getMessage());
    
    // Show user-friendly message
    if (APP_ENV === 'development') {
        die("Database error: " . $e->getMessage());
    } else {
        die("Database connection failed");
    }
}
```

## **Issue 3: Session Issues After Deployment**
```php
// In config.php:
ini_set('session.save_path', __DIR__ . '/sessions');
ini_set('session.gc_maxlifetime', 14400); // 4 hours

// Ensure sessions directory exists and is writable
if (!is_dir('sessions')) {
    mkdir('sessions',
```


# **Complete Docker + PHP + CI/CD Guide for khabribaba.com When You are Developing PHP in Docker Environment Here is the Starting Point for full Production grade Development**

## **📋 Docker-Specific Table of Contents**
1. **Why Docker for PHP?** - Benefits over traditional deployment
2. **Docker Setup on Your Computer** - Installation & configuration
3. **Creating PHP Docker Environment** - Local development
4. **Dockerizing Your PHP Application** - Dockerfile & docker-compose
5. **Database with Docker** - MySQL/PostgreSQL containers
6. **Docker in CI/CD Pipeline** - Build, test, deploy
7. **Deploying Docker to Namecheap** - Container hosting options
8. **Advanced Docker Features** - Volumes, networks, orchestration
9. **Troubleshooting Docker** - Common issues & solutions
10. **Production Docker Setup** - Security, monitoring, scaling

---

# **1. Why Docker for PHP?**

## **Traditional PHP Deployment Problems:**
```
Problem: "It works on my machine!"
- Different PHP versions
- Missing extensions
- Apache vs Nginx differences
- Environment inconsistencies
```

## **Docker Benefits:**
```
✅ Consistent environments (dev/staging/prod)
✅ Easy dependency management
✅ Isolated services (PHP, MySQL, Redis)
✅ Simplified deployment
✅ Easy scaling
✅ Version-controlled infrastructure
```

## **Docker vs Traditional for khabribaba.com:**
| **Aspect** | **Traditional** | **Docker** |
|------------|----------------|------------|
| Local Setup | Install XAMPP/MAMP | `docker-compose up` |
| PHP Version | Manual change | Specify in Dockerfile |
| Database | Manual install | Containerized |
| Deployment | Upload files | Push container |
| Rollback | Manual file restore | Switch container tag |
| Team Work | "Works on my machine" | Same everywhere |

---

# **2. Docker Setup on Your Computer**

## **Step 1: Install Docker**
**For Windows:**
```
1. Download Docker Desktop: https://www.docker.com/products/docker-desktop
2. Run installer (enable WSL2 when prompted)
3. Restart computer
4. Open Docker Desktop
```

**For Mac:**
```bash
# Option 1: Homebrew (recommended)
brew install --cask docker

# Option 2: Download from website
# https://www.docker.com/products/docker-desktop
```

**For Ubuntu/Linux:**
```bash
# Remove old versions
sudo apt remove docker docker-engine docker.io containerd runc

# Install Docker
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# Add user to docker group (so you don't need sudo)
sudo usermod -aG docker $USER
newgrp docker  # Reload group permissions
```

## **Step 2: Verify Installation**
```bash
# Open Cursor Terminal
docker --version
# Should show: Docker version 20.10+

docker-compose --version
# Should show: docker-compose version 1.29+

# Test with hello-world
docker run hello-world
```

## **Step 3: Install Docker Extension for Cursor**
```
1. In Cursor, go to Extensions (Ctrl+Shift+X)
2. Search for "Docker"
3. Install "Docker" by Microsoft
4. Also install "Remote - Containers"
```

---

# **3. Creating PHP Docker Environment**

## **Project Structure with Docker:**
```
khabribaba-docker/
├── docker/
│   ├── php/
│   │   ├── Dockerfile
│   │   └── php.ini
│   └── nginx/
│       ├── Dockerfile
│       └── nginx.conf
├── src/                    # Your PHP application
│   ├── index.php
│   ├── includes/
│   └── public/            # Web root (for nginx)
├── docker-compose.yml
├── .dockerignore
├── .env.example
└── README.md
```

## **Step 1: Create Basic PHP Application**
**In Cursor Chat:**
```
"Create a simple PHP application structure with:
- src/public/index.php (entry point)
- src/public/.htaccess (for Apache)
- src/includes/database.php
- src/includes/config.php
- src/composer.json for dependencies
```

**src/public/index.php:**
```php
<?php
// index.php - Dockerized PHP App
require_once __DIR__ . '/../includes/config.php';

echo "<!DOCTYPE html>";
echo "<html lang='en'>";
echo "<head>";
echo "    <title>KhabriBaba - Dockerized</title>";
echo "    <style>";
echo "        body { font-family: Arial, sans-serif; margin: 40px; }";
echo "        .container { max-width: 800px; margin: 0 auto; }";
echo "        .success { color: green; font-weight: bold; }";
echo "        .info { background: #e3f2fd; padding: 15px; border-radius: 5px; }";
echo "    </style>";
echo "</head>";
echo "<body>";
echo "    <div class='container'>";
echo "        <h1>🚀 KhabriBaba Dockerized</h1>";
echo "        <div class='info'>";
echo "            <h3>System Information:</h3>";
echo "            <p><strong>PHP Version:</strong> " . phpversion() . "</p>";
echo "            <p><strong>Server:</strong> " . $_SERVER['SERVER_SOFTWARE'] . "</p>";
echo "            <p><strong>Loaded Extensions:</strong> " . implode(', ', get_loaded_extensions()) . "</p>";
echo "        </div>";
echo "        ";
echo "        <div class='success'>";
echo "            <h3>✅ Docker is working!</h3>";
echo "            <p>Your PHP application is running inside a Docker container.</p>";
echo "        </div>";
echo "        ";
echo "        <h3>Next Steps:</h3>";
echo "        <ol>";
echo "            <li>Connect to MySQL database</li>";
echo "            <li>Add your application code</li>";
echo "            <li>Set up CI/CD with Docker</li>";
echo "            <li>Deploy to production</li>";
echo "        </ol>";
echo "    </div>";
echo "</body>";
echo "</html>";
```

---

# **4. Dockerizing Your PHP Application**

## **Step 2: Create Dockerfile for PHP**
**docker/php/Dockerfile:**
```dockerfile
# Use official PHP image with Apache
FROM php:8.2-apache

# Set working directory
WORKDIR /var/www/html

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    libicu-dev \
    g++ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install \
    pdo_mysql \
    mysqli \
    mbstring \
    exif \
    pcntl \
    bcmath \
    gd \
    intl \
    zip \
    opcache

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install Node.js (for frontend dependencies)
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm

# Configure Apache
RUN a2enmod rewrite headers
COPY docker/php/000-default.conf /etc/apache2/sites-available/000-default.conf

# Configure PHP
COPY docker/php/php.ini /usr/local/etc/php/conf.d/custom.ini

# Set proper permissions
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html/storage

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost/ || exit 1

# Expose port
EXPOSE 80

# Start Apache in foreground
CMD ["apache2-foreground"]
```

**docker/php/php.ini:**
```ini
; Custom PHP configuration
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
max_input_time = 300

; Error handling
display_errors = Off
display_startup_errors = Off
log_errors = On
error_log = /var/log/php_errors.log

; OpCache for performance
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=2
opcache.fast_shutdown=1

; Timezone
date.timezone = "Asia/Kolkata"
```

**docker/php/000-default.conf:**
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/public
    
    <Directory /var/www/html/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
        
        # Security headers
        Header always set X-Content-Type-Options "nosniff"
        Header always set X-Frame-Options "SAMEORIGIN"
        Header always set X-XSS-Protection "1; mode=block"
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## **Step 3: Create Nginx Dockerfile (Alternative to Apache)**
**docker/nginx/Dockerfile:**
```dockerfile
FROM nginx:alpine

# Copy custom nginx configuration
COPY docker/nginx/nginx.conf /etc/nginx/nginx.conf
COPY docker/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

# Copy SSL certificates (if any)
# COPY ssl/ /etc/nginx/ssl/

# Create directory for PHP-FPM socket
RUN mkdir -p /run/php

# Expose ports
EXPOSE 80
EXPOSE 443

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

**docker/nginx/nginx.conf:**
```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log /var/log/nginx/access.log main;
    
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    
    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript 
               application/javascript application/xml+rss 
               application/json image/svg+xml;
    
    include /etc/nginx/conf.d/*.conf;
}
```

**docker/nginx/conf.d/default.conf:**
```nginx
server {
    listen 80;
    server_name localhost;
    root /var/www/html/public;
    index index.php index.html index.htm;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    
    # Handle PHP files
    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        
        # Timeouts
        fastcgi_read_timeout 300;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
    }
    
    # Handle static files
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }
    
    # Deny access to sensitive files
    location ~* \.(env|log|sql|ini|conf)$ {
        deny all;
    }
    
    # Handle front controller
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    # Error pages
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

## **Step 4: Create docker-compose.yml**
```yaml
version: '3.8'

services:
  # PHP Application
  app:
    build:
      context: .
      dockerfile: docker/php/Dockerfile
    container_name: khabribaba-app
    restart: unless-stopped
    working_dir: /var/www/html
    volumes:
      - ./src:/var/www/html
      - ./docker/php/php.ini:/usr/local/etc/php/conf.d/custom.ini
    environment:
      - APP_ENV=development
      - APP_DEBUG=true
      - DB_HOST=db
      - DB_PORT=3306
      - DB_DATABASE=khabribaba
      - DB_USERNAME=root
      - DB_PASSWORD=secret
    depends_on:
      - db
      - redis
    networks:
      - khabribaba-network
    ports:
      - "8080:80"  # Map localhost:8080 to container port 80

  # Nginx (if using PHP-FPM)
  nginx:
    build:
      context: .
      dockerfile: docker/nginx/Dockerfile
    container_name: khabribaba-nginx
    restart: unless-stopped
    volumes:
      - ./src:/var/www/html
      - ./docker/nginx/conf.d:/etc/nginx/conf.d
    depends_on:
      - app
    networks:
      - khabribaba-network
    ports:
      - "80:80"
      - "443:443"

  # MySQL Database
  db:
    image: mysql:8.0
    container_name: khabribaba-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: khabribaba
      MYSQL_USER: khabribaba_user
      MYSQL_PASSWORD: user_password
    volumes:
      - db_data:/var/lib/mysql
      - ./sql/init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - khabribaba-network
    ports:
      - "3306:3306"
    command: --default-authentication-plugin=mysql_native_password

  # phpMyAdmin
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: khabribaba-phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: db
      PMA_PORT: 3306
      UPLOAD_LIMIT: 64M
    depends_on:
      - db
    networks:
      - khabribaba-network
    ports:
      - "8081:80"

  # Redis Cache
  redis:
    image: redis:alpine
    container_name: khabribaba-redis
    restart: unless-stopped
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - khabribaba-network
    ports:
      - "6379:6379"

  # Mailhog for email testing
  mailhog:
    image: mailhog/mailhog
    container_name: khabribaba-mailhog
    restart: unless-stopped
    networks:
      - khabribaba-network
    ports:
      - "8025:8025"  # Web UI
      - "1025:1025"  # SMTP server

  # Node.js for frontend
  node:
    image: node:18-alpine
    container_name: khabribaba-node
    restart: unless-stopped
    working_dir: /app
    volumes:
      - ./src:/app
      - node_modules:/app/node_modules
    environment:
      - NODE_ENV=development
    networks:
      - khabribaba-network
    ports:
      - "3000:3000"
    command: sh -c "npm install && npm run dev"

networks:
  khabribaba-network:
    driver: bridge

volumes:
  db_data:
    driver: local
  redis_data:
    driver: local
  node_modules:
    driver: local
```

## **Step 5: Create .dockerignore**
```dockerignore
# Ignore everything
*

# Allow only necessary files
!docker/
!src/
!docker-compose.yml
!.env.example
!README.md

# Specific exclusions within allowed directories
src/node_modules/
src/vendor/
src/.git/
src/*.log
src/.env
src/.env.local
src/uploads/
src/storage/logs/
```

## **Step 6: Create Environment Files**
**.env.example:**
```env
# Application
APP_NAME="KhabriBaba"
APP_ENV=development
APP_DEBUG=true
APP_URL=http://localhost:8080
APP_KEY=

# Database
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=khabribaba
DB_USERNAME=root
DB_PASSWORD=secret

# Redis
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

# Mail
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=hello@khabribaba.com
MAIL_FROM_NAME="KhabriBaba"

# Session
SESSION_DRIVER=redis
SESSION_LIFETIME=120

# Cache
CACHE_DRIVER=redis

# Debugging
DEBUGBAR_ENABLED=true
```

## **Step 7: Start Your Docker Environment**
```bash
# In Cursor Terminal, navigate to project:
cd ~/Desktop/khabribaba-docker

# Copy environment file
cp .env.example .env

# Build and start containers
docker-compose up -d

# Check running containers
docker-compose ps

# View logs
docker-compose logs -f app

# Stop containers
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

## **Step 8: Access Your Application**
```
Application: http://localhost:8080
phpMyAdmin: http://localhost:8081
Mailhog UI: http://localhost:8025
Node.js Dev: http://localhost:3000

MySQL: localhost:3306 (root/secret)
Redis: localhost:6379
```

---

# **5. Database with Docker**

## **MySQL Initialization Script**
**sql/init.sql:**
```sql
-- Initialize khabribaba database
CREATE DATABASE IF NOT EXISTS khabribaba CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE khabribaba;

-- Users table
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    email_verified_at TIMESTAMP NULL,
    password VARCHAR(255) NOT NULL,
    remember_token VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- News articles
CREATE TABLE news (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    content LONGTEXT NOT NULL,
    excerpt TEXT,
    author_id INT,
    category_id INT,
    featured_image VARCHAR(255),
    views INT DEFAULT 0,
    published BOOLEAN DEFAULT FALSE,
    published_at DATETIME,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_published (published, published_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Insert sample data
INSERT INTO users (name, email, password) VALUES 
('Admin User', 'admin@khabribaba.com', '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi');

INSERT INTO news (title, slug, content, author_id, published, published_at) VALUES
('Welcome to KhabriBaba', 'welcome-to-khabribaba', 'This is our first news article...', 1, TRUE, NOW()),
('Docker Deployment Success', 'docker-deployment-success', 'Our site is now running on Docker!', 1, TRUE, NOW());
```

## **Database Management Commands:**
```bash
# Access MySQL container
docker-compose exec db mysql -u root -p

# Run SQL file
docker-compose exec db mysql -u root -p khabribaba < sql/init.sql

# Backup database
docker-compose exec db mysqldump -u root -p khabribaba > backup.sql

# Restore database
docker-compose exec -T db mysql -u root -p khabribaba < backup.sql

# Check database logs
docker-compose logs db
```

---

# **6. Docker in CI/CD Pipeline**

## **Docker-Specific GitHub Actions Workflow**
**.github/workflows/docker-deploy.yml:**
```yaml
name: 🐳 Docker Build & Deploy

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # Build and test Docker image
  docker-build-test:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    
    steps:
    - name: 📥 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔧 Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: 🔐 Log in to Container Registry
      uses: docker/login-action@v2
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    
    - name: 🏗️ Build Docker image
      run: |
        docker build \
          --file docker/php/Dockerfile \
          --tag ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:pr-${{ github.event.number }} \
          .
    
    - name: 🧪 Run tests in container
      run: |
        # Create test container
        docker run --rm \
          --name khabribaba-test \
          -e APP_ENV=testing \
          -e DB_HOST=localhost \
          ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:pr-${{ github.event.number }} \
          sh -c "
            # Install Composer dependencies
            composer install --no-interaction --prefer-dist
            
            # Run PHPUnit tests
            if [ -f "vendor/bin/phpunit" ]; then
              vendor/bin/phpunit
            fi
            
            # Check for syntax errors
            find . -name '*.php' -exec php -l {} \;
            
            # Security scan
            if [ -f "vendor/bin/phpstan" ]; then
              vendor/bin/phpstan analyse
            fi
          "
    
    - name: 📊 Scan for vulnerabilities
      uses: aquasecurity/trivy-action@master
      with:
        image-ref: '${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:pr-${{ github.event.number }}'
        format: 'table'
        exit-code: '1'
        ignore-unfixed: true
        severity: 'CRITICAL,HIGH'

  # Build and push to registry
  docker-build-push:
    runs-on: ubuntu-latest
    needs: docker-build-test
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    
    steps:
    - name: 📥 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔧 Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: 🔐 Log in to Container Registry
      uses: docker/login-action@v2
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    
    - name: 🏷️ Extract metadata
      id: meta
      uses: docker/metadata-action@v4
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=sha,prefix={{branch}}-
          type=raw,value=latest,enable={{is_default_branch}}
    
    - name: 🏗️ Build and push
      uses: docker/build-push-action@v4
      with:
        context: .
        file: docker/php/Dockerfile
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max
    
    - name: 📋 Generate deployment summary
      run: |
        echo "## 🐳 Docker Image Built" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Image:** \`${{ steps.meta.outputs.tags }}\`" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Size:** $(docker images ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest --format "{{.Size}}")" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Layers:**" >> $GITHUB_STEP_SUMMARY
        echo '```' >> $GITHUB_STEP_SUMMARY
        docker history ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest --no-trunc >> $GITHUB_STEP_SUMMARY
        echo '```' >> $GITHUB_STEP_SUMMARY

  # Deploy to Namecheap server
  deploy:
    runs-on: ubuntu-latest
    needs: docker-build-push
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    
    steps:
    - name: 📥 Checkout code
      uses: actions/checkout@v3
    
    - name: 🔑 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
    
    - name: 🐳 Install Docker on server (if not present)
      run: |
        ssh khabribaba "
          # Check if Docker is installed
          if ! command -v docker &> /dev/null; then
            echo 'Installing Docker...'
            curl -fsSL https://get.docker.com -o get-docker.sh
            sh get-docker.sh
            sudo usermod -aG docker ${{ secrets.SSH_USERNAME }}
          fi
          
          # Check if Docker Compose is installed
          if ! command -v docker-compose &> /dev/null; then
            echo 'Installing Docker Compose...'
            sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
              -o /usr/local/bin/docker-compose
            sudo chmod +x /usr/local/bin/docker-compose
          fi
        "
    
    - name: 📤 Deploy Docker Compose
      run: |
        # Create production docker-compose.yml on server
        cat > docker-compose.prod.yml << 'EOF'
        version: '3.8'
        
        services:
          app:
            image: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
            container_name: khabribaba-app-prod
            restart: always
            ports:
              - "80:80"
              - "443:443"
            environment:
              - APP_ENV=production
              - APP_DEBUG=false
              - DB_HOST=${{ secrets.DB_HOST }}
              - DB_PORT=${{ secrets.DB_PORT }}
              - DB_DATABASE=${{ secrets.DB_NAME }}
              - DB_USERNAME=${{ secrets.DB_USER }}
              - DB_PASSWORD=${{ secrets.DB_PASS }}
            volumes:
              - uploads:/var/www/html/uploads
              - logs:/var/log/apache2
            networks:
              - khabribaba-prod
        
        volumes:
          uploads:
          logs:
        
        networks:
          khabribaba-prod:
            driver: bridge
        EOF
        
        # Copy to server
        scp docker-compose.prod.yml khabribaba:~/khabribaba/
        
        # Login to registry on server
        ssh khabribaba "
          echo '${{ secrets.GITHUB_TOKEN }}' | docker login ${{ env.REGISTRY }} -u ${{ github.actor }} --password-stdin
        "
        
        # Deploy on server
        ssh khabribaba "
          cd ~/khabribaba
          
          # Pull latest image
          docker pull ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
          
          # Stop and remove old container
          docker-compose -f docker-compose.prod.yml down || true
          
          # Start new container
          docker-compose -f docker-compose.prod.yml up -d
          
          # Clean up old images
          docker image prune -f
          
          # Check container status
          docker ps
          
          # Check logs
          docker logs khabribaba-app-prod --tail 20
        "
    
    - name: 🏥 Health check
      run: |
        echo "Checking if site is healthy..."
        
        max_attempts=10
        attempt=1
        
        while [ $attempt -le $max_attempts ]; do
          if curl -s -f "http://${{ secrets.SSH_HOST }}" > /dev/null; then
            echo "✅ Site is responding (attempt $attempt)"
            break
          else
            echo "⏳ Waiting for site... (attempt $attempt/$max_attempts)"
            sleep 10
            attempt=$((attempt + 1))
          fi
        done
        
        if [ $attempt -gt $max_attempts ]; then
          echo "❌ Site failed to respond after $max_attempts attempts"
          
          # Get container logs for debugging
          ssh khabribaba "docker logs khabribaba-app-prod --tail 50"
          
          exit 1
        fi
    
    - name: 📊 Deployment summary
      run: |
        echo "## 🚀 Deployment Successful" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Site:** http://${{ secrets.SSH_HOST }}" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Container:** khabribaba-app-prod" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Image:** ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Deployed at:** $(date)" >> $GITHUB_STEP_SUMMARY
```

---

# **7. Deploying Docker to Namecheap**

## **Option A: Docker on Namecheap VPS**
**If you have VPS hosting on Namecheap:**
```bash
# SSH into your Namecheap VPS
ssh username@khabribaba.com

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
  -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker --version
docker-compose --version
```

## **Option B: Docker on Shared Hosting (Limited)**
**Most Namecheap shared hosting doesn't support Docker. Alternatives:**

### **1. Use Namecheap's Docker Hosting (if available)**
```
Check if your Namecheap plan includes:
- cPanel with Docker
- VPS with Docker support
- Dedicated server
```

### **2. Use External Docker Hosting + Cloudflare**
```
1. Host Docker container on:
   - DigitalOcean: $5/month
   - Linode: $5/month
   - AWS LightSail: $3.50/month
   - Render.com: Free tier
   - Railway.app: Free tier

2. Point khabribaba.com DNS to Docker host
3. Use Cloudflare for SSL and caching
```

### **3. Recommended: DigitalOcean + Docker**
```bash
# Create Droplet (VPS)
# Choose: Ubuntu 22.04 + Docker

# After creation, SSH in:
ssh root@your-droplet-ip

# Clone your repository
git clone https://github.com/yourusername/khabribaba-docker.git
cd khabribaba-docker

# Copy production environment
cp .env.example .env.production
nano .env.production  # Edit with production values

# Start Docker
docker-compose -f docker-compose.prod.yml up -d

# Set up SSL with Let's Encrypt
sudo apt update
sudo apt install certbot python3-certbot-nginx
certbot --nginx -d khabribaba.com -d www.khabribaba.com
```

## **Production Docker Compose**
**docker-compose.prod.yml:**
```yaml
version: '3.8'

services:
  app:
    image: ghcr.io/yourusername/khabribaba:latest
    container_name: khabribaba-app-prod
    restart: always
    ports:
      - "80:80"
      - "443:443"
    environment:
      - APP_ENV=production
      - APP_DEBUG=false
      - APP_URL=https://khabribaba.com
      - DB_HOST=${DB_HOST}
      - DB_PORT=${DB_PORT}
  - DB_DATABASE=${DB_NAME}
  - DB_USERNAME=${DB_USER}
  - DB_PASSWORD=${DB_PASS}
  - REDIS_HOST=redis
volumes:
  - uploads:/var/www/html/uploads
  - sessions:/var/www/html/storage/sessions
  - logs:/var/log/apache2
  - ./ssl:/etc/ssl/certs:ro  # SSL certificates
depends_on:
  - db
  - redis
networks:
  - khabribaba-prod
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/health"]
  interval: 30s
  timeout: 10s
  retries: 3
db:
    image: mysql:8.0
    container_name: khabribaba-db-prod
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASS}
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASS}
    volumes:
      - db_data:/var/lib/mysql
      - ./sql/backups:/docker-entrypoint-initdb.d:ro
      - ./mysql.conf.d:/etc/mysql/conf.d:ro
    networks:
      - khabribaba-prod
    command: 
      - --default-authentication-plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p${DB_ROOT_PASS}"]
      interval: 30s
      timeout: 10s
      retries: 5

  redis:
    image: redis:7-alpine
    container_name: khabribaba-redis-prod
    restart: always
    command: redis-server --requirepass ${REDIS_PASS}
    volumes:
      - redis_data:/data
    networks:
      - khabribaba-prod
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASS}", "ping"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Nginx as reverse proxy (optional)
  nginx:
    image: nginx:alpine
    container_name: khabribaba-nginx-prod
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./ssl:/etc/ssl/certs:ro
      - ./logs/nginx:/var/log/nginx
    depends_on:
      - app
    networks:
      - khabribaba-prod

  # Monitoring with cAdvisor
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.47.0
    container_name: khabribaba-cadvisor
    restart: always
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
    ports:
      - "8080:8080"
    networks:
      - khabribaba-prod

networks:
  khabribaba-prod:
    driver: bridge
    driver_opts:
      com.docker.network.bridge.name: br-khabribaba

volumes:
  db_data:
    driver: local
  redis_data:
    driver: local
  uploads:
    driver: local
  sessions:
    driver: local
  logs:
    driver: local
```

## **Production Nginx Configuration**
**nginx/nginx.conf:**
```nginx
user nginx;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 1024;
    multi_accept on;
}

http {
    # Basic Settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    server_tokens off;
    
    # MIME Types
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    
    # Logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log warn;
    
    # Gzip Compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/json
        application/javascript
        application/xml+rss
        application/atom+xml
        image/svg+xml;
    
    # Virtual Host Configs
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

**nginx/conf.d/khabribaba.conf:**
```nginx
upstream php_app {
    server app:80;
}

server {
    listen 80;
    server_name khabribaba.com www.khabribaba.com;
    
    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name khabribaba.com www.khabribaba.com;
    
    # SSL Configuration
    ssl_certificate /etc/ssl/certs/khabribaba.crt;
    ssl_certificate_key /etc/ssl/certs/khabribaba.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self' https: data: 'unsafe-inline' 'unsafe-eval';" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
    # Root directory
    root /var/www/html/public;
    index index.php index.html index.htm;
    
    # Static file caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        try_files $uri =404;
    }
    
    # PHP handling
    location ~ \.php$ {
        proxy_pass http://php_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 300;
        proxy_connect_timeout 300;
        proxy_send_timeout 300;
    }
    
    # Deny access to hidden files
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    # Deny access to sensitive files
    location ~* \.(env|log|sql|ini|conf)$ {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    # Main location block
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        proxy_pass http://php_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    # Error pages
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    
    location = /50x.html {
        root /usr/share/nginx/html;
    }
    
    # Health check endpoint
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }
}
```

## **Deployment Script for Production**
**deploy.sh:**
```bash
#!/bin/bash
# deploy.sh - Production Docker deployment script

set -e  # Exit on error

echo "🚀 Starting production deployment for khabribaba.com"

# Load environment variables
if [ -f .env.production ]; then
    export $(cat .env.production | grep -v '^#' | xargs)
else
    echo "❌ .env.production file not found"
    exit 1
fi

# Variables
IMAGE_NAME="ghcr.io/$GITHUB_USERNAME/khabribaba"
DEPLOY_DIR="/opt/khabribaba"
BACKUP_DIR="/opt/khabribaba/backups/$(date +%Y%m%d_%H%M%S)"

# Create backup directory
mkdir -p "$BACKUP_DIR"

echo "📦 Backing up current deployment..."
if [ -d "$DEPLOY_DIR/current" ]; then
    cp -r "$DEPLOY_DIR/current" "$BACKUP_DIR/"
    echo "✅ Backup created at: $BACKUP_DIR"
fi

echo "🐳 Pulling latest Docker image..."
docker pull "$IMAGE_NAME:latest"

echo "🔧 Stopping current containers..."
cd "$DEPLOY_DIR"
docker-compose -f docker-compose.prod.yml down || true

echo "🔄 Starting new deployment..."
docker-compose -f docker-compose.prod.yml up -d

echo "🕒 Waiting for services to start..."
sleep 30

echo "🏥 Running health checks..."
# Check app
if curl -s -f "http://localhost/health" > /dev/null; then
    echo "✅ Application is healthy"
else
    echo "❌ Application health check failed"
    docker-compose -f docker-compose.prod.yml logs app
    exit 1
fi

# Check database
if docker-compose -f docker-compose.prod.yml exec -T db mysqladmin ping -h localhost -u root -p"$DB_ROOT_PASS" > /dev/null 2>&1; then
    echo "✅ Database is healthy"
else
    echo "❌ Database health check failed"
    exit 1
fi

echo "🧹 Cleaning up old backups (keeping last 5)..."
cd "$DEPLOY_DIR/backups"
ls -t | tail -n +6 | xargs rm -rf

echo "📊 Deployment summary:"
echo "   Image: $IMAGE_NAME:latest"
echo "   Date: $(date)"
echo "   App URL: https://khabribaba.com"
echo "   Monitoring: http://khabribaba.com:8080"

echo "✅ Deployment completed successfully!"
```

---

# **8. Advanced Docker Features**

## **Docker Volumes for Persistent Data**
```yaml
# docker-compose.volumes.yml
services:
  app:
    volumes:
      # Named volume for uploads
      - uploads:/var/www/html/uploads
      
      # Bind mount for development
      - ./src:/var/www/html
      
      # Read-only configuration
      - ./config:/var/www/config:ro
      
      # Environment-specific files
      - ./env/production/.env:/var/www/html/.env:ro

volumes:
  uploads:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /data/khabribaba/uploads
```

## **Docker Networks for Isolation**
```bash
# Create custom network
docker network create khabribaba-network

# Inspect network
docker network inspect khabribaba-network

# Connect container to network
docker network connect khabribaba-network khabribaba-app

# Disconnect container
docker network disconnect khabribaba-network khabribaba-app
```

## **Docker Health Checks**
```dockerfile
# In Dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

## **Multi-Stage Builds for PHP**
```dockerfile
# Build stage
FROM composer:2 AS composer
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader --no-scripts

# Production stage
FROM php:8.2-apache
COPY --from=composer /app/vendor /var/www/html/vendor
COPY . /var/www/html
```

## **Docker Secrets for Sensitive Data**
```bash
# Create secret
echo "mysecretpassword" | docker secret create db_password -

# Use in docker-compose
services:
  db:
    image: mysql:8.0
    secrets:
      - db_password
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    external: true
```

---

# **9. Troubleshooting Docker**

## **Common Issues & Solutions:**

### **Issue 1: Docker Container Won't Start**
```bash
# Check logs
docker logs khabribaba-app

# Check container status
docker ps -a

# Start with interactive mode
docker run -it --rm khabribaba-app /bin/bash

# Check disk space
docker system df
docker system prune -a  # Clean up
```

### **Issue 2: Permission Problems with Volumes**
```bash
# Fix permission issues
docker-compose exec app chown -R www-data:www-data /var/www/html/storage
docker-compose exec app chmod -R 775 /var/www/html/storage

# Or add to Dockerfile
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html
```

### **Issue 3: Database Connection Issues**
```bash
# Check if database is running
docker-compose ps db

# Check database logs
docker-compose logs db

# Connect to database directly
docker-compose exec db mysql -u root -p

# Check network connectivity
docker-compose exec app ping db
```

### **Issue 4: Docker Build Fails**
```bash
# Build with verbose output
docker build --no-cache --progress=plain -t khabribaba-app .

# Check Dockerfile syntax
docker run --rm -i hadolint/hadolint < Dockerfile

# Check image layers
docker history khabribaba-app
```

### **Issue 5: Docker Out of Memory**
```bash
# Check memory usage
docker stats

# Limit container memory
# In docker-compose.yml:
services:
  app:
    deploy:
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M

# Clean up
docker system prune
```

### **Issue 6: Port Already in Use**
```bash
# Check what's using the port
sudo lsof -i :80
sudo netstat -tulpn | grep :80

# Kill process using port
sudo kill -9 <PID>

# Or change port mapping
# In docker-compose.yml:
ports:
  - "8080:80"  # Instead of "80:80"
```

### **Issue 7: Slow Docker Performance on Windows/Mac**
```bash
# Increase Docker resources
# Docker Desktop → Settings → Resources
# - CPUs: 4+
# - Memory: 8GB+
# - Swap: 1GB+

# Use WSL2 backend on Windows
# Enable in Docker Desktop settings

# On Mac, use virtiofs for volume mounting
# Add to docker-compose.yml:
x-uses-virtiofs: true
```

## **Docker Debugging Commands:**
```bash
# Inspect container
docker inspect khabribaba-app

# Execute command in running container
docker exec -it khabribaba-app bash
docker exec khabribaba-app php --version

# Copy files between host and container
docker cp khabribaba-app:/var/log/apache2/error.log ./error.log
docker cp ./new-file.php khabribaba-app:/var/www/html/

# Monitor container resources
docker stats khabribaba-app
docker top khabribaba-app

# View container processes
docker-compose exec app ps aux

# Check container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' khabribaba-app
```

---

# **10. Production Docker Setup**

## **Security Best Practices:**

### **1. Non-Root User in Container**
```dockerfile
# Create non-root user
RUN groupadd -g 1000 www && \
    useradd -u 1000 -ms /bin/bash -g www www

# Switch to non-root user
USER www

# Set proper permissions
RUN chown -R www:www /var/www/html
```

### **2. Scan for Vulnerabilities**
```bash
# Use Trivy
docker run --rm aquasec/trivy image khabribaba-app

# Use Docker Scout
docker scout quickview khabribaba-app

# Use Snyk
docker scan khabribaba-app
```

### **3. Use .dockerignore**
```dockerignore
# Prevent sensitive files from being included
**/.env
**/.git
**/*.log
**/node_modules
**/vendor
**/uploads
```

### **4. Regular Updates**
```bash
# Update base images
docker pull php:8.2-apache
docker pull mysql:8.0
docker pull redis:alpine

# Rebuild with security updates
docker-compose build --no-cache
```

## **Monitoring & Logging:**

### **Docker Logging Configuration**
```yaml
# In docker-compose.yml
services:
  app:
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
        labels: "production"
        env: "app"
```

### **Log Management**
```bash
# View logs
docker-compose logs -f
docker logs --tail 100 khabribaba-app

# Export logs
docker-compose logs > logs/$(date +%Y%m%d).log

# Log rotation with logrotate
# /etc/logrotate.d/docker
/var/lib/docker/containers/*/*.log {
    rotate 7
    daily
    compress
    delaycompress
    missingok
    copytruncate
}
```

### **Monitoring with cAdvisor + Prometheus + Grafana**
```yaml
# docker-compose.monitoring.yml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--storage.tsdb.retention.time=200h'
      - '--web.enable-lifecycle'
    restart: unless-stopped
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    container_name: grafana
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_USERS_ALLOW_SIGN_UP=false
    restart: unless-stopped
    ports:
      - "3000:3000"

  node-exporter:
    image: prom/node-exporter
    container_name: node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    restart: unless-stopped
    ports:
      - "9100:9100"

volumes:
  prometheus_data:
  grafana_data:
```

## **Backup Strategy:**

### **Backup Script for Docker Volumes**
```bash
#!/bin/bash
# backup.sh - Backup Docker volumes and databases

BACKUP_DIR="/backup/docker/$(date +%Y%m%d_%H%M%S)"
mkdir -p "$BACKUP_DIR"

echo "Starting Docker backup..."

# Backup MySQL database
docker exec khabribaba-db mysqldump -u root -p"$DB_ROOT_PASSWORD" \
  --all-databases > "$BACKUP_DIR/all-databases.sql"

# Backup specific database
docker exec khabribaba-db mysqldump -u root -p"$DB_ROOT_PASSWORD" \
  khabribaba > "$BACKUP_DIR/khabribaba.sql"

# Backup Redis
docker exec khabribaba-redis redis-cli -a "$REDIS_PASSWORD" \
  SAVE
docker cp khabribaba-redis:/data/dump.rdb "$BACKUP_DIR/redis.rdb"

# Backup volumes
docker run --rm -v khabribaba_uploads:/data -v "$BACKUP_DIR":/backup \
  alpine tar czf /backup/uploads.tar.gz -C /data .

docker run --rm -v khabribaba_logs:/data -v "$BACKUP_DIR":/backup \
  alpine tar czf /backup/logs.tar.gz -C /data .

# Backup Docker Compose files
cp docker-compose.yml docker-compose.prod.yml .env.production "$BACKUP_DIR/"

# Create backup archive
tar czf "/backup/docker-backup-$(date +%Y%m%d_%H%M%S).tar.gz" -C "$BACKUP_DIR" .

# Cleanup old backups (keep last 7 days)
find /backup/docker -type f -name "*.tar.gz" -mtime +7 -delete

echo "Backup completed: $BACKUP_DIR"
```

## **Scaling with Docker Swarm (Optional):**
```yaml
# docker-compose.swarm.yml
version: '3.8'

services:
  app:
    image: khabribaba-app:latest
    deploy:
      replicas: 3
      update_config:
        parallelism: 2
        delay: 10s
        order: start-first
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
    networks:
      - khabribaba-overlay

networks:
  khabribaba-overlay:
    driver: overlay
    attachable: true
```

---

# **🎯 Quick Start Guide**

## **From Zero to Docker in 10 Minutes:**

### **Step 1: Install Docker**
```bash
# Windows/Mac: Download Docker Desktop
# Linux:
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
newgrp docker
```

### **Step 2: Clone/Setup Project**
```bash
git clone https://github.com/yourusername/khabribaba-docker.git
cd khabribaba-docker
cp .env.example .env
```

### **Step 3: Start Development Environment**
```bash
docker-compose up -d
# Access at http://localhost:8080
```

### **Step 4: Develop with Hot Reload**
```bash
# Edit files in src/ folder
# Changes automatically appear in container
# View logs:
docker-compose logs -f app
```

### **Step 5: Deploy to Production**
```bash
# Build production image
docker build -t khabribaba-app:prod -f docker/php/Dockerfile.prod .

# Push to registry
docker tag khabribaba-app:prod ghcr.io/yourusername/khabribaba:latest
docker push ghcr.io/yourusername/khabribaba:latest

# Deploy to server
scp docker-compose.prod.yml .env.production user@server:~/khabribaba/
ssh user@server "cd ~/khabribaba && docker-compose -f docker-compose.prod.yml up -d"
```

## **Daily Docker Commands Cheat Sheet:**
```bash
# Development
docker-compose up -d          # Start all services
docker-compose down           # Stop all services
docker-compose logs -f app    # Follow app logs
docker-compose exec app bash  # Enter app container
docker-compose restart app    # Restart app only

# Building
docker-compose build          # Build all images
docker-compose build --no-cache app  # Rebuild without cache

# Management
docker ps                     # List running containers
docker ps -a                  # List all containers
docker images                 # List images
docker system df              # Check disk usage
docker system prune -a        # Clean up everything

# Database
docker-compose exec db mysql -u root -p  # Connect to MySQL
docker-compose exec db mysqldump -u root -p khabribaba > backup.sql

# Debugging
docker-compose logs           # View all logs
docker inspect <container>    # Inspect container details
docker stats                  # Live resource usage
docker exec -it <container> sh  # Get shell in container
```

## **Troubleshooting Quick Reference:**
```
Problem: Container won't start
Solution: docker logs <container> to see error

Problem: Permission denied
Solution: docker-compose exec app chown -R www-data:www-data storage

Problem: Out of memory
Solution: docker system prune and increase Docker Desktop resources

Problem: Port already in use
Solution: Change port in docker-compose.yml or kill existing process

Problem: Database connection refused
Solution: Check if db container is running: docker-compose ps db
```

## **Next Steps After Docker Setup:**
1. ✅ Set up CI/CD with GitHub Actions
2. ✅ Configure SSL certificates (Let's Encrypt)
3. ✅ Set up monitoring (Prometheus + Grafana)
4. ✅ Implement auto-scaling
5. ✅ Set up disaster recovery
6. ✅ Implement blue-green deployments

---

# **📞 Getting Help with Docker**

## **Useful Resources:**
- **Docker Documentation**: https://docs.docker.com/
- **Docker Hub**: https://hub.docker.com/
- **Docker Compose Reference**: https://docs.docker.com/compose/
- **PHP Docker Official Images**: https://hub.docker.com/_/php

## **Community Support:**
- **Stack Overflow**: Tag with [docker] [php] [docker-compose]
- **Docker Community Forums**: https://forums.docker.com/
- **GitHub Issues**: For specific project issues

## **Paid Support Options:**
- **Docker Pro**: $5/month (includes commercial use)
- **Docker Business**: Team features
- **Managed Docker Hosting**: DigitalOcean, AWS, Google Cloud

---

**Remember:** Docker is a journey, not a destination. Start simple with docker-compose for development, then gradually add production features. The key benefits are consistency and reproducibility - once it works in Docker, it works everywhere!
