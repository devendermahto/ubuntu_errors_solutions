# **🎯 Complete PHP Development & CI/CD Master Guide**

## **📋 ULTIMATE TABLE OF CONTENTS**

### **PART 1: PROJECT SETUP & LOCAL DEVELOPMENT**
1.1 Launching Cursor & Creating PHP Project  
1.2 Local Development Environment (XAMPP/MAMP)  
1.3 PHP Project Structure & Best Practices  
1.4 Using Cursor AI Effectively  

### **PART 2: VERSION CONTROL & GIT**
2.1 Git Setup for PHP Projects  
2.2 .gitignore for PHP  
2.3 GitHub Repository Setup  
2.4 Daily Git Workflow  

### **PART 3: PHP DEVELOPMENT ESSENTIALS**
3.1 Database Connection & Security  
3.2 Environment Configuration (.env files)  
3.3 Security Best Practices  
3.4 Common PHP Patterns  

### **PART 4: CI/CD PIPELINE (RECOMMENDED APPROACH)**
4.1 Why Simple SSH Deployment is Best for You  
4.2 GitHub Actions Setup Step-by-Step  
4.3 SSH Key Configuration  
4.4 Complete Deployment Workflow  

### **PART 5: DEPLOYMENT TO NAMECHEAP**
5.1 Namecheap cPanel Setup  
5.2 Database Configuration  
5.3 File Permissions & Security  
5.4 Zero-Downtime Deployment Strategies  

### **PART 6: ADVANCED TOPICS (OPTIONAL)**
6.1 Automated Testing  
6.2 Staging Environment  
6.3 Monitoring & Logging  
6.4 When to Consider Docker  

### **PART 7: TROUBLESHOOTING & MAINTENANCE**
7.1 Common Deployment Issues  
7.2 Rollback Procedures  
7.3 Performance Optimization  
7.4 Security Auditing  

---

# **🚀 PART 1: PROJECT SETUP & LOCAL DEVELOPMENT**

## **1.1 Launching Cursor & Creating PHP Project**

### **Step 1: Open Cursor for the First Time**
```
1. Double-click Cursor icon
2. Click "Open Folder" 
3. Navigate to your development folder:
   - Windows: C:\xampp\htdocs\
   - Mac: /Applications/MAMP/htdocs/
   - Linux: /var/www/html/
4. Click "New Folder" → Name: khabribaba
5. Click "Open"
```

### **Step 2: Create Project Structure with AI**
**In Cursor Chat (Ctrl/Cmd + I):**
```
"Create a complete PHP project structure for a news website called KhabriBaba with:
- index.php (homepage with latest news)
- about.php 
- contact.php (with form)
- news.php (news listing)
- single-news.php (single article view)
- admin/ directory (admin panel)
- includes/ directory (for PHP classes)
- assets/css/ (stylesheets)
- assets/js/ (JavaScript files)
- uploads/ (for images)
- config/ (configuration files)
- .htaccess file
- README.md file
- .gitignore file for PHP"
```

### **Step 3: Verify Structure**
Your project should look like:
```
khabribaba/
├── index.php
├── about.php
├── contact.php
├── news.php
├── single-news.php
├── admin/
│   ├── index.php
│   ├── login.php
│   └── dashboard.php
├── includes/
│   ├── Database.php
│   ├── Auth.php
│   ├── News.php
│   └── functions.php
├── assets/
│   ├── css/
│   │   ├── style.css
│   │   └── admin.css
│   └── js/
│       ├── main.js
│       └── admin.js
├── uploads/
│   └── .gitkeep
├── config/
│   └── config.php
├── .htaccess
├── README.md
└── .gitignore
```

## **1.2 Local Development Environment**

### **For Windows (XAMPP):**
```powershell
# 1. Download XAMPP: https://www.apachefriends.org/
# 2. Install to: C:\xampp\
# 3. Start XAMPP Control Panel
# 4. Start Apache and MySQL
# 5. Put your project in: C:\xampp\htdocs\khabribaba\
# 6. Access: http://localhost/khabribaba/
```

### **For Mac (MAMP):**
```bash
# 1. Download MAMP: https://www.mamp.info/
# 2. Install and open MAMP
# 3. Preferences → Web Server → Set to: /Applications/MAMP/htdocs/khabribaba
# 4. Start Servers
# 5. Access: http://localhost:8888/
```

### **For Linux:**
```bash
# Install LAMP stack
sudo apt update
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-zip

# Enable Apache modules
sudo a2enmod rewrite
sudo systemctl restart apache2

# Set permissions
sudo chown -R $USER:$USER /var/www/html/khabribaba
sudo chmod -R 755 /var/www/html/khabribaba

# Access: http://localhost/khabribaba/
```

### **Test PHP Installation:**
**Create test.php in Cursor:**
```php
<?php
phpinfo();
?>
```
**Save to:** `khabribaba/test.php`  
**Browse to:** `http://localhost/khabribaba/test.php`

## **1.3 Create Basic PHP Files**

### **index.php (Homepage):**
```php
<?php
// Start session
session_start();

// Load configuration
require_once 'config/config.php';

// Set page title
$page_title = "KhabriBaba - Latest News";

// Include header
include 'includes/header.php';

// Database connection
require_once 'includes/Database.php';
$db = new Database();
$conn = $db->connect();

// Get latest news
$stmt = $conn->prepare("SELECT * FROM news WHERE published = 1 ORDER BY created_at DESC LIMIT 10");
$stmt->execute();
$news = $stmt->fetchAll(PDO::FETCH_ASSOC);
?>

<div class="container">
    <h1 class="text-center mb-5">Welcome to KhabriBaba</h1>
    
    <div class="row">
        <div class="col-md-8">
            <h2>Latest News</h2>
            <?php if (empty($news)): ?>
                <div class="alert alert-info">No news articles available.</div>
            <?php else: ?>
                <?php foreach ($news as $article): ?>
                    <div class="card mb-4">
                        <div class="card-body">
                            <h3 class="card-title">
                                <a href="single-news.php?id=<?php echo $article['id']; ?>">
                                    <?php echo htmlspecialchars($article['title']); ?>
                                </a>
                            </h3>
                            <p class="text-muted">
                                <small>
                                    Published: <?php echo date('F j, Y', strtotime($article['created_at'])); ?>
                                    | Author: <?php echo htmlspecialchars($article['author']); ?>
                                </small>
                            </p>
                            <p class="card-text">
                                <?php 
                                $excerpt = strip_tags($article['content']);
                                echo strlen($excerpt) > 200 ? substr($excerpt, 0, 200) . '...' : $excerpt;
                                ?>
                            </p>
                            <a href="single-news.php?id=<?php echo $article['id']; ?>" class="btn btn-primary">
                                Read More
                            </a>
                        </div>
                    </div>
                <?php endforeach; ?>
            <?php endif; ?>
        </div>
        
        <div class="col-md-4">
            <div class="card">
                <div class="card-body">
                    <h4>Categories</h4>
                    <ul class="list-unstyled">
                        <li><a href="#">Technology</a></li>
                        <li><a href="#">Politics</a></li>
                        <li><a href="#">Sports</a></li>
                        <li><a href="#">Entertainment</a></li>
                        <li><a href="#">Business</a></li>
                    </ul>
                    
                    <hr>
                    
                    <h4>Quick Links</h4>
                    <ul class="list-unstyled">
                        <li><a href="about.php">About Us</a></li>
                        <li><a href="contact.php">Contact</a></li>
                        <li><a href="news.php">All News</a></li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>

<?php
// Include footer
include 'includes/footer.php';
?>
```

### **includes/Database.php:**
```php
<?php
/**
 * Database connection class using PDO
 */
class Database {
    private $host;
    private $dbname;
    private $username;
    private $password;
    private $conn;
    
    public function __construct() {
        // Load configuration
        require_once __DIR__ . '/../config/config.php';
        
        $this->host = DB_HOST;
        $this->dbname = DB_NAME;
        $this->username = DB_USER;
        $this->password = DB_PASS;
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
            // Log error
            error_log("Database connection failed: " . $e->getMessage());
            
            // Show user-friendly message
            if (defined('APP_DEBUG') && APP_DEBUG) {
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
    
    public function lastInsertId() {
        return $this->conn->lastInsertId();
    }
}
?>
```

### **config/config.php:**
```php
<?php
/**
 * Configuration file for KhabriBaba
 */

// Database configuration
define('DB_HOST', 'localhost');
define('DB_NAME', 'khabribaba');
define('DB_USER', 'root');
define('DB_PASS', '');

// Application settings
define('APP_NAME', 'KhabriBaba');
define('APP_URL', 'http://localhost/khabribaba');
define('APP_DEBUG', true); // Set to false in production

// File paths
define('UPLOAD_PATH', __DIR__ . '/../uploads/');
define('LOG_PATH', __DIR__ . '/../logs/');

// Security
define('SESSION_TIMEOUT', 3600); // 1 hour

// Create necessary directories
$directories = [UPLOAD_PATH, LOG_PATH];
foreach ($directories as $dir) {
    if (!is_dir($dir)) {
        mkdir($dir, 0755, true);
    }
}

// Error reporting
if (APP_DEBUG) {
    error_reporting(E_ALL);
    ini_set('display_errors', 1);
} else {
    error_reporting(0);
    ini_set('display_errors', 0);
}

// Timezone
date_default_timezone_set('Asia/Kolkata');

// Start session if not already started
if (session_status() === PHP_SESSION_NONE) {
    session_start();
}
?>
```

## **1.4 Using Cursor AI Effectively**

### **Cursor Chat Commands for PHP:**
```
"Create a contact form with validation"
"Add a login system with sessions"
"Create an admin panel for news management"
"Add pagination to the news listing"
"Create a search functionality for news"
"Add image upload feature for news"
"Create a newsletter subscription system"
"Add social media sharing buttons"
"Create a comments system for articles"
"Add user registration and profile"
```

