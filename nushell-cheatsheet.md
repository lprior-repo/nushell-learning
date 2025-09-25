# Nushell Complete Cheat Sheet - Master Reference

## Core Philosophy
**Everything is structured data** - tables, records, typed values (not text streams)

---

## Essential Commands Quick Reference

### Navigation & Files
```nu
# Navigation
ls                          # List as structured table
ls | where size > 1MB       # Filter large files
ls **/*.rs                  # Recursive glob patterns
cd ~/projects              # Change directory
pwd                        # Current path

# File operations
open file.json             # Parse JSON automatically
open file.csv              # Parse CSV to table
save output.txt            # Save pipeline output
cp *.txt backup/           # Copy with patterns
rm --trash temp*           # Move to trash safely

# Content inspection
open file.txt | lines      # Get lines as list
open file.txt | str stats  # Text statistics
```

### Data Selection & Filtering
```nu
# Column operations
select name age            # Pick specific columns
get users.0.name          # Navigate nested data
drop modified             # Remove columns

# Row operations
where age > 25            # Filter rows
where name =~ "^test"     # Regex filtering
first 5                   # Take first n items
last 3                    # Take last n items
skip 2                    # Skip n items
take 10                   # Take n items
```

### Data Transformation
```nu
# Sorting & organization
sort-by size              # Sort ascending
sort-by size --reverse    # Sort descending
sort-by [name, age]       # Multi-column sort
reverse                   # Reverse order
group-by type             # Group data
group-by type --to-table  # Group to table format

# Aggregation & math
length                    # Count items
math sum                  # Sum values
math avg                  # Average
math min / math max       # Min/max values
math stddev               # Standard deviation
histogram                 # Create histogram
uniq                      # Unique values

# Data reshaping
transpose                 # Swap rows/columns
flatten                   # Flatten nested structures
merge $other_record       # Merge records
zip $other_list          # Zip lists together
```

---

## String Operations Reference

### Basic String Commands
```nu
# Length & inspection
"hello" | str length              # 5
"test" | str contains "es"        # true
"hello" | str starts-with "he"    # true
"hello" | str ends-with "lo"      # true

# Transformation
"HELLO" | str downcase           # "hello"
"hello" | str upcase             # "HELLO"
"hello" | str title              # "Hello"
"  text  " | str trim            # "text"

# Modification
"hello" | str replace "l" "x"    # "hexxo"
"hello" | str replace -a "l" "x" # Replace all
"a,b,c" | str split ","          # ["a", "b", "c"]
["a", "b"] | str join ","        # "a,b"

# Advanced
"hello world" | str words        # ["hello", "world"]
"line1\nline2" | lines          # ["line1", "line2"]
```

### Pattern Matching
```nu
# Regex patterns
where name =~ "\.txt$"           # Ends with .txt
where name =~ "^test"            # Starts with test
where name =~ "\d+"              # Contains digits
where content =~ "error|warning" # Contains either
where name =~ "(?i)readme"       # Case insensitive

# Glob patterns
ls *.txt                         # All .txt files
ls **/*.rs                       # All .rs recursively
ls test*.{txt,md}               # Multiple extensions
ls [abc]*.txt                   # Character classes
```

---

## Data Types & Conversions

### Core Types
```nu
# Numbers & units
42                    # integer
3.14                  # float
1MB                   # 1 megabyte
2hr + 30min          # 2.5 hours
2025-09-25           # date

# Collections
[1, 2, 3]                      # list
{name: "Alice", age: 30}       # record
[[a, b]; [1, 2] [3, 4]]       # table

# Ranges
1..5                 # [1, 2, 3, 4, 5]
1..<5                # [1, 2, 3, 4]
```

### Type Conversions
```nu
# Into conversions
"42" | into int               # String to integer
"3.14" | into float          # String to float
"2025-01-01" | into datetime # String to datetime
42 | into string             # Number to string
[1,2,3] | into record       # List to record

# Format conversions
$data | to json              # Convert to JSON
$data | to csv               # Convert to CSV
$data | to yaml              # Convert to YAML
$data | to toml              # Convert to TOML
$data | from json            # Parse from JSON
```

---

## Variables & Environment

### Variable Types
```nu
# Immutable (default)
let name = "nushell"
let users = (open users.json)

# Mutable
mut counter = 0
$counter = $counter + 1

# Constants (compile-time)
const CONFIG_PATH = "~/.config/app"

# Environment variables
$env.MY_VAR = "value"
$env.PATH = ($env.PATH | prepend "~/.local/bin")
```

