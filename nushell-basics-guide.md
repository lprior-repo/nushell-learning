# Nushell for the Impatient: Mastery Through Practice

## Why This Guide Works

This is your fast-track to Nushell mastery. No theory dumps, no bash comparisons—just concentrated practice with structured data. You'll learn through **doing**, not reading. Each section builds practical skills with exercises that mirror real-world usage.

**Structure**: Core concepts → Hands-on exercises → One-liners → Programming challenges → Reference sheets

**Goal**: Command-line fluency in structured data manipulation within days, not weeks.

---

## Chapter 1: The Mental Model Shift

### The Big Idea: Everything is Structured Data

Traditional shells work with text streams. Nushell works with **tables, records, and typed values**.

```nu
# Traditional shell thinking (text parsing)
ls -la | grep ".md" | awk '{print $9}'

# Nushell thinking (structured data)
ls | where name =~ "\.md$" | get name
```

### Key Data Types You Must Know

```nu
# Numbers with units (Nushell's superpower)
42              # integer
3.14            # float
1MB             # 1 megabyte
2hr + 30min     # 2.5 hours duration
2025-09-25      # date

# Collections
[1, 2, 3]                    # list
{name: "Alice", age: 30}     # record
[[a, b]; [1, 2] [3, 4]]      # table with headers

# Ranges for iteration
1..5            # [1, 2, 3, 4, 5]
1..<5           # [1, 2, 3, 4]
```

### **Exercise Set 1.1: Type Recognition**

Run these commands and observe the output structure:

```nu
# Exercise 1: Explore different data types
42 | describe
"hello" | describe
[1, 2, 3] | describe
{name: "test"} | describe

# Exercise 2: Work with units
1KB + 500B
2hr - 45min
2025-01-01 + 30day

# Exercise 3: Range operations
1..10 | math sum
1..10 | where $it mod 2 == 0
```

**Practice Goal**: Understand that every command produces typed, structured output.

---

## Chapter 2: Essential Commands - The Core Toolkit

### File System Navigation (The Structured Way)

```nu
# Navigation basics
ls                  # Returns a table, not text
cd ~/projects       # Change directory
pwd                 # Current path as string

# Smart listing with filters
ls | where size > 1MB
ls | where type == dir
ls | where modified > (date now | date to-table | get year | into int) - 1yr
```

### **Exercise Set 2.1: File Operations**

Create a practice environment:

```nu
# Setup exercises
mkdir ~/nu-practice
cd ~/nu-practice
1..5 | each {|i| $"file($i).txt" | save $in}
mkdir folder1 folder2
"large content" | str repeat 1000 | save large.txt
```

Now practice:

```nu
# Exercise 1: List and filter
ls | where size > 100B
ls | where name =~ "file[13]"
ls | select name size | sort-by size

# Exercise 2: Navigate and explore
ls | get type | uniq
ls | group-by type | transpose key items | get key

# Exercise 3: Measure and analyze
ls | get size | math sum
ls | get size | histogram
```

### Working with File Contents

```nu
# Smart file opening (Nushell parses automatically)
open data.json      # Returns structured JSON
open data.csv       # Returns table with headers
open config.yaml    # Returns structured YAML

# Raw content when needed
open file.txt | lines    # Get lines as list
open file.txt | str stats    # Get text statistics
```

### **Exercise Set 2.2: Content Processing**

Create test files:

```nu
# Setup
'{"users": [{"name": "Alice", "age": 25}, {"name": "Bob", "age": 30}]}' | save users.json
"name,age,city\nAlice,25,NYC\nBob,30,LA" | save people.csv
"line1\nline2\nline3\nline4\nline5" | save lines.txt
```

Practice:

```nu
# Exercise 1: JSON manipulation
open users.json | get users | where age > 26
open users.json | get users.0.name

# Exercise 2: CSV operations
open people.csv | where age > 26
open people.csv | select name city

# Exercise 3: Text processing
open lines.txt | lines | length
open lines.txt | lines | where $it =~ "3"
open lines.txt | lines | enumerate | where index > 2
```

---

## Chapter 3: Data Transformation - Where Nushell Shines