### **Keyboard Shortcuts:**
```
Ctrl/Cmd + I           Open Chat
Ctrl/Cmd + K           AI Code Actions
Ctrl/Cmd + /           Toggle Comment
Ctrl/Cmd + Space       Code Completion
Ctrl/Cmd + F           Find in File
Ctrl/Cmd + Shift + F   Find in All Files
Ctrl/Cmd + `           Open Terminal
```

### **Create .cursorrules File:**
```bash
# In Cursor Terminal:
echo '# Cursor Rules for KhabriBaba PHP Project

## Project Structure
- Follow MVC pattern loosely
- Keep includes/ for PHP classes
- Store assets in assets/css/ and assets/js/
- Uploads go in uploads/ directory

## Code Style
- Use 4 spaces for indentation (no tabs)
- Use single quotes for strings when possible
- Always use htmlspecialchars() for output
- Use prepared statements for all database queries
- Add comments for complex logic

## Security Rules
- Never echo user input directly
- Validate ALL user input
- Use password_hash() for passwords
- Store sensitive data in config/config.php
- Use HTTPS in production

## Database Rules
- Use PDO for database connections
- Always use try-catch for database operations
- Create indexes for frequently queried columns
- Use transactions for multiple related queries

## Naming Conventions
- Classes: PascalCase (Database, NewsArticle)
- Functions/methods: camelCase (getNews, connectDatabase)
- Variables: snake_case ($user_id, $news_list)
- Constants: UPPERCASE (DB_HOST, APP_NAME)

## Deployment Notes
- CI/CD is set up via GitHub Actions
- .env files are ignored in git
- Database migrations in sql/ directory
- Tests go in tests/ directory' > .cursorrules
```

---

# **🔧 PART 2: VERSION CONTROL & GIT**

## **2.1 Git Setup for PHP Projects**

### **Initialize Git Repository:**
```bash
# Open Cursor Terminal (Ctrl + `)

# Navigate to project
cd /path/to/khabribaba

# Initialize Git
git init

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.autocrlf false  # For Windows/Linux compatibility
git config --global core.filemode false
```

### **Create .gitignore for PHP:**
```bash
# In Cursor Terminal:
echo '# PHP .gitignore

# Environment files
.env
.env.local
.env.production
.env.development

# Dependencies
/vendor/
/node_modules/
/composer.lock
/package-lock.json
yarn.lock

# Uploads and user content
/uploads/*
!/uploads/.gitkeep
/cache/*
!/cache/.gitkeep
/sessions/*
!/sessions/.gitkeep

# Logs
*.log
error_log
access_log
/logs/*
!/logs/.gitkeep

# System files
.DS_Store
Thumbs.db
Desktop.ini
*.swp
*.swo
*~

# IDE files
.vscode/
.idea/
.phpstorm.meta.php
*.iml

# XAMPP/MAMP
/xampp/
/mamp/
/htdocs/

# Database
*.sql
*.sqlite
/db-backups/

# Temporary files
*.tmp
*.temp

# Test results
/coverage/
.phpunit.result.cache

# Development tools
composer.phar
phpunit.phar

# OS specific
ehthumbs.db
[Tt]humbs.db' > .gitignore
```

### **Create .gitkeep files for empty directories:**
```bash
# In Cursor Terminal:
touch uploads/.gitkeep
touch cache/.gitkeep
touch sessions/.gitkeep
touch logs/.gitkeep
```

## **2.2 First Git Commit**

### **Stage and Commit Files:**
```bash
# Check status
git status

# Add all files
git add .

# Check what will be committed
git status

# Commit with message
git commit -m "Initial commit: KhabriBaba PHP project structure"

# View commit history
git log --oneline
```

## **2.3 GitHub Repository Setup**

### **Create GitHub Repository:**
```
1. Go to: https://github.com
2. Sign in or create account
3. Click "+" → "New repository"
4. Repository name: khabribaba
5. Description: "KhabriBaba News Portal"
6. Choose: Private (recommended)
7. DO NOT check "Initialize with README"
8. Click "Create repository"
```

### **Connect Local Repository to GitHub:**
```bash
# Copy the commands GitHub shows you, or use:

# Add remote origin
git remote add origin https://github.com/YOUR-USERNAME/khabribaba.git

# Rename branch to main
git branch -M main

# Push to GitHub
git push -u origin main

# If asked for credentials, use GitHub Personal Access Token
# Generate at: GitHub → Settings → Developer settings → Personal access tokens
```

### **Create README.md:**
```markdown
# KhabriBaba News Portal

A modern PHP-based news portal website with admin panel, user authentication, and CI/CD pipeline.

## Features
- 📰 Latest news display
- 👥 User registration/login
- 🛡️ Admin panel for content management
- 📱 Responsive design
- 🔍 Search functionality
- 📧 Contact form
- 🚀 CI/CD with GitHub Actions

## Tech Stack
- PHP 7.4+
- MySQL/MariaDB
- HTML5/CSS3/JavaScript
- Bootstrap 5
- GitHub Actions for CI/CD

## Installation

1. Clone repository:
```bash
git clone https://github.com/YOUR-USERNAME/khabribaba.git
```

2. Import database:
```sql
mysql -u root -p khabribaba < sql/schema.sql
```

3. Configure environment:
```bash
cp .env.example .env
# Edit .env with your database credentials
```

4. Access website:
```
http://localhost/khabribaba
```

## Development

- Local server: XAMPP/MAMP
- Editor: Cursor/VSCode
- Version control: Git + GitHub
- CI/CD: GitHub Actions

## Deployment

Automatic deployment via GitHub Actions on push to main branch.

## License
MIT License
```

## **2.4 Daily Git Workflow**

### **Typical Development Day:**
```bash
# Start of day - Get latest changes
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/add-contact-form

# Make changes, add files
git add .
git commit -m "Add contact form with validation and email sending"

# Push branch to GitHub
git push origin feature/add-contact-form

# End of day - If not finished
git add .
git commit -m "WIP: Contact form progress"
git push origin feature/add-contact-form
```

### **Git Commands Cheat Sheet:**
```bash
# Basic
git status                    # Check status
git add .                     # Add all changes
git add filename.php          # Add specific file
git commit -m "Message"       # Commit changes
git push                      # Push to remote
git pull                      # Pull from remote

# Branches
git branch                    # List branches
git checkout -b new-branch    # Create and switch
git checkout branch-name      # Switch branch
git merge branch-name         # Merge branch
git branch -d branch-name     # Delete branch

# History
git log --oneline             # View commits
git diff                      # See changes
git show commit-hash          # View specific commit

# Undo
git reset --soft HEAD~1       # Undo commit, keep changes
git reset --hard HEAD~1       # Undo commit, discard changes
git checkout -- file.php      # Discard file changes
git revert commit-hash        # Create undo commit

# Remote
git remote -v                 # Show remotes
git fetch origin              # Fetch without merge
git push origin --delete branch  # Delete remote branch
```

---

# **🛡️ PART 3: PHP DEVELOPMENT ESSENTIALS**

## **3.1 Database Schema & Setup**

### **Create Database Schema:**
**sql/schema.sql:**
```sql
-- KhabriBaba Database Schema
-- Version: 1.0

CREATE DATABASE IF NOT EXISTS khabribaba;
USE khabribaba;

-- Users table
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(100),
    avatar VARCHAR(255),
    role ENUM('admin', 'editor', 'author', 'subscriber') DEFAULT 'subscriber',
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    last_login DATETIME,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_username (username),
    INDEX idx_email (email),
    INDEX idx_role (role)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Categories table
CREATE TABLE categories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    parent_id INT DEFAULT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (parent_id) REFERENCES categories(id) ON DELETE SET NULL,
    INDEX idx_slug (slug)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- News articles table
CREATE TABLE news (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    content LONGTEXT NOT NULL,
    excerpt TEXT,
    featured_image VARCHAR(255),
    author_id INT NOT NULL,
    category_id INT,
    views INT DEFAULT 0,
    status ENUM('draft', 'published', 'archived') DEFAULT 'draft',
    published_at DATETIME,
    meta_title VARCHAR(255),
    meta_description TEXT,
    meta_keywords TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL,
    INDEX idx_slug (slug),
    INDEX idx_status (status),
    INDEX idx_published_at (published_at),
    INDEX idx_author (author_id),
    INDEX idx_category (category_id),
    FULLTEXT idx_search (title, content, excerpt)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Comments table
CREATE TABLE comments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    news_id INT NOT NULL,
    user_id INT,
    parent_id INT DEFAULT NULL,
    author_name VARCHAR(100) NOT NULL,
    author_email VARCHAR(100),
    author_ip VARCHAR(45),
    content TEXT NOT NULL,
    status ENUM('pending', 'approved', 'spam', 'trash') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (news_id) REFERENCES news(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    FOREIGN KEY (parent_id) REFERENCES comments(id) ON DELETE CASCADE,
    INDEX idx_news (news_id),
    INDEX idx_status (status),
    INDEX idx_user (user_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Subscribers table
CREATE TABLE subscribers (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(100),
    token VARCHAR(100) UNIQUE NOT NULL,
    status ENUM('active', 'unsubscribed', 'bounced') DEFAULT 'active',
    subscribed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    unsubscribed_at DATETIME,
    INDEX idx_email (email),
    INDEX idx_token (token)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Settings table
CREATE TABLE settings (
    id INT PRIMARY KEY AUTO_INCREMENT,
    setting_key VARCHAR(100) UNIQUE NOT NULL,
    setting_value TEXT,
    setting_type ENUM('string', 'integer', 'boolean', 'array', 'json') DEFAULT 'string',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_key (setting_key)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Insert default categories
INSERT INTO categories (name, slug, description) VALUES
('Technology', 'technology', 'Latest tech news and reviews'),
('Politics', 'politics', 'Political news and analysis'),
('Sports', 'sports', 'Sports news and updates'),
('Entertainment', 'entertainment', 'Entertainment and celebrity news'),
('Business', 'business', 'Business and finance news'),
('Health', 'health', 'Health and wellness news'),
('Education', 'education', 'Education news and resources'),
('Science', 'science', 'Science news and discoveries');

-- Insert default admin user (password: admin123)
INSERT INTO users (username, email, password_hash, full_name, role) VALUES
('admin', 'admin@khabribaba.com', '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', 'Administrator', 'admin');

-- Insert default settings
INSERT INTO settings (setting_key, setting_value, setting_type) VALUES
('site_name', 'KhabriBaba', 'string'),
('site_description', 'Your trusted news source', 'string'),
('site_email', 'contact@khabribaba.com', 'string'),
('posts_per_page', '10', 'integer'),
('comment_moderation', '1', 'boolean'),
('maintenance_mode', '0', 'boolean'),
('social_links', '{"facebook":"https://facebook.com/khabribaba","twitter":"https://twitter.com/khabribaba","instagram":"https://instagram.com/khabribaba"}', 'json');
```

### **Import Database:**
```bash
# Using MySQL command line
mysql -u root -p khabribaba < sql/schema.sql

# Using phpMyAdmin:
# 1. Go to http://localhost/phpmyadmin
# 2. Select khabribaba database
# 3. Click "Import" tab
# 4. Choose schema.sql file
# 5. Click "Go"
```

## **3.2 Environment Configuration**

### **Create .env.example:**
```env
# KhabriBaba Environment Configuration
# Copy to .env and fill in your values

# Database Configuration
DB_HOST=localhost
DB_NAME=khabribaba
DB_USER=root
DB_PASS=

# Application Configuration
APP_NAME="KhabriBaba"
APP_ENV=development  # development, staging, production
APP_DEBUG=true
APP_URL=http://localhost/khabribaba
APP_KEY=base64:generate_32_char_random_string_here

# Session Configuration
SESSION_DRIVER=file  # file, database, redis
SESSION_LIFETIME=120
SESSION_ENCRYPT=false

# Mail Configuration
MAIL_DRIVER=smtp  # smtp, sendmail, mail
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@khabribaba.com
MAIL_FROM_NAME="KhabriBaba"

# Cache Configuration
CACHE_DRIVER=file  # file, database, redis, memcached
CACHE_PREFIX=khabribaba_

# File Uploads
UPLOAD_MAX_SIZE=5242880  # 5MB in bytes
ALLOWED_FILE_TYPES=jpg,jpeg,png,gif,webp,pdf,doc,docx
UPLOAD_PATH=uploads/

# Security
ENABLE_HTTPS=false
CSRF_PROTECTION=true
XSS_PROTECTION=true

# Third-Party Services
GOOGLE_RECAPTCHA_SITE_KEY=your_site_key
GOOGLE_RECAPTCHA_SECRET_KEY=your_secret_key
GOOGLE_ANALYTICS_ID=UA-XXXXXXXXX-X

# API Keys
NEWS_API_KEY=your_news_api_key
WEATHER_API_KEY=your_weather_api_key

# Debugging
LOG_CHANNEL=stack  # single, daily, slack, syslog, errorlog
LOG_LEVEL=debug  # emergency, alert, critical, error, warning, notice, info, debug
```

### **Update config.php to use .env:**
```php
<?php
/**
 * Configuration file that loads from .env
 */

