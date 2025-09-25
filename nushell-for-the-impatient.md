# Nushell for the Impatient
*Master CLI Commands and Programming Fundamentals Fast*

**Version 2.0**
**2024**

---

## Table of Contents

**Part I: Essential CLI Commands - Master Every Command (Pages 1-25)**
1. [Quick Start: Your First 5 Minutes](#quick-start)
2. [File Operations That Matter](#file-operations)
3. [Directory Navigation & Management](#directory-nav)
4. [Data Wrangling Essentials](#data-wrangling)
5. [Text Processing Power Tools](#text-processing)
6. [System Information Commands](#system-info)
7. [Process & Network Commands](#process-network)
8. [Date, Time & Math Operations](#date-time-math)
9. [Advanced Filtering & Sorting](#advanced-filtering)
10. [Data Import/Export Commands](#data-import-export)

**Part II: Programming Fundamentals - Every Detail Explained (Pages 26-40)**
11. [Variables and Data Types Deep Dive](#variables-types)
12. [Control Flow: If, Match, Loops Mastery](#control-flow)
13. [Functions and Custom Commands Complete Guide](#functions-commands)
14. [Error Handling the Nushell Way](#error-handling)
15. [Working with External Commands](#external-commands)
16. [Closures, Pipelines & Advanced Patterns](#advanced-patterns)

---

# Part I: Essential CLI Commands - Master Every Command

## Quick Start: Your First 5 Minutes {#quick-start}

### What Makes Nushell Different - The Revolutionary Approach

**Stop thinking text streams. Start thinking structured data.**

Nushell isn't just another shell - it's a complete paradigm shift. Every command returns structured data (tables, records, lists) instead of text. This means you can filter, sort, and manipulate data like a database query language.

```nu
# OLD WAY (bash/zsh): Parse text output with grep/awk/sed
ps aux | grep chrome | awk '{print $2, $11}' | head -5

# NEW WAY (nushell): Query structured data directly
ps | where name =~ "chrome" | select pid name | first 5
```

**What this means:** No more parsing text with regex. No more awk scripts. No more sed commands. Just pure data manipulation.

### Installation - Get Nushell Running Now

**macOS - Homebrew (recommended):**
```bash
brew install nushell
# PURPOSE: Installs latest stable Nushell via Homebrew package manager
```

**Windows - Package Manager:**
```bash
winget install nushell
# PURPOSE: Installs Nushell via Windows Package Manager
```

**Linux - Multiple Options:**
```bash
# Cargo (Rust package manager) - works on all distros
cargo install nu
# PURPOSE: Compiles and installs latest Nushell from source

# Flatpak - if you prefer sandboxed apps
flatpak install flathub org.nushell.Nu
# PURPOSE: Installs Nushell as a Flatpak application

# Download binary directly
curl -LO https://github.com/nushell/nushell/releases/latest/download/nu-linux-x64.tar.gz
tar -xzf nu-linux-x64.tar.gz
# PURPOSE: Download pre-compiled binary for immediate use
```

### Your First Commands - Essential Startup Sequence

```nu
# Start nushell (type this in your current shell)
nu
# PURPOSE: Launch Nushell interactive session

# Get help - your lifeline when stuck
help
# PURPOSE: Display all available help categories and basic usage

# List ALL commands by category - overwhelming but comprehensive
help commands
# PURPOSE: Show every single command Nushell offers, organized by function

# Get detailed help for any specific command
help ls
# PURPOSE: Show complete documentation, examples, and parameters for 'ls'

# Search help topics when you don't know the exact command
help --find "file"
# PURPOSE: Find all commands and topics related to "file" operations

# Show your current Nushell version and build info
version
# PURPOSE: Display Nushell version, build date, and Rust version used
```

### The Power of Structured Data - See It In Action

**Every command returns a table or structured data:**

```nu
# See ALL processes as a structured table (not text!)
ps
# PURPOSE: List all running processes with columns for pid, name, cpu, mem, etc.

# Filter processes using SQL-like syntax
ps | where cpu > 10
# PURPOSE: Show only processes consuming more than 10% CPU

# Sort by any column - multiple sorting options
ps | sort-by cpu          # Ascending by CPU usage
ps | sort-by -r cpu       # Descending by CPU usage
ps | sort-by mem cpu      # Sort by memory, then CPU
# PURPOSE: Order process list by resource usage for monitoring

# Get specific columns only - like SELECT in SQL
ps | select name cpu mem
# PURPOSE: Show only process name, CPU, and memory columns

# Chain operations - the real power
ps | where cpu > 5 | select name cpu | sort-by -r cpu | first 10
# PURPOSE: Find top 10 CPU-intensive processes above 5% usage

# Get detailed info about the data structure itself
ps | describe
# PURPOSE: Show the schema/structure of the process table data
```

**File listing with structured data:**

```nu
# Basic file listing - returns a table, not text
ls
# PURPOSE: List files and directories with size, type, modified date as columns

# Get file details with full information
ls -l
# PURPOSE: Long format listing with permissions, owner, size, dates

# Show hidden files too
ls -a
# PURPOSE: Include hidden files (starting with .) in the listing

# Combine flags for maximum information
ls -la
# PURPOSE: Long format including hidden files

# Recursive listing of ALL files
ls **/*
# PURPOSE: List every file in current directory and all subdirectories

# Filter files by extension using structured data
ls | where name =~ "\.rs$"
# PURPOSE: Show only Rust files (.rs extension) using regex pattern matching

# Sort files by size to find largest files
ls | sort-by size
# PURPOSE: Order files from smallest to largest

# Get total directory size
ls | get size | math sum
# PURPOSE: Add up all file sizes in current directory

# Find files modified in last day
ls | where modified > (date now) - 1day
# PURPOSE: Show files changed within the last 24 hours
```

## File Operations That Matter {#file-operations}

### File Listing Commands - Every Variation Explained

**Basic file listing with extensive options:**

```nu
# Standard listing - shows name, type, size, modified
ls
# PURPOSE: Basic file listing in table format with essential info

# Long format with detailed permissions and metadata
ls -l
# PURPOSE: Show permissions, owner, group, size, modification time

# Show hidden files (starting with .)
ls -a
# PURPOSE: Include hidden files and directories in listing

# Show ALL files including system files
ls -la
# PURPOSE: Long format plus hidden files for complete directory view

# Recursive listing - every file in subdirectories
ls **/*
# PURPOSE: List all files in current directory tree recursively

# List specific directory without changing location
ls /home/user/Documents
# PURPOSE: Examine contents of another directory remotely

# List with human-readable sizes
ls | select name size | sort-by size
# PURPOSE: Show files with sizes, sorted from smallest to largest

# List only directories
ls | where type == dir
# PURPOSE: Filter listing to show directories only

# List only regular files
ls | where type == file
# PURPOSE: Filter listing to show regular files only

# List files by extension
ls | where name =~ "\.txt$"    # Text files
ls | where name =~ "\.rs$"     # Rust files
ls | where name =~ "\.py$"     # Python files
# PURPOSE: Filter files by extension using regex patterns

# List files larger than specific size
ls | where size > 1mb
ls | where size > 1kb
ls | where size > 1gb
# PURPOSE: Find files above certain size threshold

# List recently modified files
ls | where modified > (date now) - 7day
# PURPOSE: Show files modified within last week

# List files with specific permissions (if on Unix)
ls -l | where permissions =~ "rwx"
# PURPOSE: Find executable files based on permission string
```

**Advanced file queries - SQL-like operations:**

```nu
# Count files by extension
ls | where type == file | group-by { $in.name | path parse | get extension } | transpose ext files | each { {extension: $in.ext, count: ($in.files | length)} }
# PURPOSE: Create statistics showing file count per extension type

# Find duplicate files by size
ls | where type == file | group-by size | where {($in | get files | length) > 1}
# PURPOSE: Identify files that have the same size (potential duplicates)

# Directory size summary
ls | where type == dir | each { |dir| {name: $dir.name, size: (ls $dir.name | get size | math sum)} }
# PURPOSE: Calculate total size of each subdirectory

# File age analysis
ls | where type == file | each { |file| {name: $file.name, age_days: ((date now) - $file.modified) / 1day} } | sort-by age_days
# PURPOSE: Show how old each file is in days, oldest first
```

### Directory Navigation & Management {#directory-nav}

**Navigation commands - move around efficiently:**

```nu
# Change to specific directory
cd /home/user/projects
# PURPOSE: Move to absolute path location

# Go to home directory
cd ~
cd           # Both work the same
# PURPOSE: Navigate to user's home directory quickly

# Go up one directory level
cd ..
# PURPOSE: Move to parent directory

# Go up multiple levels
cd ../..
cd ../../..
# PURPOSE: Move up directory tree efficiently

# Return to previous directory
cd -
# PURPOSE: Toggle between current and previous directory (like bash)

# Show current working directory
pwd
# PURPOSE: Display full path of current location

# Go to root directory
cd /
# PURPOSE: Navigate to filesystem root

# Navigate using environment variables
cd $env.HOME
cd $env.TEMP
# PURPOSE: Use environment variables for common locations
```

**Directory creation - make directories efficiently:**

```nu
# Create single directory
mkdir new-folder
# PURPOSE: Create one directory in current location

# Create directory with spaces in name (quote it)
mkdir "My Documents"
# PURPOSE: Create directory with spaces using quotes

# Create nested directories all at once
mkdir -p deep/nested/folder/structure
# PURPOSE: Create entire directory path, including parents if needed

# Create multiple directories simultaneously
mkdir project1 project2 project3
# PURPOSE: Create several directories in one command

# Create directories with specific permissions (Unix/Linux)
mkdir -m 755 public-folder
# PURPOSE: Create directory with specific permission bits

# Create directory and immediately navigate to it
mkdir new-project; cd new-project
# PURPOSE: Create and enter directory in one operation
```

**Directory operations - copy, move, remove:**

```nu
# Copy single directory
cp -r source-dir destination-dir
# PURPOSE: Recursively copy directory and all contents

# Copy directory contents only (not the directory itself)
cp -r source-dir/* destination-dir/
# PURPOSE: Copy files from source into destination, merge contents

# Move/rename directory
mv old-name new-name
mv directory /new/location/
# PURPOSE: Rename directory or move to different location

# Remove empty directory
rmdir empty-folder
# PURPOSE: Remove directory only if it's empty (safe operation)

# Remove directory and all contents (DANGEROUS!)
rm -rf directory-name
# PURPOSE: Recursively delete directory and everything inside

# Remove multiple directories
rm -rf dir1 dir2 dir3
# PURPOSE: Delete multiple directories and their contents

# Interactive removal (asks for confirmation)
rm -i -rf directory-name
# PURPOSE: Prompt before deleting each file (safer)
```

**Directory information and analysis:**

```nu
# Check if directory exists
ls | where name == "folder-name" and type == dir
# PURPOSE: Verify a directory exists in current location

# Count items in directory
ls some-directory | length
# PURPOSE: Count total files and subdirectories

# Get directory size (total of all files)
ls some-directory | get size | math sum
# PURPOSE: Calculate total disk space used by directory

# Find largest directories
ls | where type == dir | each { |dir| {name: $dir.name, size: (ls $dir.name | get size | math sum)} } | sort-by size
# PURPOSE: Identify which subdirectories use most disk space

# Directory tree view (if tree command available)
tree directory-name
# PURPOSE: Show hierarchical structure of directory contents
```

## Data Wrangling Essentials {#data-wrangling}

### Understanding Nushell Data Types - The Foundation

**Core data types - know these inside and out:**

```nu
# Integers - whole numbers
42
-17
1000000
# PURPOSE: Represent whole numbers for counting, indexing, math

# Floats - decimal numbers
3.14159
-0.5
1.0e6        # Scientific notation
# PURPOSE: Represent decimal values for calculations, measurements

# Strings - text data
"hello world"
'single quotes work too'
"embedded \"quotes\" need escaping"
$"interpolated string with (42) values"
# PURPOSE: Store and manipulate text data, file paths, user input

# Booleans - true/false values
true
false
# PURPOSE: Represent binary states, conditions, flags

# Lists - ordered collections
[1, 2, 3, 4, 5]
["apple", "banana", "cherry"]
[1, "mixed", true, 3.14]       # Mixed types allowed
[]                             # Empty list
# PURPOSE: Store ordered sequences of items for iteration, storage

# Records - key-value pairs (like objects/maps)
{name: "John", age: 30, city: "New York"}
{x: 10, y: 20, z: 30}
{}                             # Empty record
# PURPOSE: Structure related data together, represent entities

# Tables - list of records with consistent schema
[
  {name: "Alice", age: 25, dept: "Engineering"},
  {name: "Bob", age: 32, dept: "Marketing"},
  {name: "Carol", age: 28, dept: "Engineering"}
]
# PURPOSE: Represent tabular data like databases, CSV files, spreadsheets

# Null values - represent missing data
null
# PURPOSE: Indicate absence of data, empty results
```

**Type inspection and conversion:**

```nu
# Check what type something is
42 | describe                  # Returns "int"
"hello" | describe            # Returns "string"
[1, 2, 3] | describe         # Returns "list<int>"
{name: "test"} | describe     # Returns "record"
# PURPOSE: Understand data structure to choose appropriate operations

# Convert between types
"42" | into int              # String to integer
42 | into string             # Integer to string
"true" | into bool           # String to boolean
"3.14" | into float          # String to float
[1, 2, 3] | into string      # List to string representation
# PURPOSE: Transform data into required format for operations

# Parse structured data from strings
'{"name": "John", "age": 30}' | from json
"name,age\nJohn,30\nJane,25" | from csv
# PURPOSE: Convert external data formats into Nushell structures
```

### Core Data Commands - The Essential Toolkit

**Length and counting operations:**

```nu
# Get length of various data types
[1, 2, 3, 4, 5] | length                    # List length: 5
"hello world" | str length                   # String length: 11
{name: "John", age: 30} | length            # Record fields: 2
ls | length                                  # Count files in directory
# PURPOSE: Count items, characters, fields for validation and iteration

# Count specific conditions
ls | where type == file | length            # Count files only
ls | where size > 1mb | length              # Count large files
[1, 2, 3, 2, 1] | uniq | length            # Count unique items
# PURPOSE: Get statistics about filtered data sets
```

**Accessing elements - first, last, skip, take:**

```nu
# Get first elements
[1, 2, 3, 4, 5] | first                     # First element: 1
[1, 2, 3, 4, 5] | first 3                   # First 3 elements: [1, 2, 3]
ls | first 5                                 # First 5 files
# PURPOSE: Get beginning of data for preview, testing, sampling

# Get last elements
[1, 2, 3, 4, 5] | last                      # Last element: 5
[1, 2, 3, 4, 5] | last 3                    # Last 3 elements: [3, 4, 5]
ls | sort-by modified | last 5               # 5 newest files
# PURPOSE: Get end of data, find recent items, tail of logs

# Skip elements from beginning
[1, 2, 3, 4, 5] | skip 2                    # Skip first 2: [3, 4, 5]
ls | skip 10                                 # Skip first 10 files
# PURPOSE: Ignore header data, pagination, offset processing

# Take only specified number of elements
[1, 2, 3, 4, 5] | take 3                    # Take first 3: [1, 2, 3]
ps | take 10                                 # Top 10 processes
# PURPOSE: Limit output size, create samples, implement pagination

# Combine skip and take for pagination
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | skip 3 | take 4    # Elements 4-7
# PURPOSE: Implement pagination, process data in chunks
```

**Reverse and rotation:**

```nu
# Reverse order of elements
[1, 2, 3, 4, 5] | reverse                   # [5, 4, 3, 2, 1]
"hello" | split chars | reverse | str join   # "olleh"
ls | sort-by name | reverse                  # Reverse alphabetical
# PURPOSE: Change data order, reverse chronological data, flip results

# Rotate list elements
[1, 2, 3, 4, 5] | roll 2                    # [4, 5, 1, 2, 3] - roll right
[1, 2, 3, 4, 5] | roll -2                   # [3, 4, 5, 1, 2] - roll left
# PURPOSE: Shift data positions, circular permutations
```

**Sorting operations - comprehensive sorting toolkit:**

```nu
# Basic sorting
[3, 1, 4, 1, 5, 9, 2, 6] | sort             # Ascending: [1, 1, 2, 3, 4, 5, 6, 9]
[3, 1, 4, 1, 5, 9, 2, 6] | sort -r          # Descending: [9, 6, 5, 4, 3, 2, 1, 1]
["zebra", "apple", "cat"] | sort             # Alphabetical: ["apple", "cat", "zebra"]
# PURPOSE: Order data for analysis, readability, finding extremes

# Sort tables by column
ls | sort-by size                            # Sort files by size (ascending)
ls | sort-by -r size                         # Sort by size (descending)
ls | sort-by name                            # Sort files alphabetically
ls | sort-by modified                        # Sort by modification time
# PURPOSE: Order tabular data by specific field for analysis

# Multi-column sorting
ps | sort-by cpu mem                         # Sort by CPU, then by memory
ls | sort-by type name                       # Sort by type, then name
# PURPOSE: Complex sorting with primary and secondary criteria

# Sort with custom comparison
["file1.txt", "file10.txt", "file2.txt"] | sort
# Natural sorting (handles numbers in strings properly)
# PURPOSE: Human-readable sorting of alphanumeric data
```

### Filtering and Selecting Data - Master Data Queries

**Where clause - the powerhouse filter:**

```nu
# Basic filtering with comparison operators
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it > 5           # [6, 7, 8, 9, 10]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it < 5           # [1, 2, 3, 4]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it == 5          # [5]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it != 5          # [1, 2, 3, 4, 6, 7, 8, 9, 10]
# PURPOSE: Filter data based on numeric comparisons

# Filtering with logical operators
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it > 3 and $it < 8    # [4, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] | where $it < 3 or $it > 8     # [1, 2, 9, 10]
ls | where type == file and size > 1mb                           # Large files only
# PURPOSE: Complex filtering with multiple conditions

# String filtering with contains and regex
ls | where name =~ "\.txt$"                  # Files ending in .txt
ls | where name =~ "^test"                   # Files starting with "test"
ps | where name =~ "chrome"                  # Processes containing "chrome"
ps | where name !~ "kernel"                  # Processes NOT containing "kernel"
# PURPOSE: Pattern-based filtering using regular expressions

# Filtering records by field values
[{name: "Alice", age: 25}, {name: "Bob", age: 35}] | where age > 30
ps | where cpu > 10.0                       # High CPU processes
ls | where modified > (date now) - 1day     # Recently modified files
# PURPOSE: Filter structured data by field values

# Complex filtering expressions
ls | where (size > 1mb) and (modified > (date now) - 7day) and (name =~ "\.log$")
# PURPOSE: Multiple criteria filtering for precise data selection

# Filtering with custom functions
def is_even [num] { ($num mod 2) == 0 }
[1, 2, 3, 4, 5, 6] | where {|x| is_even $x}  # [2, 4, 6]
# PURPOSE: Filter using custom logic and functions
```

**Select and reject - column management:**

```nu
# Select specific columns from tables
ps | select name pid cpu mem                 # Show only these 4 columns
ls | select name size                        # Show only name and size
# PURPOSE: Reduce data to relevant fields, simplify output

# Select columns with renaming
ps | select name pid cpu_percent=cpu memory=mem
# PURPOSE: Choose columns and give them better names

# Select nested fields
[{person: {name: "John", age: 30}, city: "NYC"}] | select person.name city
# PURPOSE: Extract data from nested structures

# Reject (exclude) specific columns
ps | reject environ                          # Remove environment column
ls | reject readonly                         # Remove readonly column
# PURPOSE: Remove unwanted columns, clean up output

# Dynamic column selection
let cols = ["name", "size", "modified"]
ls | select $cols
# PURPOSE: Programmatically choose columns based on variables
```

**Get - extract specific values:**

```nu
# Get values from records
{name: "John", age: 30, city: "NYC"} | get name           # "John"
{name: "John", age: 30, city: "NYC"} | get age            # 30
# PURPOSE: Extract single values from records

# Get values from lists
["apple", "banana", "cherry"] | get 0                     # "apple"
["apple", "banana", "cherry"] | get 2                     # "cherry"
[1, 2, 3, 4, 5] | get 1..3                               # [2, 3, 4]
# PURPOSE: Access list elements by index or range

# Get values from tables
ls | get name                                # List of all file names
ps | get pid                                 # List of all process IDs
# PURPOSE: Extract column values as list

# Get nested values
{user: {profile: {name: "Alice"}}} | get user.profile.name    # "Alice"
# PURPOSE: Navigate nested data structures

# Get with default values
{name: "John"} | get age | default 0         # Returns 0 since age doesn't exist
# PURPOSE: Handle missing data gracefully with fallbacks
```

### Transforming Data - Reshape and Modify

**Each - apply operations to every element:**

```nu
# Transform each element in a list
[1, 2, 3, 4, 5] | each { |x| $x * 2 }                    # [2, 4, 6, 8, 10]
["hello", "world"] | each { |s| $s | str upcase }        # ["HELLO", "WORLD"]
# PURPOSE: Apply same operation to all elements in collection

# Transform table rows
ls | each { |file| $file.name | str upcase }              # Uppercase all filenames
ps | each { |proc| {name: $proc.name, high_cpu: ($proc.cpu > 10)} }
# PURPOSE: Transform each row of tabular data

# With index access
["a", "b", "c"] | enumerate | each { |item| $"($item.index): ($item.item)" }
# PURPOSE: Access both element and its position during transformation

# Parallel processing for CPU-intensive operations
1..1000 | par-each { |n| $n * $n }                       # Parallel squaring
# PURPOSE: Use multiple CPU cores for faster processing
```

**Update and insert - modify table structure:**

```nu
# Update existing columns
ls | update size { |row| $row.size / 1024 }              # Convert bytes to KB
ps | update cpu { |row| ($row.cpu * 100) | math round }  # CPU as percentage
# PURPOSE: Modify existing field values with calculations

# Update with conditional logic
ls | update type { |row|
  if $row.type == "file" {
    "📄 file"
  } else {
    "📁 directory"
  }
}
# PURPOSE: Replace values based on conditions

# Insert new columns
ls | insert size_mb { |row| $row.size / 1024 / 1024 }    # Add size in MB
ps | insert memory_gb { |row| $row.mem / 1024 / 1024 / 1024 }
# PURPOSE: Add computed columns to existing data

# Insert constants
ls | insert scanned_date { date now }                     # Add current timestamp
# PURPOSE: Add metadata or tracking information

# Complex transformations
ls | insert file_info { |row|
  {
    extension: ($row.name | path parse | get extension),
    is_large: ($row.size > 1mb),
    age_days: ((date now) - $row.modified) / 1day
  }
}
# PURPOSE: Add complex computed fields with multiple properties
```

**Group by - organize data:**

```nu
# Basic grouping
ls | group-by type                                        # Group files by type
ps | group-by { |proc| if $proc.cpu > 10 { "high" } else { "low" } }
# PURPOSE: Organize data into categories for analysis

# Multiple group keys
[
  {name: "Alice", dept: "Engineering", level: "Senior"},
  {name: "Bob", dept: "Engineering", level: "Junior"},
  {name: "Carol", dept: "Marketing", level: "Senior"}
] | group-by dept level
# PURPOSE: Create nested groupings with multiple criteria

# Group with aggregation
ls | group-by type | transpose type files | each { |group|
  {
    type: $group.type,
    count: ($group.files | length),
    total_size: ($group.files | get size | math sum)
  }
}
# PURPOSE: Group data and calculate statistics for each group

# Time-based grouping
ls | insert day { |file| $file.modified | format date "%Y-%m-%d" } | group-by day
# PURPOSE: Group by time periods for temporal analysis
```

**Reduce - aggregate data:**

```nu
# Sum all values
[1, 2, 3, 4, 5] | reduce { |item, acc| $acc + $item }    # 15
ls | get size | reduce { |item, acc| $acc + $item }       # Total directory size
# PURPOSE: Combine all values into single result

# Find maximum value
[3, 7, 2, 9, 1] | reduce { |item, acc| if $item > $acc { $item } else { $acc } }
# PURPOSE: Find extreme values through comparison

# Build complex data structures
["a", "b", "c"] | reduce -f {} { |item, acc| $acc | insert $item ($item | str upcase) }
# PURPOSE: Build records or accumulate complex results

# String concatenation
["Hello", "World", "!"] | reduce { |item, acc| $acc + " " + $item }
# PURPOSE: Join strings with custom logic
```

## Text Processing Power Tools {#text-processing}

### String Operations - Complete String Manipulation

**String length and basic info:**

```nu
# Get string length
"hello world" | str length                               # 11
"" | str length                                          # 0 (empty string)
"多字节文本" | str length                                 # Handle Unicode properly
# PURPOSE: Count characters for validation, truncation, analysis

# Check if string is empty
"hello" | str is-empty                                   # false
"" | str is-empty                                        # true
"   " | str trim | str is-empty                          # true (after trimming spaces)
# PURPOSE: Validate input, handle empty data conditions

# String statistics for analysis
"Hello World 123!" | split chars | each { |char|
  if ($char | str match -r '\d') { "digit" }
  else if ($char | str match -r '\w') { "letter" }
  else { "other" }
} | group-by {$in} | transpose type count
# PURPOSE: Analyze string composition, character statistics
```

**Case conversion - every variation:**

```nu
# Convert to lowercase
"Hello World" | str downcase                             # "hello world"
"SCREAMING_SNAKE_CASE" | str downcase                   # "screaming_snake_case"
# PURPOSE: Normalize text for comparison, search, consistency

# Convert to uppercase
"hello world" | str upcase                               # "HELLO WORLD"
"quiet_text" | str upcase                                # "QUIET_TEXT"
# PURPOSE: Create headers, emphasis, constants

# Title case - capitalize each word
"hello world" | str title-case                           # "Hello World"
"the quick brown fox" | str title-case                   # "The Quick Brown Fox"
# PURPOSE: Format headings, names, proper nouns

# Capitalize first letter only
"hello world" | str capitalize                           # "Hello world"
# PURPOSE: Start sentences, format proper nouns

# Advanced case transformations
"camelCase" | str downcase                               # "camelcase"
"snake_case" | split chars | each { |c| if $c == "_" { " " } else { $c } } | str join | str title-case
# PURPOSE: Convert between naming conventions
```

**Trimming and cleaning - remove unwanted characters:**

```nu
# Remove whitespace from both ends
"  hello world  " | str trim                             # "hello world"
"\t\nhello world\n\t" | str trim                         # "hello world" (removes tabs, newlines)
# PURPOSE: Clean user input, normalize data

# Trim from left only
"  hello world  " | str trim -l                          # "hello world  "
# PURPOSE: Remove leading whitespace while preserving trailing

# Trim from right only
"  hello world  " | str trim -r                          # "  hello world"
# PURPOSE: Remove trailing whitespace while preserving leading

# Trim specific characters
"...hello world..." | str trim -c '.'                    # "hello world"
"***important***" | str trim -c '*'                      # "important"
"##header##" | str trim -c '#'                           # "header"
# PURPOSE: Remove decorative or markup characters

# Remove all whitespace (not just ends)
"h e l l o   w o r l d" | str replace -a ' ' ''          # "helloworld"
# PURPOSE: Compact text, remove all spaces

# Clean up multiple consecutive spaces
"hello    world     test" | str replace -r '\s+' ' '     # "hello world test"
# PURPOSE: Normalize whitespace in text
```

**String splitting - break text into parts:**

```nu
# Split by delimiter
"apple,banana,cherry" | split row ","                    # ["apple", "banana", "cherry"]
"one:two:three:four" | split row ":"                     # ["one", "two", "three", "four"]
# PURPOSE: Parse delimited data, CSV processing

# Split into individual characters
"hello" | split chars                                     # ["h", "e", "l", "l", "o"]
"🚀🎉✨" | split chars                                     # ["🚀", "🎉", "✨"] (handles Unicode)
# PURPOSE: Character-level processing, analysis

# Split by whitespace
"hello world test" | split words                         # ["hello", "world", "test"]
"multiple   spaces   here" | split words                 # ["multiple", "spaces", "here"]
# PURPOSE: Parse natural language text

# Split by regex pattern
"word1 123 word2 456 word3" | str split -r '\d+'        # ["word1 ", " word2 ", " word3"]
# PURPOSE: Complex pattern-based splitting

# Split with limit (only split first N times)
"a-b-c-d-e" | split row -n 2 "-"                        # ["a", "b", "c-d-e"]
# PURPOSE: Parse structured data with known format

# Split lines (cross-platform line ending handling)
"line1\nline2\r\nline3" | lines                         # ["line1", "line2", "line3"]
# PURPOSE: Process multi-line text, file content
```

**String joining - combine text elements:**

```nu
# Basic joining with separator
["apple", "banana", "cherry"] | str join ", "            # "apple, banana, cherry"
["one", "two", "three"] | str join " "                   # "one two three"
# PURPOSE: Create CSV, formatted lists, sentences

# Join without separator
["hello", "world"] | str join                            # "helloworld"
["a", "b", "c"] | str join ""                           # "abc"
# PURPOSE: Concatenate strings directly

# Join with complex separators
["first", "second", "third"] | str join " -> "          # "first -> second -> third"
# PURPOSE: Create formatted output, navigation paths

# Join with different separators for first/last
def oxford_join [items: list<string>] {
  let len = $items | length
  if $len == 1 {
    $items | get 0
  } else if $len == 2 {
    ($items | get 0) + " and " + ($items | get 1)
  } else {
    let first = $items | take ($len - 1) | str join ", "
    let last = $items | get ($len - 1)
    $first + ", and " + $last
  }
}
# PURPOSE: Create grammatically correct English lists

# Join with line breaks for output formatting
["Error:", "  - Invalid input", "  - Missing required field"] | str join "\n"
# PURPOSE: Create multi-line error messages, reports
```

### Pattern Matching and Search

**Contains - check for substrings:**

```nu
# Basic substring checking
"hello world" | str contains "world"                     # true
"hello world" | str contains "mars"                      # false
# PURPOSE: Search for specific text, validate content

# Case-sensitive vs case-insensitive
"Hello World" | str contains "hello"                     # false (case-sensitive)
"Hello World" | str downcase | str contains "hello"      # true (after lowercasing)
# PURPOSE: Flexible text searching

# Multiple substring checks
def contains_any [text: string, patterns: list<string>] {
  $patterns | any { |pattern| $text | str contains $pattern }
}
"error: file not found" | contains_any ["error", "warning", "info"]  # true
# PURPOSE: Check for multiple possible matches

# Contains with lists
["apple", "banana", "cherry"] | where { str contains "an" }  # ["banana"]
# PURPOSE: Filter lists based on substring matching
```

**Starts with and ends with - position-specific matching:**

```nu
# Check string beginning
"hello world" | str starts-with "hello"                  # true
"hello world" | str starts-with "world"                  # false
# PURPOSE: Validate prefixes, file extensions, protocols

# Check string ending
"document.pdf" | str ends-with ".pdf"                    # true
"document.pdf" | str ends-with ".txt"                    # false
# PURPOSE: File type checking, suffix validation

# Case handling in position matching
"Hello World" | str starts-with "hello"                  # false
"Hello World" | str downcase | str starts-with "hello"  # true
# PURPOSE: Flexible prefix/suffix checking

# Filter files by extension
ls | where name | str ends-with ".txt"                   # All text files
ls | where name | str ends-with ".rs"                    # All Rust files
# PURPOSE: File type filtering, content organization

# Path validation
def is_absolute_path [path: string] {
  ($path | str starts-with "/") or ($path | str match -r '^[A-Za-z]:')
}
# PURPOSE: Validate file paths, system compatibility
```

**String replacement - find and replace text:**

```nu
# Basic replacement (first occurrence)
"hello world world" | str replace "world" "universe"     # "hello universe world"
# PURPOSE: Simple text substitution

# Replace all occurrences
"hello world world" | str replace -a "world" "universe"  # "hello universe universe"
# PURPOSE: Global text replacement

# Case-insensitive replacement
"Hello WORLD world" | str replace -i "WORLD" "universe"  # "Hello universe world"
# PURPOSE: Flexible case handling in replacement

# Regular expression replacement
"hello123world456" | str replace -r '\d+' 'X'           # "helloXworld456" (first match)
"hello123world456" | str replace -a -r '\d+' 'X'        # "helloXworldX" (all matches)
# PURPOSE: Pattern-based replacement, complex substitutions

# Complex regex with capture groups
"2024-01-15" | str replace -r '(\d{4})-(\d{2})-(\d{2})' '$3/$2/$1'  # "15/01/2024"
# PURPOSE: Format conversion, data transformation

# Multiple replacements
def clean_filename [name: string] {
  $name
  | str replace -a " " "_"        # Replace spaces with underscores
  | str replace -a -r '[<>:"/\\|?*]' ""  # Remove invalid filename characters
  | str downcase                  # Convert to lowercase
}
"My Document (Copy).txt" | clean_filename               # "my_document_copy.txt"
# PURPOSE: Text normalization, sanitization

# Conditional replacement based on context
def smart_replace [text: string, old: string, new: string] {
  if ($text | str contains $old) {
    $text | str replace -a $old $new
  } else {
    $text
  }
}
# PURPOSE: Safe replacement with validation
```

### Regular Expression Mastery

**Pattern matching with regex:**

```nu
# Check if string matches pattern
"hello123" | str match -r '\d+'                         # true (contains digits)
"hello123" | str match -r '^\w+$'                       # true (only word characters)
"user@domain.com" | str match -r '.+@.+\..+'            # true (basic email pattern)
# PURPOSE: Validate data format, check patterns

# Extract matches from string
"Contact: john@example.com or mary@test.org" | parse -r 'Contact: (?P<email1>\S+@\S+) or (?P<email2>\S+@\S+)'
# PURPOSE: Extract structured data from text

# Find all matches
"The numbers are 123, 456, and 789" | str find-replace -a -r '\d+' { |match| $"[($match)]" }
# PURPOSE: Highlight, transform all pattern occurrences

# Complex email validation
def is_valid_email [email: string] {
  $email | str match -r '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
}
"user@example.com" | is_valid_email                     # true
"invalid-email" | is_valid_email                        # false
# PURPOSE: Robust input validation

# Phone number formatting
def format_phone [number: string] {
  let digits = $number | str replace -a -r '\D' ''       # Remove non-digits
  if ($digits | str length) == 10 {
    $digits | str replace -r '^(\d{3})(\d{3})(\d{4})$' '($1) $2-$3'
  } else {
    "Invalid phone number"
  }
}
"1234567890" | format_phone                             # "(123) 456-7890"
# PURPOSE: Data formatting, standardization
```

**Advanced pattern operations:**

```nu
# URL parsing with regex
def parse_url [url: string] {
  let parts = $url | parse -r '^(?P<protocol>https?)://(?P<host>[^/]+)(?P<path>/.*)?$'
  if ($parts | length) > 0 {
    $parts | get 0
  } else {
    {protocol: null, host: null, path: null}
  }
}
"https://example.com/path/to/page" | parse_url
# PURPOSE: Extract components from complex strings

# Log parsing
def parse_log_line [line: string] {
  $line | parse -r '^(?P<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) \[(?P<level>\w+)\] (?P<message>.+)$'
}
"2024-01-15 10:30:45 [ERROR] File not found" | parse_log_line
# PURPOSE: Extract structured data from logs

# Data validation with detailed feedback
def validate_password [password: string] {
  let checks = [
    {
      rule: ($password | str length) >= 8,
      message: "At least 8 characters"
    },
    {
      rule: ($password | str match -r '[A-Z]'),
      message: "Contains uppercase letter"
    },
    {
      rule: ($password | str match -r '[a-z]'),
      message: "Contains lowercase letter"
    },
    {
      rule: ($password | str match -r '\d'),
      message: "Contains digit"
    },
    {
      rule: ($password | str match -r '[!@#$%^&*]'),
      message: "Contains special character"
    }
  ]

  let failed = $checks | where rule == false | get message
  if ($failed | length) == 0 {
    "Password is valid"
  } else {
    $"Password must have: ($failed | str join ', ')"
  }
}
# PURPOSE: Complex validation with user feedback
```

### Working with Lines and Multi-line Text

**Line operations - handle text files:**

```nu
# Split text into lines (handles different line endings)
"line1\nline2\r\nline3\rline4" | lines                  # ["line1", "line2", "line3", "line4"]
# PURPOSE: Process file content, handle cross-platform text

# Number lines (add line numbers)
"first\nsecond\nthird" | lines | enumerate | each { |item| $"($item.index + 1): ($item.item)" }
# PURPOSE: Add line numbers for reference, debugging

# Filter lines based on content
open some-file.txt | lines | where { str contains "error" }
# PURPOSE: Extract relevant lines from logs, files

# Remove empty lines
"line1\n\nline2\n\nline3" | lines | where { str length > 0 }
# PURPOSE: Clean up text, remove blank lines

# Process each line individually
open data.txt | lines | each { |line|
  $line | str trim | str upcase
}
# PURPOSE: Transform each line consistently

# Find lines matching pattern
open log.txt | lines | where { str match -r '^\d{4}-\d{2}-\d{2}' }
# PURPOSE: Extract lines with specific format (like timestamps)

# Line statistics
def analyze_lines [text: string] {
  let lines_list = $text | lines
  {
    total_lines: ($lines_list | length),
    empty_lines: ($lines_list | where { str trim | str length == 0 } | length),
    average_length: ($lines_list | get { str length } | math avg),
    longest_line: ($lines_list | sort-by { str length } | last),
    shortest_line: ($lines_list | where { str length > 0 } | sort-by { str length } | first)
  }
}
# PURPOSE: Analyze text structure, content statistics
```

**Advanced multi-line processing:**

```nu
# Join lines with custom logic
def smart_join [lines: list<string>] {
  $lines | reduce -f "" { |line, acc|
    if ($line | str trim | str ends-with ".") {
      $acc + $line + "\n\n"    # Double line break after sentences
    } else {
      $acc + $line + " "       # Single space for continuation
    }
  }
}
# PURPOSE: Format text with intelligent line breaks

# Process paragraphs (groups of lines separated by empty lines)
def split_paragraphs [text: string] {
  let lines = $text | lines
  let mut paragraphs = []
  let mut current_paragraph = []

  for line in $lines {
    if ($line | str trim | str length) == 0 {
      if ($current_paragraph | length) > 0 {
        $paragraphs = ($paragraphs | append [($current_paragraph | str join " ")])
        $current_paragraph = []
      }
    } else {
      $current_paragraph = ($current_paragraph | append $line)
    }
  }

  if ($current_paragraph | length) > 0 {
    $paragraphs | append [($current_paragraph | str join " ")]
  } else {
    $paragraphs
  }
}
# PURPOSE: Structure text processing, document analysis

# Line-based diff (simple comparison)
def compare_files [file1: string, file2: string] {
  let lines1 = open $file1 | lines
  let lines2 = open $file2 | lines
  let max_lines = [($lines1 | length), ($lines2 | length)] | math max

  0..($max_lines - 1) | each { |i|
    let line1 = $lines1 | get $i | default ""
    let line2 = $lines2 | get $i | default ""
    if $line1 != $line2 {
      {
        line_number: ($i + 1),
        file1: $line1,
        file2: $line2,
        status: "different"
      }
    }
  } | compact
}
# PURPOSE: Compare files line by line, find differences
```

## System Information Commands {#system-info}

### Process Management - Complete Process Control

**Process listing and filtering:**

```nu
# Basic process listing - all processes in table format
ps
# PURPOSE: Display all running processes with PID, name, CPU, memory usage

# Get detailed process information
ps | select pid name cpu mem virtual status
# PURPOSE: Show specific process attributes in clean table format

# Find processes by name pattern
ps | where name =~ "chrome"                             # All Chrome processes
ps | where name =~ "node"                               # All Node.js processes
ps | where name =~ "python"                             # All Python processes
# PURPOSE: Locate specific applications or services

# Find high resource usage processes
ps | where cpu > 10.0                                   # High CPU usage (>10%)
ps | where mem > 100                                    # High memory usage (>100MB)
ps | where cpu > 5.0 and mem > 50                       # Both high CPU and memory
# PURPOSE: Identify resource-intensive processes for optimization

# Sort processes by resource usage
ps | sort-by cpu                                        # Sort by CPU (ascending)
ps | sort-by -r cpu                                     # Sort by CPU (descending)
ps | sort-by mem                                        # Sort by memory usage
ps | sort-by -r virtual                                 # Sort by virtual memory
# PURPOSE: Find top resource consumers quickly

# Get top N processes
ps | sort-by -r cpu | first 10                          # Top 10 CPU users
ps | sort-by -r mem | first 5                           # Top 5 memory users
# PURPOSE: Quick identification of system bottlenecks

# Process statistics
ps | get cpu | math sum                                  # Total CPU usage
ps | get mem | math sum                                  # Total memory usage
ps | length                                             # Total number of processes
# PURPOSE: System-wide resource utilization metrics

# Find processes by user (if supported)
ps | where user == "root"                               # Root processes
ps | where user != "root"                               # Non-root processes
# PURPOSE: Security analysis, user activity monitoring
```

**Process analysis and monitoring:**

```nu
# Group processes by name (count instances)
ps | group-by name | transpose name processes | each { |group|
  {
    name: $group.name,
    count: ($group.processes | length),
    total_cpu: ($group.processes | get cpu | math sum),
    total_mem: ($group.processes | get mem | math sum)
  }
} | sort-by -r total_cpu
# PURPOSE: Analyze resource usage by application type

# Find parent-child process relationships
ps | each { |proc|
  let children = ps | where ppid == $proc.pid
  {
    pid: $proc.pid,
    name: $proc.name,
    children_count: ($children | length),
    children: ($children | get name)
  }
} | where children_count > 0
# PURPOSE: Understand process hierarchy and spawning patterns

# Monitor process over time (basic implementation)
def monitor_process [name: string, duration: int = 10] {
  1..$duration | each { |i|
    let proc_info = ps | where name =~ $name | first
    if ($proc_info | is-empty) {
      {time: $i, status: "not found"}
    } else {
      {
        time: $i,
        pid: $proc_info.pid,
        cpu: $proc_info.cpu,
        mem: $proc_info.mem,
        status: "running"
      }
    }
    sleep 1sec
  }
}
# PURPOSE: Track process resource usage over time

# Process health check
def check_process_health [name: string] {
  let processes = ps | where name =~ $name
  if ($processes | is-empty) {
    $"❌ No ($name) processes found"
  } else {
    let high_cpu = $processes | where cpu > 80
    let high_mem = $processes | where mem > 1000

    [
      $"✅ ($processes | length) ($name) process(es) running",
      (if ($high_cpu | length) > 0 { $"⚠️ ($high_cpu | length) high CPU usage" } else { "" }),
      (if ($high_mem | length) > 0 { $"⚠️ ($high_mem | length) high memory usage" } else { "" })
    ] | where { str length > 0 } | str join "\n"
  }
}
# PURPOSE: Quick process health assessment with alerts
```

**Process control (use with caution):**

```nu
# Find process ID by name
def find_pid [name: string] {
  ps | where name =~ $name | get pid
}
# PURPOSE: Get process IDs for further operations

# Kill process by name (DANGEROUS - use carefully)
def kill_by_name [name: string, --force (-f)] {
  let pids = ps | where name =~ $name | get pid
  if ($pids | length) == 0 {
    echo $"No processes found matching '($name)'"
  } else {
    echo $"Found ($pids | length) processes matching '($name)'"
    $pids | each { |pid|
      if $force {
        echo $"Force killing process ($pid)"
        kill -9 $pid
      } else {
        echo $"Terminating process ($pid)"
        kill $pid
      }
    }
  }
}
# PURPOSE: Terminate processes by name pattern

# Interactive process killer
def interactive_kill [name: string] {
  let processes = ps | where name =~ $name
  if ($processes | length) == 0 {
    echo $"No processes found matching '($name)'"
    return
  }

  echo "Found processes:"
  $processes | each { |proc|
    echo $"  PID: ($proc.pid), CPU: ($proc.cpu)%, MEM: ($proc.mem)MB"
  }

  let confirm = input "Kill these processes? (y/N): "
  if ($confirm | str downcase) == "y" {
    $processes | get pid | each { |pid|
      kill $pid
      echo $"Killed process ($pid)"
    }
  }
}
# PURPOSE: Safe interactive process termination
```

### System Information - Complete System Analysis

**Basic system information:**

```nu
# Get complete system information
sys
# PURPOSE: Display comprehensive system information including CPU, memory, disks

# CPU information
sys | get cpu                                           # CPU details and usage
sys | get cpu | length                                  # Number of CPU cores
sys | get cpu | get cpu_usage | math avg               # Average CPU usage
# PURPOSE: Monitor CPU performance and capabilities

# Memory information
sys | get mem                                          # Memory usage statistics
sys | get mem | get total                              # Total memory
sys | get mem | get used                               # Used memory
sys | get mem | get available                          # Available memory
($sys | get mem | get used) / ($sys | get mem | get total) * 100  # Memory usage percentage
# PURPOSE: Monitor memory utilization and availability

# Disk information
sys | get disks                                        # All disk information
sys | get disks | select mount total used available type  # Disk summary
sys | get disks | each { |disk|
  {
    mount: $disk.mount,
    used_percent: (($disk.used / $disk.total) * 100 | math round),
    free_gb: (($disk.available / 1024 / 1024 / 1024) | math round),
    total_gb: (($disk.total / 1024 / 1024 / 1024) | math round)
  }
}
# PURPOSE: Monitor disk usage and available space
```

**Detailed system analysis:**

```nu
# System load and performance overview
def system_overview [] {
  let system_info = sys

  {
    hostname: ($env.HOSTNAME? | default "unknown"),
    uptime: "N/A",  # Nushell doesn't provide uptime directly
    cpu_cores: ($system_info | get cpu | length),
    avg_cpu: ($system_info | get cpu | get cpu_usage | math avg | math round),
    memory_used_percent: (($system_info | get mem | get used) / ($system_info | get mem | get total) * 100 | math round),
    disk_usage: ($system_info | get disks | each { |disk|
      {
        mount: $disk.mount,
        used_percent: (($disk.used / $disk.total) * 100 | math round)
      }
    })
  }
}
# PURPOSE: Quick system health snapshot

# Network interfaces (if available)
sys | get net                                          # Network interface information
# PURPOSE: Monitor network interfaces and activity

# Detailed CPU analysis
def cpu_analysis [] {
  let cpu_info = sys | get cpu
  {
    core_count: ($cpu_info | length),
    individual_usage: ($cpu_info | each { |core| $core.cpu_usage }),
    max_usage: ($cpu_info | get cpu_usage | math max),
    min_usage: ($cpu_info | get cpu_usage | math min),
    avg_usage: ($cpu_info | get cpu_usage | math avg),
    cores_over_80: ($cpu_info | where cpu_usage > 80 | length)
  }
}
# PURPOSE: Detailed CPU performance analysis

# Memory breakdown
def memory_analysis [] {
  let mem_info = sys | get mem
  let total = $mem_info | get total
  {
    total_gb: (($total / 1024 / 1024 / 1024) | math round),
    used_gb: (($mem_info | get used / 1024 / 1024 / 1024) | math round),
    available_gb: (($mem_info | get available / 1024 / 1024 / 1024) | math round),
    used_percent: (($mem_info | get used / $total * 100) | math round),
    swap_total: (($mem_info | get swap_total / 1024 / 1024 / 1024) | math round),
    swap_used: (($mem_info | get swap_used / 1024 / 1024 / 1024) | math round)
  }
}
# PURPOSE: Comprehensive memory usage analysis
```

**Environment and configuration:**

```nu
# Display all environment variables
$env
# PURPOSE: Show all environment variables and their values

# Get specific environment variables
$env.HOME                                              # Home directory
$env.PATH                                              # Search path
$env.USER                                              # Current user
$env.SHELL                                             # Current shell
# PURPOSE: Access system configuration and user settings

# PATH analysis
$env.PATH | split row (char esep) | each { |path|
  {
    path: $path,
    exists: ($path | path exists),
    is_dir: ($path | path exists and ($path | path type) == dir)
  }
}
# PURPOSE: Analyze PATH for valid directories

# Environment variable search
def find_env [pattern: string] {
  $env | transpose key value | where key =~ $pattern
}
find_env "PYTHON"                                      # Find Python-related vars
# PURPOSE: Search environment variables by pattern

# User and system information
$env.USER                                              # Current user
$env.HOME                                              # User home directory
$env.PWD                                               # Current working directory
$env.OLDPWD                                            # Previous directory
$nu.os-info                                            # Operating system info
# PURPOSE: Get user context and OS information
```

### Command and Tool Discovery

**Finding commands and executables:**

```nu
# Check if command exists
which git                                              # Find git executable path
which python                                          # Find python executable
which code                                            # Find VS Code executable
# PURPOSE: Verify tool availability and locate executables

# Find all executables in PATH
$env.PATH | split row (char esep) | each { |dir|
  if ($dir | path exists) {
    ls $dir | where type == file and (name | str ends-with ".exe" or name | str match -r '^[^.]*$')
  }
} | flatten | get name | uniq | sort
# PURPOSE: List all available commands in PATH

# Alternative command finder
def find_command [name: string] {
  let result = which $name
  if ($result | is-empty) {
    $"❌ Command '($name)' not found"
  } else {
    $"✅ ($name) found at: ($result)"
  }
}
# PURPOSE: Check command availability with user-friendly output

# Version checking for common tools
def check_versions [] {
  let tools = ["git", "python", "node", "npm", "cargo", "docker"]
  $tools | each { |tool|
    let path_result = which $tool
    if not ($path_result | is-empty) {
      try {
        let version = ^$tool --version | lines | first
        {tool: $tool, version: $version, path: $path_result}
      } catch {
        {tool: $tool, version: "unknown", path: $path_result}
      }
    } else {
      {tool: $tool, version: "not installed", path: ""}
    }
  }
}
# PURPOSE: Check versions of common development tools

# Command history analysis
history | get command | group-by {$in} | transpose command count | sort-by -r count | first 20
# PURPOSE: Find most frequently used commands
```

## Process & Network Commands {#process-network}

### Network Information and Analysis

**Network interface information:**

```nu
# Basic network interface information (if available in sys)
try { sys | get net } catch { "Network information not available in this Nushell build" }
# PURPOSE: Display network interfaces and their status

# Check network connectivity
def ping_test [host: string = "8.8.8.8"] {
  try {
    let result = ^ping -c 4 $host | complete
    if $result.exit_code == 0 {
      $"✅ ($host) is reachable"
    } else {
      $"❌ ($host) is not reachable"
    }
  } catch {
    "❌ ping command failed"
  }
}
# PURPOSE: Test network connectivity to hosts

# DNS lookup
def dns_lookup [domain: string] {
  try {
    let result = ^nslookup $domain | complete
    if $result.exit_code == 0 {
      $result.stdout
    } else {
      $"❌ DNS lookup failed for ($domain)"
    }
  } catch {
    "❌ nslookup command not available"
  }
}
# PURPOSE: Resolve domain names to IP addresses

# Port connectivity test
def test_port [host: string, port: int] {
  try {
    let result = ^nc -z -v $host $port | complete
    if $result.exit_code == 0 {
      $"✅ Port ($port) is open on ($host)"
    } else {
      $"❌ Port ($port) is closed on ($host)"
    }
  } catch {
    try {
      let result = ^telnet $host $port | complete
      "Connection test completed (check output for results)"
    } catch {
      "❌ No network testing tools available"
    }
  }
}
# PURPOSE: Test if specific ports are accessible

# Network service discovery
def check_common_ports [host: string] {
  let common_ports = [22, 23, 25, 53, 80, 110, 143, 443, 993, 995]
  $common_ports | each { |port|
    try {
      let result = ^nc -z -w1 $host $port | complete
      {
        port: $port,
        status: (if $result.exit_code == 0 { "open" } else { "closed" }),
        service: (match $port {
          22 => "SSH",
          23 => "Telnet",
          25 => "SMTP",
          53 => "DNS",
          80 => "HTTP",
          110 => "POP3",
          143 => "IMAP",
          443 => "HTTPS",
          993 => "IMAPS",
          995 => "POP3S",
          _ => "Unknown"
        })
      }
    } catch {
      {port: $port, status: "unknown", service: ""}
    }
  } | where status == "open"
}
# PURPOSE: Scan for common open ports on a host
```

**Network monitoring and analysis:**

```nu
# Basic HTTP requests and API testing
def http_status [url: string] {
  try {
    let response = http get $url
    $"✅ ($url) responded successfully"
  } catch { |error|
    $"❌ ($url) failed: ($error.msg)"
  }
}
# PURPOSE: Test HTTP endpoint availability

# Batch URL testing
def test_urls [urls: list<string>] {
  $urls | each { |url|
    try {
      let start = date now
      let response = http get $url
      let end = date now
      {
        url: $url,
        status: "success",
        response_time_ms: (($end - $start) / 1000000),
        size: ($response | to text | str length)
      }
    } catch { |error|
      {
        url: $url,
        status: "failed",
        error: $error.msg,
        response_time_ms: null,
        size: null
      }
    }
  }
}
# PURPOSE: Monitor multiple URLs for availability and performance

# Network interface statistics (using external commands)
def network_stats [] {
  try {
    # Linux/macOS approach
    let interfaces = ^ip addr show | complete
    if $interfaces.exit_code == 0 {
      "Network interfaces found (use 'ip addr show' for details)"
    } else {
      # Windows approach
      ^ipconfig | complete | get stdout
    }
  } catch {
    "❌ Network interface commands not available"
  }
}
# PURPOSE: Display network interface configuration

# Bandwidth estimation (simple HTTP-based test)
def bandwidth_test [] {
  let test_url = "http://httpbin.org/bytes/1048576"  # 1MB test file
  try {
    let start = date now
    let response = http get $test_url
    let end = date now
    let duration_seconds = (($end - $start) / 1000000000)
    let size_mb = 1
    let speed_mbps = ($size_mb / $duration_seconds)

    {
      size_mb: $size_mb,
      duration_seconds: $duration_seconds,
      speed_mbps: ($speed_mbps | math round)
    }
  } catch {
    "❌ Bandwidth test failed"
  }
}
# PURPOSE: Estimate network bandwidth with HTTP download test
```

### External Command Integration

**System command execution:**

```nu
# Execute external commands with error handling
def safe_exec [command: string, ...args] {
  try {
    let result = ^$command ...$args | complete
    {
      command: $command,
      args: $args,
      exit_code: $result.exit_code,
      stdout: $result.stdout,
      stderr: $result.stderr,
      success: ($result.exit_code == 0)
    }
  } catch { |error|
    {
      command: $command,
      args: $args,
      exit_code: -1,
      stdout: "",
      stderr: $error.msg,
      success: false
    }
  }
}
# PURPOSE: Execute external commands with comprehensive error handling

# Cross-platform system information
def system_info [] {
  let os_info = $nu.os-info
  match $os_info.name {
    "linux" => {
      let cpu = safe_exec "cat" "/proc/cpuinfo"
      let memory = safe_exec "cat" "/proc/meminfo"
      let disk = safe_exec "df" "-h"
      {os: "Linux", cpu_info: $cpu.stdout, memory_info: $memory.stdout, disk_info: $disk.stdout}
    },
    "macos" => {
      let system = safe_exec "system_profiler" "SPHardwareDataType"
      let disk = safe_exec "df" "-h"
      {os: "macOS", system_info: $system.stdout, disk_info: $disk.stdout}
    },
    "windows" => {
      let system = safe_exec "systeminfo"
      {os: "Windows", system_info: $system.stdout}
    },
    _ => {
      {os: $os_info.name, info: "Unsupported OS for detailed info"}
    }
  }
}
# PURPOSE: Get detailed system information across different operating systems

# Package manager operations (cross-platform)
def package_info [package: string] {
  let os_info = $nu.os-info
  match $os_info.name {
    "linux" => {
      # Try different package managers
      let apt_result = safe_exec "apt" "list" "--installed" $package
      let yum_result = safe_exec "yum" "list" "installed" $package
      let dnf_result = safe_exec "dnf" "list" "installed" $package

      if $apt_result.success {
        {package_manager: "apt", info: $apt_result.stdout}
      } else if $yum_result.success {
        {package_manager: "yum", info: $yum_result.stdout}
      } else if $dnf_result.success {
        {package_manager: "dnf", info: $dnf_result.stdout}
      } else {
        {package_manager: "unknown", info: "Package manager not found or package not installed"}
      }
    },
    "macos" => {
      let brew_result = safe_exec "brew" "list" $package
      if $brew_result.success {
        {package_manager: "brew", info: $brew_result.stdout}
      } else {
        {package_manager: "brew", info: "Package not found or Homebrew not installed"}
      }
    },
    "windows" => {
      let winget_result = safe_exec "winget" "list" $package
      if $winget_result.success {
        {package_manager: "winget", info: $winget_result.stdout}
      } else {
        {package_manager: "winget", info: "Package not found or winget not available"}
      }
    },
    _ => {
      {package_manager: "unknown", info: "Unsupported OS"}
    }
  }
}
# PURPOSE: Check package installation status across different package managers
```

## Date, Time & Math Operations {#date-time-math}

### Date and Time Mastery

**Current date and time operations:**

```nu
# Get current date and time
date now                                               # Current timestamp
# PURPOSE: Get precise current moment for logging, calculations

# Format current date in various ways
date now | format date "%Y-%m-%d"                      # 2024-01-15 (ISO date)
date now | format date "%Y-%m-%d %H:%M:%S"             # 2024-01-15 14:30:45 (ISO datetime)
date now | format date "%B %d, %Y"                     # January 15, 2024 (human readable)
date now | format date "%A, %B %d, %Y"                 # Monday, January 15, 2024 (full)
date now | format date "%Y%m%d_%H%M%S"                 # 20240115_143045 (filename safe)
# PURPOSE: Format dates for different contexts (logs, filenames, display)

# Get date components
let now = date now
$now | format date "%Y"                                # Year: 2024
$now | format date "%m"                                # Month: 01
$now | format date "%d"                                # Day: 15
$now | format date "%H"                                # Hour: 14
$now | format date "%M"                                # Minute: 30
$now | format date "%S"                                # Second: 45
$now | format date "%A"                                # Day name: Monday
$now | format date "%B"                                # Month name: January
# PURPOSE: Extract specific date/time components for calculations

# Unix timestamp operations
date now | date to-table | get timestamp               # Unix timestamp in nanoseconds
date now | format date "%s"                           # Unix timestamp in seconds
# PURPOSE: Convert to/from Unix timestamps for APIs, databases
```

**Date arithmetic and calculations:**

```nu
# Date arithmetic with durations
date now + 1day                                       # Tomorrow
date now - 1day                                       # Yesterday
date now + 1hr                                        # One hour from now
date now - 30min                                      # 30 minutes ago
date now + 1week                                      # One week from now
date now + 1month                                     # One month from now (approximate)
date now + 1year                                      # One year from now
# PURPOSE: Calculate future and past dates for scheduling, filtering

# Time differences and durations
let start = date now
# ... some operation ...
let end = date now
$end - $start                                         # Duration between dates
# PURPOSE: Measure elapsed time, calculate ages, durations

# Age calculation
def calculate_age [birth_date: string] {
  let birth = $birth_date | into datetime
  let today = date now
  let age_duration = $today - $birth
  ($age_duration / 1day / 365.25) | math floor          # Age in years
}
calculate_age "1990-05-15"                            # Calculate someone's age
# PURPOSE: Calculate ages, time since events

# Business day calculations
def add_business_days [start_date: datetime, days: int] {
  let mut current = $start_date
  let mut remaining = $days

  while $remaining > 0 {
    $current = $current + 1day
    let day_of_week = $current | format date "%u" | into int  # 1=Monday, 7=Sunday
    if $day_of_week <= 5 {  # Monday through Friday
      $remaining = $remaining - 1
    }
  }
  $current
}
# PURPOSE: Calculate business days, excluding weekends

# Time zone operations (if supported)
date now | date to-timezone "UTC"                      # Convert to UTC
date now | date to-timezone "America/New_York"         # Convert to specific timezone
# PURPOSE: Handle time zones for global applications
```

**Date parsing and conversion:**

```nu
# Parse dates from strings
"2024-01-15" | into datetime                          # Parse ISO date
"January 15, 2024" | into datetime                    # Parse natural language date
"2024-01-15 14:30:45" | into datetime                 # Parse ISO datetime
# PURPOSE: Convert string dates from various sources

# Parse dates with custom formats
def parse_custom_date [date_str: string, format: string] {
  # This is a simplified version - actual implementation would depend on Nushell's date parsing capabilities
  try {
    $date_str | into datetime
  } catch {
    null
  }
}
parse_custom_date "15/01/2024" "dd/mm/yyyy"           # European format
parse_custom_date "01-15-24" "mm-dd-yy"               # US short format
# PURPOSE: Handle various date formats from different systems

# Date validation
def is_valid_date [date_str: string] {
  try {
    $date_str | into datetime
    true
  } catch {
    false
  }
}
is_valid_date "2024-02-30"                           # false (invalid date)
is_valid_date "2024-02-29"                           # true (valid leap year date)
# PURPOSE: Validate user input dates

# Date range operations
def dates_in_range [start: string, end: string] {
  let start_date = $start | into datetime
  let end_date = $end | into datetime
  let diff_days = (($end_date - $start_date) / 1day) | math floor

  0..$diff_days | each { |i|
    $start_date + ($i * 1day)
  }
}
dates_in_range "2024-01-01" "2024-01-07"             # All dates in range
# PURPOSE: Generate date sequences for iteration, reporting
```

### Mathematical Operations and Calculations

**Basic arithmetic operations:**

```nu
# Basic math operations
5 + 3                                                 # Addition: 8
10 - 4                                                # Subtraction: 6
6 * 7                                                 # Multiplication: 42
15 / 3                                                # Division: 5
17 % 5                                                # Modulo: 2 (remainder)
2 ** 8                                                # Exponentiation: 256
# PURPOSE: Perform basic calculations in data processing

# Math with lists
[1, 2, 3, 4, 5] | math sum                          # Sum: 15
[1, 2, 3, 4, 5] | math avg                          # Average: 3
[1, 2, 3, 4, 5] | math min                          # Minimum: 1
[1, 2, 3, 4, 5] | math max                          # Maximum: 5
[1, 2, 3, 4, 5] | math product                      # Product: 120
# PURPOSE: Calculate statistics on data sets

# Advanced math operations
[1, 2, 3, 4, 5] | math median                       # Median: 3
[1, 2, 3, 4, 5] | math stddev                       # Standard deviation
[1, 2, 3, 4, 5] | math variance                     # Variance
# PURPOSE: Statistical analysis of data

# Math with precision
3.14159 | math round                                 # Round: 3
3.14159 | math floor                                 # Floor: 3
3.14159 | math ceil                                  # Ceiling: 4
3.14159 | math round -p 2                           # Round to 2 places: 3.14
# PURPOSE: Control precision in calculations
```

**Financial and business calculations:**

```nu
# Percentage calculations
def percentage [value: float, total: float] {
  ($value / $total) * 100
}
percentage 25 100                                    # 25%

def percentage_change [old: float, new: float] {
  (($new - $old) / $old) * 100
}
percentage_change 100 125                            # 25% increase

# Compound interest calculation
def compound_interest [principal: float, rate: float, time: float, compounds_per_year: int = 1] {
  $principal * ((1 + ($rate / $compounds_per_year)) ** ($compounds_per_year * $time))
}
compound_interest 1000 0.05 10                      # $1000 at 5% for 10 years

# Loan payment calculation (simplified)
def monthly_payment [principal: float, annual_rate: float, years: float] {
  let monthly_rate = $annual_rate / 12
  let months = $years * 12
  $principal * ($monthly_rate * ((1 + $monthly_rate) ** $months)) / (((1 + $monthly_rate) ** $months) - 1)
}
monthly_payment 200000 0.05 30                      # $200k loan at 5% for 30 years
# PURPOSE: Financial calculations for loans, investments

# Tax calculations
def calculate_tax [income: float, rate: float] {
  $income * ($rate / 100)
}

def net_income [gross: float, tax_rate: float] {
  let tax = calculate_tax $gross $tax_rate
  {
    gross: $gross,
    tax: $tax,
    net: ($gross - $tax),
    tax_rate: $tax_rate
  }
}
net_income 50000 22                                  # Calculate net income after 22% tax
# PURPOSE: Tax and payroll calculations
```

**Unit conversions and measurements:**

```nu
# Temperature conversions
def celsius_to_fahrenheit [celsius: float] {
  ($celsius * 9 / 5) + 32
}

def fahrenheit_to_celsius [fahrenheit: float] {
  ($fahrenheit - 32) * 5 / 9
}

celsius_to_fahrenheit 25                             # 77°F
fahrenheit_to_celsius 77                             # 25°C

# Distance conversions
def miles_to_kilometers [miles: float] {
  $miles * 1.60934
}

def kilometers_to_miles [km: float] {
  $km / 1.60934
}

# Weight conversions
def pounds_to_kilograms [pounds: float] {
  $pounds / 2.20462
}

def kilograms_to_pounds [kg: float] {
  $kg * 2.20462
}

# Data size conversions
def bytes_to_human [bytes: int] {
  if $bytes >= 1073741824 {
    $"(($bytes / 1073741824) | math round -p 2)GB"
  } else if $bytes >= 1048576 {
    $"(($bytes / 1048576) | math round -p 2)MB"
  } else if $bytes >= 1024 {
    $"(($bytes / 1024) | math round -p 2)KB"
  } else {
    $"($bytes)B"
  }
}
bytes_to_human 1536                                  # "1.5KB"
bytes_to_human 2048576                               # "1.95MB"
# PURPOSE: Convert between different units for user-friendly display
```

**Random number generation and statistics:**

```nu
# Generate random numbers
random int 1..100                                    # Random integer between 1 and 100
random float 0..1                                    # Random float between 0 and 1
random bool                                          # Random boolean

# Generate random data sets
def random_dataset [size: int, min: int = 0, max: int = 100] {
  1..$size | each { random int $min..$max }
}
random_dataset 10                                    # 10 random numbers

# Statistical analysis of data
def analyze_dataset [data: list<float>] {
  {
    count: ($data | length),
    sum: ($data | math sum),
    mean: ($data | math avg),
    median: ($data | math median),
    min: ($data | math min),
    max: ($data | math max),
    std_dev: ($data | math stddev),
    range: (($data | math max) - ($data | math min))
  }
}

let sample_data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
analyze_dataset $sample_data
# PURPOSE: Generate test data and perform statistical analysis
```

## Advanced Filtering & Sorting {#advanced-filtering}

### Complex Filtering Techniques

**Multi-condition filtering with logical operators:**

```nu
# AND conditions - all conditions must be true
ls | where type == file and size > 1mb and name =~ "\.log$"
ps | where cpu > 5.0 and mem > 100 and name !~ "kernel"
# PURPOSE: Precise filtering with multiple requirements

# OR conditions - any condition can be true
ls | where size > 10mb or name =~ "\.exe$" or modified > (date now) - 1day
ps | where cpu > 50.0 or mem > 1000 or name =~ "chrome"
# PURPOSE: Broad filtering catching multiple scenarios

# Complex nested conditions
ls | where (type == file and size > 1mb) or (type == dir and name =~ "^\\.")
ps | where (name =~ "python" and cpu > 10) or (name =~ "node" and mem > 200)
# PURPOSE: Sophisticated filtering logic combining AND/OR

# NOT conditions - exclude matching items
ls | where not (name =~ "\\.(tmp|bak)$")               # Exclude temp and backup files
ps | where not (name =~ "(kernel|system)")             # Exclude system processes
# PURPOSE: Filter out unwanted items by pattern

# Range filtering
[1, 5, 10, 15, 20, 25, 30] | where $it > 10 and $it < 25    # Values between 10 and 25
ls | where size > 1kb and size < 1mb                          # Files in specific size range
# PURPOSE: Filter data within specific bounds
```

**Custom filtering functions:**

```nu
# Define reusable filter predicates
def is_large_file [file: record] {
  $file.size > 10mb
}

def is_recent_file [file: record, days: int = 7] {
  $file.modified > (date now) - ($days * 1day)
}

def is_executable_file [file: record] {
  ($file.name | str ends-with ".exe") or
  ($file.name | str ends-with ".sh") or
  ($file.name | str ends-with ".py")
}

# Use custom filters
ls | where {|file| is_large_file $file}                      # Large files
ls | where {|file| is_recent_file $file 30}                  # Files from last 30 days
ls | where {|file| is_executable_file $file}                 # Executable files
# PURPOSE: Create reusable, readable filter logic

# Combine multiple custom filters
ls | where {|file|
  (is_large_file $file) and
  (is_recent_file $file 7) and
  not (is_executable_file $file)
}
# PURPOSE: Complex filtering with readable, maintainable logic

# Conditional filtering based on context
def filter_by_context [files: table, context: string] {
  match $context {
    "development" => ($files | where name =~ "\\.(rs|py|js|ts|go)$"),
    "documents" => ($files | where name =~ "\\.(txt|md|pdf|doc)$"),
    "media" => ($files | where name =~ "\\.(jpg|png|mp4|mp3|avi)$"),
    "large" => ($files | where size > 100mb),
    "recent" => ($files | where modified > (date now) - 7day),
    _ => $files
  }
}
ls | filter_by_context "development"                          # Filter for dev files
# PURPOSE: Context-aware filtering for different use cases
```

**Pattern-based filtering with regex:**

```nu
# File extension filtering
ls | where name =~ "\\.(txt|md|rst)$"                        # Text files
ls | where name =~ "\\.(jpg|jpeg|png|gif)$"                  # Image files
ls | where name =~ "\\.(mp4|avi|mkv|mov)$"                   # Video files
# PURPOSE: Group files by type using regex patterns

# Name pattern filtering
ls | where name =~ "^test_"                                  # Files starting with "test_"
ls | where name =~ "_backup$"                                # Files ending with "_backup"
ls | where name =~ "\\d{4}-\\d{2}-\\d{2}"                   # Files with date pattern
# PURPOSE: Find files following naming conventions

# Process name patterns
ps | where name =~ "^python\\d*$"                            # Python processes (python, python3, etc.)
ps | where name =~ "chrome|firefox|safari"                   # Web browsers
ps | where name =~ "\\.(exe|service)$"                       # Windows executables and services
# PURPOSE: Group processes by type or application

# Content-based filtering (for text data)
def filter_log_entries [logs: list<string>, level: string] {
  $logs | where { str contains $"[$($level)]" }
}
open app.log | lines | filter_log_entries "ERROR"           # Error log entries
# PURPOSE: Filter text content by patterns

# Advanced regex with capture groups
def extract_version_info [files: table] {
  $files
  | where name =~ "v\\d+\\.\\d+\\.\\d+"                    # Files with version numbers
  | each {|file|
      let version_match = $file.name | parse -r "v(?P<version>\\d+\\.\\d+\\.\\d+)"
      if ($version_match | length) > 0 {
        $file | insert version ($version_match | get 0 | get version)
      } else {
        $file
      }
    }
}
# PURPOSE: Extract and use pattern data for enhanced filtering
```

### Advanced Sorting Operations

**Multi-level sorting:**

```nu
# Sort by multiple columns in order of priority
ls | sort-by type name                                       # Sort by type, then name
ps | sort-by cpu mem                                         # Sort by CPU, then memory
ls | sort-by modified size name                              # Sort by date, size, then name
# PURPOSE: Complex ordering with primary and secondary criteria

# Mixed ascending/descending sorts
ls | sort-by type -r size                                    # Type ascending, size descending
ps | sort-by name -r cpu                                     # Name ascending, CPU descending
# PURPOSE: Fine-grained control over sort direction

# Custom sorting with closures
ls | sort-by { |file| $file.size / 1024 }                   # Sort by size in KB
ps | sort-by { |proc| $proc.cpu + $proc.mem }               # Sort by combined CPU+memory
ls | sort-by { |file| $file.name | str length }             # Sort by name length
# PURPOSE: Sort by calculated values or complex criteria

# Stable sorting (preserves order of equal elements)
ls | sort-by -s type size                                    # Stable sort preserving original order
# PURPOSE: Maintain predictable ordering for equal values

# Natural sorting (handles numbers in strings correctly)
["file1.txt", "file10.txt", "file2.txt", "file20.txt"] | sort
# This gives proper natural order: file1.txt, file2.txt, file10.txt, file20.txt
# PURPOSE: Human-readable sorting of alphanumeric data
```

**Custom sorting algorithms and comparisons:**

```nu
# Sort with custom comparison function
def sort_by_priority [items: list<record>] {
  $items | sort-by {|item|
    match $item.priority {
      "high" => 1,
      "medium" => 2,
      "low" => 3,
      _ => 4
    }
  }
}

let tasks = [
  {name: "Task A", priority: "low"},
  {name: "Task B", priority: "high"},
  {name: "Task C", priority: "medium"}
]
$tasks | sort_by_priority
# PURPOSE: Sort by custom business logic, not just alphabetical

# Sort by complex calculated values
def sort_files_by_age [files: table] {
  $files | sort-by {|file|
    let age_days = ((date now) - $file.modified) / 1day
    match true {
      ($age_days < 1) => 1,      # Very recent
      ($age_days < 7) => 2,      # Recent
      ($age_days < 30) => 3,     # Moderate
      _ => 4                     # Old
    }
  }
}
ls | sort_files_by_age
# PURPOSE: Sort by computed categories rather than raw values

# Reverse sorting operations
ls | sort-by size | reverse                                  # Sort by size, then reverse
ps | sort-by cpu | reverse | first 10                       # Top 10 CPU processes
# PURPOSE: Get highest values first after sorting

# Sort with null handling
def safe_sort_by [items: list, field: string] {
  $items | sort-by {|item|
    let value = $item | get $field
    if ($value | is-empty) {
      0  # Put null values first
    } else {
      $value
    }
  }
}
# PURPOSE: Handle missing data gracefully in sorting
```

**Ranking and percentile operations:**

```nu
# Add rank to sorted data
ls | sort-by -r size | enumerate | each {|item|
  $item.item | insert rank ($item.index + 1)
}
# PURPOSE: Add ranking information to sorted results

# Calculate percentiles
def add_percentiles [data: list<record>, field: string] {
  let sorted_data = $data | sort-by $field
  let total = $sorted_data | length

  $sorted_data | enumerate | each {|item|
    let percentile = (($item.index + 1) / $total * 100) | math round
    $item.item | insert percentile $percentile
  }
}
ps | add_percentiles "cpu"                                   # Add CPU usage percentiles
# PURPOSE: Calculate relative rankings and percentiles

# Top/bottom N with ranking
def top_n_with_rank [data: list<record>, field: string, n: int] {
  $data
  | sort-by -r $field
  | first $n
  | enumerate
  | each {|item| $item.item | insert rank ($item.index + 1)}
}
ps | top_n_with_rank "cpu" 5                                # Top 5 CPU processes with rank
# PURPOSE: Get ranked top performers

# Quartile analysis
def quartile_analysis [data: list<float>] {
  let sorted = $data | sort
  let len = $sorted | length
  {
    q1: ($sorted | get (($len / 4) | math floor)),
    q2_median: ($sorted | get (($len / 2) | math floor)),
    q3: ($sorted | get ((3 * $len / 4) | math floor)),
    min: ($sorted | first),
    max: ($sorted | last)
  }
}
ls | get size | quartile_analysis                           # Size distribution quartiles
# PURPOSE: Statistical analysis of data distribution
```

## Data Import/Export Commands {#data-import-export}

### File Format Operations - Complete Data Exchange

**JSON operations - comprehensive JSON handling:**

```nu
# Read JSON from file
open data.json                                              # Auto-detects JSON and parses
open data.json | from json                                  # Explicit JSON parsing
# PURPOSE: Load structured data from JSON files

# Convert data to JSON
ls | to json                                                # Convert table to JSON string
ls | to json --indent 2                                     # Pretty-printed JSON with indentation
{name: "John", age: 30} | to json                          # Convert record to JSON
# PURPOSE: Export data to JSON format for APIs, storage

# Save data as JSON
ls | select name size | to json | save files.json          # Save file listing as JSON
ps | where cpu > 10 | to json | save high_cpu_processes.json
# PURPOSE: Persist structured data in portable format

# Complex JSON manipulation
def process_json_api [url: string] {
  http get $url                                             # Fetch JSON from API
  | get items                                               # Extract array field
  | where status == "active"                                # Filter active items
  | select id name created_at                               # Select specific fields
  | each {|item|                                            # Transform each item
      $item | insert age_days ((date now) - ($item.created_at | into datetime)) / 1day
    }
  | to json                                                 # Convert back to JSON
}
# PURPOSE: Process API responses with filtering and transformation

# Handle nested JSON structures
def flatten_json [data: record, prefix: string = ""] {
  $data | transpose key value | each {|item|
    let new_key = if ($prefix | is-empty) { $item.key } else { $"($prefix).($item.key)" }
    if ($item.value | describe | str starts-with "record") {
      flatten_json $item.value $new_key
    } else {
      {key: $new_key, value: $item.value}
    }
  } | flatten
}
# PURPOSE: Flatten complex nested JSON for easier processing
```

**CSV operations - tabular data mastery:**

```nu
# Read CSV files with various options
open data.csv                                              # Auto-detect and parse CSV
open data.csv | from csv                                   # Explicit CSV parsing
open data.csv | from csv --separator ";"                   # Custom separator (semicolon)
open data.csv | from csv --no-headers                      # CSV without header row
# PURPOSE: Import tabular data from various CSV formats

# Save data as CSV
ls | select name size type | to csv | save directory_listing.csv
ps | where cpu > 5 | select name pid cpu mem | to csv | save active_processes.csv
# PURPOSE: Export data to CSV for spreadsheet applications

# CSV with custom formatting
ls | to csv --separator "|"                                # Custom separator
ls | to csv --no-headers                                   # Skip header row in output
# PURPOSE: Generate CSV compatible with different systems

# Process large CSV files efficiently
def process_large_csv [filename: string, batch_size: int = 1000] {
  let total_lines = open $filename | lines | length
  let batches = ($total_lines / $batch_size) | math ceil

  0..($batches - 1) | each {|batch|
    open $filename
    | lines
    | skip ($batch * $batch_size)
    | take $batch_size
    | from csv
    | where amount > 100  # Example processing
  } | flatten
}
# PURPOSE: Handle large CSV files without memory issues

# CSV data analysis and cleaning
def analyze_csv [filename: string] {
  let data = open $filename | from csv
  let columns = $data | transpose | get column0

  {
    rows: ($data | length),
    columns: ($columns | length),
    column_names: $columns,
    sample_data: ($data | first 3),
    column_types: ($columns | each {|col|
      let sample_values = $data | get $col | first 10 | compact
      {
        column: $col,
        type: ($sample_values | get 0 | describe),
        non_null_count: ($sample_values | length)
      }
    })
  }
}
# PURPOSE: Analyze CSV structure and data quality
```

**XML and HTML processing:**

```nu
# Parse XML data
'<users><user id="1"><name>John</name><age>30</age></user></users>' | from xml
# PURPOSE: Process XML configuration files, APIs, documents

# Convert data to XML
[{name: "John", age: 30}, {name: "Jane", age: 25}] | to xml
# PURPOSE: Generate XML for legacy systems, configuration files

# Extract data from HTML (basic)
def extract_links [html: string] {
  $html | parse -r '<a[^>]*href="([^"]*)"[^>]*>([^<]*)</a>'
}
# PURPOSE: Extract structured data from HTML documents

# Web scraping with structured data extraction
def scrape_table [url: string, table_selector: string] {
  http get $url
  | query web $table_selector  # Requires query plugin
  | each {|row|
      $row | transpose | each {|cell| $cell.column1}
    }
}
# PURPOSE: Extract table data from web pages
```

**YAML and TOML operations:**

```nu
# Read YAML configuration files
open config.yaml | from yaml                               # Parse YAML
open docker-compose.yml | from yaml                        # Docker compose files
# PURPOSE: Load configuration files, CI/CD definitions

# Convert to YAML
{database: {host: "localhost", port: 5432}} | to yaml | save config.yaml
# PURPOSE: Generate human-readable configuration files

# Read TOML files (Cargo.toml, pyproject.toml, etc.)
open Cargo.toml | from toml                                # Parse TOML
open pyproject.toml | from toml                            # Python project config
# PURPOSE: Process programming language configuration files

# Convert to TOML
{package: {name: "myapp", version: "1.0.0"}} | to toml | save Cargo.toml
# PURPOSE: Generate configuration files for Rust, Python projects

# Configuration file management
def update_config [file: string, key: string, value: any] {
  let config = open $file | from toml
  let updated = $config | update $key $value
  $updated | to toml | save $file
}
update_config "config.toml" "database.port" 5433
# PURPOSE: Programmatically update configuration files
```

### Database and API Integration

**SQL-style operations on data:**

```nu
# SQL-like queries on structured data
ls | select name size | where size > 1mb | sort-by -r size | first 10
# SELECT name, size FROM ls WHERE size > 1mb ORDER BY size DESC LIMIT 10
# PURPOSE: Apply database query patterns to file system data

# JOIN operations (combining data sources)
def join_data [left: table, right: table, left_key: string, right_key: string] {
  $left | each {|left_row|
    let matching = $right | where { get $right_key } == ($left_row | get $left_key)
    if ($matching | length) > 0 {
      $left_row | merge ($matching | first)
    } else {
      $left_row
    }
  }
}

let users = [{id: 1, name: "John"}, {id: 2, name: "Jane"}]
let orders = [{user_id: 1, product: "laptop"}, {user_id: 1, product: "mouse"}]
join_data $users $orders "id" "user_id"
# PURPOSE: Combine related data from different sources

# GROUP BY operations with aggregation
ls | group-by type | transpose type files | each {|group|
  {
    type: $group.type,
    count: ($group.files | length),
    total_size: ($group.files | get size | math sum),
    avg_size: ($group.files | get size | math avg)
  }
}
# SELECT type, COUNT(*), SUM(size), AVG(size) FROM ls GROUP BY type
# PURPOSE: Create summary statistics grouped by categories

# Window functions (running totals, rankings)
def add_running_total [data: table, field: string] {
  let mut total = 0
  $data | each {|row|
    let value = $row | get $field
    $total = $total + $value
    $row | insert running_total $total
  }
}
ls | sort-by size | add_running_total "size"
# PURPOSE: Add cumulative calculations to data
```

**API integration patterns:**

```nu
# RESTful API operations
def api_get [base_url: string, endpoint: string, headers: record = {}] {
  let url = $"($base_url)/($endpoint)"
  let header_args = $headers | transpose key value | each { $"($in.key): ($in.value)" }

  try {
    http get $url --headers $header_args
  } catch {|error|
    {error: $error.msg, url: $url}
  }
}

def api_post [base_url: string, endpoint: string, data: record, headers: record = {}] {
  let url = $"($base_url)/($endpoint)"
  let default_headers = {"Content-Type": "application/json"}
  let all_headers = $default_headers | merge $headers

  try {
    http post $url ($data | to json) --headers $all_headers
  } catch {|error|
    {error: $error.msg, url: $url}
  }
}
# PURPOSE: Standardized API interaction with error handling

# Batch API operations
def batch_api_requests [requests: list<record>] {
  $requests | each {|req|
    try {
      let response = http get $req.url
      {request: $req, response: $response, status: "success"}
    } catch {|error|
      {request: $req, error: $error.msg, status: "failed"}
    }
  }
}

let urls = [
  {name: "service1", url: "https://api.service1.com/health"},
  {name: "service2", url: "https://api.service2.com/health"}
]
batch_api_requests $urls
# PURPOSE: Monitor multiple APIs, services concurrently

# API response processing pipeline
def process_api_response [url: string] {
  http get $url
  | get data                                                # Extract data field
  | where status == "active"                                # Filter active records
  | each {|item| $item | update created_at ($item.created_at | into datetime)}  # Parse dates
  | sort-by created_at                                      # Sort by date
  | each {|item|                                            # Add computed fields
      $item | insert age_days ((date now) - $item.created_at) / 1day
    }
}
# PURPOSE: Standard API response processing with filtering and transformation
```

**Data synchronization and ETL operations:**

```nu
# Extract, Transform, Load (ETL) pipeline
def etl_pipeline [source: string, destination: string] {
  # Extract
  let raw_data = open $source | from csv

  # Transform
  let transformed = $raw_data
  | where amount > 0                                        # Filter valid amounts
  | each {|row|                                             # Clean and standardize
      $row
      | update amount ($row.amount | into float)
      | update date ($row.date | into datetime)
      | insert month ($row.date | format date "%Y-%m")
    }
  | group-by month                                          # Group by month
  | transpose month transactions                            # Pivot for aggregation
  | each {|group|                                           # Calculate monthly totals
      {
        month: $group.month,
        transaction_count: ($group.transactions | length),
        total_amount: ($group.transactions | get amount | math sum),
        avg_amount: ($group.transactions | get amount | math avg)
      }
    }

  # Load
  $transformed | to json | save $destination
  echo $"ETL completed: ($source) -> ($destination)"
}
# PURPOSE: Complete data processing pipeline from source to destination

# Data validation and quality checks
def validate_data [data: table, rules: record] {
  let validation_results = $rules | transpose field rule | each {|check|
    let field = $check.field
    let rule = $check.rule

    let failures = $data | where {|row|
      let value = $row | get $field
      not (do $rule $value)
    }

    {
      field: $field,
      total_records: ($data | length),
      failed_records: ($failures | length),
      pass_rate: ((($data | length) - ($failures | length)) / ($data | length) * 100),
      sample_failures: ($failures | first 3)
    }
  }

  {
    overall_quality: ($validation_results | get pass_rate | math avg),
    field_results: $validation_results
  }
}

# Usage example
let validation_rules = {
  email: {|email| $email | str contains "@"},
  age: {|age| $age > 0 and $age < 150},
  phone: {|phone| ($phone | str length) >= 10}
}
validate_data $user_data $validation_rules
# PURPOSE: Ensure data quality before processing or storage
```

<function_calls>
<invoke name="TodoWrite">
<parameter name="todos">[{"content": "Remove Parts 3 and 4 from the book", "status": "completed", "activeForm": "Removing Parts 3 and 4 from the book"}, {"content": "Massively expand Part 1 (CLI commands) with detailed explanations", "status": "completed", "activeForm": "Massively expanding Part 1 with detailed explanations"}, {"content": "Massively expand Part 2 (programming fundamentals) with excessive detail", "status": "in_progress", "activeForm": "Massively expanding Part 2 with excessive detail"}, {"content": "Add command purpose explanations for every command shown", "status": "completed", "activeForm": "Adding command purpose explanations"}, {"content": "Maintain impatient tone while being comprehensive", "status": "completed", "activeForm": "Maintaining impatient tone while being comprehensive"}, {"content": "Commit and push the revised book", "status": "pending", "activeForm": "Committing and pushing the revised book"}]

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

# Part II: Programming Fundamentals - Every Detail Explained

## Variables and Data Types Deep Dive {#variables-types}

### Variable Declaration and Scoping - Master Variable Management

**Basic variable declaration - the foundation:**

```nu
# Simple variable declaration
let name = "John"                                      # Immutable string variable
let age = 30                                           # Immutable integer variable
let height = 5.9                                       # Immutable float variable
let is_active = true                                   # Immutable boolean variable
# PURPOSE: Create immutable variables for data that shouldn't change

# Using variables in expressions
echo $name                                             # Output: John
echo $"Hello, ($name)!"                               # String interpolation: Hello, John!
echo $"Age: ($age), Height: ($height)"                # Multiple interpolations
# PURPOSE: Access and display variable values in various contexts

# Variable naming conventions
let user_name = "alice"                                # Snake case (recommended)
let userAge = 25                                       # Camel case (acceptable)
let USER_ROLE = "admin"                                # Constants in upper case
let is_admin = true                                    # Boolean with descriptive prefix
# PURPOSE: Follow consistent naming for code readability

# Complex variable assignments
let full_name = $"($first_name) ($last_name)"         # Computed from other variables
let user_info = {name: $name, age: $age}              # Record from variables
let coordinates = [$x, $y, $z]                        # List from variables
# PURPOSE: Build complex data structures from simple variables
```

**Mutable variables - use sparingly but effectively:**

```nu
# Mutable variable declaration
mut counter = 0                                        # Mutable integer
mut items = []                                         # Mutable empty list
mut config = {debug: false, verbose: true}            # Mutable record
# PURPOSE: Create variables that need to change during program execution

# Modifying mutable variables
$counter = $counter + 1                                # Increment counter
$items = $items | append "new item"                    # Add to list
$config.debug = true                                   # Update record field
# PURPOSE: Change variable values as program state evolves

# Mutable variable patterns
mut total = 0
for item in [1, 2, 3, 4, 5] {
  $total = $total + $item                              # Accumulate values
}
echo $total                                            # Output: 15
# PURPOSE: Accumulate values in loops and iterative processes

# When to use mutable vs immutable
let readonly_config = {host: "localhost", port: 8080} # Immutable - configuration
mut session_data = {user: null, logged_in: false}     # Mutable - session state
let pi = 3.14159                                       # Immutable - constants
mut progress = 0                                       # Mutable - changing values
# PURPOSE: Choose appropriate variable type based on whether data changes
```

**Environment variables - system integration:**

```nu
# Access environment variables
$env.HOME                                              # User home directory
$env.PATH                                              # System PATH
$env.USER                                              # Current username
$env.PWD                                               # Current working directory
# PURPOSE: Access system configuration and user settings

# Set environment variables for current session
$env.MY_APP_DEBUG = "true"                             # Set custom variable
$env.DATABASE_URL = "postgres://localhost/mydb"       # Database connection
$env.API_KEY = "your-secret-key-here"                  # API credentials
# PURPOSE: Configure application behavior through environment

# Load environment from file
load-env {
  DATABASE_HOST: "localhost"
  DATABASE_PORT: "5432"
  DEBUG_MODE: "false"
}
# PURPOSE: Batch load configuration from structured data

# Environment variable fallbacks
let debug_mode = $env.DEBUG_MODE? | default "false"   # Use default if not set
let port = $env.PORT? | default 3000 | into int       # Convert with default
let database_url = $env.DATABASE_URL? | default "sqlite://local.db"
# PURPOSE: Handle missing environment variables gracefully

# Cross-platform environment handling
def get_home_dir [] {
  if ($nu.os-info.name == "windows") {
    $env.USERPROFILE
  } else {
    $env.HOME
  }
}
# PURPOSE: Handle platform differences in environment variables
```

### Advanced Data Type Operations

**Type inspection and validation:**

```nu
# Comprehensive type checking
42 | describe                                          # "int" - integer type
3.14 | describe                                        # "float" - floating point
"hello" | describe                                     # "string" - text
true | describe                                        # "bool" - boolean
[1, 2, 3] | describe                                   # "list<int>" - list of integers
{name: "John"} | describe                              # "record" - record structure
null | describe                                        # "nothing" - null value
# PURPOSE: Understand data types for appropriate operations and debugging

# Type validation functions
def is_string [value: any] -> bool {
  ($value | describe) == "string"
}

def is_number [value: any] -> bool {
  let type = $value | describe
  $type == "int" or $type == "float"
}

def is_list [value: any] -> bool {
  ($value | describe) | str starts-with "list"
}

def is_record [value: any] -> bool {
  ($value | describe) == "record"
}
# PURPOSE: Create reusable type checking functions for validation

# Runtime type checking with error handling
def safe_add [a: any, b: any] -> any {
  if (is_number $a) and (is_number $b) {
    $a + $b
  } else {
    error make {msg: $"Cannot add ($a | describe) and ($b | describe)"}
  }
}
# PURPOSE: Validate types before operations to prevent runtime errors
```

**Type conversion mastery:**

```nu
# String to numeric conversions
"42" | into int                                        # String to integer
"3.14159" | into float                                 # String to float
"123.45" | into int                                    # Float string to int (truncates)
# PURPOSE: Convert string input to numeric types for calculations

# Numeric to string conversions
42 | into string                                       # Integer to string
3.14159 | into string                                  # Float to string
# PURPOSE: Convert numbers to strings for display or string operations

# Boolean conversions
"true" | into bool                                     # String "true" to boolean true
"false" | into bool                                    # String "false" to boolean false
1 | into bool                                          # Non-zero to true
0 | into bool                                          # Zero to false
# PURPOSE: Convert various types to boolean for conditional logic

# Advanced type conversions with validation
def safe_into_int [value: any] -> int {
  try {
    $value | into int
  } catch {
    0  # Default value on conversion failure
  }
}

def safe_into_float [value: any] -> float {
  try {
    $value | into float
  } catch {
    0.0  # Default value on conversion failure
  }
}
# PURPOSE: Handle conversion failures gracefully with defaults

# List and record conversions
[1, 2, 3] | into string                                # List to string representation
{name: "John", age: 30} | into string                  # Record to string representation
# PURPOSE: Convert complex types to strings for logging or display
```

**Complex data structure mastery:**

```nu
# Nested record construction
let employee = {
  personal: {
    name: "Alice Johnson",
    age: 32,
    email: "alice@company.com"
  },
  work: {
    department: "Engineering",
    position: "Senior Developer",
    salary: 85000,
    start_date: "2020-03-15"
  },
  skills: ["rust", "python", "javascript", "sql"],
  active: true
}
# PURPOSE: Structure complex hierarchical data

# Accessing nested data
$employee.personal.name                                # "Alice Johnson"
$employee.work.department                              # "Engineering"
$employee.skills.0                                     # "rust" (first skill)
($employee.skills | get 1)                            # "python" (second skill)
# PURPOSE: Navigate and extract data from nested structures

# Dynamic field access
let field_name = "department"
$employee.work | get $field_name                       # Dynamic field access
# PURPOSE: Access fields using variable names

# Updating nested structures (immutable approach)
let updated_employee = $employee
| update personal.age 33                               # Update nested field
| update skills ($employee.skills | append "go")      # Add to nested list
| update work.salary 90000                             # Update salary
# PURPOSE: Create modified versions of complex structures

# Working with lists of records (table-like data)
let employees = [
  {name: "Alice", dept: "Engineering", salary: 85000, skills: ["rust", "python"]},
  {name: "Bob", dept: "Marketing", salary: 65000, skills: ["excel", "powerpoint"]},
  {name: "Carol", dept: "Engineering", salary: 90000, skills: ["java", "sql", "python"]},
  {name: "David", dept: "Sales", salary: 70000, skills: ["crm", "negotiation"]}
]

# Complex queries on structured data
$employees
| where dept == "Engineering"                          # Filter by department
| where salary > 80000                                 # Filter by salary
| select name salary skills                            # Select specific fields
| each { |emp|                                         # Transform each record
    $emp | insert skill_count ($emp.skills | length)
  }
# PURPOSE: Query and transform complex tabular data like a database
```

## Control Flow: If, Match, Loops Mastery {#control-flow}

### Conditional Statements - Complete Decision Making

**Basic conditional statements with comprehensive examples:**

```nu
# Simple if statement
let age = 25
if $age >= 18 {
  echo "You are an adult"
} else {
  echo "You are a minor"
}
# PURPOSE: Make binary decisions based on conditions

# Multi-condition if-else chain
let score = 85
if $score >= 90 {
  echo "Grade: A - Excellent!"
} else if $score >= 80 {
  echo "Grade: B - Good work!"
} else if $score >= 70 {
  echo "Grade: C - Satisfactory"
} else if $score >= 60 {
  echo "Grade: D - Needs improvement"
} else {
  echo "Grade: F - Failed"
}
# PURPOSE: Handle multiple discrete conditions with clear outcomes

# Complex boolean conditions
let user = {name: "Alice", age: 30, role: "admin", active: true}
if ($user.age >= 21) and ($user.role == "admin") and $user.active {
  echo "User has full administrative access"
} else if ($user.age >= 18) and $user.active {
  echo "User has standard access"
} else {
  echo "Access denied"
}
# PURPOSE: Combine multiple criteria for complex decision making

# Conditional assignment
let status = if $age >= 18 { "adult" } else { "minor" }
let tax_rate = if $income > 50000 { 0.25 } else { 0.15 }
let access_level = if $user.role == "admin" { "full" } else { "limited" }
# PURPOSE: Set variables based on conditions concisely

# Nested conditionals for complex logic
def determine_shipping_cost [weight: float, distance: int, priority: string] {
  if $priority == "express" {
    if $weight > 10 {
      if $distance > 500 {
        25.99
      } else {
        19.99
      }
    } else {
      if $distance > 500 {
        15.99
      } else {
        12.99
      }
    }
  } else {
    if $weight > 10 {
      9.99
    } else {
      5.99
    }
  }
}
# PURPOSE: Handle complex business logic with multiple decision points
```

**Advanced conditional patterns:**

```nu
# Conditional execution with early returns
def validate_user [user: record] -> string {
  if ($user.name | is-empty) {
    return "Name is required"
  }

  if ($user.age | is-empty) {
    return "Age is required"
  }

  if $user.age < 13 {
    return "Must be at least 13 years old"
  }

  if not ($user.email | str contains "@") {
    return "Valid email is required"
  }

  "User is valid"
}
# PURPOSE: Validate data with clear error messages and early exits

# Conditional with type checking
def safe_divide [a: any, b: any] -> any {
  if not (is_number $a) {
    return {error: "First argument must be a number"}
  }

  if not (is_number $b) {
    return {error: "Second argument must be a number"}
  }

  if $b == 0 {
    return {error: "Cannot divide by zero"}
  }

  $a / $b
}
# PURPOSE: Perform operations with comprehensive safety checks

# Conditional data processing
def process_file_by_size [file: record] -> record {
  let processed = if $file.size > 1gb {
    $file | insert processing_type "large_file_handler"
  } else if $file.size > 100mb {
    $file | insert processing_type "medium_file_handler"
  } else {
    $file | insert processing_type "small_file_handler"
  }

  $processed | insert processed_at (date now)
}
# PURPOSE: Route data processing based on characteristics
```

### Pattern Matching - Advanced Decision Making

**Match statements for complex pattern matching:**

```nu
# Basic pattern matching
let fruit = "apple"
let color = match $fruit {
  "apple" => "red",
  "banana" => "yellow",
  "grape" => "purple",
  "orange" => "orange",
  _ => "unknown color"
}
# PURPOSE: Map values to outcomes cleanly and readably

# Pattern matching with conditions
let temperature = 75
let weather_advice = match $temperature {
  $t if $t < 32 => "It's freezing! Stay inside and bundle up.",
  $t if $t < 50 => "Cold weather. Wear a heavy coat.",
  $t if $t < 70 => "Cool weather. A light jacket should be fine.",
  $t if $t < 80 => "Pleasant weather. Perfect for outdoor activities.",
  $t if $t < 90 => "Warm weather. Stay hydrated and seek shade.",
  _ => "Very hot! Avoid prolonged outdoor exposure."
}
# PURPOSE: Provide contextual responses based on ranges

# Pattern matching with complex data
def categorize_employee [employee: record] -> string {
  match $employee {
    $emp if ($emp.years_experience >= 10) and ($emp.salary >= 100000) => "Senior Executive",
    $emp if ($emp.years_experience >= 7) and ($emp.salary >= 80000) => "Senior Professional",
    $emp if ($emp.years_experience >= 4) and ($emp.salary >= 60000) => "Mid-level Professional",
    $emp if ($emp.years_experience >= 2) => "Junior Professional",
    $emp if ($emp.years_experience >= 0) => "Entry Level",
    _ => "Invalid employee data"
  }
}
# PURPOSE: Classify complex data based on multiple criteria

# Pattern matching for error handling
def handle_api_response [response: record] -> record {
  match $response {
    $r if ($r.status == 200) => {
      success: true,
      data: $r.body,
      message: "Request successful"
    },
    $r if ($r.status == 404) => {
      success: false,
      error: "Resource not found",
      retry_suggested: false
    },
    $r if ($r.status >= 500) => {
      success: false,
      error: "Server error",
      retry_suggested: true
    },
    $r if ($r.status >= 400) => {
      success: false,
      error: "Client error",
      retry_suggested: false
    },
    _ => {
      success: false,
      error: "Unknown response",
      retry_suggested: false
    }
  }
}
# PURPOSE: Handle different response scenarios with appropriate actions

# Pattern matching for data transformation
def format_file_info [file: record] -> string {
  match $file {
    $f if ($f.type == "file") and ($f.size > 1gb) =>
      $"📄 ($f.name) - Large file (($f.size | bytes_to_human))",
    $f if ($f.type == "file") and ($f.name | str ends-with ".exe") =>
      $"⚙️ ($f.name) - Executable (($f.size | bytes_to_human))",
    $f if ($f.type == "file") and ($f.name | str contains "log") =>
      $"📋 ($f.name) - Log file (($f.size | bytes_to_human))",
    $f if ($f.type == "file") =>
      $"📄 ($f.name) (($f.size | bytes_to_human))",
    $f if ($f.type == "dir") =>
      $"📁 ($f.name)/",
    _ => $"❓ Unknown file type"
  }
}
# PURPOSE: Create rich, contextual descriptions based on file characteristics
```

### Loop Constructs - Master Iteration

**For loops with comprehensive examples:**

```nu
# Basic range iteration
for i in 1..5 {
  echo $"Processing item ($i)"
}
# PURPOSE: Repeat operations a specific number of times

# Iterate over lists with various data types
let fruits = ["apple", "banana", "cherry", "date"]
for fruit in $fruits {
  echo $"I like ($fruit)s"
}
# PURPOSE: Process each item in a collection individually

# Iterate over complex data structures
let employees = [
  {name: "Alice", dept: "Engineering", salary: 75000},
  {name: "Bob", dept: "Marketing", salary: 65000},
  {name: "Carol", dept: "Engineering", salary: 80000}
]

for employee in $employees {
  let status = if $employee.salary > 70000 { "high" } else { "standard" }
  echo $"($employee.name) from ($employee.dept): ($status) salary"
}
# PURPOSE: Process structured data with conditional logic

# For loops with enumeration and indexing
let items = ["first", "second", "third", "fourth"]
for item in ($items | enumerate) {
  echo $"Item ($item.index): ($item.item)"
}
# PURPOSE: Access both index and value during iteration

# Nested loops for complex processing
let matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
]

for row in ($matrix | enumerate) {
  echo $"Row ($row.index):"
  for cell in ($row.item | enumerate) {
    echo $"  Column ($cell.index): ($cell.item)"
  }
}
# PURPOSE: Process multi-dimensional data structures

# For loops with filtering and transformation
let numbers = 1..20
for num in $numbers {
  if ($num mod 2) == 0 {
    echo $"($num) is even"
  } else if $num > 15 {
    echo $"($num) is odd and greater than 15"
  }
}
# PURPOSE: Combine iteration with conditional processing
```

**While loops and advanced iteration patterns:**

```nu
# Basic while loop with counter
mut counter = 0
while $counter < 5 {
  echo $"Counter value: ($counter)"
  $counter = $counter + 1
}
# PURPOSE: Repeat operations until a condition is met

# While loop with complex conditions
mut attempts = 0
mut success = false
while ($attempts < 3) and (not $success) {
  $attempts = $attempts + 1
  echo $"Attempt ($attempts)..."

  # Simulate some operation that might succeed
  let result = (random bool)
  if $result {
    $success = true
    echo "Operation succeeded!"
  } else {
    echo "Operation failed, retrying..."
  }
}

if not $success {
  echo "All attempts failed"
}
# PURPOSE: Implement retry logic with maximum attempts

# While loop for data processing
mut data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
mut processed = []
while ($data | length) > 0 {
  let batch = $data | take 3
  $data = $data | skip 3

  let batch_sum = $batch | math sum
  $processed = $processed | append $batch_sum
  echo $"Processed batch: ($batch) -> Sum: ($batch_sum)"
}
echo $"Final results: ($processed)"
# PURPOSE: Process data in batches using while loops

# Infinite loop with break conditions
mut user_input = ""
while true {
  $user_input = input "Enter command (quit to exit): "

  if $user_input == "quit" {
    echo "Goodbye!"
    break
  } else if $user_input == "help" {
    echo "Available commands: help, status, quit"
  } else if $user_input == "status" {
    echo "System is running normally"
  } else {
    echo $"Unknown command: ($user_input)"
  }
}
# PURPOSE: Create interactive command loops with multiple exit conditions
```

**Advanced loop control and patterns:**

```nu
# Loop with continue and break
let numbers = 1..20
for num in $numbers {
  if ($num mod 3) == 0 {
    continue  # Skip multiples of 3
  }

  if $num > 15 {
    echo "Stopping at 15"
    break
  }

  echo $"Processing: ($num)"
}
# PURPOSE: Skip certain iterations and exit loops early based on conditions

# Loop accumulator patterns
def sum_squares [numbers: list<int>] -> int {
  mut total = 0
  for num in $numbers {
    $total = $total + ($num * $num)
  }
  $total
}

sum_squares [1, 2, 3, 4, 5]  # Returns 55
# PURPOSE: Accumulate values through iteration

# Loop with error handling
def process_files_safely [files: list<string>] -> list<record> {
  mut results = []

  for file in $files {
    try {
      let content = open $file
      let size = $content | str length
      $results = $results | append {file: $file, size: $size, status: "success"}
    } catch { |error|
      $results = $results | append {file: $file, error: $error.msg, status: "failed"}
    }
  }

  $results
}
# PURPOSE: Process items with comprehensive error handling

# Advanced loop with state management
def simulate_game [rounds: int] -> record {
  mut player_score = 0
  mut computer_score = 0
  mut round_results = []

  for round in 1..$rounds {
    let player_roll = (random int 1..6)
    let computer_roll = (random int 1..6)

    let winner = if $player_roll > $computer_roll {
      $player_score = $player_score + 1
      "player"
    } else if $computer_roll > $player_roll {
      $computer_score = $computer_score + 1
      "computer"
    } else {
      "tie"
    }

    $round_results = $round_results | append {
      round: $round,
      player_roll: $player_roll,
      computer_roll: $computer_roll,
      winner: $winner
    }
  }

  {
    final_scores: {player: $player_score, computer: $computer_score},
    winner: (if $player_score > $computer_score { "player" } else if $computer_score > $player_score { "computer" } else { "tie" }),
    round_details: $round_results
  }
}
# PURPOSE: Manage complex state through multiple iterations
```

## Functions and Custom Commands Complete Guide {#functions-commands}

### Basic Function Definition and Parameters

**Simple function definitions with comprehensive examples:**

```nu
# Basic function with no parameters
def greet [] {
  echo "Hello, World!"
}
greet  # Call the function
# PURPOSE: Create reusable code blocks for common operations

# Function with single parameter
def greet_person [name: string] {
  echo $"Hello, ($name)!"
}
greet_person "Alice"  # Output: Hello, Alice!
# PURPOSE: Create parameterized functions for flexible operations

# Function with multiple parameters and types
def calculate_rectangle_area [width: float, height: float] -> float {
  $width * $height
}
let area = calculate_rectangle_area 5.5 3.2  # Returns 17.6
# PURPOSE: Perform calculations with strongly typed parameters

# Function with mixed parameter types
def create_user_profile [name: string, age: int, active: bool] -> record {
  {
    name: $name,
    age: $age,
    active: $active,
    created_at: (date now),
    id: (random int 1000..9999)
  }
}
let user = create_user_profile "John Doe" 30 true
# PURPOSE: Build complex data structures from simple inputs

# Function with comprehensive parameter validation
def validate_and_create_account [username: string, email: string, age: int] -> record {
  if ($username | str length) < 3 {
    return {success: false, error: "Username must be at least 3 characters"}
  }

  if not ($email | str contains "@") {
    return {success: false, error: "Email must contain @ symbol"}
  }

  if $age < 13 {
    return {success: false, error: "Must be at least 13 years old"}
  }

  {
    success: true,
    account: {
      username: $username,
      email: $email,
      age: $age,
      created: (date now)
    }
  }
}
# PURPOSE: Validate inputs and provide clear success/failure feedback
```

**Optional parameters and default values:**

```nu
# Optional parameters with type annotations
def greet_optional [name?: string] {
  let actual_name = $name | default "Anonymous"
  echo $"Hello, ($actual_name)!"
}
greet_optional        # Output: Hello, Anonymous!
greet_optional "Bob"  # Output: Hello, Bob!
# PURPOSE: Create flexible functions that work with or without all parameters

# Default parameter values
def create_server_config [
  host: string = "localhost",
  port: int = 8080,
  debug: bool = false
] -> record {
  {
    host: $host,
    port: $port,
    debug: $debug,
    created_at: (date now)
  }
}

create_server_config                                   # Uses all defaults
create_server_config "example.com" 3000 true          # Override all values
create_server_config "api.company.com"                # Override host only
# PURPOSE: Provide sensible defaults while allowing customization

# Complex optional parameter handling
def fetch_data [
  url: string,
  method: string = "GET",
  headers?: record,
  timeout: int = 30,
  retry_count: int = 3
] -> record {
  let request_headers = $headers | default {}

  echo $"Fetching ($url) with method ($method)"
  echo $"Headers: ($request_headers)"
  echo $"Timeout: ($timeout)s, Retries: ($retry_count)"

  {
    url: $url,
    method: $method,
    headers: $request_headers,
    timeout: $timeout,
    retry_count: $retry_count,
    timestamp: (date now)
  }
}
# PURPOSE: Handle complex configurations with optional and default parameters

# Rest parameters for variable arguments
def sum_all [...numbers: int] -> int {
  $numbers | math sum
}
sum_all 1 2 3 4 5                    # 15
sum_all 10 20                        # 30
sum_all                              # 0 (empty list)
# PURPOSE: Accept any number of arguments of the same type

# Mixed parameters with rest
def create_team [team_name: string, leader: string, ...members: string] -> record {
  {
    name: $team_name,
    leader: $leader,
    members: $members,
    total_size: (($members | length) + 1),
    created: (date now)
  }
}
let team = create_team "Development" "Alice" "Bob" "Carol" "David"
# PURPOSE: Combine fixed and variable parameters effectively
```

### Advanced Function Features

**Flags and command-line style interfaces:**

```nu
# Functions with flags (switches)
def list_files [
  path?: string,          # Optional positional parameter
  --all (-a),             # Boolean flag with short alias
  --long (-l),            # Boolean flag
  --sort-by (-s): string, # Flag with value
  --limit: int = 50       # Flag with default value
] {
  let target_path = $path | default "."

  echo $"Listing files in: ($target_path)"

  if $all {
    echo "Including hidden files"
  }

  if $long {
    echo "Using long format"
  }

  if not ($sort_by | is-empty) {
    echo $"Sorting by: ($sort_by)"
  }

  echo $"Limit: ($limit) files"

  # Actual implementation would use ls with these parameters
  ls $target_path
}

# Usage examples:
list_files                                 # Basic usage
list_files --all --long                    # With flags
list_files /home --sort-by size --limit 10 # With flag values
list_files -al -s name                     # Short flag syntax
# PURPOSE: Create command-line style interfaces with flexible options

# Complex flag combinations
def backup_files [
  source_dir: string,
  --destination (-d): string = "./backup",
  --compress (-c),
  --verbose (-v),
  --dry-run (-n),
  --exclude (-x): list<string> = [],
  --max-size (-m): string = "1GB"
] {
  if $dry_run {
    echo "DRY RUN MODE - No files will be copied"
  }

  echo $"Backing up from: ($source_dir)"
  echo $"Backing up to: ($destination)"

  if $compress {
    echo "Compression: enabled"
  }

  if ($exclude | length) > 0 {
    echo $"Excluding: ($exclude | str join ', ')"
  }

  echo $"Max file size: ($max_size)"

  if $verbose {
    echo "Verbose output enabled"
  }

  # Implementation would perform actual backup
  echo "Backup operation would execute here"
}
# PURPOSE: Create sophisticated command interfaces with multiple options
```

**Function composition and higher-order functions:**

```nu
# Functions that return other functions (closures)
def create_multiplier [factor: int] -> closure {
  { |x| $x * $factor }
}

let double = create_multiplier 2
let triple = create_multiplier 3
5 | do $double   # 10
7 | do $triple   # 21
# PURPOSE: Create specialized functions from general templates

# Functions that accept other functions as parameters
def apply_twice [func: closure, value: any] -> any {
  $value | do $func | do $func
}

let increment = { |x| $x + 1 }
apply_twice $increment 5    # Returns 7 (5 + 1 + 1)

let square = { |x| $x * $x }
apply_twice $square 2       # Returns 16 (2² = 4, 4² = 16)
# PURPOSE: Create flexible operations that work with any function

# Complex function composition
def pipe_functions [...functions: closure] -> closure {
  { |initial_value|
    $functions | reduce { |func, acc| $acc | do $func } $initial_value
  }
}

let add_one = { |x| $x + 1 }
let multiply_by_two = { |x| $x * 2 }
let square = { |x| $x * $x }

let complex_operation = pipe_functions $add_one $multiply_by_two $square
5 | do $complex_operation   # ((5 + 1) * 2)² = 144
# PURPOSE: Chain multiple functions together for complex transformations

# Map, filter, and reduce with custom functions
def process_numbers [numbers: list<int>] -> record {
  let doubled = $numbers | each { |x| $x * 2 }
  let evens_only = $doubled | where { |x| ($x mod 2) == 0 }
  let sum_total = $evens_only | math sum

  {
    original: $numbers,
    doubled: $doubled,
    evens: $evens_only,
    final_sum: $sum_total
  }
}
# PURPOSE: Apply functional programming patterns to data processing
```

**Error handling and validation in functions:**

```nu
# Functions with comprehensive error handling
def divide_safely [dividend: float, divisor: float] -> record {
  try {
    if $divisor == 0 {
      error make {
        msg: "Division by zero is not allowed",
        label: {
          text: "Divisor cannot be zero",
          span: (metadata $divisor).span
        }
      }
    }

    {
      success: true,
      result: ($dividend / $divisor)
    }
  } catch { |error|
    {
      success: false,
      error: $error.msg
    }
  }
}
# PURPOSE: Provide safe operations with clear success/failure indication

# Input validation with detailed error messages
def create_email_account [username: string, domain: string] -> record {
  # Validate username
  if ($username | str length) < 2 {
    return {
      success: false,
      error: "Username must be at least 2 characters long",
      field: "username"
    }
  }

  if ($username | str contains " ") {
    return {
      success: false,
      error: "Username cannot contain spaces",
      field: "username"
    }
  }

  # Validate domain
  if not ($domain | str contains ".") {
    return {
      success: false,
      error: "Domain must contain at least one dot",
      field: "domain"
    }
  }

  # Create account if validation passes
  let email = $"($username)@($domain)"
  {
    success: true,
    email: $email,
    created_at: (date now)
  }
}
# PURPOSE: Validate complex inputs with specific error feedback

# Functions with retry logic and error recovery
def fetch_with_retry [url: string, max_attempts: int = 3] -> record {
  mut attempts = 0
  mut last_error = ""

  while $attempts < $max_attempts {
    $attempts = $attempts + 1

    try {
      let response = http get $url
      return {
        success: true,
        data: $response,
        attempts: $attempts
      }
    } catch { |error|
      $last_error = $error.msg
      echo $"Attempt ($attempts) failed: ($error.msg)"

      if $attempts < $max_attempts {
        echo "Retrying in 2 seconds..."
        sleep 2sec
      }
    }
  }

  {
    success: false,
    error: $last_error,
    attempts: $attempts
  }
}
# PURPOSE: Implement robust operations with automatic retry logic
```

## Error Handling the Nushell Way {#error-handling}

### Try-Catch Error Handling

**Basic try-catch patterns:**

```nu
# Simple try-catch for file operations
try {
  let content = open "nonexistent.txt"
  echo $"File content: ($content)"
} catch { |error|
  echo $"Could not read file: ($error.msg)"
}
# PURPOSE: Handle file operations that might fail gracefully

# Try-catch with specific error handling
def safe_file_read [filename: string] -> record {
  try {
    let content = open $filename
    {
      success: true,
      data: $content,
      size: ($content | str length)
    }
  } catch { |error|
    {
      success: false,
      error: $error.msg,
      filename: $filename,
      attempted_at: (date now)
    }
  }
}
# PURPOSE: Wrap file operations with structured error information

# Multiple try-catch blocks for different operations
def backup_important_files [files: list<string>, backup_dir: string] -> record {
  mut successful = []
  mut failed = []

  # Ensure backup directory exists
  try {
    mkdir $backup_dir
  } catch {
    echo $"Warning: Could not create backup directory ($backup_dir)"
  }

  for file in $files {
    try {
      cp $file $backup_dir
      $successful = $successful | append $file
      echo $"✅ Backed up: ($file)"
    } catch { |error|
      $failed = $failed | append {file: $file, error: $error.msg}
      echo $"❌ Failed to backup: ($file) - ($error.msg)"
    }
  }

  {
    successful_backups: $successful,
    failed_backups: $failed,
    total_attempted: ($files | length),
    success_rate: (($successful | length) / ($files | length) * 100)
  }
}
# PURPOSE: Handle batch operations with individual error tracking

# Nested try-catch for complex operations
def process_json_files [directory: string] -> list<record> {
  mut results = []

  try {
    let files = ls $directory | where name =~ "\.json$"

    for file in $files {
      try {
        let data = open $file.name | from json
        try {
          # Attempt to validate the JSON structure
          let validated = if ($data | get id? | is-empty) {
            $data | insert id (random int 1000..9999)
          } else {
            $data
          }

          $results = $results | append {
            file: $file.name,
            status: "success",
            data: $validated
          }
        } catch { |validation_error|
          $results = $results | append {
            file: $file.name,
            status: "validation_failed",
            error: $validation_error.msg
          }
        }
      } catch { |parse_error|
        $results = $results | append {
          file: $file.name,
          status: "parse_failed",
          error: $parse_error.msg
        }
      }
    }
  } catch { |directory_error|
    echo $"Could not access directory ($directory): ($directory_error.msg)"
  }

  $results
}
# PURPOSE: Handle complex nested operations with granular error reporting
```

**Advanced error handling patterns:**

```nu
# Custom error types and error propagation
def create_custom_error [type: string, message: string, context?: record] -> nothing {
  let error_details = {
    type: $type,
    message: $message,
    context: ($context | default {}),
    timestamp: (date now)
  }

  error make {
    msg: $"[($type)] ($message)",
    label: {
      text: $message,
      span: {start: 0, end: 1}
    },
    help: $"Error details: ($error_details | to json)"
  }
}

def divide_with_context [a: float, b: float, operation_id?: string] -> float {
  let id = $operation_id | default "unknown"

  if $b == 0 {
    create_custom_error "DIVISION_ERROR" "Cannot divide by zero" {
      dividend: $a,
      divisor: $b,
      operation_id: $id
    }
  }

  $a / $b
}
# PURPOSE: Create informative, structured errors for complex applications

# Error recovery with fallback strategies
def fetch_data_with_fallbacks [primary_url: string, fallback_urls: list<string>] -> record {
  # Try primary URL first
  try {
    let data = http get $primary_url
    return {
      success: true,
      data: $data,
      source: "primary",
      url: $primary_url
    }
  } catch { |error|
    echo $"Primary URL failed: ($error.msg)"
  }

  # Try fallback URLs
  for url in $fallback_urls {
    try {
      let data = http get $url
      return {
        success: true,
        data: $data,
        source: "fallback",
        url: $url
      }
    } catch { |error|
      echo $"Fallback URL ($url) failed: ($error.msg)"
    }
  }

  # All attempts failed
  {
    success: false,
    error: "All data sources failed",
    attempted_urls: ([$primary_url] | append $fallback_urls)
  }
}
# PURPOSE: Implement resilient systems with multiple fallback options

# Error aggregation and reporting
def validate_user_data [users: list<record>] -> record {
  mut valid_users = []
  mut errors = []

  for user in ($users | enumerate) {
    let user_data = $user.item
    let user_index = $user.index
    mut user_errors = []

    # Validate each field
    if ($user_data.name? | is-empty) {
      $user_errors = $user_errors | append "Name is required"
    }

    if ($user_data.email? | is-empty) {
      $user_errors = $user_errors | append "Email is required"
    } else if not ($user_data.email | str contains "@") {
      $user_errors = $user_errors | append "Email must contain @ symbol"
    }

    if ($user_data.age? | is-empty) {
      $user_errors = $user_errors | append "Age is required"
    } else if $user_data.age < 0 {
      $user_errors = $user_errors | append "Age must be positive"
    }

    # Collect results
    if ($user_errors | length) == 0 {
      $valid_users = $valid_users | append $user_data
    } else {
      $errors = $errors | append {
        user_index: $user_index,
        user_data: $user_data,
        errors: $user_errors
      }
    }
  }

  {
    valid_users: $valid_users,
    invalid_users: $errors,
    total_users: ($users | length),
    valid_count: ($valid_users | length),
    error_count: ($errors | length),
    validation_success_rate: (($valid_users | length) / ($users | length) * 100)
  }
}
# PURPOSE: Perform comprehensive validation with detailed error reporting
```

### Defensive Programming and Assertions

**Input validation and sanitization:**

```nu
# Comprehensive input validation
def validate_and_sanitize_input [input: any, expected_type: string, constraints?: record] -> record {
  let rules = $constraints | default {}

  # Type validation
  let actual_type = $input | describe
  if $actual_type != $expected_type {
    return {
      valid: false,
      error: $"Expected ($expected_type), got ($actual_type)",
      sanitized_input: null
    }
  }

  # Type-specific validation and sanitization
  match $expected_type {
    "string" => {
      let sanitized = $input | str trim

      if ($rules.min_length? | default 0) > ($sanitized | str length) {
        return {
          valid: false,
          error: $"String must be at least ($rules.min_length) characters",
          sanitized_input: null
        }
      }

      if ($rules.max_length? | default 1000000) < ($sanitized | str length) {
        return {
          valid: false,
          error: $"String must be no more than ($rules.max_length) characters",
          sanitized_input: null
        }
      }

      {valid: true, error: null, sanitized_input: $sanitized}
    },
    "int" => {
      if ($rules.min? | default -2147483648) > $input {
        return {
          valid: false,
          error: $"Value must be at least ($rules.min)",
          sanitized_input: null
        }
      }

      if ($rules.max? | default 2147483647) < $input {
        return {
          valid: false,
          error: $"Value must be no more than ($rules.max)",
          sanitized_input: null
        }
      }

      {valid: true, error: null, sanitized_input: $input}
    },
    _ => {
      {valid: true, error: null, sanitized_input: $input}
    }
  }
}
# PURPOSE: Provide robust input validation with type checking and constraints

# Precondition and postcondition checking
def calculate_loan_payment [
  principal: float,
  annual_rate: float,
  years: int
] -> record {
  # Precondition checks
  if $principal <= 0 {
    error make {msg: "Principal must be positive"}
  }

  if $annual_rate < 0 or $annual_rate > 1 {
    error make {msg: "Annual rate must be between 0 and 1"}
  }

  if $years <= 0 {
    error make {msg: "Loan term must be positive"}
  }

  # Calculate payment
  let monthly_rate = $annual_rate / 12
  let months = $years * 12
  let payment = if $monthly_rate == 0 {
    $principal / $months
  } else {
    $principal * ($monthly_rate * ((1 + $monthly_rate) ** $months)) / (((1 + $monthly_rate) ** $months) - 1)
  }

  # Postcondition checks
  if $payment <= 0 {
    error make {msg: "Calculated payment is invalid"}
  }

  if $payment > ($principal * 2) {
    error make {msg: "Calculated payment seems unreasonably high"}
  }

  {
    monthly_payment: $payment,
    total_paid: ($payment * $months),
    total_interest: (($payment * $months) - $principal)
  }
}
# PURPOSE: Ensure function inputs and outputs meet expected constraints

# Invariant checking during complex operations
def manage_inventory [operations: list<record>] -> record {
  mut inventory = {}
  mut log = []

  for operation in $operations {
    let item = $operation.item
    let change = $operation.change
    let op_type = $operation.type

    # Get current quantity (invariant: quantities are non-negative)
    let current_qty = $inventory | get $item | default 0
    let new_qty = $current_qty + $change

    # Check invariant
    if $new_qty < 0 {
      let error_msg = $"Operation would result in negative inventory for ($item): ($new_qty)"
      $log = $log | append {
        operation: $operation,
        status: "failed",
        error: $error_msg
      }
      continue
    }

    # Apply operation
    $inventory = $inventory | upsert $item $new_qty
    $log = $log | append {
      operation: $operation,
      status: "success",
      previous_qty: $current_qty,
      new_qty: $new_qty
    }

    # Verify invariant holds
    if $new_qty < 0 {
      error make {msg: "Invariant violation: negative inventory detected"}
    }
  }

  {
    final_inventory: $inventory,
    operation_log: $log,
    total_operations: ($operations | length),
    successful_operations: ($log | where status == "success" | length)
  }
}
# PURPOSE: Maintain system invariants during state-changing operations
```

## Working with External Commands {#external-commands}

### External Command Execution and Integration

**Safe external command execution:**

```nu
# Basic external command with error handling
def run_command_safely [command: string, ...args: string] -> record {
  try {
    let result = ^$command ...$args | complete
    {
      success: ($result.exit_code == 0),
      exit_code: $result.exit_code,
      stdout: $result.stdout,
      stderr: $result.stderr,
      command: $command,
      args: $args
    }
  } catch { |error|
    {
      success: false,
      exit_code: -1,
      stdout: "",
      stderr: $error.msg,
      command: $command,
      args: $args,
      error: "Command execution failed"
    }
  }
}

# Usage examples
run_command_safely "ls" "-la" "/tmp"
run_command_safely "git" "status"
run_command_safely "nonexistent_command"
# PURPOSE: Execute external commands with comprehensive error handling and result capture

# Command execution with timeout and retry
def run_command_with_timeout [
  command: string,
  args: list<string> = [],
  timeout_seconds: int = 30,
  retry_count: int = 1
] -> record {
  mut attempts = 0
  mut last_result = {}

  while $attempts < $retry_count {
    $attempts = $attempts + 1

    try {
      # Note: Nushell doesn't have built-in timeout, this is conceptual
      let result = ^$command ...$args | complete

      if $result.exit_code == 0 {
        return {
          success: true,
          result: $result,
          attempts: $attempts,
          timeout_occurred: false
        }
      } else {
        $last_result = $result
        if $attempts < $retry_count {
          echo $"Command failed (attempt ($attempts)), retrying..."
          sleep 1sec
        }
      }
    } catch { |error|
      $last_result = {exit_code: -1, stdout: "", stderr: $error.msg}
      if $attempts < $retry_count {
        echo $"Command error (attempt ($attempts)): ($error.msg)"
        sleep 1sec
      }
    }
  }

  {
    success: false,
    result: $last_result,
    attempts: $attempts,
    timeout_occurred: false
  }
}
# PURPOSE: Execute commands with retry logic and timeout handling

# Cross-platform command execution
def execute_cross_platform [
  linux_command: string,
  macos_command: string,
  windows_command: string,
  args: list<string> = []
] -> record {
  let os_name = $nu.os-info.name

  let command = match $os_name {
    "linux" => $linux_command,
    "macos" => $macos_command,
    "windows" => $windows_command,
    _ => $linux_command  # Default fallback
  }

  run_command_safely $command ...$args
  | insert os $os_name
  | insert selected_command $command
}

# Usage: different package managers by OS
execute_cross_platform "apt" "brew" "choco" ["list", "--installed"]
# PURPOSE: Handle platform-specific commands seamlessly
```

**Advanced external command patterns:**

```nu
# Command pipeline builder
def build_command_pipeline [commands: list<record>] -> record {
  mut pipeline_result = {success: true, data: "", error: ""}

  for command in $commands {
    if not $pipeline_result.success {
      break
    }

    try {
      let result = match $command.type {
        "shell" => {
          ^$command.cmd ...$command.args | complete
        },
        "nushell" => {
          # Execute Nushell code
          if $command.cmd == "filter" {
            {exit_code: 0, stdout: ($pipeline_result.data | str trim), stderr: ""}
          } else {
            {exit_code: 0, stdout: $pipeline_result.data, stderr: ""}
          }
        },
        _ => {
          {exit_code: 1, stdout: "", stderr: "Unknown command type"}
        }
      }

      if $result.exit_code == 0 {
        $pipeline_result.data = $result.stdout
      } else {
        $pipeline_result.success = false
        $pipeline_result.error = $result.stderr
      }
    } catch { |error|
      $pipeline_result.success = false
      $pipeline_result.error = $error.msg
    }
  }

  $pipeline_result
}

# Usage example
let commands = [
  {type: "shell", cmd: "ls", args: ["-la"]},
  {type: "nushell", cmd: "filter", args: []},
  {type: "shell", cmd: "head", args: ["-10"]}
]
build_command_pipeline $commands
# PURPOSE: Create complex command pipelines with mixed shell and Nushell operations

# Environment management for external commands
def run_with_env [command: string, args: list<string>, env_vars: record] -> record {
  # Save current environment
  let original_env = $env

  try {
    # Set new environment variables
    for var in ($env_vars | transpose key value) {
      $env = $env | insert $var.key $var.value
    }

    # Run command
    let result = ^$command ...$args | complete

    {
      success: ($result.exit_code == 0),
      result: $result,
      env_used: $env_vars
    }
  } catch { |error|
    {
      success: false,
      error: $error.msg,
      env_used: $env_vars
    }
  } finally {
    # Restore original environment (conceptual - Nushell handles this automatically)
    # $env = $original_env
  }
}

# Usage
run_with_env "python" ["script.py"] {
  PYTHONPATH: "/custom/path",
  DEBUG: "1"
}
# PURPOSE: Execute commands with specific environment configurations

# Command output processing and transformation
def process_command_output [
  command: string,
  args: list<string>,
  processor: closure
] -> record {
  try {
    let result = ^$command ...$args | complete

    if $result.exit_code == 0 {
      let processed_output = $result.stdout | do $processor
      {
        success: true,
        raw_output: $result.stdout,
        processed_output: $processed_output,
        stderr: $result.stderr
      }
    } else {
      {
        success: false,
        error: $result.stderr,
        exit_code: $result.exit_code
      }
    }
  } catch { |error|
    {
      success: false,
      error: $error.msg
    }
  }
}

# Usage examples
process_command_output "ps" ["aux"] { |output|
  $output | lines | where {str contains "python"} | length
}

process_command_output "df" ["-h"] { |output|
  $output | lines | skip 1 | each {str replace -a -r '\s+' '|'} | from csv -s '|'
}
# PURPOSE: Transform command output using Nushell's data processing capabilities
```

## Closures, Pipelines & Advanced Patterns {#advanced-patterns}

### Closure Mastery and Functional Programming

**Understanding and creating closures:**

```nu
# Basic closure creation and usage
let add_five = { |x| $x + 5 }
10 | do $add_five                             # Returns 15
# PURPOSE: Create reusable anonymous functions for data transformation

# Closures with multiple parameters
let calculate_distance = { |x1, y1, x2, y2|
  let dx = $x2 - $x1
  let dy = $y2 - $y1
  ($dx * $dx + $dy * $dy) | math sqrt
}
do $calculate_distance 0 0 3 4               # Returns 5.0
# PURPOSE: Create complex calculations as portable functions

# Closures with captured variables
let multiplier = 3
let multiply_by_captured = { |x| $x * $multiplier }
[1, 2, 3, 4] | each $multiply_by_captured    # [3, 6, 9, 12]
# PURPOSE: Create closures that capture and use surrounding scope

# Higher-order functions with closures
def apply_operation [data: list<int>, operation: closure] -> list<int> {
  $data | each $operation
}

let square = { |x| $x * $x }
let cube = { |x| $x * $x * $x }
let add_ten = { |x| $x + 10 }

apply_operation [1, 2, 3, 4, 5] $square      # [1, 4, 9, 16, 25]
apply_operation [1, 2, 3, 4, 5] $cube        # [1, 8, 27, 64, 125]
apply_operation [1, 2, 3, 4, 5] $add_ten     # [11, 12, 13, 14, 15]
# PURPOSE: Create flexible functions that accept different operations

# Closure composition for complex transformations
def compose [f: closure, g: closure] -> closure {
  { |x| $x | do $g | do $f }
}

let add_one = { |x| $x + 1 }
let double = { |x| $x * 2 }
let add_one_then_double = compose $double $add_one

5 | do $add_one_then_double                  # ((5 + 1) * 2) = 12
# PURPOSE: Combine simple operations into complex transformations

# Closures for conditional operations
def conditional_transform [
  data: list<any>,
  condition: closure,
  transform: closure
] -> list<any> {
  $data | each { |item|
    if ($item | do $condition) {
      $item | do $transform
    } else {
      $item
    }
  }
}

let is_even = { |x| ($x mod 2) == 0 }
let square = { |x| $x * $x }

conditional_transform [1, 2, 3, 4, 5] $is_even $square  # [1, 4, 3, 16, 5]
# PURPOSE: Apply transformations conditionally based on predicates
```

**Advanced closure patterns:**

```nu
# Memoization with closures
def create_memoized_fibonacci [] -> closure {
  let mut cache = {}

  { |n|
    if $n in $cache {
      $cache | get $n
    } else {
      let result = if $n <= 1 {
        $n
      } else {
        # This is conceptual - actual implementation would need recursion
        $n  # Simplified for example
      }
      $cache = $cache | insert $n $result
      $result
    }
  }
}

let fib = create_memoized_fibonacci
do $fib 10
# PURPOSE: Create closures with persistent state for optimization

# Event handling with closures
def create_event_handler [event_type: string] -> closure {
  { |event_data|
    match $event_type {
      "click" => {
        echo $"Handling click event: ($event_data)"
        {type: "click", handled: true, data: $event_data}
      },
      "keypress" => {
        echo $"Handling keypress event: ($event_data)"
        {type: "keypress", handled: true, data: $event_data}
      },
      _ => {
        echo $"Unknown event type: ($event_type)"
        {type: $event_type, handled: false, data: $event_data}
      }
    }
  }
}

let click_handler = create_event_handler "click"
let key_handler = create_event_handler "keypress"

do $click_handler {x: 100, y: 200}
do $key_handler {key: "Enter"}
# PURPOSE: Create specialized handlers for different types of events

# Validation pipelines with closures
def create_validator [rules: list<record>] -> closure {
  { |data|
    mut errors = []

    for rule in $rules {
      let is_valid = $data | do $rule.validator
      if not $is_valid {
        $errors = $errors | append {
          field: $rule.field,
          message: $rule.message,
          value: ($data | get $rule.field)
        }
      }
    }

    {
      valid: ($errors | length) == 0,
      errors: $errors,
      data: $data
    }
  }
}

let user_validator = create_validator [
  {
    field: "name",
    validator: { |user| ($user.name | str length) >= 2 },
    message: "Name must be at least 2 characters"
  },
  {
    field: "age",
    validator: { |user| $user.age >= 18 },
    message: "Age must be at least 18"
  },
  {
    field: "email",
    validator: { |user| $user.email | str contains "@" },
    message: "Email must contain @ symbol"
  }
]

{name: "John", age: 25, email: "john@example.com"} | do $user_validator
# PURPOSE: Create configurable validation systems using closures
```

### Advanced Pipeline Patterns

**Complex data transformation pipelines:**

```nu
# Multi-stage data processing pipeline
def process_sales_data [raw_data: list<record>] -> record {
  let processed = $raw_data
  | where amount > 0                          # Remove invalid transactions
  | each { |sale|                             # Standardize and enrich data
      $sale
      | insert date ($sale.date | into datetime)
      | insert month ($sale.date | format date "%Y-%m")
      | insert quarter (match ($sale.date | format date "%m" | into int) {
          $m if $m <= 3 => "Q1",
          $m if $m <= 6 => "Q2",
          $m if $m <= 9 => "Q3",
          _ => "Q4"
        })
      | insert profit_margin (($sale.amount - $sale.cost) / $sale.amount * 100)
    }
  | group-by quarter                          # Group by quarter
  | transpose quarter sales                   # Pivot for analysis
  | each { |quarter_data|                     # Calculate quarterly metrics
      {
        quarter: $quarter_data.quarter,
        total_sales: ($quarter_data.sales | get amount | math sum),
        total_transactions: ($quarter_data.sales | length),
        avg_transaction: ($quarter_data.sales | get amount | math avg),
        avg_profit_margin: ($quarter_data.sales | get profit_margin | math avg),
        top_product: ($quarter_data.sales | group-by product | transpose product sales | sort-by {|x| $x.sales | get amount | math sum} | last | get product)
      }
    }

  {
    quarterly_summary: $processed,
    total_revenue: ($processed | get total_sales | math sum),
    best_quarter: ($processed | sort-by total_sales | last | get quarter),
    processing_timestamp: (date now)
  }
}
# PURPOSE: Create comprehensive data analysis pipelines with multiple transformation stages

# Error-resilient pipeline with recovery
def resilient_data_pipeline [
  data: list<record>,
  transformations: list<record>
] -> record {
  mut successful = []
  mut failed = []
  mut step_results = []

  for item in ($data | enumerate) {
    mut current_data = $item.item
    mut processing_log = []
    mut failed_step = false

    for transformation in ($transformations | enumerate) {
      let step_name = $transformation.item.name
      let step_func = $transformation.item.function

      try {
        $current_data = $current_data | do $step_func
        $processing_log = $processing_log | append {
          step: $step_name,
          status: "success"
        }
      } catch { |error|
        $processing_log = $processing_log | append {
          step: $step_name,
          status: "failed",
          error: $error.msg
        }
        $failed_step = true
        break
      }
    }

    if $failed_step {
      $failed = $failed | append {
        original_index: $item.index,
        original_data: $item.item,
        processing_log: $processing_log
      }
    } else {
      $successful = $successful | append {
        original_index: $item.index,
        processed_data: $current_data,
        processing_log: $processing_log
      }
    }
  }

  {
    successful_items: $successful,
    failed_items: $failed,
    success_rate: (($successful | length) / ($data | length) * 100),
    total_processed: ($data | length)
  }
}

# Usage example
let data = [
  {name: "Alice", age: "30", salary: "75000"},
  {name: "Bob", age: "invalid", salary: "65000"},
  {name: "Carol", age: "28", salary: "80000"}
]

let transformations = [
  {
    name: "convert_age",
    function: { |record| $record | update age ($record.age | into int) }
  },
  {
    name: "convert_salary",
    function: { |record| $record | update salary ($record.salary | into int) }
  },
  {
    name: "add_tax_bracket",
    function: { |record|
      $record | insert tax_bracket (
        if $record.salary > 70000 { "high" } else { "standard" }
      )
    }
  }
]

resilient_data_pipeline $data $transformations
# PURPOSE: Process data with comprehensive error handling and recovery tracking

# Parallel processing pipeline (conceptual)
def parallel_pipeline [data: list<any>, worker_func: closure, batch_size: int = 10] -> list<any> {
  let batches = $data
  | enumerate
  | group-by { |item| ($item.index / $batch_size) | math floor }
  | transpose batch_num items

  $batches
  | par-each { |batch|
      $batch.items | each { |item| $item.item | do $worker_func }
    }
  | flatten
}

# Usage
let heavy_computation = { |x|
  # Simulate heavy work
  sleep 100ms
  $x * $x
}

parallel_pipeline (1..100 | into list) $heavy_computation 20
# PURPOSE: Process large datasets efficiently using parallel execution
```

**Advanced pipeline utilities:**

```nu
# Pipeline debugging and monitoring
def debug_pipeline [data: any, label: string] -> any {
  echo $"DEBUG [($label)]: ($data | describe) - ($data | length) items"
  if ($data | describe) == "list" {
    echo $"  Sample: ($data | first 3)"
  } else {
    echo $"  Value: ($data)"
  }
  $data  # Pass through unchanged
}

# Usage in pipeline
ls
| debug_pipeline "initial_files"
| where size > 1mb
| debug_pipeline "large_files_only"
| select name size
| debug_pipeline "final_selection"
# PURPOSE: Add debugging information to pipelines without changing data flow

# Conditional pipeline branches
def pipeline_branch [
  condition: closure,
  true_pipeline: closure,
  false_pipeline: closure
] -> closure {
  { |data|
    if ($data | do $condition) {
      $data | do $true_pipeline
    } else {
      $data | do $false_pipeline
    }
  }
}

let large_file_processor = { |files|
  $files | each { |file| $file | insert processing_type "batch" }
}
let small_file_processor = { |files|
  $files | each { |file| $file | insert processing_type "individual" }
}
let has_large_files = { |files|
  ($files | where size > 100mb | length) > 0
}

ls | do (pipeline_branch $has_large_files $large_file_processor $small_file_processor)
# PURPOSE: Create conditional processing paths within pipelines

# Pipeline result aggregation
def aggregate_results [pipelines: list<closure>] -> closure {
  { |data|
    let results = $pipelines | each { |pipeline| $data | do $pipeline }
    {
      input: $data,
      results: $results,
      combined: ($results | flatten),
      summary: {
        pipeline_count: ($pipelines | length),
        total_results: ($results | each {|r| $r | length} | math sum)
      }
    }
  }
}

let count_files = { |files| $files | length }
let total_size = { |files| $files | get size | math sum }
let file_types = { |files| $files | get type | uniq }

ls | do (aggregate_results [$count_files, $total_size, $file_types])
# PURPOSE: Apply multiple analyses to the same data and combine results
```

---

## Conclusion

This comprehensive guide covers every aspect of Nushell's CLI commands and programming fundamentals in excessive detail. You now have:

**Part I Mastery:**
- Complete command reference for file operations, data processing, text manipulation
- System administration and process management
- Network operations and external command integration
- Date/time operations and mathematical calculations
- Advanced filtering, sorting, and data import/export

**Part II Mastery:**
- Deep understanding of variables, data types, and type operations
- Complete control flow including conditionals, pattern matching, and loops
- Advanced function creation with parameters, flags, and error handling
- Comprehensive error handling and defensive programming techniques
- External command integration and closure programming

**Key Nushell Advantages You've Learned:**
1. **Structured Data Everywhere** - No more text parsing with awk/sed
2. **Type Safety** - Catch errors before they cause problems
3. **Functional Programming** - Pure functions and immutable data
4. **Pipeline Power** - Chain operations naturally and efficiently
5. **Error Handling** - Built-in try/catch with structured error information

**Next Steps:**
- Practice with real data processing tasks
- Build custom commands for your workflow
- Contribute to the Nushell community
- Explore advanced plugins and integrations

You're now equipped with comprehensive Nushell knowledge - from basic file operations to sophisticated data processing pipelines. Start building amazing things! 🚀

---

*This comprehensive guide focuses intensively on CLI mastery and programming fundamentals - the core skills you need to become a Nushell power user. Practice with real data, build custom functions, and join the amazing Nushell community!*