# Nushell for the Impatient

*Master structured shell programming and data processing fast*

**Version 3.0 - January 2025**

---

## Table of Contents

**Chapter 1: [Fundamental Programming Concepts](#chapter-1)**
*Variables, data types, and basic operations*

**Chapter 2: [Working with Structured Data](#chapter-2)**
*Tables, records, lists, and data transformations*

**Chapter 3: [Control Flow and Functions](#chapter-3)**
*Conditionals, loops, and custom commands*

**Chapter 4: [File System and I/O Operations](#chapter-4)**
*File handling, directories, and data formats*

**Chapter 5: [Text Processing and Pattern Matching](#chapter-5)**
*String manipulation and parsing*

**Chapter 6: [System Integration](#chapter-6)**
*External commands, environment, and process management*

**Chapter 7: [Advanced Data Processing](#chapter-7)**
*Complex transformations, pipelines, and error handling*

**Chapter 8: [Building Real Applications](#chapter-8)**
*Putting it all together with practical examples*

---

## Chapter 1: Fundamental Programming Concepts {#chapter-1}

### Why Choose Nushell?

Traditional shells treat everything as text. This works until you need to parse, filter, or transform data—then you're wrestling with `awk`, `sed`, `grep`, and custom parsing logic. Nushell changes the game by making structured data a first-class citizen.

**The Old Way:**
```bash
# Fragile text parsing
ps aux | grep chrome | awk '{print $2, $11}' | head -5
```

**The Nushell Way:**
```nu
# Direct data queries
ps | where name =~ chrome | select pid name | first 5
```

When formats change, the old way breaks. Nushell's structured approach adapts automatically.

### Getting Started

Install Nushell for your platform:
```bash
# macOS
brew install nushell

# Windows
winget install nushell

# Linux
cargo install nu
```

Launch and explore:
```nu
nu                    # Start Nushell
version              # Check your version
help                 # Browse documentation
help commands        # See all available commands
```

### Variables and Assignment

Nushell uses immutable variables by default—a key difference from traditional shells:

```nu
# Immutable variables (recommended)
let name = "Alice"
let age = 30
let active = true

# Mutable when needed
mut counter = 0
$counter = $counter + 1
```

**Why immutable by default?** It prevents accidental modifications and makes your scripts more reliable. You can still use mutability when you need it.

### Data Types

Nushell has rich, built-in data types that make complex data manipulation simple:

```nu
# Basic types
42                    # int
3.14159              # float
"hello world"        # string
true                 # bool
null                 # null value

# Collection types
[1, 2, 3]            # list
{name: "Bob", age: 25}  # record

# Tables (list of records)
[
  {name: "Alice", role: "Engineer"},
  {name: "Bob", role: "Designer"}
]
```

**Key insight:** Every Nushell command returns one of these structured types, not raw text. This is what enables powerful data processing.

### Type Checking and Conversion

Check types and convert between them safely:

```nu
# Type inspection
42 | describe         # "int"
[1, 2, 3] | describe  # "list<int>"

# Safe conversions
"42" | into int       # string to integer
42 | into string      # integer to string
"true" | into bool    # string to boolean

# Handle conversion errors
"not_a_number" | into int  # Error with clear message
```

### Basic Operations

Arithmetic and string operations work intuitively:

```nu
# Math operators
3 + 4 * 2           # 11 (standard precedence)
2 ** 8              # 256 (exponentiation)
17 mod 5            # 2 (modulo)
15 // 4             # 3 (floor division)

# String interpolation
let name = "World"
$"Hello, ($name)!"  # "Hello, World!"

# Comparison operators
$age >= 18          # boolean result
$name == "Alice"    # exact match
$text =~ "pattern"  # regex match
$text !~ "pattern"  # inverse regex match
$text starts-with "Hello"  # prefix check
$text ends-with "world"    # suffix check

# Logical operators
true and false      # false
true or false       # true
not true           # false
true xor true      # false

# Bitwise operations
5 bit-and 3        # 1
5 bit-or 3         # 7
5 bit-xor 3        # 6
8 bit-shl 2        # 32 (shift left)
8 bit-shr 2        # 2 (shift right)
```

### Your First Data Pipeline

Here's where Nushell shines—chaining operations on structured data:

```nu
# Traditional approach (broken across shell boundaries)
ls | grep .txt | wc -l

# Nushell approach (seamless data flow)
ls | where name =~ "\.txt$" | length
```

This pipeline:
1. `ls` returns a table of file information
2. `where` filters rows based on conditions
3. `length` counts the results

**Exercise:** Try counting different file types in your current directory. What happens when you change the pattern?


### Quick Start Examples (From the Cookbook)

```nu
# Get a random joke (HTTP request)
(http get https://api.chucknorris.io/jokes/random).value

# Find high CPU processes
ps | where cpu > 0 | sort-by cpu | reverse

# Parse command output into structured data
df -h | str replace "Mounted on" Mounted_On | detect columns

# Get file counts by type
ls | group-by type | transpose type files | each { {type: $in.type, count: ($in.files | length)} }

# Find large files
ls **/* | where type == file and size > 10mb | sort-by size | reverse
```

---

## Chapter 2: Working with Structured Data {#chapter-2}

### Understanding Tables

Everything in Nushell flows through pipelines as structured data. The most common structure is a table—a list of records with consistent columns:

```nu
# See structured output
ps                   # Process table
ls                   # File table
sys                  # System info table
```

Each command returns data you can immediately filter, sort, and transform without parsing.

### Selecting and Projecting Data

Choose which columns you want to work with:

```nu
# Pick specific columns
ps | select pid name cpu
ls | select name size modified

# Rename columns during selection
ps | select pid, process_name: name, cpu_usage: cpu
```

**Best practice:** Select only the columns you need. It makes your data easier to work with and more efficient.

### Filtering Rows

Use `where` to filter rows based on conditions:

```nu
# Numeric comparisons
ps | where cpu > 10.0
ls | where size > 1mb

# String matching
ps | where name == "chrome"
ps | where name =~ "fire"    # regex pattern
ls | where name ends-with ".txt"

# Complex conditions
ps | where cpu > 5.0 and mem > 100mb
ls | where (size > 1mb) or (name =~ "important")
```

### Sorting Data

Order your results meaningfully:

```nu
# Sort by single column
ls | sort-by size
ps | sort-by cpu

# Reverse order
ps | sort-by cpu | reverse

# Multi-column sorting
ls | sort-by type name
```

### Transforming Data

Use `each` to transform every row:

```nu
# Transform individual values
ls | each { |file| $file.name | str upcase }

# Create computed columns
ls | each { |file|
  {
    name: $file.name,
    size_mb: ($file.size / 1mb),
    is_large: ($file.size > 10mb)
  }
}
```

### Grouping and Aggregation

Analyze data by categories with comprehensive table operations:

```nu
# Basic grouping
ls | group-by type                      # Group files by type
ps | group-by name                      # Group processes by name

# Count and analyze groups
ls | group-by type | transpose type files | each { |group|
  {type: $group.type, count: ($group.files | length)}
}

# Advanced table operations
ls | sort-by size                       # Sort by column
ls | sort-by type name                  # Multi-column sort
ls | select name size modified          # Select specific columns
ls | reject type                        # Remove columns
ls | rename size file_size              # Rename columns
ls | move name --after size             # Reorder columns

# Table modifications
ls | insert category "documents"        # Add new column
ls | update size { |row| $row.size / 1mb }  # Transform column
ls | upsert category "files"            # Insert or update column

# Combining tables
$table1 | append $table2                # Vertical concatenation
$table1 | merge $table2                 # Horizontal merge

# Advanced filtering and selection
ls | first 5                           # First 5 rows
ls | last 3                            # Last 3 rows
ls | skip 2                            # Skip first 2 rows
ls | take 10                           # Take first 10 rows
ls | drop 5                            # Remove last 5 rows

# Aggregation operations
ps | select cpu | math sum             # Total CPU usage
ls | select size | math avg            # Average file size
ls | select size | math max            # Largest file size
ls | select size | math min            # Smallest file size
ps | select mem | math median          # Median memory usage
ps | select cpu | math stddev          # CPU usage standard deviation

# Complex analysis
ls | group-by type | transpose type files | each { |group|
  {
    type: $group.type,
    count: ($group.files | length),
    total_size: ($group.files | get size | math sum),
    avg_size: ($group.files | get size | math avg),
    largest: ($group.files | sort-by size | last | get name)
  }
} | sort-by total_size | reverse
```

### Working with Lists

Lists are ordered collections that can hold any data type:

```nu
# Create lists (multiple syntaxes)
let numbers = [1, 2, 3, 4, 5]     # Standard syntax
let colors = [red yellow green]    # Bare words
let mixed = [1, "text", true]      # Mixed types

# Access and extract
$numbers.1                    # Get index 1: 2
$numbers | get 2              # Get index 2: 3
$numbers | first 3            # [1, 2, 3]
$numbers | last 2             # [4, 5]
$numbers | skip 1             # [2, 3, 4, 5]
$numbers | length             # 5
$numbers | is-empty           # false

# Transform and filter
$numbers | each { |n| $n * 2 }          # [2, 4, 6, 8, 10]
$numbers | where { |n| $n mod 2 == 0 }   # [2, 4]
$numbers | enumerate                     # Add index column
$colors | where ($it | str ends-with 'w') # [yellow]

# Modify lists (returns new list)
$numbers | insert 1 99        # [1, 99, 2, 3, 4, 5]
$numbers | update 1 99        # [1, 99, 3, 4, 5]
$numbers | prepend 0          # [0, 1, 2, 3, 4, 5]
$numbers | append 6           # [1, 2, 3, 4, 5, 6]

# Combine lists
[1, 2] | append [3, 4]        # [1, 2, 3, 4]
$numbers | flatten            # Expand nested lists
$list1 | zip $list2           # Combine element-wise

# Reduce operations
$numbers | reduce { |it, acc| $acc + $it }  # Sum: 15
$colors | str join ", "       # "red, yellow, green"

# Membership testing
3 in $numbers                 # true
"blue" not-in $colors         # true
```

### Working with Records

Records are like objects or dictionaries—collections of named values:

```nu
# Create records
let person = {name: "Alice", age: 30, active: true}

# Access fields
$person.name         # "Alice"
$person | get age    # 30

# Update records (creates new record)
$person | insert role "Engineer"
$person | update age 31
$person | upsert status "active"  # Insert or update
```

### Practical Example: Log Analysis

Let's analyze some hypothetical web server logs:

```nu
# Parse log entries (imagine this data comes from a file)
let logs = [
  {timestamp: "2024-01-15 10:30:00", method: "GET", path: "/api/users", status: 200, size: 1024},
  {timestamp: "2024-01-15 10:31:00", method: "POST", path: "/api/login", status: 401, size: 256},
  {timestamp: "2024-01-15 10:32:00", method: "GET", path: "/api/users", status: 200, size: 2048}
]

# Find error responses
$logs | where status >= 400

# Analyze traffic by endpoint
$logs | group-by path | transpose endpoint requests | each { |group|
  {
    endpoint: $group.endpoint,
    requests: ($group.requests | length),
    avg_size: ($group.requests | select size | math avg)
  }
}
```

### Cookbook Recipe: Analyzing Directory Structure

```nu
# Complete directory analysis
def analyze_directory [path: string = "."] {
  let files = ls $path | where type == file
  let dirs = ls $path | where type == dir

  {
    summary: {
      total_files: ($files | length),
      total_dirs: ($dirs | length),
      total_size: ($files | get size | math sum),
      avg_file_size: ($files | get size | math avg)
    },
    by_extension: (
      $files | group-by { $in.name | path parse | get extension } |
      transpose ext files | each { {extension: $in.ext, count: ($in.files | length), total_size: ($in.files | get size | math sum)} } |
      sort-by count | reverse
    ),
    largest_files: ($files | sort-by size | reverse | first 5 | select name size)
  }
}

# Usage: analyze_directory ~/Documents
```

**Exercise:** Create a small dataset representing books (title, author, year, pages) and practice filtering, sorting, and grouping operations.


---

## Chapter 3: Control Flow and Functions {#chapter-3}

### Conditional Expressions

Nushell uses expressions for conditionals, making them composable:

```nu
# Simple if-else
let status = if $age >= 18 { "adult" } else { "minor" }

# Chained conditions
let grade = if $score >= 90 {
  "A"
} else if $score >= 80 {
  "B"
} else if $score >= 70 {
  "C"
} else {
  "F"
}

# Conditions in pipelines
ls | each { |file|
  if $file.size > 1mb {
    $file | insert category "large"
  } else {
    $file | insert category "small"
  }
}
```

### Pattern Matching with Match

For complex branching, `match` is more elegant than chained if-else:

```nu
# Match specific values
let response = match $status_code {
  200 => "OK",
  404 => "Not Found",
  500 => "Server Error",
  _ => "Unknown"
}

# Match ranges and patterns
let category = match $score {
  90..100 => "Excellent",
  80..89 => "Good",
  70..79 => "Fair",
  _ => "Needs Improvement"
}

# Match multiple conditions
match $file.type {
  "file" if $file.size > 1gb => "large file",
  "file" => "regular file",
  "dir" => "directory",
  _ => "unknown"
}
```

### Loops and Iteration

Nushell prefers functional iteration over traditional loops:

```nu
# Transform each item (preferred)
[1, 2, 3, 4, 5] | each { |n| $n * 2 }

# Traditional for loop (when needed)
for i in 1..10 {
  print $"Processing item ($i)"
}

# While loops for state-dependent iteration
mut count = 0
while $count < 5 {
  print $"Count: ($count)"
  $count = $count + 1
}

# Loop with break and continue
for file in (ls) {
  if $file.type == "dir" { continue }
  if $file.size > 100mb { break }
  print $file.name
}
```

**Best practice:** Use `each`, `where`, `reduce` and other functional operations instead of explicit loops when possible. They're more concise and often more efficient.

### Defining Functions

Create reusable code with custom functions:

```nu
# Simple function
def greet [name: string] {
  $"Hello, ($name)!"
}

greet "World"  # "Hello, World!"

# Function with multiple parameters and types
def calculate_area [length: float, width: float] {
  $length * $width
}

# Optional parameters with defaults
def greet_formal [name: string, title: string = ""] {
  if ($title | is-empty) {
    $"Hello, ($name)"
  } else {
    $"Hello, ($title) ($name)"
  }
}

greet_formal "Alice"              # "Hello, Alice"
greet_formal "Smith" "Dr."        # "Hello, Dr. Smith"
```

### Functions with Flags

Add command-line style options to your functions:

```nu
def search_files [
  pattern: string,                    # Required parameter
  --case-sensitive,                   # Boolean flag
  --extension (-e): string,           # Flag with value
  --max-results (-n): int = 10,       # Flag with default
  ...paths: string                    # Rest parameters
] {
  let files = if ($extension | is-empty) {
    ls
  } else {
    ls | where name ends-with $extension
  }

  let filtered = if $case_sensitive {
    $files | where name =~ $pattern
  } else {
    $files | where name =~ ("(?i)" + $pattern)
  }

  $filtered | first $max_results
}

# Advanced parameter features
def greet [
  name: string,                       # Required typed parameter
  age?: int,                          # Optional parameter
  --title: string = "Mr/Ms",          # Flag with default
  --verbose (-v),                     # Short flag alias
  ...nicknames: string                # Variable arguments
] {
  let greeting = $"Hello, ($title) ($name)"
  if $verbose {
    print $"($greeting) (age: ($age // "unknown"))"
    if ($nicknames | length) > 0 {
      print $"Also known as: ($nicknames | str join ', ')"
    }
  } else {
    print $greeting
  }
}

# Usage examples
search_files "test"                           # Basic search
search_files "Test" --case-sensitive          # Case sensitive
search_files "config" -e ".json" -n 5        # With extension and limit
greet "Alice" 30 --verbose "Al" "Ally"       # With optional and rest params

# Environment-modifying functions
def --env cd-and-list [path: string] {
  cd $path
  ls
}
```

### Pipeline Functions

Create functions that work naturally in pipelines:

```nu
# Input from pipeline
def format_size [] {
  each { |file|
    $file | insert size_mb ($file.size / 1mb | math round --precision 2)
  }
}

# Use in pipeline
ls | format_size | select name size_mb

# Filter function
def large_files [min_size: filesize = 1mb] {
  where size >= $min_size
}

ls | large_files 10mb | select name size
```

### Error Handling in Functions

Handle errors gracefully:

```nu
def safe_divide [dividend: float, divisor: float] {
  if $divisor == 0 {
    error make { msg: "Cannot divide by zero" }
  }
  $dividend / $divisor
}

def read_config [path: string] {
  try {
    open $path | from json
  } catch { |err|
    print $"Warning: Could not read config from ($path): ($err.msg)"
    {}  # Return empty record as fallback
  }
}
```

### Practical Example: File Organization

Let's build a function that organizes files by extension:

```nu
def organize_files [target_dir: string = "."] {
  let files = ls $target_dir | where type == "file"

  $files | group-by { |file|
    $file.name | path parse | get extension
  } | transpose extension files | each { |group|
    let ext = if ($group.extension | is-empty) { "no_extension" } else { $group.extension }
    let dir_name = $"($target_dir)/($ext)_files"

    print $"Creating directory: ($dir_name)"
    mkdir $dir_name

    $group.files | each { |file|
      print $"Moving ($file.name) to ($dir_name)/"
      mv $file.name $dir_name
    }

    {extension: $ext, count: ($group.files | length)}
  }
}

# Usage
organize_files "~/Downloads"
```

### Cookbook Recipe: System Monitoring Dashboard

```nu
# Create a system monitoring function
def system_monitor [] {
  print "=== System Monitor ==="

  # Memory usage
  let memory = sys | get mem
  let mem_percent = ($memory.used / $memory.total * 100 | math round)
  print $"Memory: ($mem_percent)% used (($memory.used | into string) / ($memory.total | into string))"

  # CPU intensive processes
  print "\nTop CPU processes:"
  ps | sort-by cpu | reverse | first 5 | each { |proc|
    $"  ($proc.name): ($proc.cpu)%"
  }

  # Memory intensive processes
  print "\nTop Memory processes:"
  ps | sort-by mem | reverse | first 5 | each { |proc|
    $"  ($proc.name): ($proc.mem)"
  }

  # Disk usage
  print "\nDisk usage:"
  sys | get disks | each { |disk|
    let usage = ($disk.used / $disk.total * 100 | math round)
    $"  ($disk.mount): ($usage)% used"
  }
}
```

**Exercise:** Create a function that analyzes disk usage by directory and reports the top space consumers.


---

## Chapter 4: File System and I/O Operations {#chapter-4}

### Navigation and Directory Operations

Nushell provides familiar navigation with structured enhancements:

```nu
# Basic navigation
cd ~/Documents      # Change directory
pwd                 # Current directory
ls                  # List with rich information
ls -la              # Include hidden files

# Directory operations
mkdir new_project
mkdir -p deep/nested/structure  # Create parent directories
rmdir empty_directory
rm -rf directory_with_content   # Recursive removal
```

**Key difference:** Unlike traditional shells, `ls` returns structured data you can immediately query:

```nu
# Traditional shell workflow
ls -la | grep ".txt" | wc -l

# Nushell workflow
ls | where name ends-with ".txt" | length
```

### File Operations

Handle files with clear, safe operations:

```nu
# Copy and move
cp source.txt backup.txt
cp -r source_dir backup_dir
mv old_name.txt new_name.txt

# Safe removal with confirmation
rm important_file.txt
rm -i *.txt          # Interactive removal

# File information
ls | where name == "myfile.txt" | first
stat myfile.txt      # Detailed file stats
```

### Reading and Writing Files

Nushell automatically handles common file formats:

```nu
# Text files
open readme.txt                    # Read as string
open readme.txt | lines           # Read as list of lines
"Hello, World!" | save greeting.txt

# Structured data (automatic parsing)
open config.json                  # Automatically parsed JSON
open data.csv                     # Automatically parsed CSV
open settings.yaml                # Automatically parsed YAML

# Save structured data
{name: "Alice", age: 30} | save person.json
[{a: 1}, {a: 2}] | save data.csv
```

**Power feature:** Nushell recognizes file extensions and automatically parses/formats data. No more manual `jq` or CSV parsing!

### Working with Different Data Formats

Convert between formats effortlessly:

```nu
# Format conversion
open data.json | to yaml | save data.yaml
open data.csv | to json | save data.json
open config.toml | to nuon | save config.nuon

# Handle format errors gracefully
try {
  open potentially_invalid.json
} catch { |err|
  print $"JSON parsing failed: ($err.msg)"
  {}  # Return empty record
}
```

### File Content Analysis

Analyze file contents with structured approaches:

```nu
# Analyze text files
open large_text.txt | lines | length           # Count lines
open log.txt | lines | where $it =~ "ERROR"   # Find error lines
open data.txt | lines | each { str length } | math avg  # Average line length

# Analyze structured data
open sales.json | length                       # Count records
open sales.json | get price | math sum        # Sum prices
open users.csv | group-by role | transpose role users |
  each { {role: $in.role, count: ($in.users | length)} }
```

### Directory Analysis

Get insights about directory structures:

```nu
# Size analysis
ls | select name size | sort-by size | reverse
ls **/* | where type == "file" | get size | math sum  # Total size recursively

# File type distribution
ls **/* | where type == "file" |
  each { |file| $file.name | path parse | get extension } |
  group-by { $in } | transpose ext files |
  each { {extension: $in.ext, count: ($in.files | length)} } |
  sort-by count | reverse

# Find large files
ls **/* | where type == "file" and size > 100mb |
  select name size | sort-by size | reverse
```

### Batch File Operations

Process multiple files efficiently:

```nu
# Process all JSON files
ls | where name ends-with ".json" | each { |file|
  let data = open $file.name
  # Process data here
  $data | save $"processed_($file.name)"
}

# Rename files based on pattern
ls | where name starts-with "temp_" | each { |file|
  let new_name = $file.name | str replace "temp_" "final_"
  mv $file.name $new_name
}

# Convert file encodings or formats
ls | where name ends-with ".csv" | each { |file|
  open $file.name | to json | save ($file.name | str replace ".csv" ".json")
}
```

### File Watching and Monitoring

Monitor file system changes:

```nu
# Watch directory for changes (if available)
watch . { |op, path, new_path|
  match $op {
    "Create" => print $"Created: ($path)",
    "Write" => print $"Modified: ($path)",
    "Remove" => print $"Deleted: ($path)",
    _ => print $"Other operation on ($path)"
  }
}
```

### Practical Example: Log File Analysis

Analyze web server logs:

```nu
def analyze_logs [log_file: string] {
  let logs = open $log_file | lines |
    where $it !~ "^#" |  # Skip comments
    each { |line|
      # Parse common log format: IP - - [timestamp] "method path protocol" status size
      let parts = $line | parse '{ip} - - [{timestamp}] "{method} {path} {protocol}" {status} {size}'
      if ($parts | length) > 0 { $parts | first } else { null }
    } |
    where $it != null

  print $"Total requests: ($logs | length)"

  print "\nTop 10 IP addresses:"
  $logs | group-by ip | transpose ip requests |
    each { {ip: $in.ip, count: ($in.requests | length)} } |
    sort-by count | reverse | first 10

  print "\nStatus code distribution:"
  $logs | group-by status | transpose status requests |
    each { {status: $in.status, count: ($in.requests | length)} } |
    sort-by status

  print "\nMost requested paths:"
  $logs | group-by path | transpose path requests |
    each { {path: $in.path, count: ($in.requests | length)} } |
    sort-by count | reverse | first 10
}

# Usage
analyze_logs "access.log"
```

### Cookbook Recipe: File Processing Pipeline

```nu
# Process multiple files with error handling
def process_files [pattern: string, action: closure] {
  ls | where name =~ $pattern | each { |file|
    try {
      let result = do $action $file
      {file: $file.name, status: "success", result: $result}
    } catch { |err|
      {file: $file.name, status: "error", error: $err.msg}
    }
  }
}

# Usage examples:
# process_files "\.txt$" { |file| open $file.name | lines | length }  # Count lines in text files
# process_files "\.json$" { |file| open $file.name | length }          # Count records in JSON files
```

**Exercise:** Create a function that organizes photos by date taken (using file modification time) into year/month directory structure.


---

## Chapter 5: Text Processing and Pattern Matching {#chapter-5}

### String Manipulation Basics

Nushell provides comprehensive string operations with multiple string types:

```nu
# String types
'single quotes'                    # No escape sequences
"double quotes\nwith escapes"      # C-style escapes (\n, \t, \u{1F60A})
r#'raw string with 'quotes'#       # Raw strings, flexible delimiters
`backtick string with spaces`      # Simple quoted strings
hello                              # Bare word (limited characters)
$"interpolated string: (2 + 3)"   # String interpolation

# Basic string info
"hello world" | str length              # 11
"" | str is-empty                       # true
"   spaces   " | str trim               # "spaces"
"text" | str reverse                    # "txet"

# Case conversion
"Hello World" | str downcase            # "hello world"
"hello world" | str upcase              # "HELLO WORLD"
"hello world" | str title-case          # "Hello World"
"hello world" | str capitalize          # "Hello world"

# String slicing and indexing
"Hello World" | str substring 0..4      # "Hello"
"Hello World" | str substring 6..       # "World"
"abcdef" | str index-of "cd"            # 2
"text" | str chars                      # Split into character list

# Advanced string operations
"hello" | str pad-left 10 "-"           # "-----hello"
"hello" | str pad-right 10 "-"          # "hello-----"
"banana" | str replace "a" "o"          # "bonono" (first occurrence)
"banana" | str replace --all "a" "o"    # "bonono" (all occurrences)
```

### String Splitting and Joining

Break strings apart and recombine them:

```nu
# Split by delimiter
"apple,banana,cherry" | split row ","   # ["apple", "banana", "cherry"]
"one:two:three" | split row ":"         # ["one", "two", "three"]

# Split into characters
"hello" | split chars                   # ["h", "e", "l", "l", "o"]

# Split by whitespace
"hello   world   test" | split words    # ["hello", "world", "test"]

# Split lines
"line1\nline2\nline3" | lines          # ["line1", "line2", "line3"]

# Join strings
["hello", "world"] | str join " "       # "hello world"
["a", "b", "c"] | str join ", "         # "a, b, c"
```

### Pattern Matching and Regular Expressions

Find and match text patterns:

```nu
# Simple matching
"hello world" | str contains "world"    # true
"hello world" | str starts-with "hello" # true
"hello world" | str ends-with "world"   # true

# Regular expressions
"hello123world" | str match '\d+'       # "123"
"email@domain.com" | str match '[\w\.]+@[\w\.]+\.\w+' # Full email

# Find all matches
"abc 123 def 456" | str match --all '\d+' # ["123", "456"]

# Test patterns
"test123" | str match '\w+\d+' != null  # true (matches pattern)
```

### String Replacement

Replace text with precision:

```nu
# Simple replacement
"hello world" | str replace "world" "Nushell"     # "hello Nushell"
"hello world world" | str replace --all "world" "nu" # "hello nu nu"

# Regular expression replacement
"price: $19.99" | str replace '\$(\d+\.\d+)' 'USD $1' # "price: USD 19.99"

# Multiple replacements
"hello world" | str replace "hello" "hi" | str replace "world" "there"
```

### Advanced Text Parsing

Extract structured data from text:

```nu
# Parse structured patterns
"John Doe, 30 years old" | parse "{name}, {age} years old"
# Result: [{name: "John Doe", age: "30"}]

# Parse log entries
"2024-01-15 ERROR Failed to connect" | parse "{date} {level} {message}"

# Parse multiple patterns
"Server started on port 8080" | parse "Server {action} on port {port}"
"Connection from 192.168.1.100" | parse "Connection from {ip}"

# Handle optional fields
"User: alice (admin)" | parse "User: {name} ({role})"
"User: bob" | parse "User: {name} ({role}?)"  # Role is optional
```

### Working with CSV and Structured Text

Parse common text formats:

```nu
# CSV parsing (automatic with .csv files)
"name,age,city\nAlice,30,NYC\nBob,25,LA" | from csv

# TSV (tab-separated)
"name\tage\tcity\nAlice\t30\tNYC" | from tsv

# Custom delimiter
"name|age|city\nAlice|30|NYC" | split row "\n" |
  each { split row "|" } |
  skip 1 | each { |row| {name: ($row | get 0), age: ($row | get 1), city: ($row | get 2)} }
```

### Text Formatting and Templates

Generate formatted text output:

```nu
# String interpolation
let name = "Alice"
let age = 30
$"Hello, my name is ($name) and I'm ($age) years old."

# Multi-line formatting
let report = $"
Report Summary:
--------------
Name: ($name)
Age: ($age)
Status: Active
"

# Format numbers
let price = 19.99
$"Price: $($price | into string)"

# Format dates
let now = date now
$"Current time: ($now | format date '%Y-%m-%d %H:%M:%S')"
```

### Text Analysis

Analyze text content statistically:

```nu
# Word frequency analysis
def word_frequency [text: string] {
  $text | str downcase | split words |
    group-by { $in } | transpose word occurrences |
    each { {word: $in.word, count: ($in.occurrences | length)} } |
    sort-by count | reverse
}

# Usage
open document.txt | word_frequency | first 10

# Character analysis
def analyze_text [text: string] {
  let chars = $text | split chars
  {
    total_chars: ($chars | length),
    letters: ($chars | where $it =~ '[a-zA-Z]' | length),
    digits: ($chars | where $it =~ '\d' | length),
    whitespace: ($chars | where $it =~ '\s' | length)
  }
}
```

### Practical Example: Log Processing

Process and analyze application logs:

```nu
def process_application_logs [log_file: string] {
  let entries = open $log_file | lines |
    where $it !~ '^$' |  # Skip empty lines
    each { |line|
      # Parse: [2024-01-15 10:30:00] INFO: User login successful for alice@company.com
      let parsed = $line | parse '[{timestamp}] {level}: {message}'
      if ($parsed | length) > 0 {
        $parsed | first
      } else {
        null
      }
    } |
    where $it != null

  print $"Processed ($entries | length) log entries\n"

  # Error analysis
  let errors = $entries | where level == "ERROR"
  print $"Errors found: ($errors | length)"
  if ($errors | length) > 0 {
    print "Most common errors:"
    $errors | group-by message | transpose error entries |
      each { {error: $in.error, count: ($in.entries | length)} } |
      sort-by count | reverse | first 5
  }

  # Timeline analysis
  print "\nActivity by hour:"
  $entries | each { |entry|
    let hour = $entry.timestamp | str replace ' .*' '' | split row ' ' | get 1 | split row ':' | get 0
    {hour: $hour, entry: $entry}
  } | group-by hour | transpose hour entries |
    each { {hour: $in.hour, count: ($in.entries | length)} } |
    sort-by hour
}

# Usage
process_application_logs "app.log"
```

### Regular Expression Reference

Common patterns for Nushell text processing:

```nu
# Email validation
let email_pattern = '[\w\.-]+@[\w\.-]+\.\w+'
"user@example.com" | str match $email_pattern

# Phone number extraction
let phone_pattern = '\(?(\d{3})\)?[-.\s]?(\d{3})[-.\s]?(\d{4})'
"Call me at (555) 123-4567" | str match $phone_pattern

# URL extraction
let url_pattern = 'https?://[\w\.-]+\.\w+[\w\./\?&=%]*'
"Visit https://nushell.sh for more info" | str match $url_pattern

# IP address validation
let ip_pattern = '(\d{1,3}\.){3}\d{1,3}'
"Server at 192.168.1.100 is online" | str match $ip_pattern
```

### Cookbook Recipe: Text Processing Toolkit

```nu
# Parse log files with custom patterns
def parse_logs [log_file: string, pattern: string] {
  open $log_file | lines | each { |line|
    try {
      $line | parse $pattern | first
    } catch {
      null
    }
  } | where $it != null
}

# Extract emails from text
def extract_emails [text: string] {
  $text | find --regex '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' | get match
}

# Clean and format phone numbers
def format_phone [phone: string] {
  let digits = $phone | str replace --all '[^0-9]' ''
  let clean = $digits | str replace '^1' ''

  if ($clean | str length) == 10 {
    $"(($clean | str substring 0..3)) ($clean | str substring 3..6)-($clean | str substring 6..10)"
  } else {
    $"Invalid: ($phone)"
  }
}

# Usage:
# format_phone "555-123-4567"     # "(555) 123-4567"
# format_phone "15551234567"      # "(555) 123-4567"
```

**Exercise:** Create a function that validates and formats phone numbers from various input formats into a standard format.


---

## Chapter 6: System Integration {#chapter-6}

### Running External Commands

Nushell seamlessly integrates with existing command-line tools:

```nu
# Explicit external commands (recommended)
^ls -la                    # Run system ls
^git status                # Run git
^python script.py          # Run Python script

# Implicit external (when no Nushell command exists)
git status                 # Works if git is in PATH
docker ps                  # Run Docker commands
curl "https://api.github.com/users/octocat"
```

**Best practice:** Use `^` prefix for external commands to be explicit and avoid conflicts with Nushell built-ins.

### Capturing and Processing External Output

Transform external command output into structured data:

```nu
# Capture as string
let git_output = ^git log --oneline -5

# Process line by line
^git log --oneline -5 | lines | each { |line|
  let parts = $line | split row ' '
  {hash: ($parts | first), message: ($parts | skip 1 | str join ' ')}
}

# Parse structured output
^docker ps --format "{{.ID}}\t{{.Image}}\t{{.Status}}" |
  lines | each { |line|
    let parts = $line | split row '\t'
    {id: ($parts | get 0), image: ($parts | get 1), status: ($parts | get 2)}
  }
```

### Environment Variables

Manage environment variables efficiently:

```nu
# View environment
$env                        # All environment variables
$env.PATH                   # Specific variable
$env.HOME                   # Home directory

# Set environment variables
$env.MY_VAR = "hello"
$env.PATH = ($env.PATH | append "/usr/local/bin")

# Temporary environment for command
with-env {DEBUG: "true"} {
  ^my-app --verbose
}

# Environment for external commands
$env.RUST_LOG = "debug"
^cargo test
```

### Process Management

Monitor and control processes:

```nu
# View processes as structured data
ps                          # All processes
ps | where name =~ "chrome" # Filter processes
ps | sort-by cpu | reverse  # Sort by CPU usage

# Process details
ps | where pid == 1234 | first
ps | select pid name cpu mem | first 10

# Kill processes safely
ps | where name == "runaway_app" | get pid | each { |p| kill $p }

# Background job management
^long-running-task &        # Run in background
jobs                        # List background jobs
```

### File System Monitoring

Watch for file system changes:

```nu
# Monitor directory changes (if supported)
def watch_directory [path: string] {
  print $"Watching ($path) for changes..."
  # This would use system-specific file watching
  ^inotifywait -m -e create,delete,modify $path | lines | each { |event|
    let parts = $event | split row ' '
    {
      path: ($parts | get 0),
      events: ($parts | get 1),
      filename: ($parts | get 2)
    }
  }
}
```

### Network Operations

Handle network requests and data:

```nu
# HTTP requests with structured output
http get "https://api.github.com/users/octocat"
http post "https://httpbin.org/post" {name: "test", value: 42}

# Download files
http get "https://example.com/data.json" | save local_data.json

# Check connectivity
^ping -c 4 google.com | lines | where $it =~ "time=" | length
```

### System Information

Get comprehensive system details:

```nu
# System overview
sys                         # Complete system info
sys | get host             # Host information
sys | get cpu              # CPU details
sys | get mem              # Memory usage
sys | get disks            # Disk information

# Current user and session
whoami                      # Current user
^id                        # User ID details
$env.USER                  # User from environment
```

### Date and Time Operations

Handle dates and time calculations:

```nu
# Current time
date now                    # Current timestamp
date now | format date "%Y-%m-%d %H:%M:%S"

# Date arithmetic
(date now) + 1day          # Tomorrow
(date now) - 1week         # Week ago
(date now) + 2hr           # Two hours from now

# Parse dates
"2024-01-15" | into datetime
"Jan 15, 2024" | into datetime

# Date comparisons in data
ls | where modified > ((date now) - 1day)  # Files from last day
```

### Practical Example: System Health Monitor

Create a comprehensive system monitoring script:

```nu
def system_health_check [] {
  print "=== System Health Check ==="
  print $"Timestamp: (date now | format date '%Y-%m-%d %H:%M:%S')"

  # CPU and Memory
  let system_info = sys
  let cpu_usage = $system_info | get cpu | get cpu_usage
  let memory = $system_info | get mem
  let memory_usage = ($memory.used / $memory.total * 100 | math round)

  print $"\nCPU Usage: ($cpu_usage)%"
  print $"Memory Usage: ($memory_usage)% (($memory.used | into string) / ($memory.total | into string))"

  # Disk Usage
  print "\nDisk Usage:"
  $system_info | get disks | each { |disk|
    let usage = ($disk.used / $disk.total * 100 | math round)
    $"  ($disk.mount): ($usage)% used (($disk.free | into string) free)"
  }

  # High CPU processes
  print "\nTop 5 CPU processes:"
  ps | sort-by cpu | reverse | first 5 | select name pid cpu | each { |proc|
    $"  ($proc.name) (PID: ($proc.pid)): ($proc.cpu)%"
  }

  # High memory processes
  print "\nTop 5 Memory processes:"
  ps | sort-by mem | reverse | first 5 | select name pid mem | each { |proc|
    $"  ($proc.name) (PID: ($proc.pid)): ($proc.mem)"
  }

  # Network connectivity
  print "\nNetwork Check:"
  try {
    ^ping -c 1 8.8.8.8 | lines | where $it =~ "1 received"
    if ($in | length) > 0 {
      print "  ✓ Internet connectivity OK"
    } else {
      print "  ✗ Internet connectivity issues"
    }
  } catch {
    print "  ✗ Cannot check internet connectivity"
  }

  # Load average (Unix-like systems)
  try {
    let uptime = ^uptime | lines | first
    print $"\nLoad Average: ($uptime | str match 'load average: (.*)' | first)"
  } catch {
    print "\nLoad average not available"
  }
}

# Usage
system_health_check
```

### Integration Patterns

Common patterns for integrating external tools:

```nu
# Transform external JSON output
^kubectl get pods -o json | from json | get items | each { |pod|
  {
    name: $pod.metadata.name,
    status: $pod.status.phase,
    ready: $pod.status.conditions | where type == "Ready" | first | get status
  }
}

# Process CSV from external tools
^aws ec2 describe-instances --output table |
  lines | skip 3 | each { |line|
    # Parse AWS CLI table output
    $line | split row '|' | each { str trim }
  }

# Chain external commands with Nushell processing
^find /var/log -name "*.log" -mtime -1 | lines | each { |file|
  {
    file: $file,
    size: (^stat -c %s $file | into int),
    lines: (^wc -l $file | split row ' ' | first | into int)
  }
} | sort-by size | reverse
```

### Cookbook Recipe: External Command Integration

```nu
# Git repository analysis
def git_stats [] {
  {
    branch: (^git branch --show-current),
    commits_today: (^git log --since="midnight" --oneline | lines | length),
    total_commits: (^git rev-list --all --count | into int),
    contributors: (^git log --format="%an" | lines | uniq | length),
    file_changes: (^git diff --stat | lines | length)
  }
}

# Docker container monitoring
def docker_monitor [] {
  ^docker ps --format "{{.ID}}\t{{.Image}}\t{{.Status}}\t{{.Names}}" |
  lines | each { |line|
    let parts = $line | split row '\t'
    {
      id: ($parts | get 0),
      image: ($parts | get 1),
      status: ($parts | get 2),
      name: ($parts | get 3)
    }
  }
}

# System service monitoring
def service_health [] {
  # Check critical services
  ["nginx", "postgresql", "redis"] | each { |service|
    let status = try { ^systemctl is-active $service } catch { "unknown" }
    {service: $service, status: $status}
  }
}
```

**Exercise:** Create a function that monitors a web service by periodically checking its health endpoint and logging response times and status codes.


---

## Chapter 7: Advanced Data Processing {#chapter-7}

### Complex Data Transformations

Nushell excels at transforming data through composition of simple operations:

```nu
# Pivot tables
ls | group-by type | transpose type files | each { |group|
  {
    type: $group.type,
    count: ($group.files | length),
    total_size: ($group.files | get size | math sum),
    avg_size: ($group.files | get size | math avg)
  }
}

# Data enrichment
ps | each { |process|
  $process | insert cpu_category (
    if $process.cpu > 50 { "high" }
    else if $process.cpu > 10 { "medium" }
    else { "low" }
  )
} | group-by cpu_category
```

### Reduce and Accumulation

Use `reduce` for complex aggregations:

```nu
# Calculate running totals
[1, 2, 3, 4, 5] | reduce -f [] { |item, acc|
  $acc | append ($acc | math sum + $item)
}

# Build complex data structures
["apple", "banana", "cherry"] | reduce -f {} { |item, acc|
  $acc | insert $item ($item | str length)
}

# Find max/min with additional context
ls | reduce -f {name: "", size: 0} { |file, largest|
  if $file.size > $largest.size {
    {name: $file.name, size: $file.size}
  } else {
    $largest
  }
}
```

### Working with Nested Data

Handle complex nested structures effectively:

```nu
# Access nested fields
let data = {
  users: [
    {name: "Alice", profile: {age: 30, city: "NYC"}},
    {name: "Bob", profile: {age: 25, city: "LA"}}
  ]
}

$data | get users | select name profile.age profile.city

# Flatten nested structures
$data | get users | each { |user|
  {
    name: $user.name,
    age: $user.profile.age,
    city: $user.profile.city
  }
}

# Deep filtering
$data | get users | where profile.age > 28
```

### Data Validation and Cleaning

Ensure data quality through validation:

```nu
# Validate required fields
def validate_users [users: list] {
  $users | each { |user|
    let errors = []
    let errors = if ($user.name | is-empty) {
      $errors | append "Name is required"
    } else { $errors }
    let errors = if ($user.age | describe) != "int" {
      $errors | append "Age must be an integer"
    } else { $errors }

    $user | insert validation_errors $errors
  } | where ($in.validation_errors | length) == 0
}

# Clean and normalize data
def clean_phone_numbers [data: list] {
  $data | each { |record|
    let clean_phone = $record.phone |
      str replace --all "[^0-9]" "" |  # Remove non-digits
      str replace '^1' ""              # Remove country code

    $record | update phone $clean_phone
  }
}
```

### Advanced Filtering Techniques

Build complex filters:

```nu
# Multi-condition filtering with computed values
ps | where (cpu > 10) and (mem > ($env.available_memory? | default 1000000)) and (name !~ "system")

# Filter with statistical conditions
let avg_cpu = ps | get cpu | math avg
ps | where cpu > ($avg_cpu * 1.5)  # Processes using 50% more than average

# Dynamic filtering based on data
def filter_outliers [data: list, field: string, multiplier: float = 1.5] {
  let avg = $data | get $field | math avg
  let threshold = $avg * $multiplier
  $data | where ($in | get $field) <= $threshold
}
```

### Error Handling Strategies

Handle errors gracefully in data processing pipelines:

```nu
# Graceful error handling in transformations
def safe_process_files [directory: string] {
  ls $directory | each { |file|
    try {
      let content = open $file.name
      let processed = $content | some_complex_transformation
      {file: $file.name, status: "success", result: $processed}
    } catch { |err|
      {file: $file.name, status: "error", error: $err.msg}
    }
  }
}

# Continue processing despite errors
def process_with_fallbacks [data: list] {
  $data | each { |item|
    try {
      primary_processing $item
    } catch {
      try {
        fallback_processing $item
      } catch { |err|
        {item: $item, status: "failed", error: $err.msg}
      }
    }
  }
}
```

### Performance Optimization

Optimize data processing for large datasets:

```nu
# Stream processing for large files
def process_large_log [file: string] {
  # Process in chunks to avoid memory issues
  open $file | lines |
    enumerate |
    group-by {|row| ($row.index / 1000 | math floor)} |
    each { |chunk|
      $chunk.items | each { |line|
        # Process individual line
        $line.item | parse_log_line
      }
    } | flatten
}

# Efficient filtering before expensive operations
def analyze_expensive_data [data: list] {
  $data |
    where relevant_field == "target_value" |  # Filter early
    each { |item| expensive_computation $item } |
    sort-by result |
    first 10
}
```

### Parallel Processing Patterns

Handle concurrent operations:

```nu
# Process multiple files concurrently (conceptual)
def process_files_parallel [files: list] {
  $files | each { |file|
    # Each file processing could be done in parallel
    {
      file: $file.name,
      result: (process_single_file $file),
      timestamp: (date now)
    }
  }
}
```

### Practical Example: Data Pipeline

Build a comprehensive data processing pipeline:

```nu
def sales_analysis_pipeline [data_dir: string, output_file: string] {
  print "Starting sales analysis pipeline..."

  # 1. Data ingestion
  let raw_data = ls $data_dir | where name =~ "sales.*\.csv$" | each { |file|
    try {
      let data = open $file.name
      $data | insert source_file $file.name
    } catch { |err|
      print $"Warning: Could not process ($file.name): ($err.msg)"
      []
    }
  } | flatten

  print $"Loaded ($raw_data | length) sales records from ($ls $data_dir | where name =~ 'sales.*\.csv$' | length) files"

  # 2. Data validation and cleaning
  let clean_data = $raw_data | each { |record|
    # Validate required fields
    if ($record.amount? == null) or ($record.date? == null) or ($record.product? == null) {
      null
    } else {
      # Clean and normalize
      $record |
        update amount ($record.amount | into float) |
        update date ($record.date | into datetime) |
        update product ($record.product | str trim | str title-case)
    }
  } | where $it != null

  print $"Cleaned data: ($clean_data | length) valid records"

  # 3. Data analysis
  let analysis = {
    # Total sales
    total_revenue: ($clean_data | get amount | math sum),

    # Sales by product
    product_analysis: (
      $clean_data | group-by product | transpose product sales | each { |group|
        {
          product: $group.product,
          total_sales: ($group.sales | get amount | math sum),
          order_count: ($group.sales | length),
          avg_order: ($group.sales | get amount | math avg)
        }
      } | sort-by total_sales | reverse
    ),

    # Monthly trends
    monthly_trends: (
      $clean_data | each { |record|
        $record | insert month ($record.date | format date "%Y-%m")
      } | group-by month | transpose month sales | each { |group|
        {
          month: $group.month,
          revenue: ($group.sales | get amount | math sum),
          orders: ($group.sales | length)
        }
      } | sort-by month
    ),

    # Summary statistics
    statistics: {
      avg_order_value: ($clean_data | get amount | math avg),
      median_order_value: ($clean_data | get amount | math median),
      total_orders: ($clean_data | length),
      date_range: {
        from: ($clean_data | get date | math min),
        to: ($clean_data | get date | math max)
      }
    }
  }

  # 4. Generate report
  print "\n=== Sales Analysis Report ==="
  print $"Total Revenue: $($analysis.statistics.total_orders)"
  print $"Total Orders: ($analysis.statistics.total_orders)"
  print $"Average Order Value: $($analysis.statistics.avg_order_value | math round --precision 2)"

  print "\nTop 5 Products by Revenue:"
  $analysis.product_analysis | first 5 | each { |product|
    print $"  ($product.product): $($product.total_sales | math round --precision 2) (($product.order_count) orders)"
  }

  # 5. Save detailed results
  $analysis | save $output_file
  print $"\nDetailed analysis saved to ($output_file)"
}

# Usage
sales_analysis_pipeline "data/sales" "sales_report.json"
```

### Cookbook Recipe: Advanced Data Analysis

```nu
# Data quality assessment
def assess_data_quality [data: list] {
  let total_records = $data | length
  let columns = $data | first | columns

  {
    total_records: $total_records,
    column_analysis: (
      $columns | each { |col|
        let values = $data | get $col
        let non_null = $values | where $it != null
        let unique_count = $non_null | uniq | length

        {
          column: $col,
          missing_count: ($total_records - ($non_null | length)),
          missing_percent: (($total_records - ($non_null | length)) / $total_records * 100 | math round),
          unique_values: $unique_count,
          data_types: ($non_null | each { describe } | uniq)
        }
      }
    ),
    duplicate_records: (
      $data | group-by { $in | to json } |
      where { ($in | length) > 1 } | length
    )
  }
}

# Statistical outlier detection
def find_outliers [data: list, column: string] {
  let values = $data | get $column | where $it != null
  let q1 = $values | math percentile 25
  let q3 = $values | math percentile 75
  let iqr = $q3 - $q1
  let lower_bound = $q1 - (1.5 * $iqr)
  let upper_bound = $q3 + (1.5 * $iqr)

  $data | where ($in | get $column) < $lower_bound or ($in | get $column) > $upper_bound
}
```

**Exercise:** Create a data quality assessment function that analyzes a dataset and reports missing values, data type inconsistencies, and potential outliers.


---

## Chapter 8: Building Real Applications {#chapter-8}

### Application Architecture

Structure larger Nushell applications using modules and clear organization:

```nu
# config.nu - Application configuration
export def get_config [] {
  {
    database_path: "app.db",
    log_level: "info",
    max_retries: 3,
    timeout: 30sec
  }
}

# utils.nu - Utility functions
export def log [level: string, message: string] {
  let timestamp = date now | format date "%Y-%m-%d %H:%M:%S"
  print $"[($timestamp)] ($level | str upcase): ($message)"
}

export def retry [times: int, command: closure] {
  mut attempt = 0
  while $attempt < $times {
    try {
      return (do $command)
    } catch { |err|
      $attempt = $attempt + 1
      if $attempt == $times {
        error make {msg: $"Failed after ($times) attempts: ($err.msg)"}
      }
      log "warn" $"Attempt ($attempt) failed: ($err.msg). Retrying..."
      sleep 1sec
    }
  }
}
```

### Command-Line Interface Design

Create professional CLI applications:

```nu
# app.nu - Main application
use config.nu *
use utils.nu *

def main [
  command: string,           # Command to execute
  --config (-c): string,     # Configuration file path
  --verbose (-v),            # Enable verbose output
  --dry-run (-n),           # Show what would be done
  ...args                   # Additional arguments
] {
  let config = if ($config | is-empty) {
    get_config
  } else {
    open $config | from json
  }

  if $verbose {
    log "info" $"Starting ($command) with config: ($config)"
  }

  match $command {
    "init" => init_command $args,
    "process" => process_command $args --dry-run=$dry_run,
    "report" => report_command $args,
    "cleanup" => cleanup_command $args --dry-run=$dry_run,
    _ => {
      print "Usage: app.nu <command> [options]"
      print "Commands: init, process, report, cleanup"
      exit 1
    }
  }
}

def init_command [args: list] {
  log "info" "Initializing application..."
  # Implementation here
}

def process_command [args: list, --dry-run] {
  log "info" $"Processing with args: ($args)"
  if $dry_run {
    print "DRY RUN: Would process the following items..."
    # Show what would be done
  } else {
    # Actual processing
  }
}
```

### Data Processing Pipeline

Build a complete data processing application:

```nu
# data_pipeline.nu
def main [
  input_dir: string,         # Input directory
  output_dir: string,        # Output directory
  --format (-f): string = "json",  # Output format
  --parallel (-p): int = 4,        # Parallel workers
  --filter: string,                # Filter expression
] {
  # Validate inputs
  if not ($input_dir | path exists) {
    error make {msg: $"Input directory does not exist: ($input_dir)"}
  }

  # Create output directory
  mkdir $output_dir

  # Discover input files
  let input_files = ls $input_dir | where type == "file" and name =~ "\.(csv|json|yaml)$"
  log "info" $"Found ($input_files | length) input files"

  # Process files
  let results = $input_files | each { |file|
    try {
      process_file $file.name $output_dir --format $format --filter $filter
    } catch { |err|
      log "error" $"Failed to process ($file.name): ($err.msg)"
      {file: $file.name, status: "error", error: $err.msg}
    }
  }

  # Generate summary report
  let summary = generate_summary $results
  $summary | save ($output_dir | path join "processing_summary.json")

  print_summary $summary
}

def process_file [input_file: string, output_dir: string, --format: string, --filter: string] {
  log "info" $"Processing ($input_file)"

  # Load data based on file extension
  let data = match ($input_file | path parse | get extension) {
    "csv" => (open $input_file),
    "json" => (open $input_file),
    "yaml" => (open $input_file),
    _ => {error make {msg: $"Unsupported file format"}}
  }

  # Apply filter if specified
  let filtered_data = if ($filter | is-empty) {
    $data
  } else {
    # Apply custom filter logic here
    $data | where ($filter | into closure)
  }

  # Transform data
  let processed_data = $filtered_data | each { |record|
    # Add processing timestamp
    $record | insert processed_at (date now)
  }

  # Save output
  let output_file = ($input_file | path parse | get stem) + "_processed." + $format
  let output_path = $output_dir | path join $output_file

  match $format {
    "json" => ($processed_data | to json | save $output_path),
    "csv" => ($processed_data | to csv | save $output_path),
    "yaml" => ($processed_data | to yaml | save $output_path),
    _ => {error make {msg: $"Unsupported output format: ($format)"}}
  }

  {
    input_file: $input_file,
    output_file: $output_path,
    records_processed: ($processed_data | length),
    status: "success"
  }
}
```

### Web Service Integration

Build applications that interact with web services:

```nu
# api_client.nu
def main [
  endpoint: string,          # API endpoint
  --method (-m): string = "GET",    # HTTP method
  --data (-d): string,              # Request data
  --headers (-h): list = [],        # Custom headers
  --output (-o): string,            # Output file
] {
  let url = build_url $endpoint
  let request_headers = build_headers $headers

  let response = try {
    match $method {
      "GET" => (http get $url --headers $request_headers),
      "POST" => (http post $url ($data | from json) --headers $request_headers),
      "PUT" => (http put $url ($data | from json) --headers $request_headers),
      "DELETE" => (http delete $url --headers $request_headers),
      _ => {error make {msg: $"Unsupported HTTP method: ($method)"}}
    }
  } catch { |err|
    log "error" $"API request failed: ($err.msg)"
    exit 1
  }

  # Process response
  let processed_response = process_api_response $response

  # Output results
  if ($output | is-empty) {
    $processed_response | to json
  } else {
    $processed_response | save $output
    log "info" $"Response saved to ($output)"
  }
}

def build_url [endpoint: string] {
  let base_url = $env.API_BASE_URL? | default "https://api.example.com"
  if ($endpoint | str starts-with "/") {
    $base_url + $endpoint
  } else {
    $base_url + "/" + $endpoint
  }
}

def process_api_response [response: record] {
  {
    timestamp: (date now),
    status: "success",
    data: $response,
    metadata: {
      response_time: "calculated_from_headers",
      content_length: ($response | to json | str length)
    }
  }
}
```

### Configuration Management

Handle application configuration properly:

```nu
# config_manager.nu
export def load_config [config_path: string = "config.json"] {
  let default_config = {
    database: {
      host: "localhost",
      port: 5432,
      name: "myapp"
    },
    logging: {
      level: "info",
      file: "app.log"
    },
    features: {
      enable_cache: true,
      max_workers: 4
    }
  }

  if ($config_path | path exists) {
    try {
      let user_config = open $config_path
      merge_config $default_config $user_config
    } catch { |err|
      log "warn" $"Could not load config file ($config_path): ($err.msg)"
      log "info" "Using default configuration"
      $default_config
    }
  } else {
    log "info" $"Config file ($config_path) not found, using defaults"
    $default_config
  }
}

def merge_config [default: record, user: record] {
  # Deep merge configuration objects
  mut result = $default
  for key in ($user | columns) {
    if ($default | get $key | describe) == "record" and ($user | get $key | describe) == "record" {
      $result = ($result | upsert $key (merge_config ($default | get $key) ($user | get $key)))
    } else {
      $result = ($result | upsert $key ($user | get $key))
    }
  }
  $result
}

export def validate_config [config: record] {
  let required_keys = ["database.host", "database.port", "logging.level"]

  for key in $required_keys {
    let keys = $key | split row "."
    let value = $keys | reduce -f $config { |k, acc| $acc | get $k }
    if ($value | is-empty) {
      error make {msg: $"Required configuration key missing: ($key)"}
    }
  }

  # Additional validation
  if ($config.database.port | into int) < 1024 {
    log "warn" "Database port is in reserved range"
  }

  true
}
```

### Testing Framework

Build tests for your Nushell applications:

```nu
# test_framework.nu
export def assert_equal [actual: any, expected: any, message: string = ""] {
  if $actual != $expected {
    let msg = if ($message | is-empty) {
      $"Assertion failed: expected ($expected), got ($actual)"
    } else {
      $"($message): expected ($expected), got ($actual)"
    }
    error make {msg: $msg}
  }
}

export def assert_true [condition: bool, message: string = ""] {
  if not $condition {
    let msg = if ($message | is-empty) {
      "Assertion failed: expected true"
    } else {
      $"($message): expected true"
    }
    error make {msg: $msg}
  }
}

export def run_tests [test_dir: string = "tests"] {
  let test_files = ls $test_dir | where name ends-with "_test.nu"

  mut results = []
  for test_file in $test_files {
    print $"Running tests in ($test_file.name)..."
    try {
      nu $test_file.name
      $results = ($results | append {file: $test_file.name, status: "passed"})
      print $"  ✓ ($test_file.name) passed"
    } catch { |err|
      $results = ($results | append {file: $test_file.name, status: "failed", error: $err.msg})
      print $"  ✗ ($test_file.name) failed: ($err.msg)"
    }
  }

  let passed = $results | where status == "passed" | length
  let failed = $results | where status == "failed" | length

  print $"\nTest Results: ($passed) passed, ($failed) failed"

  if $failed > 0 { exit 1 }
}

# Example test file: utils_test.nu
use ../utils.nu *
use test_framework.nu *

def test_log_format [] {
  # Test would go here - this is just an example structure
  assert_true true "Placeholder test"
}

def main [] {
  test_log_format
  print "All utils tests passed!"
}
```

### Complete Application Example

A file organizer application that demonstrates all concepts:

```nu
#!/usr/bin/env nu

# File Organizer - Complete Nushell Application
# Organizes files in a directory based on various criteria

use std log

def main [
  target_dir: string,        # Directory to organize
  --by (-b): string = "ext", # Organization method: ext, date, size
  --dry-run (-n),           # Show what would be done
  --config (-c): string,     # Configuration file
  --verbose (-v),           # Verbose output
] {
  let config = load_app_config $config

  if $verbose {
    log info $"Starting file organization for ($target_dir)"
    log info $"Method: ($by), Dry run: ($dry_run)"
  }

  # Validate target directory
  if not ($target_dir | path exists) {
    log error $"Directory does not exist: ($target_dir)"
    exit 1
  }

  # Get files to organize
  let files = ls $target_dir | where type == "file"
  if ($files | length) == 0 {
    log info "No files found to organize"
    exit 0
  }

  log info $"Found ($files | length) files to organize"

  # Create organization plan
  let plan = match $by {
    "ext" => (organize_by_extension $files $target_dir),
    "date" => (organize_by_date $files $target_dir),
    "size" => (organize_by_size $files $target_dir $config),
    _ => {
      log error $"Unknown organization method: ($by)"
      exit 1
    }
  }

  # Execute or preview plan
  if $dry_run {
    preview_plan $plan
  } else {
    execute_plan $plan $verbose
  }
}

def load_app_config [config_path: string] {
  let default = {
    size_categories: {
      small: 1mb,
      medium: 10mb,
      large: 100mb
    },
    date_format: "%Y/%m",
    create_subdirs: true
  }

  if ($config_path | is-empty) or not ($config_path | path exists) {
    $default
  } else {
    try {
      let user_config = open $config_path
      $default | merge $user_config
    } catch {
      log warning $"Could not load config from ($config_path), using defaults"
      $default
    }
  }
}

def organize_by_extension [files: list, base_dir: string] {
  $files | group-by { |file|
    let ext = $file.name | path parse | get extension
    if ($ext | is-empty) { "no_extension" } else { $ext }
  } | transpose extension files | each { |group|
    let target_dir = [$base_dir, $"($group.extension)_files"] | path join
    {
      category: $group.extension,
      target_dir: $target_dir,
      files: $group.files
    }
  }
}

def organize_by_date [files: list, base_dir: string] {
  $files | group-by { |file|
    $file.modified | format date "%Y-%m"
  } | transpose date files | each { |group|
    let target_dir = [$base_dir, $group.date] | path join
    {
      category: $group.date,
      target_dir: $target_dir,
      files: $group.files
    }
  }
}

def organize_by_size [files: list, base_dir: string, config: record] {
  $files | each { |file|
    let category = if $file.size < $config.size_categories.small {
      "small"
    } else if $file.size < $config.size_categories.medium {
      "medium"
    } else if $file.size < $config.size_categories.large {
      "large"
    } else {
      "huge"
    }

    $file | insert size_category $category
  } | group-by size_category | transpose category files | each { |group|
    let target_dir = [$base_dir, $"($group.category)_files"] | path join
    {
      category: $group.category,
      target_dir: $target_dir,
      files: $group.files
    }
  }
}

def preview_plan [plan: list] {
  print "=== Organization Plan (DRY RUN) ==="

  for group in $plan {
    print $"\nCategory: ($group.category)"
    print $"Target Directory: ($group.target_dir)"
    print $"Files to move: ($group.files | length)"

    $group.files | each { |file|
      print $"  ($file.name) -> ($group.target_dir)"
    }
  }

  let total_files = $plan | get files | flatten | length
  print $"\nTotal files to organize: ($total_files)"
}

def execute_plan [plan: list, verbose: bool] {
  print "=== Executing Organization Plan ==="

  mut moved_count = 0
  mut error_count = 0

  for group in $plan {
    # Create target directory
    try {
      mkdir $group.target_dir
      if $verbose {
        log info $"Created directory: ($group.target_dir)"
      }
    } catch { |err|
      log error $"Could not create directory ($group.target_dir): ($err.msg)"
      continue
    }

    # Move files
    for file in $group.files {
      try {
        let target_path = [$group.target_dir, $file.name] | path join
        mv $file.name $target_path
        $moved_count = $moved_count + 1

        if $verbose {
          log info $"Moved ($file.name) to ($target_path)"
        }
      } catch { |err|
        log error $"Could not move ($file.name): ($err.msg)"
        $error_count = $error_count + 1
      }
    }
  }

  print $"\n=== Organization Complete ==="
  print $"Files moved: ($moved_count)"
  print $"Errors: ($error_count)"

  if $error_count > 0 {
    exit 1
  }
}
```

### Complete Command Reference

**Essential Commands by Category:**

**File & Directory Operations:**
```nu
ls, cd, pwd, mkdir, rmdir, cp, mv, rm, open, save, touch
glob, which, du, path {parse, join, exists, dirname, basename}
start  # Open file with system default application
```

**Data Processing (Core Pipeline Commands):**
```nu
# Selection and Filtering
where, select, reject, get, first, last, skip, take, drop, nth

# Transformation
each, map, reduce, collect, enumerate, zip, chunks
insert, update, upsert, rename, move

# Aggregation
group-by, uniq, count, length, is-empty
transpose, pivot, flatten, wrap, unwrap

# Ordering
sort-by, reverse, shuffle, rotate

# Combining
append, prepend, merge, join, zip
```

**String Processing (str subcommands):**
```nu
# Basic operations
str length, str trim, str reverse, str upcase, str downcase
str title-case, str capitalize, str snake-case, str kebab-case

# Testing and matching
str contains, str starts-with, str ends-with, str match
str find, str index-of

# Modification
str replace, str pad-left, str pad-right, str substring
str chars, str split, str join

# Parsing
parse, split {row, column, chars, words}, lines, detect columns
```

**Math & Statistics:**
```nu
# Basic math
math {sum, avg, min, max, median, mode}
math {stddev, variance, product, abs}
math {round, floor, ceil, sqrt, log}

# Operations
+, -, *, /, mod, **, bit-and, bit-or, bit-xor
range, seq, random
```

**Data Format Conversion:**
```nu
# Input formats
from {json, yaml, csv, tsv, toml, xml, nuon, ssv, ini}

# Output formats
to {json, yaml, csv, tsv, toml, xml, nuon, html, md}

# Special formats
format, table, grid, print
```

**System Information:**
```nu
ps, sys, whoami, id, uname, date, cal, history
version, help, which, env, register
```

**Network & HTTP:**
```nu
http {get, post, put, patch, delete, head, options}
url {parse, join, encode, decode}
port  # Check if port is open
```

**Process & Job Control:**
```nu
jobs, fg, bg, disown, wait, kill, killall
sleep, watch, run-external
```

**Control Flow & Programming:**
```nu
# Conditionals
if, match, try-catch, error

# Loops
for, while, loop, break, continue, return

# Variables & Functions
let, mut, const, def, do, source, use
export, alias, register

# Iteration
each, where, reduce, fold
```

**External Integration:**
```nu
# External commands
^command, run-external, with-env

# Environment
$env, env, export, hide-env, load-env

# Modules & Scripts
use, source, overlay {new, add, remove, list}
module, export-env
```

### Power User One-Liners (From nu_scripts and Cheat Sheet)

```nu
# String manipulation one-liners
"one,two,three" | split row ","                    # Split string
[zero one two] | str join ','                       # Join with separator
'Hello World!' | str substring 4..8                # Slice string: "o Wo"
'Nushell 0.80' | parse '{shell} {version}'        # Parse into record
$"greetings, ($name)!"                            # String interpolation

# List manipulation one-liners
[foo bar baz] | insert 1 'beeze'                   # Insert at index
[1, 2, 3, 4] | update 1 10                        # Update by index
$numbers | append 4 | prepend 0                    # Chain operations
$planets | each { |elt| $"($elt) is a planet" }    # Transform each
$scores | reduce { |elt, acc| $acc + $elt }        # Sum with reduce

# Advanced data processing
ls **/* | where type == file | insert hash { |f| open $f.name | hash sha256 } | group-by hash | where { ($in | length) > 1 }  # Find duplicates

ps | where cpu > 0 | sort-by cpu | reverse         # High CPU processes

# File operations
glob **/*.{rs,toml} --depth 2                      # Find files by pattern
ls | sort-by size | last 5                         # 5 largest files
ls | where modified > ((date now) - 7day)         # Recent files

# Data conversion chains
open data.csv | where price > 100 | to json | save expensive.json

# System monitoring
1..10 | each { |i| {time: (date now), cpu: (sys | get cpu.cpu_usage)} | sleep 1sec }  # Monitor CPU over time

# Network and APIs
http get "https://api.github.com/repos/nushell/nushell" | get stargazers_count  # Get repo stars
http get $"https://wttr.in/($city)?format=j1" | get current_condition.0      # Weather data

# Text processing chains
open log.txt | lines | where $it =~ "ERROR" | length                        # Count error lines
open config.toml | to yaml | save config.yaml                               # Convert formats

# Advanced table operations
open sales.csv | group-by product | transpose product sales | each { |group|
  {product: $group.product, revenue: ($group.sales | get price | math sum)}
} | sort-by revenue | reverse                                                 # Sales analysis

# Color and formatting
$'(ansi purple_bold)Purple text!(ansi reset)'      # Colored output
[one two three] | to yaml                          # Convert to YAML
'text' | save file.txt                             # Save string to file

# Date and time manipulation
date now | date to-timezone "Europe/London"        # Timezone conversion
{name: "project", date: (date now)} | upsert language 'Rust'  # Update records
```

**Exercise:** Extend the file organizer to support custom organization rules defined in a configuration file, including regex patterns for file names and custom directory structures.

---

## Appendix B: Configuration and Customization

### Configuration Files

Nushell loads configuration in this order:
1. `env.nu` - Environment variables and paths
2. `config.nu` - Nushell settings and customizations
3. Autoload directories - Automatic module loading
4. `login.nu` - Login shell specific settings

```nu
# env.nu - Environment setup
$env.PATH = ($env.PATH | split row (char esep) | append "/usr/local/bin")
$env.EDITOR = "code"
$env.NU_LOG_LEVEL = "INFO"

# config.nu - Nushell configuration
$env.config = {
  show_banner: false,
  edit_mode: vi,
  shell_integration: true,
  buffer_editor: "code",
  table: {
    mode: rounded,
    index_mode: always,
    show_empty: true
  },
  completions: {
    case_sensitive: false,
    quick: true,
    partial: true
  }
}
```

### Custom Prompt

```nu
# In config.nu
$env.PROMPT_COMMAND = { ||
  let dir = match (pwd | path basename) {
    "" => "<root>",
    $dirname => $dirname
  }

  let git_branch = try {
    ^git branch --show-current 2>/dev/null | str trim
  } catch {
    ""
  }

  let branch_part = if ($git_branch | is-empty) {
    ""
  } else {
    $" (ansi cyan)($git_branch)(ansi reset)"
  }

  $"(ansi green)($dir)(ansi reset)($branch_part)> "
}
```

### Useful Aliases and Functions

```nu
# In config.nu
alias ll = ls -la
alias la = ls -a
alias g = git
alias k = kubectl
alias d = docker
alias c = code
alias .. = cd ..
alias ... = cd ../..

# Function-based helpers
def gst [] { git status }
def gco [branch] { git checkout $branch }
def gp [] { git push }
def gl [] { git log --oneline -10 }
```

---

## Appendix C: Modules and Code Organization

### Working with Modules

Modules help organize and reuse code across Nushell scripts and applications:

```nu
# Import entire module
use std/log
log info "Application started"

# Import specific definitions
use std/math PI
let circumference = 2 * $PI * $radius

# Import all definitions with wildcard
use std/formats *

# Import with custom naming
use my-utils as utils

# Import from file paths
use ./local_module.nu
use ../shared/common.nu
```

### Creating Modules

```nu
# math_utils.nu - Module file
export def square [x: float] {
  $x * $x
}

export def cube [x: float] {
  $x * $x * $x
}

export const PI = 3.14159

export def-env set_math_mode [] {
  $env.MATH_PRECISION = "high"
}
```

### Module Organization Best Practices

```nu
# Project structure
project/
├── main.nu          # Main script
├── config/
│   └── settings.nu  # Configuration module
├── utils/
│   ├── files.nu     # File utilities
│   ├── network.nu   # Network utilities
│   └── math.nu      # Math utilities
└── tests/
    └── test_utils.nu

# In main.nu
use config/settings
use utils/files *
use utils/network as net

def main [] {
  let config = settings get_config
  let files = list_files "./data"
  let response = net fetch_data $config.api_url
}
```

### Working with Overlays

Overlays provide dynamic environment management:

```nu
# Create and manage overlays
overlay new my-env              # Create new overlay
overlay use my-env              # Activate overlay
overlay list                    # List active overlays

# Overlay from module
overlay use std/math           # Import module as overlay
overlay hide std/math          # Deactivate overlay

# Scoped overlays
overlay use --scoped my-tools {
  # Commands available only in this block
  special-command "data"
}

# Overlay with prefixes
overlay use utils as util-     # Prefix all commands with 'util-'
```


---

## Conclusion

You now have a comprehensive understanding of Nushell's core concepts and practical applications. The key insights to remember:

1. **Structured Data First**: Everything in Nushell is structured data, making complex data processing natural and composable.

2. **Type Safety**: Strong typing prevents common shell scripting errors and provides better error messages.

3. **Functional Approach**: Prefer immutable data and functional transformations over imperative operations.

4. **Pipeline Composition**: Build complex operations by composing simple, focused commands.

5. **Integration**: Nushell works alongside existing tools while providing better data handling.

**Next Steps:**
- Practice with real data processing tasks
- Explore the Nushell standard library
- Join the Nushell community for advanced techniques
- Build your own modules and applications

Nushell transforms shell programming from text manipulation to structured data processing, making it more reliable, maintainable, and powerful for modern computing tasks.

## Appendix A: Command Quick Reference

### Most Used Commands (90% of daily usage)
```nu
# Navigation & Files
ls, cd, pwd, open, save

# Data Processing
where, select, sort-by, group-by, first, last, length

# Transformation
each, reduce, append, insert, update

# Text Processing
str trim, str replace, lines, parse, split row

# System
ps, sys, help, which

# Math
math sum, math avg, math max, math min
```

### Advanced Commands (for power users)
```nu
# Complex Data Operations
transpose, flatten, wrap, unwrap, zip, enumerate, collect

# External Integration
^command, with-env, jobs, fg, bg

# Network & APIs
http get, http post, url parse

# File Formats
from/to: json, csv, yaml, toml, xml, ssv, nuon

# Module System
use, source, export, overlay {new, use, hide, list}

# Advanced Operators
bit-and, bit-or, bit-xor, bit-shl, bit-shr
starts-with, ends-with, =~, !~
```

### Tips for Transitioning from Other Shells

**From Bash/Zsh:**
- Use `ls | where name =~ pattern` instead of `ls | grep pattern`
- Use `ps | where name == "process"` instead of `ps aux | grep process`
- Use `open file.json` instead of `cat file.json | jq`

**From PowerShell:**
- Nushell's objects are similar but syntax is more functional
- Use `|` for pipelines, `{ }` for blocks
- `where` and `select` work similarly

**From Fish:**
- Nushell has similar modern features but with structured data
- Completions work automatically for most commands
- Configuration is more programmatic

## Appendix D: Common Patterns

```nu
# Pattern: Safe file processing
ls | where type == file and name =~ pattern | each { |file|
  try {
    # process file
    {file: $file.name, status: "success"}
  } catch { |err|
    {file: $file.name, status: "error", error: $err.msg}
  }
}

# Pattern: Data aggregation
data | group-by category | transpose category items | each { |group|
  {
    category: $group.category,
    count: ($group.items | length),
    total: ($group.items | get value | math sum)
  }
}

# Pattern: Configuration with defaults
def load_config [path: string = "config.json"] {
  let defaults = {setting1: "default", setting2: 42}
  if ($path | path exists) {
    $defaults | merge (open $path)
  } else {
    $defaults
  }
}
```