// Load .env file if it exists
$env_path = __DIR__ . '/../.env';
if (file_exists($env_path)) {
    $lines = file($env_path, FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
    foreach ($lines as $line) {
        if (strpos(trim($line), '#') === 0) {
            continue;
        }
        list($name, $value) = explode('=', $line, 2);
        $name = trim($name);
        $value = trim($value);
        
        // Remove quotes if present
        if (preg_match('/^"(.*)"$/', $value, $matches)) {
            $value = $matches[1];
        } elseif (preg_match("/^'(.*)'$/", $value, $matches)) {
            $value = $matches[1];
        }
        
        putenv("$name=$value");
        $_ENV[$name] = $value;
        $_SERVER[$name] = $value;
    }
}

// Database configuration from environment
define('DB_HOST', getenv('DB_HOST') ?: 'localhost');
define('DB_NAME', getenv('DB_NAME') ?: 'khabribaba');
define('DB_USER', getenv('DB_USER') ?: 'root');
define('DB_PASS', getenv('DB_PASS') ?: '');

// Application settings
define('APP_NAME', getenv('APP_NAME') ?: 'KhabriBaba');
define('APP_ENV', getenv('APP_ENV') ?: 'production');
define('APP_DEBUG', filter_var(getenv('APP_DEBUG'), FILTER_VALIDATE_BOOLEAN) ?: false);
define('APP_URL', getenv('APP_URL') ?: 'http://localhost/khabribaba');

// File paths
define('UPLOAD_PATH', __DIR__ . '/../' . (getenv('UPLOAD_PATH') ?: 'uploads/'));
define('LOG_PATH', __DIR__ . '/../logs/');

// Security
define('SESSION_TIMEOUT', getenv('SESSION_LIFETIME') ?: 3600);

// Create necessary directories
$directories = [UPLOAD_PATH, LOG_PATH];
foreach ($directories as $dir) {
    if (!is_dir($dir)) {
        mkdir($dir, 0755, true);
    }
}

// Error reporting based on environment
if (APP_DEBUG) {
    error_reporting(E_ALL);
    ini_set('display_errors', 1);
    ini_set('log_errors', 1);
    ini_set('error_log', LOG_PATH . 'php_errors.log');
} else {
    error_reporting(0);
    ini_set('display_errors', 0);
}

// Timezone
date_default_timezone_set('Asia/Kolkata');

// Start session if not already started
if (session_status() === PHP_SESSION_NONE) {
    session_start();
    
    // Set session security
    if (APP_ENV === 'production') {
        ini_set('session.cookie_secure', 1);
        ini_set('session.cookie_httponly', 1);
        ini_set('session.cookie_samesite', 'Strict');
    }
}

// Force HTTPS in production
if (APP_ENV === 'production' && (!isset($_SERVER['HTTPS']) || $_SERVER['HTTPS'] !== 'on')) {
    if (getenv('ENABLE_HTTPS')) {
        header('Location: https://' . $_SERVER['HTTP_HOST'] . $_SERVER['REQUEST_URI']);
        exit();
    }
}
?>
```

## **3.3 Security Best Practices**

### **Input Validation Class:**
**includes/Validator.php:**
```php
<?php
/**
 * Input validation and sanitization class
 */
class Validator {
    
    /**
     * Validate email address
     */
    public static function validateEmail($email) {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            return false;
        }
        
        // Check DNS MX records
        if (!self::checkDns($email)) {
            return false;
        }
        
        return true;
    }
    
    /**
     * Validate URL
     */
    public static function validateUrl($url) {
        if (!filter_var($url, FILTER_VALIDATE_URL)) {
            return false;
        }
        
        // Allowed protocols
        $allowed_protocols = ['http', 'https', 'ftp'];
        $parsed = parse_url($url);
        
        if (!in_array($parsed['scheme'], $allowed_protocols)) {
            return false;
        }
        
        return true;
    }
    
    /**
     * Sanitize string input
     */
    public static function sanitizeString($input, $strip_tags = true) {
        if ($strip_tags) {
            $input = strip_tags($input);
        }
        
        $input = trim($input);
        $input = stripslashes($input);
        $input = htmlspecialchars($input, ENT_QUOTES, 'UTF-8');
        
        return $input;
    }
    
    /**
     * Sanitize integer
     */
    public static function sanitizeInt($input) {
        return filter_var($input, FILTER_SANITIZE_NUMBER_INT);
    }
    
    /**
     * Sanitize float
     */
    public static function sanitizeFloat($input) {
        return filter_var($input, FILTER_SANITIZE_NUMBER_FLOAT, FILTER_FLAG_ALLOW_FRACTION);
    }
    
    /**
     * Validate password strength
     */
    public static function validatePassword($password) {
        // At least 8 characters
        if (strlen($password) < 8) {
            return false;
        }
        
        // At least one uppercase letter
        if (!preg_match('/[A-Z]/', $password)) {
            return false;
        }
        
        // At least one lowercase letter
        if (!preg_match('/[a-z]/', $password)) {
            return false;
        }
        
        // At least one number
        if (!preg_match('/[0-9]/', $password)) {
            return false;
        }
        
        // At least one special character
        if (!preg_match('/[^A-Za-z0-9]/', $password)) {
            return false;
        }
        
        return true;
    }
    
    /**
     * Validate file upload
     */
    public static function validateFile($file, $allowed_types = ['jpg', 'jpeg', 'png', 'gif'], $max_size = 5242880) {
        $errors = [];
        
        // Check for upload errors
        if ($file['error'] !== UPLOAD_ERR_OK) {
            $errors[] = 'File upload error: ' . $file['error'];
            return $errors;
        }
        
        // Check file size
        if ($file['size'] > $max_size) {
            $errors[] = 'File too large. Maximum size: ' . ($max_size / 1024 / 1024) . 'MB';
        }
        
        // Get file extension
        $file_ext = strtolower(pathinfo($file['name'], PATHINFO_EXTENSION));
        
        // Check file type
        if (!in_array($file_ext, $allowed_types)) {
            $errors[] = 'Invalid file type. Allowed: ' . implode(', ', $allowed_types);
        }
        
        // Check MIME type
        $finfo = finfo_open(FILEINFO_MIME_TYPE);
        $mime_type = finfo_file($finfo, $file['tmp_name']); 
        finfo_close($finfo);
        
        $allowed_mimes = [
            'jpg' => 'image/jpeg',
            'jpeg' => 'image/jpeg',
            'png' => 'image/png',
            'gif' => 'image/gif',
            'pdf' => 'application/pdf'
        ];
        
        if (!in_array($mime_type, $allowed_mimes)) {
            $errors[] = 'Invalid MIME type';
        }
        
        return empty($errors) ? true : $errors;
    }
    
    /**
     * Check DNS MX records for email validation
     */
    private static function checkDns($email) {
        if (!function_exists('getmxrr')) {
            return true; // Skip if function not available
        }
        
        $domain = substr(strrchr($email, "@"), 1);
        return getmxrr($domain, $mxhosts);
    }
    
    /**
     * Prevent SQL injection
     */
    public static function preventSqlInjection($input) {
        // Remove SQL keywords (basic protection - use prepared statements!)
        $sql_keywords = ['SELECT', 'INSERT', 'UPDATE', 'DELETE', 'DROP', 'UNION', '--', ';'];
        $input = str_ireplace($sql_keywords, '', $input);
        
        return $input;
    }
    
    /**
     * Prevent XSS attacks
     */
    public static function preventXss($input) {
        // Remove JavaScript events
        $events = ['onload', 'onclick', 'onmouseover', 'onkeypress', 'javascript:'];
        $input = str_ireplace($events, '', $input);
        
        // Remove script tags
        $input = preg_replace('/<script\b[^>]*>(.*?)<\/script>/is', '', $input);
        
        return $input;
    }
    
    /**
     * Generate CSRF token
     */
    public static function generateCsrfToken() {
        if (empty($_SESSION['csrf_token'])) {
            $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
        }
        return $_SESSION['csrf_token'];
    }
    
    /**
     * Validate CSRF token
     */
    public static function validateCsrfToken($token) {
        if (empty($_SESSION['csrf_token']) || !hash_equals($_SESSION['csrf_token'], $token)) {
            return false;
        }
        return true;
    }
}
?>
```

### **Authentication System:**
**includes/Auth.php:**
```php
<?php
/**
 * Authentication and authorization class
 */
