# Nushell for the Impatient
*A Practical Guide to Modern Shell Programming*

**Version 1.0**
**2024**

---

## Table of Contents

**Part I: Essential CLI Commands (Pages 1-15)**
1. [Quick Start: Your First 5 Minutes](#quick-start)
2. [File Operations That Matter](#file-operations)
3. [Data Wrangling Essentials](#data-wrangling)
4. [Text Processing Power Tools](#text-processing)
5. [System Information Commands](#system-info)

**Part II: Programming Fundamentals (Pages 16-25)**
6. [Variables and Data Types](#variables-types)
7. [Control Flow: If, Match, Loops](#control-flow)
8. [Functions and Custom Commands](#functions-commands)
9. [Error Handling the Nushell Way](#error-handling)
10. [Working with External Commands](#external-commands)

**Part III: Advanced Programming (Pages 26-35)**
11. [Modules and Code Organization](#modules)
12. [Advanced Data Structures](#advanced-data)
13. [Configuration and Customization](#configuration)
14. [Performance and Optimization](#performance)
15. [Testing and Debugging](#testing-debugging)

**Part IV: Extensions and Fun Projects (Pages 36-40)**
16. [Building Useful Scripts](#useful-scripts)
17. [Integration with Other Tools](#integrations)
18. [Real-World Projects](#real-world-projects)
19. [Community Resources](#community)

---

# Part I: Essential CLI Commands

## Quick Start: Your First 5 Minutes {#quick-start}

### What Makes Nushell Different

Nushell isn't just another shell - it's a structured data powerhouse. Instead of text streams, you work with tables, records, and lists. Think of it as Excel meets PowerShell meets modern programming.

```nu
# Traditional shell: text pipes
ls | grep ".txt" | wc -l

# Nushell: structured data pipes
ls | where name =~ ".txt" | length
```

### Installation

```bash
# macOS
brew install nushell

# Windows
winget install nushell

# Linux (most distros)
cargo install nu

# Or download from https://github.com/nushell/nushell/releases
```

### Your First Commands

```nu
# Start nushell
nu

# Get help (your best friend)
help

# List commands by category
help commands

# Get help for specific command
help ls
```

### The Power of Structured Data

```nu
# See processes as a table
ps

# Filter and sort
ps | where cpu > 10 | sort-by cpu

# Get specific columns
ps | select name cpu mem | first 5
```

## File Operations That Matter {#file-operations}

### Essential File Commands

```nu
# List files (returns a table!)
ls

# List with details
ls -l

# Show hidden files
ls -a

# Recursive listing
ls **/*

# Filter by extension
ls | where name =~ "\.rs$"

# Size information
ls | select name size | sort-by size
```

### Directory Navigation

```nu
# Change directory
cd /path/to/directory

# Go home
cd ~

# Previous directory
cd -

# Show current directory
pwd

# Create directory
mkdir new-folder

# Create nested directories
mkdir -p deep/nested/folders
```

### File Operations

```nu
# Copy files
cp source.txt destination.txt

# Copy directories
cp -r source-dir/ dest-dir/

# Move/rename
mv old-name.txt new-name.txt

# Remove files
rm file.txt

# Remove directories (be careful!)
rm -rf directory/

# Touch files
touch new-file.txt
```

### Working with File Contents

```nu
# View file content
open file.txt

# View as lines
open file.txt | lines

# View first/last lines
open file.txt | lines | first 5
open file.txt | lines | last 5

# Search in files
open file.txt | str contains "search-term"

# Count lines
open file.txt | lines | length
```

## Data Wrangling Essentials {#data-wrangling}

### Understanding Nushell Data Types

```nu
# Numbers
42
3.14

# Strings
"hello world"
'single quotes work too'

# Lists
[1, 2, 3, 4, 5]
["apple", "banana", "cherry"]

# Records (like objects/maps)
{name: "John", age: 30, city: "New York"}

# Tables (list of records)
[
  {name: "Alice", score: 95},
  {name: "Bob", score: 87}
]
```

### Core Data Commands

```nu
# Length of collections
[1, 2, 3] | length
"hello" | str length

# First and last elements
[1, 2, 3, 4, 5] | first 2
[1, 2, 3, 4, 5] | last 2

# Skip elements
[1, 2, 3, 4, 5] | skip 2

# Take elements
[1, 2, 3, 4, 5] | take 3

# Reverse
[1, 2, 3] | reverse

# Sort
[3, 1, 4, 1, 5] | sort
```

### Filtering and Selecting

```nu
# Where clause (filtering)
ls | where size > 1kb
[1, 2, 3, 4, 5] | where $it > 3

# Select columns
ls | select name size

# Reject columns (opposite of select)
ls | reject modified

# Get specific values
{name: "John", age: 30} | get name
```

### Transforming Data

```nu
# Each (map over elements)
[1, 2, 3] | each { |it| $it * 2 }

# Update specific fields
ls | update size { |row| $row.size / 1024 }

# Insert new columns
ls | insert size_kb { |row| $row.size / 1024 }

# Group by
ls | group-by type

# Reduce (fold)
[1, 2, 3, 4, 5] | reduce { |it, acc| $acc + $it }
```

## Text Processing Power Tools {#text-processing}

### String Operations

```nu
# String length
"hello world" | str length

# Case conversion
"Hello World" | str downcase
"hello world" | str upcase
"hello world" | str title-case

# Trimming
"  hello world  " | str trim

# Splitting strings
"apple,banana,cherry" | split row ","
"hello world" | split chars

# Joining
["apple", "banana", "cherry"] | str join ", "
```

### Pattern Matching

```nu
# Contains
"hello world" | str contains "world"

# Starts with / ends with
"hello world" | str starts-with "hello"
"hello world" | str ends-with "world"

# Replace
"hello world" | str replace "world" "nushell"

# Replace all
"hello world world" | str replace -a "world" "nu"

# Regular expressions
"hello123world456" | str contains -r "\d+"
"hello123world456" | str replace -a -r "\d+" "X"
```

### Working with Lines

```nu
# Split into lines
"line1\nline2\nline3" | lines

# Process each line
open file.txt | lines | each { |line| $line | str trim }

# Filter lines
open file.txt | lines | where $it =~ "pattern"

# Number lines
open file.txt | lines | enumerate
```

## System Information Commands {#system-info}

### Process Management

```nu
# List processes
ps

# Find specific process
ps | where name =~ "chrome"

# Sort by CPU usage
ps | sort-by cpu

# Kill process (be careful!)
# ps | where name =~ "process-name" | get pid | each { |pid| kill $pid }
```

### System Information

```nu
# System info
sys

# CPU info
sys | get cpu

# Memory info
sys | get mem

# Disk usage
sys | get disks

# Environment variables
$env

# Specific environment variable
$env.HOME
$env.PATH
```

### Network Information

```nu
# Network interfaces (if available)
sys | get net

# Check if command exists
which git
which python

# Command history
history

# Current date/time
date now

# Format date
date now | format date "%Y-%m-%d %H:%M:%S"
```

---

# Part II: Programming Fundamentals

## Variables and Data Types {#variables-types}

### Variable Declaration

```nu
# Simple variables
let name = "John"
let age = 30
let height = 5.9

# Using variables
echo $name
echo $"Hello, ($name)!"
echo $"Age: ($age), Height: ($height)"
```

### Mutable Variables

```nu
# Mutable variables (use sparingly)
mut count = 0
$count = $count + 1
echo $count
```

### Environment Variables

```nu
# Set environment variable
$env.MY_VAR = "some value"

# Use environment variable
echo $env.MY_VAR

# Load from .env file
load-env { DATABASE_URL: "postgres://localhost" }
```

### Data Type Deep Dive

```nu
# Inspect types
42 | describe
"hello" | describe
[1, 2, 3] | describe
{name: "John"} | describe

# Type conversion
"42" | into int
42 | into string
"true" | into bool
```

### Complex Data Structures

```nu
# Nested records
let person = {
  name: "Alice",
  contact: {
    email: "alice@example.com",
    phone: "555-0123"
  },
  skills: ["rust", "python", "sql"]
}

# Access nested data
$person.name
$person.contact.email
$person.skills.0

# Working with nested structures
$person | get contact.email
$person | get skills | where $it == "rust"
```

### Lists and Tables

```nu
# Lists
let fruits = ["apple", "banana", "cherry"]
let numbers = [1, 2, 3, 4, 5]

# Tables (structured data)
let employees = [
  {name: "Alice", dept: "Engineering", salary: 75000},
  {name: "Bob", dept: "Marketing", salary: 65000},
  {name: "Carol", dept: "Engineering", salary: 80000}
]

# Query like a database
$employees | where dept == "Engineering"
$employees | where salary > 70000
$employees | group-by dept
```

## Control Flow: If, Match, Loops {#control-flow}

### Conditional Statements

```nu
# Basic if statement
let age = 20
if $age >= 18 {
  echo "Adult"
} else {
  echo "Minor"
}

# If with multiple conditions
let score = 85
if $score >= 90 {
  echo "A"
} else if $score >= 80 {
  echo "B"
} else if $score >= 70 {
  echo "C"
} else {
  echo "F"
}
```

### Pattern Matching

```nu
# Match statement (like switch)
let fruit = "apple"
match $fruit {
  "apple" => "red fruit",
  "banana" => "yellow fruit",
  "grape" => "purple fruit",
  _ => "unknown fruit"
}

# Match with conditions
let number = 42
match $number {
  x if $x < 0 => "negative",
  x if $x == 0 => "zero",
  x if $x > 100 => "big",
  _ => "normal"
}
```

### Loops and Iteration

```nu
# For loop over range
for i in 1..5 {
  echo $"Number: ($i)"
}

# For loop over list
let items = ["a", "b", "c"]
for item in $items {
  echo $"Item: ($item)"
}

# While loop
mut counter = 0
while $counter < 5 {
  echo $"Counter: ($counter)"
  $counter = $counter + 1
}

# Loop with break and continue
for i in 1..10 {
  if $i == 3 { continue }
  if $i == 8 { break }
  echo $i
}
```

### Advanced Iteration

```nu
# Each with index
["a", "b", "c"] | enumerate | each { |item|
  echo $"($item.index): ($item.item)"
}

# Parallel processing
1..5 | par-each { |n|
  sleep 1sec
  $n * $n
}

# Collect results
let squares = 1..5 | each { |n| $n * $n } | collect
```

## Functions and Custom Commands {#functions-commands}

### Basic Custom Commands

```nu
# Simple command
def greet [name: string] {
  echo $"Hello, ($name)!"
}

# Call the command
greet "World"

# Command with multiple parameters
def add [x: int, y: int] {
  $x + $y
}

add 5 3
```

### Parameter Types and Validation

```nu
# Optional parameters
def greet-optional [name?: string] {
  let actual_name = $name | default "Anonymous"
  echo $"Hello, ($actual_name)!"
}

# Default values
def greet-default [name: string = "World"] {
  echo $"Hello, ($name)!"
}

# Rest parameters
def sum [...numbers: int] {
  $numbers | math sum
}

sum 1 2 3 4 5
```

### Advanced Command Features

```nu
# Flags/switches
def list-files [
  --all (-a)        # Show all files
  --long (-l)       # Long format
  path?: string     # Optional path
] {
  let target = $path | default "."

  if $all and $long {
    ls -al $target
  } else if $all {
    ls -a $target
  } else if $long {
    ls -l $target
  } else {
    ls $target
  }
}

# Usage: list-files --all --long /home
```

### Return Values and Error Handling

```nu
# Command that returns a value
def calculate-bmi [weight: float, height: float] {
  let bmi = $weight / ($height * $height)

  if $bmi < 18.5 {
    {bmi: $bmi, category: "underweight"}
  } else if $bmi < 25.0 {
    {bmi: $bmi, category: "normal"}
  } else if $bmi < 30.0 {
    {bmi: $bmi, category: "overweight"}
  } else {
    {bmi: $bmi, category: "obese"}
  }
}

let result = calculate-bmi 70.0 1.75
echo $"BMI: ($result.bmi), Category: ($result.category)"
```

### Closures and Higher-Order Functions

```nu
# Closure (anonymous function)
let double = { |x| $x * 2 }
5 | do $double

# Higher-order function
def apply-twice [func: closure] {
  { |x| $x | do $func | do $func }
}

let quadruple = apply-twice $double
5 | do $quadruple  # Returns 20
```

## Error Handling the Nushell Way {#error-handling}

### Try-Catch Blocks

```nu
# Basic try-catch
try {
  let result = 10 / 0
  echo $"Result: ($result)"
} catch { |error|
  echo $"Error occurred: ($error.msg)"
}

# Try with specific error handling
try {
  open nonexistent-file.txt
} catch { |error|
  echo $"Could not open file: ($error.msg)"
  echo "Creating default content..."
  "Default content" | save default.txt
}
```

### Error Propagation

```nu
# Function that can fail
def safe-divide [a: float, b: float] {
  if $b == 0 {
    error make {
      msg: "Division by zero",
      label: {
        text: "Cannot divide by zero",
        span: (metadata $b).span
      }
    }
  } else {
    $a / $b
  }
}

# Using the function
try {
  safe-divide 10 0
} catch { |error|
  echo $"Math error: ($error.msg)"
}
```

### Validation and Assertions

```nu
# Input validation
def process-age [age: int] {
  if $age < 0 {
    error make {msg: "Age cannot be negative"}
  }
  if $age > 150 {
    error make {msg: "Age seems unrealistic"}
  }

  if $age >= 18 {
    "adult"
  } else {
    "minor"
  }
}

# Assertions
def factorial [n: int] {
  if $n < 0 {
    error make {msg: "Factorial is not defined for negative numbers"}
  }

  if $n <= 1 {
    1
  } else {
    $n * (factorial ($n - 1))
  }
}
```

### Graceful Degradation

```nu
# Function with fallback behavior
def get-user-info [username: string] {
  try {
    # Try to get from primary source
    http get $"https://api.github.com/users/($username)"
  } catch {
    # Fallback to local cache
    try {
      open $"~/.cache/users/($username).json"
    } catch {
      # Final fallback
      {
        login: $username,
        name: "Unknown User",
        source: "fallback"
      }
    }
  }
}
```

## Working with External Commands {#external-commands}

### Running External Commands

```nu
# Basic command execution
git status
ls -la
python --version

# Capture output
let git_status = git status | complete
echo $git_status.stdout

# Handle command failure
let result = git push | complete
if $result.exit_code != 0 {
  echo $"Git push failed: ($result.stderr)"
}
```

### Command Pipelines

```nu
# Mixed Nu and external commands
git log --oneline | lines | first 5

# Complex pipeline
ps | where cpu > 5 | get pid | each { |pid| kill $pid }

# Working with JSON from external commands
curl -s "https://api.github.com/repos/nushell/nushell" | from json | get stargazers_count
```

### Environment and Context

```nu
# Run command in specific directory
cd /tmp; git init; cd -

# With environment variables
with-env {RUST_LOG: "debug"} {
  cargo test
}

# Conditional external commands
def deploy [environment: string] {
  match $environment {
    "prod" => {
      echo "Deploying to production..."
      kubectl apply -f prod-config.yaml
    },
    "staging" => {
      echo "Deploying to staging..."
      kubectl apply -f staging-config.yaml
    },
    _ => {
      error make {msg: "Unknown environment"}
    }
  }
}
```

---

# Part III: Advanced Programming

## Modules and Code Organization {#modules}

### Creating Modules

```nu
# math_utils.nu
export def add [x: int, y: int] -> int {
  $x + $y
}

export def multiply [x: int, y: int] -> int {
  $x * $y
}

export def factorial [n: int] -> int {
  if $n <= 1 {
    1
  } else {
    $n * (factorial ($n - 1))
  }
}

# Private function (not exported)
def internal-helper [] {
  "This is internal"
}
```

### Using Modules

```nu
# Import entire module
use math_utils.nu

# Use functions
math_utils add 5 3
math_utils factorial 5

# Import specific functions
use math_utils.nu [add, multiply]
add 5 3
multiply 4 6

# Import with aliases
use math_utils.nu [add as plus, multiply as times]
plus 5 3
times 4 6
```

### Module Structure

```nu
# Directory structure:
# my_project/
#   ├── main.nu
#   ├── utils/
#   │   ├── mod.nu
#   │   ├── string_utils.nu
#   │   └── file_utils.nu
#   └── config/
#       └── settings.nu

# utils/mod.nu
export use string_utils.nu *
export use file_utils.nu *

# main.nu
use utils

utils str-reverse "hello"
```

### Advanced Module Features

```nu
# Conditional exports
export def platform-specific [] {
  match $nu.os-info.name {
    "linux" => { echo "Linux specific code" },
    "windows" => { echo "Windows specific code" },
    "macos" => { echo "macOS specific code" },
    _ => { echo "Unknown platform" }
  }
}

# Module with constants
export const VERSION = "1.0.0"
export const DEFAULT_CONFIG = {
  host: "localhost",
  port: 8080,
  debug: false
}

# Module initialization
export-env {
  let config_path = $env.HOME | path join ".myapp" "config.toml"
  if not ($config_path | path exists) {
    mkdir ($config_path | path dirname)
    $DEFAULT_CONFIG | to toml | save $config_path
  }
}
```

## Advanced Data Structures {#advanced-data}

### Working with Complex JSON

```nu
# Parse complex JSON
let api_response = '{
  "users": [
    {"id": 1, "name": "Alice", "roles": ["admin", "user"]},
    {"id": 2, "name": "Bob", "roles": ["user"]}
  ],
  "metadata": {
    "total": 2,
    "page": 1
  }
}' | from json

# Navigate nested structures
$api_response | get users
$api_response | get users | where name == "Alice"
$api_response | get users | get roles | flatten

# Transform nested data
$api_response
| get users
| each { |user|
    {
      id: $user.id,
      name: $user.name,
      is_admin: ("admin" in $user.roles)
    }
  }
```

### Data Transformation Pipelines

```nu
# Complex data processing
let sales_data = [
  {date: "2024-01-01", product: "A", amount: 100, region: "US"},
  {date: "2024-01-01", product: "B", amount: 150, region: "EU"},
  {date: "2024-01-02", product: "A", amount: 120, region: "US"},
  {date: "2024-01-02", product: "B", amount: 180, region: "EU"}
]

# Aggregate data
$sales_data
| group-by region
| transpose region data
| each { |region_data|
    {
      region: $region_data.region,
      total_sales: ($region_data.data | get amount | math sum),
      avg_sale: ($region_data.data | get amount | math avg),
      product_count: ($region_data.data | get product | uniq | length)
    }
  }

# Time series analysis
$sales_data
| group-by date
| transpose date sales
| each { |day|
    {
      date: $day.date,
      daily_total: ($day.sales | get amount | math sum),
      transactions: ($day.sales | length)
    }
  }
| sort-by date
```

### Custom Data Structures

```nu
# Tree structure
def create-tree [value: any] {
  {
    value: $value,
    children: []
  }
}

def add-child [tree: record, child: any] {
  $tree | update children { |node| $node.children | append (create-tree $child) }
}

def tree-map [tree: record, func: closure] {
  let new_value = $tree.value | do $func
  let new_children = $tree.children | each { |child| tree-map $child $func }

  {
    value: $new_value,
    children: $new_children
  }
}

# Usage
let tree = create-tree "root"
let tree = add-child $tree "child1"
let tree = add-child $tree "child2"
let doubled_tree = tree-map $tree { |x| $x ++ "_doubled" }
```

### Working with Databases

```nu
# SQLite operations (if nu-sqlite plugin is available)
def query-users [database: string, min_age?: int] {
  let age_filter = if ($min_age | is-empty) {
    ""
  } else {
    $" WHERE age >= ($min_age)"
  }

  open $database | query db $"SELECT * FROM users($age_filter)"
}

# CSV database simulation
def csv-query [file: string, filter_func?: closure] {
  let data = open $file | from csv

  if ($filter_func | is-empty) {
    $data
  } else {
    $data | where { |row| $row | do $filter_func }
  }
}

# Usage
csv-query "employees.csv" { |emp| $emp.salary > 50000 }
```

## Configuration and Customization {#configuration}

### Nushell Configuration

```nu
# config.nu - Main configuration file

# Environment variables
$env.EDITOR = "code"
$env.BROWSER = "firefox"

# Custom prompt
$env.PROMPT_COMMAND = {||
  let home = $nu.home-path
  let dir = ([
    ($env.PWD | str substring 0..($home | str length) | str replace $home "~"),
    ($env.PWD | str substring ($home | str length)..)
  ] | str join "")

  $"(ansi green)($dir)(ansi reset)> "
}

# Aliases
alias ll = ls -l
alias la = ls -a
alias grep = rg
alias cat = bat

# Custom commands for daily use
def weather [city?: string] {
  let location = $city | default "London"
  http get $"http://wttr.in/($location)?format=3"
}

def myip [] {
  http get "http://httpbin.org/ip" | get origin
}
```

### Environment Management

```nu
# env.nu - Environment configuration

# Path management
$env.PATH = ($env.PATH | split row (char esep) | append [
  "/usr/local/bin",
  ($env.HOME | path join ".cargo" "bin"),
  ($env.HOME | path join "bin")
] | uniq)

# Development environments
$env.RUST_BACKTRACE = 1
$env.NODE_ENV = "development"

# Database connections
$env.DATABASE_URL = "sqlite:./app.db"

# API keys (load from secure files)
try {
  load-env (open ~/.config/nushell/secrets.env)
} catch {
  echo "No secrets file found, using defaults"
}
```

### Custom Themes and Styling

```nu
# Theme configuration
$env.LS_COLORS = (vivid generate dracula | str trim)

# Color scheme
$env.config = {
  color_config: {
    separator: white
    leading_trailing_space_bg: { attr: n }
    header: green_bold
    empty: blue
    bool: {
      true: light_cyan
      false: light_gray
    }
    int: white
    float: white
    range: white
    string: white
    record: white
    list: white
    block: white
    hints: dark_gray
  }
}

# Table formatting
$env.config.table = {
  mode: rounded
  index_mode: always
  show_empty: true
  trim: {
    methodology: wrapping
    wrapping_try_keep_words: true
    truncating_suffix: "..."
  }
}
```

### Plugin Management

```nu
# Plugin configuration
def install-plugin [plugin: string] {
  echo $"Installing plugin: ($plugin)"
  cargo install $plugin
  register ($plugin | str replace "nu_plugin_" "nu-plugin-")
}

def list-plugins [] {
  plugin list | where is_plugin == true
}

# Useful plugins to install
# install-plugin nu_plugin_query
# install-plugin nu_plugin_formats
# install-plugin nu_plugin_chart
```

## Performance and Optimization {#performance}

### Profiling and Benchmarking

```nu
# Timing commands
def time-command [command: closure] {
  let start = date now
  do $command
  let end = date now
  $end - $start
}

# Example usage
time-command { ls | length }

# Benchmark function
def benchmark [command: closure, iterations: int = 10] {
  let times = 1..$iterations | each {
    time-command $command
  }

  {
    min: ($times | math min),
    max: ($times | math max),
    avg: ($times | math avg),
    total: ($times | math sum)
  }
}
```

### Memory Optimization

```nu
# Streaming large files
def process-large-file [filename: string] {
  # Instead of: open $filename | lines | each { ... }
  # Use streaming approach:
  open $filename
  | lines
  | take 1000
  | each { |line|
      # Process line
      $line | str trim
    }
}

# Lazy evaluation
def fibonacci-sequence [n: int] {
  # Generate sequence lazily
  0..$n | reduce { |_, acc|
    if ($acc | length) < 2 {
      $acc | append 1
    } else {
      let prev = $acc | last 2
      $acc | append (($prev | first) + ($prev | last))
    }
  }
}
```

### Parallel Processing

```nu
# Parallel file processing
def process-files-parallel [pattern: string] {
  glob $pattern
  | par-each { |file|
      {
        file: $file,
        size: (ls $file | get size | first),
        lines: (open $file | lines | length)
      }
    }
}

# Parallel HTTP requests
def check-urls-parallel [urls: list<string>] {
  $urls
  | par-each { |url|
      try {
        let response = http get $url
        {url: $url, status: "ok", size: ($response | str length)}
      } catch {
        {url: $url, status: "error", size: 0}
      }
    }
}
```

### Caching Strategies

```nu
# Simple memoization
def fibonacci-cached [n: int] {
  let cache_file = $"~/.cache/fib_($n).txt"

  if ($cache_file | path exists) {
    open $cache_file | into int
  } else {
    let result = if $n <= 1 { $n } else {
      (fibonacci-cached ($n - 1)) + (fibonacci-cached ($n - 2))
    }
    $result | save $cache_file
    $result
  }
}

# Cache with expiration
def cached-api-call [url: string, ttl_minutes: int = 60] {
  let cache_key = $url | hash md5
  let cache_file = $"~/.cache/api_($cache_key).json"

  let is_expired = if ($cache_file | path exists) {
    let file_age = (ls $cache_file | get modified | first)
    let now = date now
    ($now - $file_age) > ($ttl_minutes * 60 * 1000000000)
  } else {
    true
  }

  if $is_expired {
    let response = http get $url
    $response | save $cache_file
    $response
  } else {
    open $cache_file
  }
}
```

## Testing and Debugging {#testing-debugging}

### Unit Testing Framework

```nu
# test_framework.nu - Simple testing framework
def assert-equal [actual: any, expected: any, message?: string] {
  if $actual != $expected {
    let msg = $message | default "Assertion failed"
    error make {
      msg: $"($msg): expected ($expected), got ($actual)"
    }
  }
}

def assert-true [condition: bool, message?: string] {
  if not $condition {
    let msg = $message | default "Assertion failed"
    error make {msg: $"($msg): expected true, got false"}
  }
}

def run-test [test_name: string, test_func: closure] {
  try {
    do $test_func
    echo $"✅ ($test_name)"
  } catch { |error|
    echo $"❌ ($test_name): ($error.msg)"
  }
}

def run-test-suite [tests: record] {
  $tests | transpose name func | each { |test|
    run-test $test.name $test.func
  }
}
```

### Example Test Suite

```nu
# math_utils_test.nu
use test_framework.nu [assert-equal, run-test-suite]
use math_utils.nu

def test-add [] {
  assert-equal (add 2 3) 5 "Addition test"
  assert-equal (add -1 1) 0 "Addition with negatives"
}

def test-multiply [] {
  assert-equal (multiply 3 4) 12 "Multiplication test"
  assert-equal (multiply 0 5) 0 "Multiplication by zero"
}

def test-factorial [] {
  assert-equal (factorial 0) 1 "Factorial of 0"
  assert-equal (factorial 5) 120 "Factorial of 5"
}

# Run tests
run-test-suite {
  "add function": {|| test-add},
  "multiply function": {|| test-multiply},
  "factorial function": {|| test-factorial}
}
```

### Debugging Techniques

```nu
# Debug printing
def debug [value: any] {
  echo $"DEBUG: ($value | describe): ($value)"
  $value  # Pass through for pipeline
}

# Usage in pipeline
ls | debug | where size > 1kb | debug | length

# Conditional debugging
let DEBUG = true

def debug-if [value: any, condition: bool = $DEBUG] {
  if $condition {
    echo $"DEBUG: ($value)"
  }
  $value
}

# Stack trace simulation
def trace [function_name: string] {
  echo $"TRACE: Entering ($function_name)"
  let start = date now

  # Return a closure that logs exit
  {||
    let end = date now
    echo $"TRACE: Exiting ($function_name) (($end - $start) elapsed)"
  }
}

def traced-function [x: int, y: int] {
  let tracer = trace "traced-function"

  let result = $x + $y
  do $tracer
  $result
}
```

### Property-Based Testing

```nu
# Property-based testing helpers
def random-int [min: int = 0, max: int = 100] {
  (random integer $min..$max)
}

def random-string [length: int = 10] {
  1..$length | each {
    [(random integer 97..122) | into string] | str join ""
  } | str join ""
}

def property-test [property: closure, iterations: int = 100] {
  let failures = 1..$iterations | each { |i|
    try {
      do $property
      null
    } catch { |error|
      {iteration: $i, error: $error.msg}
    }
  } | compact

  if ($failures | is-empty) {
    echo $"✅ Property test passed ($iterations iterations)"
  } else {
    echo $"❌ Property test failed:"
    $failures | each { |failure|
      echo $"  Iteration ($failure.iteration): ($failure.error)"
    }
  }
}

# Example property test
property-test {
  let x = random-int -100 100
  let y = random-int -100 100
  let sum = $x + $y
  assert-equal ($sum - $x) $y "Subtraction property"
} 50
```

---

# Part IV: Extensions and Fun Projects

## Building Useful Scripts {#useful-scripts}

### System Administration Scripts

```nu
# system_monitor.nu - System monitoring dashboard
def system-dashboard [] {
  clear
  echo "=== System Dashboard ==="
  echo ""

  # CPU and Memory
  let system = sys
  echo $"CPU Usage: ($system.cpu | each {get cpu_usage} | math avg | math round)%"
  echo $"Memory: (($system.mem.used / 1024 / 1024 / 1024) | math round)GB / (($system.mem.total / 1024 / 1024 / 1024) | math round)GB"
  echo ""

  # Disk usage
  echo "Disk Usage:"
  $system.disks | each { |disk|
    let used_pct = ($disk.used / $disk.total * 100) | math round
    echo $"  ($disk.mount): ($used_pct)% (($disk.used / 1024 / 1024 / 1024 | math round)GB / ($disk.total / 1024 / 1024 / 1024 | math round)GB)"
  }
  echo ""

  # Top processes
  echo "Top Processes by CPU:"
  ps | sort-by cpu | last 5 | each { |proc|
    echo $"  ($proc.name): ($proc.cpu)%"
  }
}

# Backup script
def backup-directory [source: string, destination: string] {
  let timestamp = date now | format date "%Y%m%d_%H%M%S"
  let backup_name = $"backup_($timestamp)"
  let backup_path = $destination | path join $backup_name

  echo $"Creating backup: ($source) -> ($backup_path)"

  try {
    cp -r $source $backup_path
    echo $"✅ Backup completed: ($backup_path)"

    # Cleanup old backups (keep last 5)
    let old_backups = ls $destination
    | where name =~ "backup_"
    | sort-by modified
    | drop last 5

    $old_backups | each { |backup|
      echo $"🗑️ Removing old backup: ($backup.name)"
      rm -rf $backup.name
    }
  } catch { |error|
    echo $"❌ Backup failed: ($error.msg)"
  }
}
```

### Development Tools

```nu
# project_manager.nu - Development project manager
def new-rust-project [name: string] {
  cargo new $name
  cd $name

  # Add common dependencies
  cargo add serde --features derive
  cargo add tokio --features full
  cargo add anyhow

  # Create additional directories
  mkdir src/bin tests examples

  # Create README
  $"# ($name)

A Rust project created with Nushell project manager.

## Usage

```bash
cargo run
```

## Testing

```bash
cargo test
```
" | save README.md

  echo $"✅ Created Rust project: ($name)"
}

def project-stats [path?: string] {
  let project_path = $path | default "."
  cd $project_path

  echo $"=== Project Statistics for ($project_path) ==="
  echo ""

  # File counts by extension
  let file_stats = glob **/*
  | where {|f| $f | path type} == "file"
  | each {|f| $f | path parse | get extension | default "no-extension"}
  | group-by { $in }
  | transpose extension count
  | sort-by count

  echo "Files by extension:"
  $file_stats | each { |stat|
    echo $"  .($stat.extension): ($stat.count.0) files"
  }
  echo ""

  # Lines of code
  let total_lines = glob **/*.{rs,py,js,ts,nu}
  | each {|f| open $f | lines | length}
  | math sum

  echo $"Total lines of code: ($total_lines)"

  # Git stats (if available)
  try {
    let commits = git log --oneline | lines | length
    let contributors = git log --format="%an" | lines | uniq | length
    echo $"Git commits: ($commits)"
    echo $"Contributors: ($contributors)"
  }
}
```

### Data Processing Scripts

```nu
# log_analyzer.nu - Log file analyzer
def analyze-logs [log_file: string] {
  let logs = open $log_file | lines

  echo $"=== Log Analysis for ($log_file) ==="
  echo $"Total lines: ($logs | length)"
  echo ""

  # Error analysis
  let errors = $logs | where {str contains "ERROR"}
  echo $"Error lines: ($errors | length)"

  if ($errors | length) > 0 {
    echo "Most common errors:"
    $errors
    | each {str replace -r '^\[.*?\]\s*ERROR\s*' ''}
    | group-by { $in }
    | transpose error count
    | sort-by count
    | last 5
    | each { echo $"  ($in.count.0)x: ($in.error)" }
  }
  echo ""

  # Time-based analysis
  let hourly_stats = $logs
  | where {str contains -r '^\[\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}'}
  | each {str replace -r '^\[(\d{4}-\d{2}-\d{2} \d{2}):\d{2}:\d{2}.*' '$1'}
  | group-by { $in }
  | transpose hour count
  | sort-by hour

  echo "Activity by hour:"
  $hourly_stats | each { echo $"  ($in.hour): ($in.count.0) entries" }
}

# CSV data processor
def process-csv [file: string, --group-by (-g): string, --filter (-f): string] {
  let data = open $file | from csv

  mut processed = $data

  # Apply filter if provided
  if not ($filter | is-empty) {
    $processed = $processed | where { |row|
      $filter | each { |condition|
        # Simple condition parsing - extend as needed
        true  # Placeholder for actual filtering logic
      }
    }
  }

  # Group by column if specified
  if not ($group_by | is-empty) {
    $processed = $processed
    | group-by { |row| $row | get $group_by }
    | transpose group data
    | each { |group|
        {
          group: $group.group,
          count: ($group.data | length),
          summary: $group.data
        }
      }
  }

  $processed
}
```

## Integration with Other Tools {#integrations}

### Git Integration

```nu
# git_helpers.nu - Enhanced Git workflows
def git-status-enhanced [] {
  let status = git status --porcelain | lines
  let branch = git rev-parse --abbrev-ref HEAD | str trim

  echo $"Current branch: ($branch)"

  let modified = $status | where {str starts-with " M"} | str replace " M " "" | length
  let added = $status | where {str starts-with "A "} | str replace "A  " "" | length
  let deleted = $status | where {str starts-with " D"} | str replace " D " "" | length
  let untracked = $status | where {str starts-with "??"} | str replace "?? " "" | length

  echo $"Modified: ($modified), Added: ($added), Deleted: ($deleted), Untracked: ($untracked)"

  if ($status | length) > 0 {
    echo "\nFiles:"
    $status | each { echo $"  ($in)" }
  }
}

def git-commit-stats [since?: string] {
  let since_date = $since | default "1 month ago"

  let commits = git log $"--since=($since_date)" --pretty=format:"%h|%an|%s|%ad" --date=short
  | lines
  | each { split column "|" hash author message date }

  echo $"Commits since ($since_date): ($commits | length)"
  echo ""

  # Author statistics
  echo "Top contributors:"
  $commits
  | group-by author
  | transpose author commits
  | each { {author: $in.author, count: ($in.commits | length)} }
  | sort-by count
  | last 5
  | each { echo $"  ($in.author): ($in.count) commits" }
}

def git-branch-cleanup [] {
  let merged_branches = git branch --merged
  | lines
  | where {not (str contains "*")}
  | where {not (str contains "main")}
  | where {not (str contains "master")}
  | str trim

  if ($merged_branches | length) > 0 {
    echo "Merged branches to delete:"
    $merged_branches | each { echo $"  ($in)" }

    let confirm = input "Delete these branches? (y/N): "
    if ($confirm | str downcase) == "y" {
      $merged_branches | each { |branch|
        git branch -d $branch
        echo $"Deleted: ($branch)"
      }
    }
  } else {
    echo "No merged branches to clean up"
  }
}
```

### Docker Integration

```nu
# docker_helpers.nu - Docker management tools
def docker-cleanup [] {
  echo "🧹 Docker cleanup started..."

  # Remove stopped containers
  let stopped_containers = docker ps -a -q -f status=exited | lines | where {str length} > 0
  if ($stopped_containers | length) > 0 {
    echo $"Removing ($stopped_containers | length) stopped containers..."
    $stopped_containers | each { docker rm $in }
  }

  # Remove dangling images
  let dangling_images = docker images -f dangling=true -q | lines | where {str length} > 0
  if ($dangling_images | length) > 0 {
    echo $"Removing ($dangling_images | length) dangling images..."
    $dangling_images | each { docker rmi $in }
  }

  # Show disk usage
  docker system df
}

def docker-dev-env [service: string] {
  match $service {
    "postgres" => {
      docker run -d \
        --name dev-postgres \
        -e POSTGRES_PASSWORD=dev \
        -e POSTGRES_DB=dev \
        -p 5432:5432 \
        postgres:latest
    },
    "redis" => {
      docker run -d \
        --name dev-redis \
        -p 6379:6379 \
        redis:latest
    },
    "mysql" => {
      docker run -d \
        --name dev-mysql \
        -e MYSQL_ROOT_PASSWORD=dev \
        -e MYSQL_DATABASE=dev \
        -p 3306:3306 \
        mysql:8.0
    },
    _ => {
      echo "Unknown service. Available: postgres, redis, mysql"
    }
  }
}

def docker-logs-tail [container: string, lines: int = 100] {
  docker logs -f --tail $lines $container
}
```

### API Integration

```nu
# api_helpers.nu - REST API utilities
def api-get [url: string, --headers (-h): record = {}] {
  let header_args = $headers
  | transpose key value
  | each { [$"--header" $"($in.key): ($in.value)"] }
  | flatten

  curl -s $url ...$header_args | from json
}

def api-post [url: string, data: record, --headers (-h): record = {}] {
  let header_args = $headers
  | transpose key value
  | each { [$"--header" $"($in.key): ($in.value)"] }
  | flatten

  let json_data = $data | to json
  curl -s -X POST -d $json_data -H "Content-Type: application/json" $url ...$header_args | from json
}

def github-repo-info [owner: string, repo: string] {
  api-get $"https://api.github.com/repos/($owner)/($repo)"
}

def weather-forecast [city: string = "London"] {
  # Using OpenWeatherMap API (requires API key)
  let api_key = $env.OPENWEATHER_API_KEY? | default ""
  if ($api_key | is-empty) {
    echo "Please set OPENWEATHER_API_KEY environment variable"
    return
  }

  let weather = api-get $"https://api.openweathermap.org/data/2.5/weather?q=($city)&appid=($api_key)&units=metric"

  echo $"Weather in ($weather.name):"
  echo $"Temperature: ($weather.main.temp)°C (feels like ($weather.main.feels_like)°C)"
  echo $"Description: ($weather.weather.0.description | str title-case)"
  echo $"Humidity: ($weather.main.humidity)%"
  echo $"Wind: ($weather.wind.speed) m/s"
}
```

## Real-World Projects {#real-world-projects}

### Project 1: Personal Finance Tracker

```nu
# finance_tracker.nu - Personal finance management
const TRANSACTIONS_FILE = "~/.finance/transactions.csv"
const CATEGORIES_FILE = "~/.finance/categories.json"

def init-finance [] {
  let finance_dir = "~/.finance"
  if not ($finance_dir | path exists) {
    mkdir $finance_dir
  }

  if not ($TRANSACTIONS_FILE | path exists) {
    "date,amount,description,category\n" | save $TRANSACTIONS_FILE
  }

  if not ($CATEGORIES_FILE | path exists) {
    [
      "groceries", "utilities", "entertainment", "transport",
      "healthcare", "income", "investment", "other"
    ] | to json | save $CATEGORIES_FILE
  }

  echo "Finance tracker initialized!"
}

def add-transaction [amount: float, description: string, category?: string] {
  let cat = $category | default "other"
  let date = date now | format date "%Y-%m-%d"
  let new_row = $"($date),($amount),($description),($cat)\n"

  $new_row | save --append $TRANSACTIONS_FILE
  echo $"Added transaction: ($description) - $($amount) (($cat))"
}

def finance-summary [--period (-p): string = "month"] {
  let transactions = open $TRANSACTIONS_FILE | from csv

  let start_date = match $period {
    "week" => (date now | date to-record | update day { $in - 7 } | date from-record),
    "month" => (date now | date to-record | update day 1 | date from-record),
    "year" => (date now | date to-record | update month 1 | update day 1 | date from-record),
    _ => (date now | date to-record | update day 1 | date from-record)
  }

  let filtered = $transactions
  | where {|t| ($t.date | into datetime) >= $start_date}

  echo $"=== Finance Summary (($period)) ==="
  echo $"Total transactions: ($filtered | length)"
  echo ""

  let income = $filtered | where amount > 0 | get amount | math sum
  let expenses = $filtered | where amount < 0 | get amount | math sum | math abs
  let net = $income - $expenses

  echo $"Income: $($income)"
  echo $"Expenses: $($expenses)"
  echo $"Net: $($net)"
  echo ""

  echo "Expenses by category:"
  $filtered
  | where amount < 0
  | group-by category
  | transpose category transactions
  | each { |cat|
      {
        category: $cat.category,
        amount: ($cat.transactions | get amount | math sum | math abs),
        count: ($cat.transactions | length)
      }
    }
  | sort-by amount
  | each { echo $"  ($in.category): $($in.amount) (($in.count) transactions)" }
}

def budget-alert [category: string, limit: float] {
  let current_month = date now | format date "%Y-%m"
  let transactions = open $TRANSACTIONS_FILE | from csv

  let category_spend = $transactions
  | where {|t| ($t.date | str starts-with $current_month) and ($t.category == $category) and ($t.amount < 0)}
  | get amount
  | math sum
  | math abs

  echo $"($category) spending this month: $($category_spend)"

  if $category_spend > $limit {
    echo $"⚠️ Budget exceeded! Limit: $($limit)"
  } else {
    let remaining = $limit - $category_spend
    echo $"✅ Within budget. Remaining: $($remaining)"
  }
}
```

### Project 2: Website Monitor

```nu
# website_monitor.nu - Website uptime monitoring
const MONITOR_CONFIG = "~/.monitor/sites.json"
const LOG_FILE = "~/.monitor/uptime.log"

def init-monitor [] {
  let monitor_dir = "~/.monitor"
  if not ($monitor_dir | path exists) {
    mkdir $monitor_dir
  }

  if not ($MONITOR_CONFIG | path exists) {
    [
      {
        name: "Example Site",
        url: "https://example.com",
        expected_status: 200,
        timeout: 10
      }
    ] | to json | save $MONITOR_CONFIG
  }

  echo "Website monitor initialized!"
  echo $"Config: ($MONITOR_CONFIG)"
  echo $"Logs: ($LOG_FILE)"
}

def add-site [name: string, url: string, expected_status: int = 200] {
  let sites = open $MONITOR_CONFIG | from json
  let new_site = {
    name: $name,
    url: $url,
    expected_status: $expected_status,
    timeout: 10
  }

  $sites | append $new_site | to json | save $MONITOR_CONFIG
  echo $"Added site: ($name) - ($url)"
}

def check-site [site: record] -> record {
  let start_time = date now

  try {
    let response = http get $site.url
    let end_time = date now
    let response_time = ($end_time - $start_time) / 1000000  # Convert to milliseconds

    {
      name: $site.name,
      url: $site.url,
      status: "up",
      response_time: $response_time,
      timestamp: $start_time
    }
  } catch { |error|
    {
      name: $site.name,
      url: $site.url,
      status: "down",
      error: $error.msg,
      response_time: null,
      timestamp: $start_time
    }
  }
}

def monitor-all [] {
  let sites = open $MONITOR_CONFIG | from json

  echo $"Checking ($sites | length) sites..."

  let results = $sites | each { |site| check-site $site }

  # Log results
  $results | each { |result|
    let log_entry = $"($result.timestamp | format date '%Y-%m-%d %H:%M:%S'),($result.name),($result.status),($result.response_time)\n"
    $log_entry | save --append $LOG_FILE
  }

  # Display results
  echo ""
  $results | each { |result|
    if $result.status == "up" {
      echo $"✅ ($result.name): ($result.response_time)ms"
    } else {
      echo $"❌ ($result.name): ($result.error)"
    }
  }

  # Summary
  let up_count = $results | where status == "up" | length
  let down_count = $results | where status == "down" | length
  echo ""
  echo $"Summary: ($up_count) up, ($down_count) down"
}

def uptime-report [days: int = 7] {
  if not ($LOG_FILE | path exists) {
    echo "No log file found. Run monitor-all first."
    return
  }

  let cutoff_date = (date now) - ($days * 24 * 60 * 60 * 1000000000)

  let logs = open $LOG_FILE
  | lines
  | each { str split "," }
  | each { {
      timestamp: ($in.0 | into datetime),
      name: $in.1,
      status: $in.2,
      response_time: ($in.3 | into float)
    }}
  | where timestamp > $cutoff_date

  echo $"=== Uptime Report (Last ($days) days) ==="
  echo ""

  let sites = $logs | get name | uniq

  $sites | each { |site|
    let site_logs = $logs | where name == $site
    let total_checks = $site_logs | length
    let up_checks = $site_logs | where status == "up" | length
    let uptime_pct = if $total_checks > 0 { ($up_checks / $total_checks * 100) | math round } else { 0 }
    let avg_response = $site_logs | where status == "up" | get response_time | compact | math avg | math round

    echo $"($site):"
    echo $"  Uptime: ($uptime_pct)% (($up_checks)/($total_checks) checks)"
    echo $"  Avg response time: ($avg_response)ms"
    echo ""
  }
}
```

### Project 3: Development Workflow Automation

```nu
# dev_workflow.nu - Development workflow automation
def setup-project [name: string, type: string = "web"] {
  mkdir $name
  cd $name

  match $type {
    "web" => {
      # Initialize npm project
      npm init -y

      # Install common dependencies
      npm install express cors helmet morgan
      npm install -D nodemon jest supertest

      # Create basic structure
      mkdir src tests public

      # Create basic files
      "console.log('Hello from', process.env.NODE_ENV || 'development');" | save src/index.js
      "# ($name)\n\nA web project created with Nushell automation.\n" | save README.md

      # Update package.json scripts
      let package = open package.json | from json
      $package
      | update scripts {
          start: "node src/index.js",
          dev: "nodemon src/index.js",
          test: "jest"
        }
      | to json
      | save package.json
    },
    "python" => {
      # Create virtual environment
      python -m venv venv

      # Create requirements.txt
      "flask\nrequests\npytest\n" | save requirements.txt

      # Create basic structure
      mkdir src tests

      # Create basic files
      "print('Hello from Python!')" | save src/main.py
      "# ($name)\n\nA Python project created with Nushell automation.\n" | save README.md
    },
    "rust" => {
      cargo init .

      # Add common dependencies
      cargo add serde --features derive
      cargo add tokio --features full

      # Create additional structure
      mkdir tests examples
    },
    _ => {
      echo "Unknown project type. Available: web, python, rust"
      return
    }
  }

  # Initialize git
  git init
  git add .
  git commit -m "Initial commit"

  echo $"✅ Project ($name) created successfully!"
  echo $"Type: ($type)"
  echo $"Location: (pwd)"
}

def deploy-to-staging [--service (-s): string] {
  echo "🚀 Deploying to staging..."

  # Run tests first
  let test_result = match $service {
    "node" => { npm test },
    "python" => { python -m pytest },
    "rust" => { cargo test },
    _ => {
      # Auto-detect
      if ("package.json" | path exists) {
        npm test
      } else if ("Cargo.toml" | path exists) {
        cargo test
      } else if ("requirements.txt" | path exists) {
        python -m pytest
      }
    }
  } | complete

  if $test_result.exit_code != 0 {
    echo "❌ Tests failed! Aborting deployment."
    echo $test_result.stderr
    return
  }

  echo "✅ Tests passed!"

  # Build if necessary
  if ("Dockerfile" | path exists) {
    echo "🐳 Building Docker image..."
    docker build -t app:staging .
  }

  # Deploy (customize for your deployment method)
  echo "📦 Deploying to staging environment..."

  # Example: Deploy to staging server
  # scp -r . user@staging-server:/path/to/app
  # ssh user@staging-server "cd /path/to/app && pm2 restart app"

  echo "✅ Deployment completed!"
}

def code-quality-check [] {
  echo "🔍 Running code quality checks..."

  # Check for common issues
  if ("package.json" | path exists) {
    echo "Checking Node.js project..."

    # Check for outdated dependencies
    let outdated = npm outdated | complete
    if $outdated.exit_code != 0 {
      echo "⚠️ Outdated dependencies found"
    }

    # Run linting if available
    if (open package.json | from json | get scripts? | get lint? | is-not-empty) {
      npm run lint
    }
  }

  if ("Cargo.toml" | path exists) {
    echo "Checking Rust project..."
    cargo fmt --check
    cargo clippy
  }

  if (".git" | path exists) {
    # Check git status
    let status = git status --porcelain | lines
    if ($status | length) > 0 {
      echo "⚠️ Uncommitted changes found"
    }

    # Check for large files
    let large_files = git ls-files | each { |file|
      if ($file | path exists) {
        let size = ls $file | get size | first
        if $size > 1MB {
          {file: $file, size: $size}
        }
      }
    } | compact

    if ($large_files | length) > 0 {
      echo "⚠️ Large files found in repository:"
      $large_files | each { echo $"  ($in.file): ($in.size)" }
    }
  }

  echo "✅ Code quality check completed!"
}
```

## Community Resources {#community}

### Getting Help and Contributing

#### Official Resources
- **Documentation**: https://www.nushell.sh/book/ - The comprehensive Nushell book
- **GitHub**: https://github.com/nushell/nushell - Source code and issue tracker
- **Discord**: Active community chat for real-time help
- **Reddit**: r/nushell - Community discussions and examples

#### Learning Resources

```nu
# Built-in help system
help                    # List all categories
help commands          # List all commands
help <command>         # Get detailed help for specific command
help --find <term>     # Search help topics

# Cookbook and examples
help cookbook          # Built-in cookbook with examples
```

#### Contributing to Nushell

```nu
# Set up development environment
def setup-nushell-dev [] {
  git clone https://github.com/nushell/nushell.git
  cd nushell

  # Install Rust if not already installed
  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

  # Build Nushell
  cargo build --release

  # Run tests
  cargo test

  echo "Nushell development environment ready!"
  echo "Binary location: ./target/release/nu"
}

# Example contribution workflow
def contribute-to-nushell [feature: string] {
  # Fork and clone your fork first
  git checkout -b $"feature/($feature)"

  # Make your changes
  echo "Make your changes now..."

  # Test your changes
  cargo test
  cargo clippy
  cargo fmt

  # Commit and push
  git add .
  git commit -m $"Add ($feature) feature"
  git push origin $"feature/($feature)"

  echo "Ready to create a pull request!"
}
```

### Useful Plugins and Extensions

#### Essential Plugins

```nu
# Installing plugins (examples)
def install-essential-plugins [] {
  let plugins = [
    "nu_plugin_query",     # Advanced querying capabilities
    "nu_plugin_formats",   # Additional format support
    "nu_plugin_chart",     # Data visualization
    "nu_plugin_net",       # Network utilities
    "nu_plugin_inc",       # Increment/decrement values
  ]

  $plugins | each { |plugin|
    echo $"Installing ($plugin)..."
    try {
      cargo install $plugin
    } catch {
      echo $"Failed to install ($plugin)"
    }
  }
}
```

#### Community Scripts and Modules

```nu
# Curated list of useful community modules
def awesome-nushell [] {
  let resources = [
    {
      name: "nu_scripts",
      url: "https://github.com/nushell/nu_scripts",
      description: "Collection of useful Nushell scripts"
    },
    {
      name: "nushell themes",
      url: "https://github.com/nushell/nu_scripts/tree/main/themes",
      description: "Color themes for Nushell"
    },
    {
      name: "completions",
      url: "https://github.com/nushell/nu_scripts/tree/main/custom-completions",
      description: "Custom completions for popular tools"
    }
  ]

  echo "=== Awesome Nushell Resources ==="
  $resources | each { |resource|
    echo $"($resource.name):"
    echo $"  URL: ($resource.url)"
    echo $"  Description: ($resource.description)"
    echo ""
  }
}

# Install community completions
def install-completions [] {
  let completions_url = "https://raw.githubusercontent.com/nushell/nu_scripts/main/custom-completions"
  let completions_dir = $env.HOME | path join ".config" "nushell" "completions"

  mkdir $completions_dir

  let popular_completions = [
    "git-completions.nu",
    "docker-completions.nu",
    "npm-completions.nu",
    "cargo-completions.nu"
  ]

  $popular_completions | each { |completion|
    let url = $"($completions_url)/($completion)"
    let dest = $completions_dir | path join $completion

    try {
      http get $url | save $dest
      echo $"✅ Installed ($completion)"
    } catch {
      echo $"❌ Failed to install ($completion)"
    }
  }

  echo ""
  echo "To use completions, add this to your config.nu:"
  echo $"source ($completions_dir)/*.nu"
}
```

### Best Practices and Style Guide

#### Code Style Guidelines

```nu
# Good Nushell style examples

# 1. Use descriptive names
def calculate-monthly-interest [principal: float, rate: float] -> float {
  $principal * ($rate / 100) / 12
}

# 2. Keep functions focused and small
def validate-email [email: string] -> bool {
  $email | str contains "@" and ($email | str contains ".")
}

def send-email [to: string, subject: string, body: string] {
  if not (validate-email $to) {
    error make {msg: "Invalid email address"}
  }

  # Send email logic here
}

# 3. Use structured data effectively
def process-sales-data [data: list<record>] {
  $data
  | where amount > 0
  | group-by region
  | transpose region sales
  | each { |region|
      {
        region: $region.region,
        total_sales: ($region.sales | get amount | math sum),
        avg_sale: ($region.sales | get amount | math avg),
        transaction_count: ($region.sales | length)
      }
    }
}

# 4. Handle errors gracefully
def safe-file-operation [filename: string] {
  try {
    open $filename | from json
  } catch { |error|
    echo $"Warning: Could not process ($filename): ($error.msg)"
    {}  # Return empty record as fallback
  }
}

# 5. Use pipelines effectively
def analyze-log-files [pattern: string] {
  glob $pattern
  | par-each { |file|
      open $file
      | lines
      | where {str contains "ERROR"}
      | length
    }
  | math sum
}
```

### Performance Tips

```nu
# Performance optimization tips

# 1. Use par-each for CPU-intensive operations
def parallel-processing [items: list] {
  # Good: parallel processing
  $items | par-each { |item| expensive-operation $item }

  # Instead of: sequential processing
  # $items | each { |item| expensive-operation $item }
}

# 2. Stream large files instead of loading entirely
def process-large-csv [filename: string] {
  # Good: streaming approach
  open $filename
  | from csv
  | where column > 100
  | take 1000

  # Instead of loading all data then filtering
}

# 3. Use take/skip for pagination
def paginated-results [data: list, page: int, size: int = 20] {
  $data
  | skip ($page * $size)
  | take $size
}

# 4. Cache expensive operations
def cached-operation [input: string] {
  let cache_file = $"~/.cache/operation_($input | hash md5).json"

  if ($cache_file | path exists) {
    open $cache_file | from json
  } else {
    let result = expensive-computation $input
    $result | to json | save $cache_file
    $result
  }
}
```

---

## Conclusion

Nushell represents a paradigm shift in shell design, bringing structured data processing and modern programming concepts to the command line. This book has covered:

**Essential Skills:**
- Core CLI commands and data manipulation
- Programming fundamentals with types and control flow
- Advanced features like modules and error handling
- Real-world project examples

**Key Takeaways:**
1. **Think in Data Structures** - Nushell's power comes from treating everything as structured data
2. **Embrace the Pipeline** - Chain operations naturally with Nu's pipeline system
3. **Type Safety Matters** - Use Nu's type system to catch errors early
4. **Modularize Your Code** - Build reusable modules and functions
5. **Community First** - Leverage existing scripts and contribute back

**Next Steps:**
- Join the Nushell community on Discord and GitHub
- Contribute to nu_scripts repository with your own utilities
- Build custom plugins for specialized needs
- Share your Nushell scripts and workflows

Remember: Nushell is not just a shell—it's a data processing powerhouse that can transform how you work with the command line. Start small, think in pipelines, and let the structured data approach guide your solutions.

**Happy Nushelling!** 🚀

---

*This book is a living document. As Nushell evolves, so should your knowledge. Keep exploring, keep learning, and keep building amazing things with this powerful tool.*