### Selection and Filtering

```nu
# Column operations
select name age         # Pick columns
get users.0.name        # Navigate nested data
drop modified           # Remove columns

# Row operations
where age > 25          # Filter rows
first 3                 # Take first n items
last 5                  # Take last n items
skip 2                  # Skip n items
```

### **Exercise Set 3.1: Data Selection**

```nu
# Create rich dataset
let employees = [
    {name: "Alice", dept: "Engineering", salary: 95000, years: 3}
    {name: "Bob", dept: "Marketing", salary: 65000, years: 5}
    {name: "Carol", dept: "Engineering", salary: 105000, years: 7}
    {name: "Dave", dept: "Sales", salary: 75000, years: 2}
    {name: "Eve", dept: "Engineering", salary: 85000, years: 4}
]
```

Practice problems:

```nu
# Exercise 1: Basic selection
$employees | select name salary
$employees | where dept == "Engineering"
$employees | where salary > 80000

# Exercise 2: Advanced filtering
$employees | where dept == "Engineering" and years > 3
$employees | where salary > 70000 | select name dept
$employees | where years < 5 | sort-by salary | reverse

# Exercise 3: Multi-step transformations
$employees | group-by dept | transpose department staff
$employees | where dept == "Engineering" | get salary | math avg
$employees | insert seniority {|emp| if $emp.years > 4 {"Senior"} else {"Junior"}}
```

### Sorting and Grouping

```nu
# Sorting operations
sort-by column_name           # Sort ascending
sort-by column_name --reverse # Sort descending
sort-by [col1, col2]         # Multi-column sort

# Grouping operations
group-by department           # Group by column
group-by department --to-table # Group to table format
```

### **Exercise Set 3.2: Organization Operations**

```nu
# Exercise 1: Complex sorting
$employees | sort-by dept years
$employees | sort-by salary --reverse | first 3
$employees | sort-by [dept, salary]

# Exercise 2: Grouping analysis
$employees | group-by dept | get Engineering | length
$employees | group-by dept | transpose key value | insert avg_salary {|group| $group.value | get salary | math avg}

# Exercise 3: Statistical operations
$employees | get salary | math min
$employees | get salary | math max
$employees | get salary | math stddev
$employees | get years | histogram
```

---

## Chapter 4: One-Liner Mastery

Master these patterns to become dangerously productive.

### File System One-Liners

```nu
# Find large files
ls **/* | where size > 10MB | sort-by size --reverse

# Count files by extension
ls **/* | where type == file | get name | split extension | group-by extension | transpose ext count | sort-by count --reverse

# Find recently modified files
ls **/* | where modified > ((date now) - 1wk) | select name modified | sort-by modified --reverse

# Disk usage by directory
ls | where type == dir | insert total_size {|dir| ls $dir.name **/* | get size | math sum} | sort-by total_size --reverse
```

### **Exercise Set 4.1: File System Challenges**

Create a complex directory structure:

```nu
# Setup
mkdir -p ~/nu-practice/{docs,images,code,temp}
1..10 | each {|i| $"document($i).md" | save ~/nu-practice/docs/$in}
1..5 | each {|i| $"image($i).jpg" | save ~/nu-practice/images/$in}
1..15 | each {|i| $"script($i).nu" | save ~/nu-practice/code/$in}
"x" | str repeat 5000 | save ~/nu-practice/temp/large.tmp
cd ~/nu-practice
```

Solve these one-liner challenges:

```nu
# Challenge 1: Find all .md files and count them
# Solution: ls **/*.md | length

# Challenge 2: List directories sorted by file count
# Solution: ls | where type == dir | insert file_count {|dir| ls $dir.name | length} | sort-by file_count --reverse

# Challenge 3: Find the largest file in each subdirectory
# Solution: ls | where type == dir | each {|dir| {directory: $dir.name, largest: (ls $dir.name | where type == file | sort-by size --reverse | first | get name)}}

# Challenge 4: Get total size by file extension
# Solution: ls **/* | where type == file | group-by {|file| $file.name | split extension | get extension} | transpose ext files | insert total_size {|group| $group.files | get size | math sum}
```