class Auth {
    private $db;
    
    public function __construct() {
        $this->db = new Database();
    }
    
    /**
     * User login
     */
    public function login($username, $password) {
        // Validate input
        $username = Validator::sanitizeString($username);
        
        // Find user by username or email
        $stmt = $this->db->query(
            "SELECT * FROM users WHERE (username = :username OR email = :username) AND status = 'active'",
            [':username' => $username]
        );
        
        $user = $stmt->fetch();
        
        if ($user && password_verify($password, $user['password_hash'])) {
            // Regenerate session ID to prevent fixation
            session_regenerate_id(true);
            
            // Set session variables
            $_SESSION['user_id'] = $user['id'];
            $_SESSION['username'] = $user['username'];
            $_SESSION['email'] = $user['email'];
            $_SESSION['role'] = $user['role'];
            $_SESSION['logged_in'] = true;
            $_SESSION['login_time'] = time();
            
            // Update last login
            $this->db->query(
                "UPDATE users SET last_login = NOW() WHERE id = :id",
                [':id' => $user['id']]
            );
            
            // Log login activity
            $this->logActivity($user['id'], 'login', 'User logged in');
            
            return true;
        }
        
        // Log failed attempt
        $this->logActivity(0, 'failed_login', 'Failed login attempt for username: ' . $username);
        
        return false;
    }
    
    /**
     * User registration
     */
    public function register($data) {
        // Validate input
        $errors = [];
        
        if (!Validator::validateEmail($data['email'])) {
            $errors['email'] = 'Invalid email address';
        }
        
        if (strlen($data['username']) < 3) {
            $errors['username'] = 'Username must be at least 3 characters';
        }
        
        if (!Validator::validatePassword($data['password'])) {
            $errors['password'] = 'Password must be at least 8 characters with uppercase, lowercase, number and special character';
        }
        
        if ($data['password'] !== $data['confirm_password']) {
            $errors['confirm_password'] = 'Passwords do not match';
        }
        
        // Check if username/email exists
        $stmt = $this->db->query(
            "SELECT COUNT(*) as count FROM users WHERE username = :username OR email = :email",
            [':username' => $data['username'], ':email' => $data['email']]
        );
        
        if ($stmt->fetch()['count'] > 0) {
            $errors['general'] = 'Username or email already exists';
        }
        
        if (!empty($errors)) {
            return ['success' => false, 'errors' => $errors];
        }
        
        // Hash password
        $hashed_password = password_hash($data['password'], PASSWORD_DEFAULT);
        
        // Generate verification token
        $verification_token = bin2hex(random_bytes(32));
        
        // Insert user
        $stmt = $this->db->query(
            "INSERT INTO users (username, email, password_hash, full_name, role) 
             VALUES (:username, :email, :password, :full_name, 'subscriber')",
            [
                ':username' => $data['username'],
                ':email' => $data['email'],
                ':password' => $hashed_password,
                ':full_name' => $data['full_name']
            ]
        );
        
        $user_id = $this->db->lastInsertId();
        
        // Log registration
        $this->logActivity($user_id, 'registration', 'New user registered');
        
        // Send welcome email
        $this->sendWelcomeEmail($data['email'], $data['username']);
        
        return ['success' => true, 'user_id' => $user_id];
    }
    
    /**
     * Check if user is logged in
     */
    public function isLoggedIn() {
        if (!isset($_SESSION['logged_in']) || $_SESSION['logged_in'] !== true) {
            return false;
        }
        
        // Check session timeout
        if (isset($_SESSION['login_time']) && (time() - $_SESSION['login_time']) > SESSION_TIMEOUT) {
            $this->logout();
            return false;
        }
        
        // Update last activity
        $_SESSION['last_activity'] = time();
        
        return true;
    }
    
    /**
     * Check user role
     */
    public function hasRole($role) {
        if (!$this->isLoggedIn()) {
            return false;
        }
        
        return $_SESSION['role'] === $role;
    }
    
    /**
     * Check permission
     */
    public function can($permission) {
        if (!$this->isLoggedIn()) {
            return false;
        }
        
        // Define role permissions
        $permissions = [
            'admin' => ['manage_users', 'manage_content', 'manage_settings', 'view_reports'],
            'editor' => ['manage_content', 'view_reports'],
            'author' => ['create_content', 'edit_own_content'],
            'subscriber' => ['view_content', 'comment']
        ];
        
        $role = $_SESSION['role'];
        
        return in_array($permission, $permissions[$role] ?? []);
    }
    
    /**
     * User logout
     */
    public function logout() {
        // Log activity
        if (isset($_SESSION['user_id'])) {
            $this->logActivity($_SESSION['user_id'], 'logout', 'User logged out');
        }
        
        // Clear session
        $_SESSION = [];
        
        // Destroy session
        if (ini_get("session.use_cookies")) {
            $params = session_get_cookie_params();
            setcookie(session_name(), '', time() - 42000,
                $params["path"], $params["domain"],
                $params["secure"], $params["httponly"]
            );
        }
        
        session_destroy();
    }
    
    /**
     * Password reset
     */
    public function requestPasswordReset($email) {
        $email = Validator::sanitizeString($email);
        
        $stmt = $this->db->query(
            "SELECT id FROM users WHERE email = :email AND status = 'active'",
            [':email' => $email]
        );
        
        $user = $stmt->fetch();
        
        if ($user) {
            // Generate reset token
            $reset_token = bin2hex(random_bytes(32));
            $expires = date('Y-m-d H:i:s', strtotime('+1 hour'));
            
            // Store token in database
            $this->db->query(
                "INSERT INTO password_resets (user_id, token, expires_at) 
                 VALUES (:user_id, :token, :expires)",
                [
                    ':user_id' => $user['id'],
                    ':token' => $reset_token,
                    ':expires' => $expires
                ]
            );
            
            // Send reset email
            $reset_link = APP_URL . "/reset-password.php?token=" . $reset_token;
            $this->sendPasswordResetEmail($email, $reset_link);
            
            return true;
        }
        
        return false;
    }
    
    /**
     * Reset password with token
     */
    public function resetPassword($token, $new_password) {
        // Validate token
        $stmt = $this->db->query(
            "SELECT user_id FROM password_resets 
             WHERE token = :token AND expires_at > NOW() AND used = 0",
            [':token' => $token]
        );
        
        $reset = $stmt->fetch();
        
        if (!$reset) {
            return false;
        }
        
        // Validate new password
        if (!Validator::validatePassword($new_password)) {
            return false;
        }
        
        // Update password
        $hashed_password = password_hash($new_password, PASSWORD_DEFAULT);
        
        $this->db->query(
            "UPDATE users SET password_hash = :password WHERE id = :id",
            [
                ':password' => $hashed_password,
                ':id' => $reset['user_id']
            ]
        );
        
        // Mark token as used
        $this->db->query(
            "UPDATE password_resets SET used = 1 WHERE token = :token",
            [':token' => $token]
        );
        
        // Log password reset
        $this->logActivity($reset['user_id'], 'password_reset', 'Password reset successfully');
        
        return true;
    }
    
    /**
     * Log user activity
     */
    private function logActivity($user_id, $action, $description) {
        $this->db->query(
            "INSERT INTO activity_logs (user_id, action, description, ip_address, user_agent) 
             VALUES (:user_id, :action, :description, :ip, :ua)",
            [
                ':user_id' => $user_id,
                ':action' => $action,
                ':description' => $description,
                ':ip' => $_SERVER['REMOTE_ADDR'] ?? '0.0.0.0',
                ':ua' => $_SERVER['HTTP_USER_AGENT'] ?? ''
            ]
        );
    }
    
    /**
     * Send welcome email
     */
    private function sendWelcomeEmail($email, $username) {
        $subject = "Welcome to KhabriBaba!";
        $body = "Hello {$username},\n\nWelcome to KhabriBaba! We're excited to have you on board.\n\n";
        $body .= "You can now login to your account and start exploring.\n\n";
        $body .= "Best regards,\nThe KhabriBaba Team";
        
        // In a real application, use PHPMailer or similar
        // mail($email, $subject, $body);
    }
    
