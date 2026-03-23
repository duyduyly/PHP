# Composer

## Check 
- If `composer --version`
    - (work) have version → Composer OK
    - ❌ command not found → not installed or not in PATH
- If `which composer`
    - (work) shows path → Composer location OK
    - ❌ no output → not in PATH

# 1. Composer Command Cheat Sheet

Quick summary of essential Composer checks:

* [Check Version](#1-check-version): Verify Composer is installed and working
* [Locate Composer](#2-locate-composer): Find where Composer binary is located
* [Diagnose Environment](#3-diagnose-environment): Validate system requirements
* [List Installed Packages](#4-list-installed-packages): Show all dependencies
* [Check Specific Package](#5-check-specific-package): Verify a package installation
* [Validate composer.json](#6-validate-composerjson): Check syntax and structure
* [Dry Run Install](#7-dry-run-install): Simulate dependency installation
* [Verbose Debug Install](#8-verbose-debug-install): Deep debug for issues
* [Inspect composer.json](#9-inspect-composerjson): Review project requirements

---

## 1. Check Version

```bash
composer --version
```

* Confirms Composer is installed
* Shows current version
* Useful first check before debugging anything

---

## 2. Locate Composer

```bash
which composer
```

* Returns path of Composer binary
* Helps detect path issues or multiple installations

---

## 3. Diagnose Environment

```bash
composer diagnose
```

* Checks PHP version compatibility
* Verifies required extensions
* Tests network and config
* Detects common setup problems

---

## 4. List Installed Packages

```bash
composer show
```

* Lists all installed dependencies
* Shows versions and metadata
* Useful to verify installation success

---

## 5. Check Specific Package

```bash
composer show yiisoft/yii2
```

* Displays info for a specific package
* Confirms whether it is installed
* Shows version and dependencies

---

## 6. Validate composer.json

```bash
composer validate
```

* Checks JSON syntax
* Validates structure against Composer rules
* Prevents install errors early

---

## 7. Dry Run Install

```bash
composer install --dry-run
```

* Simulates installation without changes
* Detects missing dependencies
* Safe way to preview actions

---

## 8. Verbose Debug Install

```bash
composer install -vvv
```

* Provides detailed debug output
* Shows exact failure reasons
* Essential for troubleshooting

---

## 9. Inspect composer.json

```bash
cat composer.json
```

* Displays project dependencies
* Helps understand required packages
* Useful during debugging

---

# 2. Execution Flow

Typical workflow when working with Composer:

1. Check Composer availability

```bash
composer --version
```

2. Validate project configuration

```bash
composer validate
```

3. Install dependencies

```bash
composer install
```

4. Verify installed packages

```bash
composer show
```

5. Debug if something fails

```bash
composer install -vvv
```

---

# 3. Complete Example

### Step 1: Enter container

```bash
docker exec -it php sh
```

### Step 2: Check Composer

```bash
composer --version
```

### Step 3: Validate config

```bash
composer validate
```

### Step 4: Install dependencies

```bash
composer install
```

### Step 5: Verify installation

```bash
composer show
```

### Step 6: Debug if needed

```bash
composer install -vvv
```

---

# 🎯 Key Takeaway

* Always verify Composer first
* Validate before installing
* Use `-vvv` for real debugging
* Avoid guessing — rely on logs