### Data Processing One-Liners

```nu
# JSON API processing
http get https://api.github.com/users/microsoft/repos | where stargazers_count > 1000 | select name stargazers_count | sort-by stargazers_count --reverse | first 5

# CSV analysis
open sales.csv | group-by region | transpose region data | insert total_sales {|row| $row.data | get amount | math sum} | sort-by total_sales --reverse

# Log file analysis
open access.log | lines | parse "{ip} - - [{timestamp}] \"{method} {url} {protocol}\" {status} {size}" | where status >= 400 | group-by status | transpose status count

# System monitoring
ps | where cpu > 10.0 | select name cpu pid | sort-by cpu --reverse
```

### **Exercise Set 4.2: Data Processing Challenges**

Create sample datasets:

```nu
# Create sample data
let sales = [
    {rep: "Alice", region: "North", product: "Widget", amount: 1200, date: "2025-01-15"}
    {rep: "Bob", region: "South", product: "Gadget", amount: 800, date: "2025-01-16"}
    {rep: "Carol", region: "North", product: "Widget", amount: 1500, date: "2025-01-17"}
    {rep: "Dave", region: "East", product: "Tool", amount: 950, date: "2025-01-18"}
    {rep: "Eve", region: "South", product: "Gadget", amount: 1100, date: "2025-01-19"}
]

$sales | to csv | save sales.csv
```

One-liner challenges:

```nu
# Challenge 1: Top performing rep by total sales
# Solution: open sales.csv | group-by rep | transpose rep sales | insert total {|row| $row.sales | get amount | math sum} | sort-by total --reverse | first

# Challenge 2: Average sale amount by region
# Solution: open sales.csv | group-by region | transpose region sales | insert avg_amount {|row| $row.sales | get amount | math avg}

# Challenge 3: Products sold in multiple regions
# Solution: open sales.csv | group-by product | transpose product sales | insert regions {|row| $row.sales | get region | uniq} | where ($it.regions | length) > 1

# Challenge 4: Sales trend by date
# Solution: open sales.csv | group-by date | transpose date sales | insert total {|row| $row.sales | get amount | math sum} | sort-by date
```

---

## Chapter 5: Programming in Nushell

### Variables and Scope

```nu
# Immutable variables (default)
let name = "nushell"
let users = (open users.json | get users)

# Mutable variables
mut counter = 0
$counter = $counter + 1

# Constants (compile-time)
const CONFIG_PATH = "~/.config/app"

# Environment variables
$env.MY_VAR = "value"
$env.PATH = ($env.PATH | prepend "~/.local/bin")
```

### **Exercise Set 5.1: Variable Practice**

```nu
# Exercise 1: Data transformation with variables
let data = [1, 2, 3, 4, 5]
let threshold = 3
$data | where $it > $threshold

# Exercise 2: Mutable state
mut total = 0
[10, 20, 30] | each {|x| $total = $total + $x}
$total

# Exercise 3: Environment setup
$env.CUSTOM_PATH = "~/my-tools"
$env.PATH = ($env.PATH | prepend $env.CUSTOM_PATH)
```

### Control Flow and Logic

```nu
# Conditionals
if $age >= 18 {
    "adult"
} else {
    "minor"
}

# Pattern matching
match $status {
    "active" => "✓ Running"
    "stopped" => "✗ Not running"
    _ => "? Unknown status"
}

# Loops with each
1..10 | each {|x| $x * $x}

# Loops with for
for file in (ls *.txt) {
    print $"Processing ($file.name)"
}
```

### **Exercise Set 5.2: Control Flow**

```nu
# Exercise 1: Grade calculator
let score = 85
let grade = if $score >= 90 {
    "A"
} else if $score >= 80 {
    "B"
} else if $score >= 70 {
    "C"
} else {
    "F"
}

# Exercise 2: File processor
for file in (ls *.md) {
    let line_count = (open $file.name | lines | length)
    print $"($file.name): ($line_count) lines"
}

# Exercise 3: Status checker
let services = ["web", "db", "cache"]
$services | each {|service|
    let status = (random bool)  # Simulate status check
    {
        service: $service
        status: (if $status {"running"} else {"stopped"})
        action: (if $status {"✓"} else {"restart needed"})
    }
}
```