    /**
     * Send password reset email
     */
    private function sendPasswordResetEmail($email, $reset_link) {
        $subject = "Password Reset Request - KhabriBaba";
        $body = "Hello,\n\n";
        $body .= "You requested a password reset. Click the link below to reset your password:\n";
        $body .= $reset_link . "\n\n";
        $body .= "This link will expire in 1 hour.\n\n";
        $body .= "If you didn't request this, please ignore this email.\n\n";
        $body .= "Best regards,\nThe KhabriBaba Team";
        
        // In a real application, use PHPMailer or similar
        // mail($email, $subject, $body);
    }
}
?>
```

### **Activity Logs Table:**
Add to **sql/schema.sql**:
```sql
-- Activity logs table
CREATE TABLE activity_logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    action VARCHAR(100) NOT NULL,
    description TEXT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user (user_id),
    INDEX idx_action (action),
    INDEX idx_created (created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Password reset tokens table
CREATE TABLE password_resets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    token VARCHAR(100) UNIQUE NOT NULL,
    expires_at DATETIME NOT NULL,
    used BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_token (token),
    INDEX idx_expires (expires_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

# **🚀 PART 4: CI/CD PIPELINE (RECOMMENDED APPROACH)**

## **4.1 Why Simple SSH Deployment is Best for You**

### **The Reality Check:**
```
✅ YOU CURRENTLY: SSH into server, edit files directly
✅ GOAL: Auto-deploy when you push to GitHub
✅ SIMPLEST PATH: GitHub Actions + SSH deployment
✅ COMPLEX PATH: Docker, containers, orchestration

RECOMMENDATION: Start SIMPLE. Get CI/CD working TODAY.
```

### **Benefits of SSH Deployment:**
1. **Works with Namecheap shared hosting** (most plans)
2. **Uses skills you already have** (SSH)
3. **Easy to debug** - You can manually fix things
4. **No additional costs** - Uses existing infrastructure
5. **Quick setup** - 1-2 hours vs days for Docker

## **4.2 Complete CI/CD Setup Step-by-Step**

### **Step 1: Generate SSH Keys**
```bash
# On YOUR COMPUTER (Cursor Terminal):
ssh-keygen -t ed25519 -C "github-actions@khabribaba.com" -f ~/.ssh/khabribaba-deploy

# When asked for passphrase, press Enter (empty)
# This creates:
# ~/.ssh/khabribaba-deploy      (PRIVATE KEY - Keep secret!)
# ~/.ssh/khabribaba-deploy.pub  (PUBLIC KEY - Goes to server)

# View public key
cat ~/.ssh/khabribaba-deploy.pub
# Copy ALL output (starts with ssh-ed25519...)
```

### **Step 2: Add Public Key to Namecheap Server**
```bash
# SSH into your Namecheap server (one last manual login)
ssh your-username@khabribaba.com
# Enter password when asked

# On server, add the public key:
mkdir -p ~/.ssh
echo "PASTE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# Test it works (back on YOUR computer):
ssh -i ~/.ssh/khabribaba-deploy your-username@khabribaba.com
# Should login without password!
```

### **Step 3: Add GitHub Secrets**
```
1. Go to your GitHub repository: https://github.com/YOUR-USERNAME/khabribaba
2. Click "Settings" → "Secrets and variables" → "Actions"
3. Click "New repository secret"
```

**Add these secrets:**

**Secret 1: SSH_PRIVATE_KEY**
```bash
# On YOUR COMPUTER, view and copy PRIVATE key:
cat ~/.ssh/khabribaba-deploy
# Copy ALL text including -----BEGIN OPENSSH PRIVATE KEY----- to -----END OPENSSH PRIVATE KEY-----
```

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
# Find exact path by SSHing in and typing: pwd
```

**Secret 5: DB_HOST, DB_NAME, DB_USER, DB_PASS** (for database operations)
```
Get these from your Namecheap cPanel → MySQL Databases
```

### **Step 4: Create GitHub Actions Workflow**

**.github/workflows/deploy.yml:**
```yaml
name: 🚀 Deploy to Production

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  PHP_VERSION: '8.1'

jobs:
  # Test on pull requests
  test:
    if: github.event_name == 'pull_request'
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
    
    - name: 📦 Install dependencies
      run: |
        # Install Composer dependencies if composer.json exists
        if [ -f "composer.json" ]; then
          composer install --no-progress --no-suggest
        fi
        
        # Install Node.js dependencies if package.json exists
        if [ -f "package.json" ]; then
          npm install
        fi
    
    - name: 🧪 Run tests
      run: |
        echo "Running PHP syntax checks..."
        find . -name "*.php" -not -path "./vendor/*" -exec php -l {} \;
        
        echo "Checking for exposed credentials..."
        if grep -r "password\|secret\|key\|token" . --include="*.php" | grep -v ".env.example" | grep -v ".git" | head -5; then
          echo "⚠️  WARNING: Possible secrets found in code"
          # Don't fail, just warn for now
        fi
        
        echo "Running custom tests if available..."
        if [ -f "vendor/bin/phpunit" ]; then
          vendor/bin/phpunit
        fi
    
    - name: 📊 Code quality
      run: |
        echo "Checking code quality..."
        # Add your code quality checks here

  # Deploy to production
  deploy:
    needs: test
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
        
        # Create SSH config
        cat >> ~/.ssh/config << EOF
        Host khabribaba
          HostName ${{ secrets.SSH_HOST }}
          User ${{ secrets.SSH_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
        
        # Test connection
        echo "Testing SSH connection..."
        ssh khabribaba "echo '✅ SSH connection successful!'"
    
    - name: 🗃️ Backup current site
      run: |
        echo "Creating backup of current site..."
        TIMESTAMP=$(date +%Y%m%d_%H%M%S)
        BACKUP_DIR="/tmp/khabribaba_backup_$TIMESTAMP"
        
        ssh khabribaba "
          mkdir -p $BACKUP_DIR
          cp -r ${{ secrets.DEPLOY_PATH }}/* $BACKUP_DIR/ 2>/dev/null || true
          echo '📦 Backup created at: $BACKUP_DIR'
        "
    
    - name: 📤 Deploy files
      run: |
        echo "Deploying files to production..."
        
        # Create production environment file
        cat > .env.production << EOF
        # Auto-generated by GitHub Actions - $(date)
        DB_HOST=${{ secrets.DB_HOST }}
        DB_NAME=${{ secrets.DB_NAME }}
        DB_USER=${{ secrets.DB_USER }}
        DB_PASS=${{ secrets.DB_PASS }}
        
        APP_ENV=production
        APP_DEBUG=false
        APP_URL=https://khabribaba.com
        
        # Security - generate new app key
        APP_KEY=$(openssl rand -base64 32)
        EOF
        
        # Deploy using rsync (efficient file transfer)
        rsync -avz --delete \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="README.md" \
          --exclude="composer.json" \
          --exclude="composer.lock" \
          --exclude="package.json" \
          --exclude="package-lock.json" \
          --exclude="node_modules/" \
          --exclude="vendor/" \
          --exclude="*.sql" \
          --exclude=".env.example" \
          --exclude=".env.local" \
          --exclude=".env.development" \
          --include=".env.production" \
          --chmod=Du=rwx,Dg=rx,Do=rx,Fu=rw,Fg=r,Fo=r \
          ./ khabribaba:${{ secrets.DEPLOY_PATH }}/
        
        echo "✅ Files deployed successfully!"
    
    - name: 🔧 Set permissions
      run: |
        echo "Setting proper permissions..."
        ssh khabribaba "
          # Set directory permissions
          find ${{ secrets.DEPLOY_PATH }} -type d -exec chmod 755 {} \;
          
          # Set file permissions
          find ${{ secrets.DEPLOY_PATH }} -type f -exec chmod 644 {} \;
          
          # Special permissions for writable directories
          chmod 775 ${{ secrets.DEPLOY_PATH }}/uploads 2>/dev/null || true
          chmod 775 ${{ secrets.DEPLOY_PATH }}/cache 2>/dev/null || true
          chmod 775 ${{ secrets.DEPLOY_PATH }}/sessions 2>/dev/null || true
          chmod 775 ${{ secrets.DEPLOY_PATH }}/logs 2>/dev/null || true
          
          # Set ownership (adjust for your server setup)
          chown -R ${{ secrets.SSH_USERNAME }}:www-data ${{ secrets.DEPLOY_PATH }} 2>/dev/null || true
          
          # Protect .env file
          chmod 640 ${{ secrets.DEPLOY_PATH }}/.env.production 2>/dev/null || true
          
          # Rename to .env
          mv ${{ secrets.DEPLOY_PATH }}/.env.production ${{ secrets.DEPLOY_PATH }}/.env 2>/dev/null || true
        "
    
    - name: 📦 Install dependencies on server
      run: |
        echo "Installing Composer dependencies on server..."
        ssh khabribaba "
          cd ${{ secrets.DEPLOY_PATH }}
          
          # Install Composer if not present
          if ! command -v composer &> /dev/null && [ -f \"composer.json\" ]; then
            echo 'Installing Composer...'
            php -r \"copy('https://getcomposer.org/installer', 'composer-setup.php');\"
            php composer-setup.php
            php -r \"unlink('composer-setup.php');\"
            php composer.phar install --no-dev --optimize-autoloader
          elif [ -f \"composer.json\" ]; then
            composer install --no-dev --optimize-autoloader
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
          
          # Clear file cache
          rm -rf cache/* 2>/dev/null || true
          rm -rf sessions/* 2>/dev/null || true
          
          # Clear PHP opcache if enabled
          if command -v php > /dev/null; then
            # Create cache clear script
            cat > clear_cache.php << 'EOF'
            <?php
            if (function_exists('opcache_reset')) {
              opcache_reset();
              echo 'OPCache cleared successfully';
            } else {
              echo 'OPCache not enabled';
            }
            EOF
            
            php clear_cache.php
            rm -f clear_cache.php
          fi
          
          echo '✅ Cache cleared successfully'
        "
    
    - name: 🏥 Health check
      run: |
        echo "Running health check..."
        MAX_ATTEMPTS=10
        ATTEMPT=1
        SUCCESS=false
        
        while [ $ATTEMPT -le $MAX_ATTEMPTS ]; do
          echo "Checking site health (Attempt $ATTEMPT/$MAX_ATTEMPTS)..."
          
          if curl -s -f --max-time 30 "https://khabribaba.com" > /dev/null; then
            echo "✅ Site is responding!"
            SUCCESS=true
            break
          else
            echo "⏳ Site not responding yet, waiting 10 seconds..."
            sleep 10
            ATTEMPT=$((ATTEMPT + 1))
          fi
        done
        
        if [ "$SUCCESS" = false ]; then
          echo "❌ Site failed to respond after $MAX_ATTEMPTS attempts"
          
          # Debug: Check server status
          echo "Checking server status..."
          ssh khabribaba "
            echo '--- Apache Status ---'
            systemctl status apache2 2>/dev/null || service apache2 status 2>/dev/null || echo 'Apache not found'
            
            echo '--- Last 20 lines of error log ---'
            tail -20 /var/log/apache2/error.log 2>/dev/null || tail -20 /var/log/httpd/error_log 2>/dev/null || echo 'Error log not found'
            
            echo '--- Disk space ---'
            df -h
            
            echo '--- Memory usage ---'
            free -h
          "
          
          exit 1
        fi
    
    - name: 📢 Deployment summary
      if: success()
      run: |
        echo "🚀 DEPLOYMENT SUCCESSFUL!"
        echo "========================"
        echo "📅 Date: $(date)"
        echo "👤 Deployed by: ${{ github.actor }}"
        echo "📝 Commit: ${{ github.event.head_commit.message }}"
        echo "🔗 Site: https://khabribaba.com"
        echo "📊 Repository: ${{ github.repository }}"
        echo "🏷️  Commit SHA: ${{ github.sha }}"
        
        # Create deployment summary for GitHub UI
        echo "## 🎉 Deployment Successful" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Website:** https://khabribaba.com" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Deployed at:** $(date)" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Commit:** \`${{ github.sha }}\`" >> $GITHUB_STEP_SUMMARY
        echo "" >> $GITHUB_STEP_SUMMARY
        echo "**Changes:** ${{ github.event.head_commit.message }}" >> $GITHUB_STEP_SUMMARY
    
    - name: 🚨 Rollback on failure
      if: failure()
      run: |
        echo "🚨 DEPLOYMENT FAILED - Initiating rollback..."
        
        # Find latest backup
        LATEST_BACKUP=$(ssh khabribaba "ls -td /tmp/khabribaba_backup_* 2>/dev/null | head -1")
        
        if [ -z "$LATEST_BACKUP" ]; then
          echo "❌ No backup found for rollback!"
          exit 1
        fi
        
        echo "Rolling back to: $LATEST_BACKUP"
        
        # Restore from backup
        ssh khabribaba "
          echo 'Restoring from backup...'
          rm -rf ${{ secrets.DEPLOY_PATH }}/*
          cp -r $LATEST_BACKUP/* ${{ secrets.DEPLOY_PATH }}/
          echo '✅ Rollback complete!'
        "
        
        echo "🎯 Rollback completed successfully"
        echo "⚠️  Manual intervention may be required"
```

### **Step 5: Create Database Migration Script**

**scripts/migrate.php:**
```php
<?php
/**
 * Database migration script
 * Run via: php scripts/migrate.php
 */

require_once __DIR__ . '/../includes/Database.php';

class Migration {
    private $db;
    private $conn;
    
    public function __construct() {
        $this->db = new Database();
        $this->conn = $this->db->connect();
        
        // Create migrations table if not exists
        $this->createMigrationsTable();
    }
    
    private function createMigrationsTable() {
        $sql = "CREATE TABLE IF NOT EXISTS migrations (
            id INT PRIMARY KEY AUTO_INCREMENT,
            migration VARCHAR(255) UNIQUE NOT NULL,
            batch INT NOT NULL,
            executed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4";
        
        $this->conn->exec($sql);
    }
    
    public function run() {
        echo "🚀 Starting database migrations...\n\n";
        
        // Get already executed migrations
        $stmt = $this->conn->query("SELECT migration FROM migrations");
        $executed = $stmt->fetchAll(PDO::FETCH_COLUMN);
        
        // Define migrations (in order)
        $migrations = [
            '001_create_users_table',
            '002_create_categories_table',
            '003_create_news_table',
            '004_create_comments_table',
            '005_create_subscribers_table',
            '006_create_settings_table',
            '007_create_activity_logs_table',
            '008_create_password_resets_table',
            '009_insert_default_data'
        ];
        
        $batch = $this->getNextBatchNumber();
        $executed_count = 0;
        
        foreach ($migrations as $migration) {
            if (in_array($migration, $executed)) {
                echo "✓ $migration (already executed)\n";
                continue;
            }
            
            echo "▶ Executing: $migration\n";
            
            try {
                $this->conn->beginTransaction();
                
                // Execute migration
                $this->executeMigration($migration);
                
                // Record migration
                $stmt = $this->conn->prepare(
                    "INSERT INTO migrations (migration, batch) VALUES (?, ?)"
                );
                $stmt->execute([$migration, $batch]);
                
                $this->conn->commit();
                echo "✅ $migration completed\n";
                $executed_count++;
            } catch (Exception $e) {
                $this->conn->rollBack();
                echo "❌ $migration failed: " . $e->getMessage() . "\n";
                exit(1);
            }
        }
        
        echo "\n🎉 Migrations completed: $executed_count new migration(s) executed\n";
    }
    
    private function executeMigration($migration) {
        $method = str_replace(' ', '', ucwords(str_replace('_', ' ', $migration)));
        
        if (method_exists($this, $method)) {
            $this->$method();
        } else {
            // Try to find SQL file
            $sql_file = __DIR__ . "/../sql/migrations/{$migration}.sql";
            if (file_exists($sql_file)) {
                $sql = file_get_contents($sql_file);
                $this->conn->exec($sql);
            } else {
                throw new Exception("Migration method or file not found: $migration");
            }
        }
    }
    
    private function getNextBatchNumber() {
        $stmt = $this->conn->query("SELECT MAX(batch) as max_batch FROM migrations");
        $result = $stmt->fetch();
        return ($result['max_batch'] ?? 0) + 1;
    }
    
    // Migration methods (you can add more as needed)
    private function createUsersTable() {
        $sql = "CREATE TABLE IF NOT EXISTS users (
            id INT PRIMARY KEY AUTO_INCREMENT,
            username VARCHAR(50) UNIQUE NOT NULL,
            email VARCHAR(100) UNIQUE NOT NULL,
            password_hash VARCHAR(255) NOT NULL,
            full_name VARCHAR(100),
            avatar VARCHAR(255),
            role ENUM('admin', 'editor', 'author', 'subscriber') DEFAULT 'subscriber',
            status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
            last_login DATETIME,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            INDEX idx_username (username),
            INDEX idx_email (email),
            INDEX idx_role (role)
        ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4";
        
        $this->conn->exec($sql);
    }
    
    private function insertDefaultData() {
        // Insert default admin user (password: admin123)
        $hashed_password = password_hash('admin123', PASSWORD_DEFAULT);
        
        $sql = "INSERT IGNORE INTO users (username, email, password_hash, full_name, role) 
                VALUES ('admin', 'admin@khabribaba.com', ?, 'Administrator', 'admin')";
        
        $stmt = $this->conn->prepare($sql);
        $stmt->execute([$hashed_password]);
        
        // Insert default categories
        $categories = [
            ['Technology', 'technology', 'Latest tech news and reviews'],
            ['Politics', 'politics', 'Political news and analysis'],
            ['Sports', 'sports', 'Sports news and updates'],
            ['Entertainment', 'entertainment', 'Entertainment and celebrity news'],
            ['Business', 'business', 'Business and finance news']
        ];
        
        foreach ($categories as $category) {
            $sql = "INSERT IGNORE INTO categories (name, slug, description) VALUES (?, ?, ?)";
            $stmt = $this->conn->prepare($sql);
            $stmt->execute($category);
        }
    }
}

// Run migrations
if (php_sapi_name() === 'cli') {
    $migration = new Migration();
    $migration->run();
} else {
    die("This script must be run from the command line.");
}
```

### **Step 6: Push and Test Deployment**
```bash
# In Cursor Terminal:

# Add GitHub Actions workflow
git add .github/workflows/deploy.yml

# Add migration script
git add scripts/migrate.php

# Commit changes
git commit -m "Add CI/CD deployment pipeline"

# Push to GitHub
git push origin main

# Watch deployment:
# 1. Go to GitHub repository
# 2. Click "Actions" tab
# 3. Watch the deployment run
# 4. Check for green checkmark ✅
```

---

# **🌐 PART 5: DEPLOYMENT TO NAMECHEAP**

## **5.1 Namecheap cPanel Setup**

### **Step 1: Create Database in cPanel**
```
1. Login to Namecheap cPanel
2. Find "MySQL Databases" or "MySQL Database Wizard"
3. Create New Database: khabribaba_db (will become username_khabribaba_db)
4. Create User: khabribaba_user (will become username_khabribaba_user)
5. Create Password: Use strong password generator
6. Add User to Database
7. Assign ALL PRIVILEGES
8. Note down: Database name, Username, Password
```

### **Step 2: Update GitHub Secrets**
Update these GitHub secrets with your Namecheap details:

```
DB_HOST: localhost
DB_NAME: username_khabribaba_db
DB_USER: username_khabribaba_user
DB_PASS: your_strong_password
```

### **Step 3: cPanel PHP Configuration**
```
1. In cPanel, search for "PHP Version"
2. Select PHP 8.0 or higher (recommended)
3. Enable extensions: mysqli, pdo_mysql, gd, curl, mbstring, zip
4. Set memory_limit to 256M
5. Set upload_max_filesize to 64M
6. Set max_execution_time to 300
```

### **Step 4: Create .htaccess for cPanel**
**.htaccess in public_html/:**
```apache
# Namecheap cPanel .htaccess

# Enable rewrite engine
RewriteEngine On

# Security headers
Header set X-Content-Type-Options "nosniff"
Header set X-Frame-Options "SAMEORIGIN"
Header set X-XSS-Protection "1; mode=block"
Header set Referrer-Policy "strict-origin-when-cross-origin"

# Force HTTPS (if you have SSL)
# RewriteCond %{HTTPS} off
# RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

# Set PHP settings (if allowed by host)
php_value upload_max_filesize 64M
php_value post_max_size 64M
php_value memory_limit 256M
php_value max_execution_time 300
php_value max_input_time 300

# Prevent directory listing
Options -Indexes

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

<Directory "config">
    Order deny,allow
    Deny from all
</Directory>

# Custom error pages
ErrorDocument 404 /404.php
ErrorDocument 403 /403.php
ErrorDocument 500 /500.php

# URL rewriting (if you want pretty URLs)
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^news/([0-9]+)/?$ single-news.php?id=$1 [L,QSA]
RewriteRule ^category/([a-z0-9-]+)/?$ category.php?slug=$1 [L,QSA]
RewriteRule ^author/([a-z0-9-]+)/?$ author.php?username=$1 [L,QSA]

# Handle front controller
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php?/$1 [L,QSA]
```

## **5.2 Zero-Downtime Deployment Strategy**

### **Create Deployment Script for Zero-Downtime:**
**scripts/deploy-zero-downtime.sh:**
```bash
#!/bin/bash
# Zero-downtime deployment script for Namecheap

set -e  # Exit on error

echo "🚀 Starting zero-downtime deployment..."

# Configuration
SITE_DIR="/home/$(whoami)/public_html"
NEW_DEPLOY_DIR="$SITE_DIR.new"
OLD_DEPLOY_DIR="$SITE_DIR.old"
BACKUP_DIR="/home/$(whoami)/backups/$(date +%Y%m%d_%H%M%S)"

# Create backup
echo "📦 Creating backup..."
mkdir -p "$BACKUP_DIR"
cp -r "$SITE_DIR" "$BACKUP_DIR/"
echo "✅ Backup created: $BACKUP_DIR"

# Create new deployment directory
echo "🛠️ Preparing new deployment..."
rm -rf "$NEW_DEPLOY_DIR"
mkdir -p "$NEW_DEPLOY_DIR"

# Copy files (adjust based on your deployment method)
# This would be replaced by rsync from GitHub Actions
# For manual testing:
# cp -r . "$NEW_DEPLOY_DIR/"

# Set permissions
echo "🔐 Setting permissions..."
find "$NEW_DEPLOY_DIR" -type d -exec chmod 755 {} \;
find "$NEW_DEPLOY_DIR" -type f -exec chmod 644 {} \;
chmod -R 775 "$NEW_DEPLOY_DIR/uploads" 2>/dev/null || true
chmod -R 775 "$NEW_DEPLOY_DIR/cache" 2>/dev/null || true
chmod -R 775 "$NEW_DEPLOY_DIR/sessions" 2>/dev/null || true

# Create maintenance page
echo "🛑 Enabling maintenance mode..."
MAINTENANCE_HTML="$SITE_DIR/maintenance.html"
cat > "$MAINTENANCE_HTML" << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Site Maintenance - KhabriBaba</title>
    <style>
        body { text-align: center; padding: 150px; font-family: Arial, sans-serif; }
        h1 { font-size: 50px; }
        body { font: 20px Helvetica, sans-serif; color: #333; }
        article { display: block; text-align: left; width: 650px; margin: 0 auto; }
        a { color: #dc8100; text-decoration: none; }
        a:hover { color: #333; text-decoration: none; }
    </style>
</head>
<body>
    <article>
        <h1>We&rsquo;ll be back soon!</h1>
        <div>
            <p>Sorry for the inconvenience but we&rsquo;re performing some maintenance at the moment. We&rsquo;ll be back online shortly!</p>
            <p>&mdash; The KhabriBaba Team</p>
        </div>
    </article>
</body>
</html>
EOF

# Switch directories (atomic operation)
echo "🔄 Switching to new version..."
if [ -d "$SITE_DIR" ]; then
    mv "$SITE_DIR" "$OLD_DEPLOY_DIR"
fi
mv "$NEW_DEPLOY_DIR" "$SITE_DIR"

# Disable maintenance mode
echo "✅ Removing maintenance mode..."
rm -f "$SITE_DIR/maintenance.html"

# Cleanup old deployment
echo "🧹 Cleaning up..."
rm -rf "$OLD_DEPLOY_DIR"

# Clear cache
echo "🗑️ Clearing cache..."
rm -rf "$SITE_DIR/cache/*" 2>/dev/null || true
rm -rf "$SITE_DIR/sessions/*" 2>/dev/null || true

echo "🎉 Deployment completed successfully!"
echo "🌐 Site: https://khabribaba.com"
```

### **Update GitHub Actions for Zero-Downtime:**
Add to your **deploy.yml**:
```yaml
    - name: 🚀 Zero-downtime deployment
      run: |
        echo "Performing zero-downtime deployment..."
        
        # Create maintenance page
        MAINT_HTML="<html><body style='text-align:center;padding:50px;'><h1>🔄 Updating KhabriBaba</h1><p>We're deploying new updates. Back in a moment!</p></body></html>"
        
        ssh khabribaba "
          # Enable maintenance mode
          echo '$MAINT_HTML' > ${{ secrets.DEPLOY_PATH }}/maintenance.html
          
          # Deploy to temporary directory
          TEMP_DIR=\"${{ secrets.DEPLOY_PATH }}_new\"
          rm -rf \"\$TEMP_DIR\"
          mkdir -p \"\$TEMP_DIR\"
        "
        
        # Sync files to temp directory
        rsync -avz ./ khabribaba:\$TEMP_DIR/ \
          --exclude=".git" \
          --exclude=".github" \
          --exclude=".gitignore" \
          --exclude="node_modules/" \
          --exclude="vendor/"
        
        ssh khabribaba "
          # Switch directories atomically
          OLD_DIR=\"${{ secrets.DEPLOY_PATH }}_old\"
          rm -rf \"\$OLD_DIR\"
          
          if [ -d \"${{ secrets.DEPLOY_PATH }}\" ]; then
            mv \"${{ secrets.DEPLOY_PATH }}\" \"\$OLD_DIR\"
          fi
          
          mv \"\$TEMP_DIR\" \"${{ secrets.DEPLOY_PATH }}\"
          
          # Remove maintenance page
          rm -f \"${{ secrets.DEPLOY_PATH }}/maintenance.html\"
          
          # Cleanup old directory
          rm -rf \"\$OLD_DIR\"
          
          echo '✅ Zero-downtime deployment complete!'
        "
```

## **5.3 Monitoring Setup**

### **Create Health Check Endpoint:**
**health.php:**
```php
<?php
/**
 * Health check endpoint for monitoring
 */

header('Content-Type: application/json');

$health = [
    'status' => 'healthy',
    'timestamp' => date('Y-m-d H:i:s'),
    'services' => [],
    'metrics' => []
];

// Check database connection
try {
    require_once 'includes/Database.php';
    $db = new Database();
    $conn = $db->connect();
    
    $stmt = $conn->query("SELECT 1 as healthy");
    $result = $stmt->fetch();
    
    $health['services']['database'] = $result['healthy'] == 1 ? 'healthy' : 'unhealthy';
} catch (Exception $e) {
    $health['services']['database'] = 'unhealthy';
    $health['status'] = 'unhealthy';
}

// Check disk space
$disk_free = disk_free_space(__DIR__);
$disk_total = disk_total_space(__DIR__);
$disk_percent = round(($disk_total - $disk_free) / $disk_total * 100, 2);

$health['metrics']['disk_usage_percent'] = $disk_percent;
$health['metrics']['disk_free_gb'] = round($disk_free / 1024 / 1024 / 1024, 2);
$health['metrics']['disk_total_gb'] = round($disk_total / 1024 / 1024 / 1024, 2);

// Check PHP version
$health['metrics']['php_version'] = PHP_VERSION;

// Check memory usage
$health['metrics']['memory_usage_mb'] = round(memory_get_usage(true) / 1024 / 1024, 2);
$health['metrics']['memory_limit_mb'] = round(ini_get('memory_limit') / 1024 / 1024, 2);

// Check uploads directory
$uploads_dir = __DIR__ . '/uploads';
if (is_dir($uploads_dir) && is_writable($uploads_dir)) {
    $health['services']['uploads'] = 'healthy';
} else {
    $health['services']['uploads'] = 'unhealthy';
    $health['status'] = 'unhealthy';
}

// Add response code
if ($health['status'] === 'healthy') {
    http_response_code(200);
} else {
    http_response_code(503);
}

echo json_encode($health, JSON_PRETTY_PRINT);
```

### **Update GitHub Actions Health Check:**
```yaml
    - name: 🏥 Health check
      run: |
        echo "Running comprehensive health check..."
        
        # Wait for site to be ready
        sleep 10
        
        # Check health endpoint
        HEALTH_URL="https://khabribaba.com/health.php"
        
        if curl -s -f --max-time 30 "$HEALTH_URL" > /dev/null; then
            echo "✅ Basic health check passed"
            
            # Get detailed health info
            HEALTH_INFO=$(curl -s "$HEALTH_URL")
            echo "Health status:"
            echo "$HEALTH_INFO" | jq -r '.status'
            
            # Check specific services
            DB_STATUS=$(echo "$HEALTH_INFO" | jq -r '.services.database')
            if [ "$DB_STATUS" = "healthy" ]; then
                echo "✅ Database: $DB_STATUS"
            else
                echo "❌ Database: $DB_STATUS"
                exit 1
            fi
            
            # Check disk space
            DISK_USAGE=$(echo "$HEALTH_INFO" | jq -r '.metrics.disk_usage_percent')
            if (( $(echo "$DISK_USAGE < 90" | bc -l) )); then
                echo "✅ Disk usage: ${DISK_USAGE}%"
            else
                echo "⚠️  High disk usage: ${DISK_USAGE}%"
            fi
            
        else
            echo "❌ Health check failed"
            exit 1
        fi
```

---

# **🎯 PART 6: ADVANCED TOPICS (WHEN YOU NEED THEM)**

## **6.1 When to Add Docker (Later, Not Now)**

### **Signs You Need Docker:**
```
✅ Multiple developers with different setups
✅ Need exact environment matching (dev/staging/prod)
✅ Microservices architecture
✅ Scaling requirements
✅ Learning Docker for career growth
```

### **Simple Docker Start (When Ready):**
```dockerfile
# docker/php/Dockerfile
FROM php:8.2-apache

# Install extensions
RUN docker-php-ext-install pdo_mysql mysqli gd curl mbstring zip

# Copy application
COPY . /var/www/html/

# Set permissions
RUN chown -R www-data:www-data /var/www/html

EXPOSE 80
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:80"
    volumes:
      - .:/var/www/html
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
```

## **6.2 Staging Environment Setup**

### **Create Staging Workflow:**
**.github/workflows/deploy-staging.yml:**
```yaml
name: 🚀 Deploy to Staging

on:
  push:
    branches: [ develop ]

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: 🔑 Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.STAGING_SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
        chmod 600 ~/.ssh/id_ed25519
        
        cat >> ~/.ssh/config << EOF
        Host staging
          HostName ${{ secrets.STAGING_HOST }}
          User ${{ secrets.STAGING_USERNAME }}
          IdentityFile ~/.ssh/id_ed25519
          StrictHostKeyChecking no
        EOF
    
    - name: 📤 Deploy to staging
      run: |
        echo "Deploying to staging environment..."
        rsync -avz ./ staging:${{ secrets.STAGING_DEPLOY_PATH }}/
        
        ssh staging "
          cd ${{ secrets.STAGING_DEPLOY_PATH }}
          # Run staging-specific setup
          cp .env.staging .env
          composer install
          echo '✅ Staging deployment complete!'
        "
```

## **6.3 Automated Testing (When Ready)**

### **Simple Test Example:**
**tests/DatabaseTest.php:**
```php
<?php
use PHPUnit\Framework\TestCase;

class DatabaseTest extends TestCase {
    public function testDatabaseConnection() {
        require_once 'includes/Database.php';
        
        $db = new Database();
        $conn = $db->connect();
        
        $this->assertInstanceOf(PDO::class, $conn);
    }
    
    public function testQueryExecution() {
        require_once 'includes/Database.php';
        
        $db = new Database();
        $conn = $db->connect();
        
        $stmt = $conn->query("SELECT 1 as result");
        $result = $stmt->fetch();
        
        $this->assertEquals(1, $result['result']);
    }
}
```

### **Add Testing to GitHub Actions:**
```yaml
    - name: 🧪 Run tests
      run: |
        if [ -f "phpunit.xml" ]; then
          echo "Running PHPUnit tests..."
          vendor/bin/phpunit --colors=always
        else
          echo "No PHPUnit configuration found, skipping tests."
        fi
```

---

# **🔧 PART 7: TROUBLESHOOTING & MAINTENANCE**

## **7.1 Common Deployment Issues**

### **Issue: Permission Denied**
```bash
# Fix permissions on server
ssh username@khabribaba.com
cd public_html
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
chmod -R 775 uploads cache sessions
chown -R username:www-data .
```

### **Issue: Database Connection Failed**
```php
// Debug database connection
try {
    $pdo = new PDO($dsn, $user, $pass, $options);
    echo "Connected successfully";
} catch (PDOException $e) {
    error_log("DB Error: " . $e->getMessage());
    
    // Check credentials
    echo "Host: " . DB_HOST . "<br>";
    echo "Database: " . DB_NAME . "<br>";
    echo "User: " . DB_USER . "<br>";
    
    if (APP_DEBUG) {
        die("Database error: " . $e->getMessage());
    }
}
```

### **Issue: GitHub Actions Failing**
```
1. Check Actions tab for error details
2. Common fixes:
   - Wrong SSH key format
   - Incorrect DEPLOY_PATH
   - Server out of disk space
   - PHP extension missing
3. Enable debug mode in workflow:
   - name: Debug
     run: ssh khabribaba "php --version && pwd && ls -la"
```

## **7.2 Rollback Procedures**

### **Manual Rollback Script:**
**scripts/rollback.sh:**
```bash
#!/bin/bash
# Manual rollback script

echo "🚨 Initiating rollback..."

# Find latest backup
BACKUP_DIR="/tmp/khabribaba_backup_*"
LATEST_BACKUP=$(ls -td $BACKUP_DIR 2>/dev/null | head -1)

if [ -z "$LATEST_BACKUP" ]; then
    echo "❌ No backups found!"
    exit 1
fi

echo "📦 Restoring from: $LATEST_BACKUP"

# Restore files
cp -r "$LATEST_BACKUP"/* /home/username/public_html/

echo "✅ Rollback complete!"
echo "🔍 Check: https://khabribaba.com"
```

### **Git Rollback:**
```bash
# Revert to previous commit
git log --oneline  # Find commit hash
git revert <commit-hash>
git push origin main
# CI/CD will deploy the revert
```

## **7.3 Performance Optimization**

### **PHP Optimization:**
```php
// Enable OPcache in php.ini
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=2

// Database optimization
// Add indexes to frequently queried columns
// Cache query results
// Use EXPLAIN to analyze slow queries
```

### **Frontend Optimization:**
```bash
# Minify CSS/JS during deployment
npm install -g uglify-js clean-css-cli

# Add to GitHub Actions:
- name: 🎨 Optimize assets
  run: |
    if [ -f "package.json" ]; then
      npm run build  # or specific minify commands
    fi
```

## **7.4 Security Auditing**

### **Monthly Security Checklist:**
```bash
# Check for outdated dependencies
composer outdated
npm outdated

# Check file permissions
find . -type f -perm /o+w -ls  # World-writable files

# Check for PHP vulnerabilities
php -m | grep -E '(mbstring|openssl|filter|hash)'  # Essential extensions

# Backup verification
# Test restoring from backup
```

### **Security Monitoring:**
```php
// Log security events
function logSecurityEvent($event, $details) {
    $log = date('Y-m-d H:i:s') . " | $event | " . json_encode($details) . PHP_EOL;
    file_put_contents('logs/security.log', $log, FILE_APPEND);
}

// Monitor for:
// - Failed login attempts
// - SQL injection attempts
// - XSS attempts
// - File upload attempts
```

---

# **🎯 FINAL RECOMMENDATION & ACTION PLAN**

## **Your Path Forward:**

### **WEEK 1: Foundation**
```
✅ Day 1: Set up local PHP environment (XAMPP/MAMP)
✅ Day 2: Create project structure in Cursor
✅ Day 3: Set up Git and GitHub repository
✅ Day 4: Create basic PHP pages
✅ Day 5: Set up database and .env configuration
```

### **WEEK 2: CI/CD Setup**
```
✅ Day 6: Generate SSH keys and configure server
✅ Day 7: Set up GitHub Actions workflow
✅ Day 8: Test first automated deployment
✅ Day 9: Add database migrations
✅ Day 10: Implement health checks
```

### **WEEK 3: Polish & Optimize**
```
✅ Day 11: Add error handling and logging
✅ Day 12: Implement security features
✅ Day 13: Set up monitoring
✅ Day 14: Create backup strategy
✅ Day 15: Document everything
```

## **What to Use NOW:**

### **Technology Stack:**
```
✅ Editor: Cursor (you already use it)
✅ Local Server: XAMPP/MAMP (simple, works)
✅ Version Control: Git + GitHub (essential)
✅ CI/CD: GitHub Actions + SSH (perfect for Namecheap)
✅ Hosting: Namecheap cPanel (you already have it)
✅ Database: MySQL via phpMyAdmin
```

### **What NOT to Use Now:**
```
❌ Docker (too complex for starting)
❌ Kubernetes (overkill)
❌ AWS/Azure/GCP (expensive, complex)
❌ Complex orchestration (keep it simple)
```

## **Key Success Metrics:**
```
✅ Can deploy with: git add . && git commit -m "..." && git push
✅ Deployment takes < 5 minutes
✅ Zero manual SSH editing for deployments
✅ Can rollback in < 10 minutes
✅ Have automated backups
```

## **When to Consider Advanced Options:**
```
✅ IF: Site traffic grows significantly
✅ IF: Team grows beyond 3 developers  
✅ IF: Need microservices architecture
✅ IF: Learning advanced DevOps for career
✅ IF: Moving to VPS/dedicated server
```

## **Emergency Contacts:**
```
🔧 GitHub Actions Docs: https://docs.github.com/en/actions
🔧 Namecheap Support: https://www.namecheap.com/support/
🔧 PHP Manual: https://www.php.net/manual/
🔧 MySQL Docs: https://dev.mysql.com/doc/
```

---

# **📚 Quick Reference Cheat Sheet**

## **Daily Commands:**
```bash
# Development
php -S localhost:8000                 # PHP built-in server
mysql -u root -p khabribaba           # MySQL console

# Git
git status                            # Check status
git add .                             # Stage changes
git commit -m "message"               # Commit
git push                              # Deploy
git pull                              # Get updates

# Deployment
ssh username@khabribaba.com           # Manual SSH
tail -f /var/log/apache2/error.log    # View logs
```

## **Common Files & Locations:**
```
📁 Project: /path/to/khabribaba/
├── 📄 .env.example                   # Environment template
├── 📄 .env                           # Local environment (ignored in git)
├── 📄 .gitignore                     # Git ignore rules
├── 📁 .github/workflows/            # CI/CD workflows
│   └── 📄 deploy.yml                # Main deployment
├── 📁 includes/                      # PHP classes
├── 📁 uploads/                       # User uploads
├── 📁 logs/                         # Application logs
└── 📁 sql/                          # Database scripts
```

## **Troubleshooting Checklist:**
```
1. ✅ Check GitHub Actions logs
2. ✅ SSH into server manually
3. ✅ Check file permissions
4. ✅ Verify database connection
5. ✅ Check PHP error logs
6. ✅ Verify .env file exists
7. ✅ Check disk space
8. ✅ Test health endpoint
```

---

# **🚀 Your Next Steps (Right Now):**

## **Immediate Action Items:**
```
1. Open Cursor
2. Create your PHP project folder
3. Initialize Git: git init
4. Create GitHub repository
5. Follow Section 4 (CI/CD Setup)
6. Make your first auto-deployment TODAY
```

## **Remember:**
> **"Perfect is the enemy of good. Start simple, deploy today, improve tomorrow."**

Your goal isn't perfect DevOps. Your goal is: **Stop SSH editing, start auto-deploying.**

**You can do this!** Start with the simple SSH-based CI/CD in Section 4. Get it working. Then improve incrementally.

---

**Need help with a specific step?** Share where you're stuck and I'll guide you through it!        