### Essential Environment
```nu
$env.PATH                    # System PATH
$env.HOME                    # Home directory
$env.PWD                     # Current directory
$env.USER                    # Current user
$nu.home-path               # Nushell home
$nu.config-path             # Config file path
```

---

## Control Flow & Logic

### Conditionals
```nu
# Basic if/else
if $age >= 18 { "adult" } else { "minor" }

# Multi-condition
if $score >= 90 {
    "A"
} else if $score >= 80 {
    "B"
} else {
    "F"
}

# Pattern matching
match $status {
    "active" => "✓ Running"
    "stopped" => "✗ Stopped"
    _ => "? Unknown"
}
```

### Loops & Iteration
```nu
# With each (functional)
1..10 | each {|x| $x * $x}
$files | each {|f| open $f.name | lines | length}

# With for (imperative)
for file in (ls *.txt) {
    print $"Processing ($file.name)"
}

# Parallel processing
$large_list | par-each {|item| expensive_operation $item}
```

---

## Custom Functions

### Function Syntax
```nu
# Basic function
def greet [name: string] {
    $"Hello, ($name)!"
}

# With optional parameters
def analyze [
    file: path              # Required parameter
    --lines (-l)           # Boolean flag
    --format: string = "json"  # Optional with default
] {
    let content = (open $file)
    if $lines {
        $content | lines | length
    } else {
        $content | describe
    }
}

# Return structured data
def file-summary [path: path] {
    {
        name: ($path | path basename)
        size: ($path | path stat | get size)
        lines: (open $path | lines | length)
        modified: ($path | path stat | get modified)
    }
}
```

---

## Error Handling & Safety

### Try/Catch Patterns
```nu
# Basic error handling
try {
    open config.json | get database_url
} catch {
    "default-db-url"
}

# Custom errors
def validate-email [email: string] {
    if not ($email | str contains "@") {
        error make {
            msg: "Invalid email format"
            label: {text: "missing @ symbol"}
        }
    }
    $email
}

# Safe external commands
def run-safe [cmd: string, args: list = []] {
    let result = (do { ^$cmd ...$args } | complete)
    if $result.exit_code != 0 {
        error make {msg: $"($cmd) failed: ($result.stderr)"}
    }
    $result.stdout
}
```

---

## System & Process Operations

### System Information
```nu
sys                          # Full system info
sys cpu                      # CPU information
sys mem                      # Memory usage
sys host                     # Host details
ps                          # Process list
ps | where name =~ "firefox" # Filter processes
ps | where cpu > 10.0       # High CPU processes
```

### External Commands
```nu
# Explicit external command
^ls -la                     # Force external ls
^git status                 # External git

# With error handling
do -c { cat nonexistent.txt }  # Stop on error
complete { git status }         # Capture all output
```

---

## Network & HTTP

### HTTP Requests
```nu
# Basic requests
http get https://api.github.com/users/octocat
http post https://api.example.com/data {name: "test"}

# Process JSON responses
http get https://api.github.com/repos/nushell/nushell
| select name stargazers_count created_at
| where stargazers_count > 1000

# Download files
http get https://example.com/file.zip | save file.zip
```

---

## Configuration Management

### Config Files
```nu
# Edit configurations
config nu                   # Edit main config
config env                  # Edit environment config

# Common config settings
$env.config = {
    edit_mode: "emacs"      # or "vi"
    show_banner: false
    shell_integration: true
}

# Useful aliases (add to config.nu)
alias ll = ls -la
alias la = ls -a
alias gst = git status
```

---

## One-Liner Patterns

### File System One-Liners
```nu
# Find large files
ls **/* | where size > 10MB | sort-by size --reverse

# Count files by extension
ls **/* | where type == file | get name | each {|f| $f | split extension | get extension} | group-by $it | transpose ext count

# Recent files
ls **/* | where modified > ((date now) - 1wk) | select name modified

# Directory sizes
ls | where type == dir | insert total_size {|d| ls $d.name **/* | get size | math sum}

# Duplicate finder
ls **/* | where type == file | group-by size | transpose size files | where ($it.files | length) > 1
```