### Custom Commands (Functions)

```nu
# Basic function
def greet [name: string] {
    $"Hello, ($name)!"
}

# Function with optional parameters
def analyze-file [
    file: path          # Required parameter
    --lines (-l)        # Boolean flag
    --format: string = "json"  # Optional with default
] {
    let content = (open $file)
    let result = {
        file: $file
        size: ($file | path stat | get size)
    }

    if $lines {
        $result | insert line_count ($content | lines | length)
    } else {
        $result
    }
}

# Function returning structured data
def system-summary [] {
    {
        cpu_count: (sys cpu | length)
        memory_total: (sys mem | get total)
        memory_used: (sys mem | get used)
        disk_usage: (ls / | where type == dir | get name | each {|dir|
            {path: $dir, size: (du $dir | get apparent | math sum)}
        })
    }
}
```

### **Exercise Set 5.3: Function Building**

```nu
# Exercise 1: File statistics function
def file-stats [path: path] {
    let content = (open $path)
    {
        name: ($path | path basename)
        size: ($path | path stat | get size)
        lines: ($content | lines | length)
        words: ($content | str words | length)
        chars: ($content | str length)
    }
}

# Exercise 2: Repository analyzer
def analyze-repo [path: path = "."] {
    cd $path
    let files = (ls **/* | where type == file)
    let by_ext = ($files | group-by {|f| $f.name | split extension | get extension} |
                 transpose ext files |
                 insert count {|row| $row.files | length})

    {
        total_files: ($files | length)
        total_size: ($files | get size | math sum)
        by_extension: $by_ext
        largest_files: ($files | sort-by size --reverse | first 5 | select name size)
    }
}

# Exercise 3: Data validator
def validate-users [data] {
    $data | each {|user|
        let errors = []
        let errors = if ($user.name | is-empty) {
            $errors | append "Missing name"
        } else { $errors }
        let errors = if ($user.age | is-empty) or ($user.age < 0) {
            $errors | append "Invalid age"
        } else { $errors }

        {
            user: $user
            valid: ($errors | is-empty)
            errors: $errors
        }
    }
}
```

---

## Chapter 6: Real-World Scenarios

### Scenario 1: Log Analysis Pipeline

```nu
# Parse web server logs and generate insights
def analyze-access-logs [log_file: path] {
    open $log_file
    | lines
    | parse "{ip} - - [{timestamp}] \"{method} {url} {version}\" {status} {size}"
    | insert date {|row| $row.timestamp | str substring 0..10 | into datetime}
    | insert hour {|row| $row.timestamp | str substring 12..13 | into int}
}

# Generate hourly traffic report
def traffic-by-hour [logs] {
    $logs
    | group-by hour
    | transpose hour requests
    | insert request_count {|row| $row.requests | length}
    | select hour request_count
    | sort-by hour
}

# Find error patterns
def error-analysis [logs] {
    $logs
    | where ($it.status | into int) >= 400
    | group-by [status, url]
    | transpose key errors
    | insert count {|row| $row.errors | length}
    | sort-by count --reverse
}
```

### **Exercise Set 6.1: Log Analysis**

Create sample log data:

```nu
# Create sample access log
let sample_log = [
    '192.168.1.1 - - [25/Sep/2025:10:00:01 +0000] "GET /home HTTP/1.1" 200 1234'
    '192.168.1.2 - - [25/Sep/2025:10:00:02 +0000] "POST /api/login HTTP/1.1" 401 567'
    '192.168.1.1 - - [25/Sep/2025:11:00:01 +0000] "GET /dashboard HTTP/1.1" 200 2345'
    '192.168.1.3 - - [25/Sep/2025:11:00:30 +0000] "GET /missing HTTP/1.1" 404 123'
    '192.168.1.2 - - [25/Sep/2025:12:00:15 +0000] "POST /api/data HTTP/1.1" 500 89'
]

$sample_log | str join "\n" | save access.log
```

Practice:

```nu
# Challenge 1: Parse and analyze the log
let parsed = (analyze-access-logs access.log)
$parsed | table

# Challenge 2: Generate traffic report
traffic-by-hour $parsed

# Challenge 3: Find error patterns
error-analysis $parsed

# Challenge 4: IP address analysis
$parsed | group-by ip | transpose ip requests | insert request_count {|row| $row.requests | length} | sort-by request_count --reverse
```

### Scenario 2: Configuration Management

```nu
# Manage application configurations across environments
def load-config [env: string] {
    let base_config = (open base-config.json)
    let env_config = (open $"($env)-config.json")

    # Merge configurations
    $base_config | merge $env_config
}

# Validate configuration
def validate-config [config] {
    let required_keys = ["database_url", "api_key", "port"]
    let missing = ($required_keys | where {|key| ($config | get -i $key) == null})

    {
        valid: ($missing | is-empty)
        missing_keys: $missing
        config: $config
    }
}

# Deploy configuration
def deploy-config [env: string, --dry-run] {
    let config = (load-config $env)
    let validation = (validate-config $config)

    if not $validation.valid {
        error make {msg: $"Invalid config: ($validation.missing_keys)"}
    }

    if $dry_run {
        print "Dry run - would deploy:"
        $config | table
    } else {
        $config | to json | save $"/etc/app/($env).json"
        print $"Deployed configuration for ($env)"
    }
}
```

### **Exercise Set 6.2: Configuration Management**

```nu
# Setup configuration files
let base_config = {
    app_name: "MyApp"
    version: "1.0.0"
    debug: false
    timeout: 30
}

let prod_config = {
    database_url: "postgres://prod-server/db"
    api_key: "prod-key-12345"
    port: 8080
    debug: false
}

let dev_config = {
    database_url: "postgres://dev-server/db"
    api_key: "dev-key-67890"
    port: 8081
    debug: true
}

$base_config | to json | save base-config.json
$prod_config | to json | save prod-config.json
$dev_config | to json | save dev-config.json
```

Practice:

```nu
# Challenge 1: Load and merge configurations
load-config "prod"
load-config "dev"

# Challenge 2: Test validation
validate-config (load-config "prod")

# Challenge 3: Test deployment (dry run)
deploy-config "dev" --dry-run
```

### Scenario 3: Data Pipeline Processing

```nu
# ETL pipeline for sales data
def extract-sales-data [sources: list] {
    $sources | each {|source|
        match ($source | path extension) {
            "csv" => (open $source),
            "json" => (open $source | get data),
            _ => (error make {msg: $"Unsupported format: ($source)"})
        }
    } | flatten
}

def transform-sales-data [raw_data] {
    $raw_data
    | insert month {|row| $row.date | into datetime | format date '%Y-%m'}
    | insert revenue_category {|row|
        if $row.amount > 10000 { "high" }
        else if $row.amount > 5000 { "medium" }
        else { "low" }
    }
    | where amount > 0  # Remove invalid records
}

def load-sales-summary [processed_data, output_path: path] {
    let summary = ($processed_data
        | group-by [region, month]
        | transpose key data
        | insert total_sales {|row| $row.data | get amount | math sum}
        | insert avg_sale {|row| $row.data | get amount | math avg}
        | insert transaction_count {|row| $row.data | length})

    $summary | to csv | save $output_path
    print $"Summary saved to ($output_path)"
    $summary
}
```

---

## Chapter 7: Advanced Patterns and Techniques

### Error Handling and Robustness

```nu
# Graceful error handling
def safe-file-operation [file: path] {
    try {
        open $file | lines | length
    } catch {
        print $"Warning: Could not read ($file)"
        0
    }
}

# Validation with custom errors
def validate-email [email: string] {
    if not ($email | str contains "@") {
        error make {
            msg: "Invalid email format"
            label: {
                text: "missing @ symbol"
                span: (metadata $email).span
            }
        }
    }
    $email
}

# Fallback patterns
def get-config-value [key: string, fallback: any] {
    try {
        open config.json | get $key
    } catch {
        $fallback
    }
}
```

### Working with External Commands

