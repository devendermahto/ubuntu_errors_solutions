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