### Data Processing One-Liners
```nu
# CSV quick analysis
open data.csv | group-by category | transpose cat items | insert count {|row| $row.items | length}

# JSON API processing
http get https://api.github.com/users/microsoft/repos | where stargazers_count > 1000 | sort-by stargazers_count --reverse | first 10

# Log analysis
open access.log | lines | parse "{ip} - - [{timestamp}] \"{method} {url}\" {status}" | where status >= 400 | group-by status

# System monitoring
ps | where cpu > 5.0 | select name cpu pid | sort-by cpu --reverse | first 10
```

### Text Processing One-Liners
```nu
# Word frequency
open text.txt | str words | group-by $it | transpose word count | sort-by count --reverse

# Line statistics
open file.txt | lines | insert length {|line| $line | str length} | get length | math avg

# Find duplicates
open file.txt | lines | group-by $it | transpose line occurrences | where ($it.occurrences | length) > 1
```

---

## Advanced Patterns

### Transform-Filter-Aggregate Pattern
```nu
# Template for most data tasks
$data
| insert derived_field {|row| calculation $row}  # Transform
| where condition                                # Filter
| group-by category                             # Group
| transpose category items                       # Prepare aggregation
| insert summary {|row| $row.items | get field | math sum}  # Aggregate
```

### Configuration Loading Pattern
```nu
def load-config [name: string, --default: record = {}] {
    let config_file = $"~/.config/app/($name).json"
    if ($config_file | path exists) {
        open $config_file | merge $default
    } else {
        $default
    }
}
```

### Data Pipeline Pattern
```nu
def process-data [input: path, output: path] {
    open $input
    | where valid_field != null      # Clean
    | insert computed {|row| transform $row}  # Transform
    | group-by category              # Organize
    | transpose category data        # Aggregate prep
    | insert summary {|row| summarize $row.data}  # Aggregate
    | to csv                         # Format
    | save $output                   # Output
}
```

---

## Performance Tips

### Memory Optimization
```nu
# Stream large files (don't load all)
open huge.csv | first 1000 | group-by category

# Use take/skip for chunks
open large.log | skip 1000 | take 1000

# Parallel processing for CPU tasks
$files | par-each {|file| process_file $file}
```

### External Command Optimization
```nu
# Use ^ for known externals (faster lookup)
^grep "pattern" file.txt

# Batch external operations
$files | each {|f| ^wc -l $f.name} | math sum
```

---

## Debugging & Inspection

### Data Inspection
```nu
$data | describe                # Data type info
$data | table                   # Formatted display
$data | explore                 # Interactive explorer
$data | length                  # Count items
$data | columns                 # Column names (tables)
```

### Error Debugging
```nu
# Verbose error info
$env.RUST_BACKTRACE = "1"

# Debug pipeline steps
$data
| (print "After step 1:"; $in | table; $in)  # Debug print
| where condition
| (print "After filter:"; $in | table; $in)  # Debug print
| select fields
```

---

## Common Gotchas & Solutions

### Parse vs Runtime Issues
```nu
# Problem: source needs files at parse time
# Solution: Use constants for paths
const SCRIPT_DIR = "~/scripts"
source ($SCRIPT_DIR | path join "helpers.nu")
```

### External Command Errors
```nu
# Problem: External commands don't fail pipeline by default
# Solution: Use do -c for critical commands
do -c { git clone $repo }  # Will stop pipeline on error
```

### Mutable Variable Capture
```nu
# Problem: Closures can't capture mutable variables
# Solution: Use functional approaches
[1, 2, 3] | enumerate | each {|item| $"Item #($item.index)"}
```

---

## Quick Command Lookup

| Task | Command | Example |
|------|---------|---------|
| List files | `ls` | `ls \| where size > 1MB` |
| Open file | `open` | `open data.json \| get users` |
| Filter data | `where` | `ps \| where cpu > 10` |
| Select columns | `select` | `ls \| select name size` |
| Sort data | `sort-by` | `ls \| sort-by size --reverse` |
| Group data | `group-by` | `ps \| group-by name` |
| Count items | `length` | `ls \| length` |
| Sum values | `math sum` | `ls \| get size \| math sum` |
| Transform each | `each` | `[1,2,3] \| each {\|x\| $x * 2}` |
| Get help | `help` | `help ls` |

---

## Essential Keyboard Shortcuts

- `Tab` - Auto-completion
- `Ctrl+R` - History search
- `Ctrl+C` - Cancel current command
- `Ctrl+D` - Exit Nushell
- `↑/↓` - Command history navigation

---

*Keep this cheat sheet handy - it covers 95% of daily Nushell usage patterns!*