```nu
# Safe external command execution
def run-safe [command: string, args: list = []] {
    let result = (do { run-external $command ...$args } | complete)

    if $result.exit_code != 0 {
        error make {msg: $"Command failed: ($result.stderr)"}
    }

    $result.stdout
}

# Docker management example
def docker-stats [] {
    run-safe "docker" ["ps", "--format", "table {{.Names}}\t{{.Status}}\t{{.Ports}}"]
    | lines
    | skip 1
    | parse "{name}\t{status}\t{ports}"
}

# Git repository analysis
def git-summary [] {
    {
        branch: (run-safe "git" ["branch", "--show-current"] | str trim)
        commits: (run-safe "git" ["rev-list", "--count", "HEAD"] | str trim | into int)
        status: (run-safe "git" ["status", "--porcelain"] | lines | length)
        contributors: (run-safe "git" ["shortlog", "-sn"] | lines | length)
    }
}
```

### Performance Optimization

```nu
# Parallel processing with par-each
def process-files-parallel [files: list] {
    $files | par-each {|file|
        {
            file: $file.name
            lines: (open $file.name | lines | length)
            size: $file.size
        }
    }
}

# Streaming large datasets
def process-large-csv [file: path] {
    # Process in chunks instead of loading everything
    open $file
    | skip 1  # Skip header
    | take 1000  # Process first 1000 rows
    | group-by category
    | transpose category data
    | insert count {|row| $row.data | length}
}

# Memory-efficient operations
def analyze-logs-efficiently [log_file: path] {
    # Stream processing instead of loading all data
    open $log_file
    | lines
    | where $it =~ "ERROR"
    | parse "{timestamp} {level} {message}"
    | group-by {|row| $row.timestamp | str substring 0..10}
    | transpose date errors
    | insert error_count {|row| $row.errors | length}
}
```

---

## Chapter 8: Essential Reference Sheets

### Quick Command Reference

#### Core Data Operations
| Command | Purpose | Example |
|---------|---------|---------|
| `select` | Choose columns | `ls \| select name size` |
| `where` | Filter rows | `ps \| where cpu > 10` |
| `get` | Extract values | `ls \| get 0.name` |
| `sort-by` | Sort data | `ls \| sort-by size --reverse` |
| `group-by` | Group data | `ps \| group-by name` |
| `uniq` | Remove duplicates | `ls \| get type \| uniq` |
| `length` | Count items | `ls \| length` |
| `first` | Take first n | `ls \| first 5` |
| `last` | Take last n | `ls \| last 3` |
| `skip` | Skip n items | `ls \| skip 2` |

#### Math and Statistics
| Command | Purpose | Example |
|---------|---------|---------|
| `math sum` | Sum values | `ls \| get size \| math sum` |
| `math avg` | Average | `ps \| get cpu \| math avg` |
| `math min` | Minimum | `ls \| get size \| math min` |
| `math max` | Maximum | `ls \| get size \| math max` |
| `math stddev` | Standard deviation | `data \| get values \| math stddev` |
| `math variance` | Variance | `data \| get values \| math variance` |
| `histogram` | Create histogram | `ls \| get size \| histogram` |

#### String Operations
| Command | Purpose | Example |
|---------|---------|---------|
| `str length` | String length | `"hello" \| str length` |
| `str contains` | Check contains | `"test" \| str contains "es"` |
| `str starts-with` | Check prefix | `"hello" \| str starts-with "he"` |
| `str ends-with` | Check suffix | `"hello" \| str ends-with "lo"` |
| `str replace` | Replace text | `"hello" \| str replace "l" "x"` |
| `str trim` | Remove whitespace | `" text " \| str trim` |
| `str split` | Split string | `"a,b,c" \| str split ","` |
| `str join` | Join strings | `["a", "b"] \| str join ","` |

### Pattern Matching Reference

#### Regular Expressions
```nu
# Basic patterns
where name =~ "\.txt$"        # Ends with .txt
where name =~ "^test"         # Starts with test
where name =~ "\d+"           # Contains digits
where content =~ "error\|warning"  # Contains error or warning

# Case insensitive
where name =~ "(?i)readme"    # Case insensitive match
```

#### Glob Patterns
```nu
ls *.txt                      # All .txt files
ls **/*.rs                    # All .rs files recursively
ls test*.{txt,md}            # test*.txt and test*.md files
ls [abc]*.txt                # Files starting with a, b, or c
```

### Data Type Conversion Reference

#### Into Conversions
```nu
"42" | into int               # String to integer
"3.14" | into float           # String to float
"2025-01-01" | into datetime  # String to datetime
42 | into string              # Number to string
[1,2,3] | into record        # List to record
```

#### Format Conversions
```nu
data | to json                # Convert to JSON
data | to csv                 # Convert to CSV
data | to yaml                # Convert to YAML
data | to toml                # Convert to TOML
data | to xml                 # Convert to XML
```

### Environment and Configuration

#### Essential Environment Variables
```nu
$env.PATH                     # System PATH
$env.HOME                     # Home directory
$env.PWD                      # Current directory
$env.USER                     # Current user
$env.SHELL                    # Current shell
```

#### Configuration Commands
```nu
config nu                     # Edit main config
config env                    # Edit environment config
$nu.home-path                 # Nushell home directory
$nu.config-path              # Config file path
$nu.env-path                 # Environment file path
```

---

## Chapter 9: Challenge Problems

### Beginner Challenges

**Challenge B1: File System Explorer**
Create a command that analyzes a directory and returns:
- Total files and directories
- Largest file
- Most common file extension
- Total disk usage

```nu
def explore-dir [path: path = "."] {
    # Your solution here
}
```

**Challenge B2: CSV Data Cleaner**
Clean a CSV file by:
- Removing rows with missing required fields
- Standardizing date formats
- Converting text to proper case
- Removing duplicate entries

**Challenge B3: System Monitor**
Create a system monitoring dashboard that shows:
- Top 5 processes by CPU usage
- Memory usage percentage
- Disk usage by mount point
- Network interface status

### Intermediate Challenges

**Challenge I1: Log Analyzer Pro**
Build a comprehensive log analysis tool that:
- Detects log format automatically
- Generates hourly/daily traffic reports
- Identifies error patterns and trends
- Creates performance metrics
- Outputs results in multiple formats

**Challenge I2: Configuration Manager**
Create a multi-environment configuration system that:
- Supports environment inheritance
- Validates configuration schemas
- Manages secrets securely
- Tracks configuration changes
- Supports rollback functionality

**Challenge I3: Data Pipeline Builder**
Build a flexible ETL pipeline that:
- Supports multiple input formats
- Performs data validation and cleaning
- Applies business rules and transformations
- Handles errors gracefully
- Outputs to various destinations

### Advanced Challenges

**Challenge A1: Distributed System Monitor**
Monitor multiple servers and applications:
- Collect metrics from remote systems
- Aggregate data across nodes
- Detect anomalies and alerts
- Generate executive dashboards
- Store historical data for analysis

**Challenge A2: Code Quality Analyzer**
Analyze codebases for quality metrics:
- Count lines of code by language
- Identify code complexity patterns
- Track test coverage statistics
- Find potential security issues
- Generate quality reports

**Challenge A3: Infrastructure as Code Manager**
Manage cloud infrastructure configurations:
- Parse and validate Terraform/CloudFormation
- Track resource dependencies
- Calculate cost estimates
- Plan deployment sequences
- Monitor drift detection

---

## Chapter 10: Solutions and Best Practices

### Challenge Solutions

#### Beginner Challenge B1 Solution
```nu
def explore-dir [path: path = "."] {
    let items = (ls $path -a | where name != "." and name != "..")
    let files = ($items | where type == file)
    let dirs = ($items | where type == dir)

    {
        summary: {
            total_files: ($files | length)
            total_directories: ($dirs | length)
            total_items: ($items | length)
        }
        largest_file: ($files | sort-by size --reverse | first | select name size)
        most_common_extension: (
            $files
            | get name
            | each {|name| $name | split extension | get extension}
            | group-by $it
            | transpose extension count
            | sort-by count --reverse
            | first
        )
        total_size: ($items | get size | math sum)
        breakdown_by_type: ($items | group-by type | transpose type items | insert count {|row| $row.items | length})
    }
}
```

#### CSV Data Cleaner Solution
```nu
def clean-csv [
    input_file: path
    output_file: path
    --required-fields: list = []
] {
    let raw_data = (open $input_file)

    # Remove rows with missing required fields
    let filtered_data = if ($required_fields | is-empty) {
        $raw_data
    } else {
        $raw_data | where {|row|
            $required_fields | all {|field|
                ($row | get -i $field) != null and ($row | get -i $field) != ""
            }
        }
    }

    # Standardize and clean data
    let cleaned_data = $filtered_data
        | each {|row|
            $row | items {|key, value|
                let cleaned_value = match ($value | describe) {
                    "string" => ($value | str trim | str title),
                    _ => $value
                }
                {key: $key, value: $cleaned_value}
            } | transpose -rd
        }
        | uniq

    $cleaned_data | to csv | save $output_file
    {
        original_rows: ($raw_data | length)
        cleaned_rows: ($cleaned_data | length)
        removed_rows: (($raw_data | length) - ($cleaned_data | length))
        output_file: $output_file
    }
}
```

### Best Practices Summary

#### 1. Data-First Thinking
- Always consider the structure of your data
- Use `describe` to understand data types
- Design commands to return structured data
- Think in tables and records, not text

#### 2. Function Design
- Keep functions focused on one task
- Use type annotations for parameters
- Return structured data consistently
- Handle errors gracefully with try/catch

#### 3. Performance Guidelines
- Use `par-each` for CPU-intensive operations
- Stream large datasets instead of loading all data
- Avoid nested loops when possible
- Use `complete` for external command error handling

#### 4. Code Organization
- Group related functions into modules
- Use consistent naming conventions
- Document complex functions with examples
- Keep configuration in environment variables

#### 5. Error Handling
- Validate inputs early
- Provide meaningful error messages
- Use fallback values for non-critical failures
- Log errors for debugging

### Common Patterns to Master

#### The Transform-Filter-Aggregate Pattern
```nu
# Template for most data processing tasks
data
| transform_step                    # Clean and enrich
| where filter_condition           # Filter relevant items
| group-by category_field          # Organize data
| transpose category items         # Prepare for aggregation
| insert summary_field {|row|      # Calculate summaries
    $row.items | get value_field | math sum
}
```

#### The Safe External Command Pattern
```nu
def safe-external [command: string, args: list = []] {
    let result = (do { ^$command ...$args } | complete)
    if $result.exit_code != 0 {
        error make {msg: $"($command) failed: ($result.stderr)"}
    }
    $result.stdout
}
```

#### The Configuration Loading Pattern
```nu
def load-config [name: string, --default: record = {}] {
    let config_file = $"~/.config/app/($name).json"
    if ($config_file | path exists) {
        open $config_file
    } else {
        $default
    }
}
```

---

## Conclusion: Your Nushell Journey

You now have the foundation for Nushell mastery. The key insights to remember:

1. **Everything is Data**: Think in tables, records, and structured types
2. **Composition Over Complexity**: Build complex operations from simple, composable commands
3. **Types Matter**: Leverage Nushell's type system for robust scripts
4. **Practice Deliberately**: Work through the exercises and challenges regularly
5. **Community Engagement**: Join the Nushell Discord and contribute back

### Next Steps
- Practice the one-liners daily until they become second nature
- Build your own custom commands for repetitive tasks
- Explore the standard library for advanced utilities
- Contribute to the Nushell ecosystem with modules and scripts

### Resources
- Official Documentation: https://www.nushell.sh/book/
- Discord Community: https://discord.gg/NtAbbGn
- GitHub Repository: https://github.com/nushell/nushell
- Cookbook: https://www.nushell.sh/cookbook/

Master these patterns, complete the challenges, and you'll find yourself thinking in structured data—making you incredibly productive with Nushell and changing how you approach command-line work forever.

**Total Pages: ~30** | **Focus: Practical Mastery** | **Approach: Learning by Doing**