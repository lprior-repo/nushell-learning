# Nushell for the Impatient

## CLI Commands
1. [Start](#start) 2. [Files](#files) 3. [Dirs](#dirs) 4. [Data](#data) 5. [Text](#text) 6. [System](#sys) 7. [Process](#proc) 8. [Math](#math) 9. [Filter](#filter) 10. [Import](#import)

## Programming
11. [Variables](#vars) 12. [Control](#control) 13. [Functions](#funcs) 14. [Errors](#errors) 15. [External](#ext) 16. [Advanced](#adv)

## Start {#start}

### Why Nushell Beats Unix Tools

**Type Safety**: Unix commands output untyped text that breaks pipelines when formats change. Nushell outputs structured data with guaranteed types—no more parsing failures.

**Data Flow**: Instead of learning awk syntax + sed regex + grep patterns + cut fields, you learn ONE syntax that works everywhere. `ps | where cpu > 50 | select name pid` reads like English.

**Error Messages**: Unix: `awk: syntax error near line 1`. Nushell: `Expected int, found string "abc" at column 'age' in row 3`. You know exactly what's wrong and where.

**Data Type Conversion**: Convert between JSON, CSV, YAML, TOML instantly. No external tools needed. `open data.json | to csv` just works.

**Composability**: Every command outputs the same structured format, so they compose perfectly. No impedance mismatches between tools.

```nu
# Install
brew install nushell          # mac
winget install nushell        # windows
cargo install nu              # linux

# Launch
nu
help
help commands
help ls
```

## Files {#files}
*File operations and content management.*

```nu
# List
ls
ls -a                         # hidden
ls **/*                       # recursive
ls /path/to/dir
ls | where type == dir        # dirs only
ls | where type == file       # files only
ls | where name =~ "\\.txt$"    # by extension
ls | where size > 1mb         # by size
ls | where modified > (date now) - 7day  # recent

# Advanced queries
ls | group-by { $in.name | path parse | get extension }  # count by ext
ls | group-by size | where {($in | length) > 1}         # duplicates
ls | each { {name: $in.name, age: ((date now) - $in.modified) / 1day} }  # age

# File operations
open file.txt
save output.txt
cp file.txt backup.txt
cp -r dir1 dir2              # recursive
mv old.txt new.txt
rm file.txt
rm -rf directory             # recursive delete

# File content
open file.txt | lines        # as lines
open file.json               # auto-parse JSON
open file.csv                # auto-parse CSV
'hello world' | save file.txt
```

## Dirs {#dirs}
*Navigate filesystem and manage directories efficiently.*

```nu
# Navigate
cd /path                      # absolute path
cd ~                         # home directory
cd ..                        # parent directory
cd -                         # previous directory
pwd                          # show current path

# Create/Remove
mkdir dir                    # create directory
mkdir -p deep/nested/path    # create with parents
rmdir empty-dir              # remove empty directory
rm -rf dir-with-content      # remove directory tree

# Operations
cp -r src dest               # copy directory
mv old new                   # rename/move directory
ls | where type == dir       # list dirs
ls dir | length              # count items
ls dir | get size | math sum # directory size
tree dir                     # show tree structure
```

## Data {#data}
*Query and transform structured data like a database.*

**Core Types**: `int`, `float`, `string`, `bool`, `list`, `record`, `table`, `null`

```nu
# Type checking and conversion
42 | describe                  # "int"
"42" | into int               # string to int
'{"x": 1}' | from json        # parse JSON
"a,b\n1,2" | from csv          # parse CSV

# Core operations
ps | where name =~ chrome              # filter rows
ps | select pid name                   # pick columns
ps | sort-by cpu                       # sort
ps | reverse                           # reverse order
ps | first 5                           # first N items
ps | last 3                            # last N items
ps | skip 2                            # skip N items
ps | length                            # count items
ps | uniq                              # unique values
ps | group-by name                     # group by column

# Transform data
ps | each { |row| $row.name | str upcase }    # transform each row
ps | reduce { |it, acc| $acc + $it.cpu }      # aggregate values
ps | insert status 'running'                  # add column
ps | update name { str upcase }               # update column
```

## Text {#text}
*String processing and text manipulation.*

```nu
# String info
"hello" | str length                  # character count
"" | str is-empty                     # check if empty

# Case conversion
"Hello" | str downcase                # "hello"
"hello" | str upcase                  # "HELLO"
"hello world" | str title-case        # "Hello World"
"hello world" | str capitalize        # "Hello world"

# Clean up
"  hello  " | str trim               # remove whitespace
"...hello..." | str trim -c '.'       # remove specific chars
"a  b   c" | str replace -r '\\s+' ' '  # normalize whitespace

# Split and join
"a,b,c" | split row ","              # split by delimiter
"hello" | split chars                 # split to characters
"hello world" | split words           # split by whitespace
"line1\nline2" | lines                # split by newlines
["a", "b", "c"] | str join ", "       # join with separator

# Search and replace
"hello world" | str contains "world"  # check if contains
"hello" | str starts-with "he"       # check prefix
"world" | str ends-with "ld"          # check suffix
"hello world" | str replace "world" "nu"  # replace text
"abc abc" | str replace -a "a" "x"    # replace all

# Pattern matching
"hello123world" | parse "{text}{digits}{text}"  # structured parsing
"2023-12-25" | parse "{year}-{month}-{day}"      # date parsing
```

## System {#sys}
*System information and environment.*

```nu
# System info
sys                                   # full system info
sys | get host                        # hostname, uptime, users
sys | get cpu                         # CPU info and usage
sys | get mem                         # memory usage
sys | get disks                       # disk usage
sys | get net                         # network interfaces
sys | get temp                        # temperatures

# Environment
$env                                  # all environment variables
$env.PATH                            # specific variable
$env.HOME                            # home directory
$env.USER                            # current user

# Date and time
date now                             # current timestamp
date now | format date "%Y-%m-%d"    # format date
date now | format date "%H:%M:%S"    # format time
(date now) + 1day                    # date arithmetic
(date now) - 1hr                     # subtract time
```

## Process {#proc}
*Process management and monitoring.*

```nu
# Process info
ps                                    # all processes
ps | where name =~ chrome             # filter by name
ps | where cpu > 10                  # high CPU usage
ps | where mem > 100MB               # high memory usage
ps | sort-by cpu | reverse | first 10 # top CPU processes

# Process control
kill 1234                            # kill process by PID
kill --force 1234                    # force kill
killall chrome                       # kill by name

# Jobs and background
^long-running-command &              # run in background
jobs                                 # list background jobs
fg                                   # bring to foreground
bg                                   # send to background

# Network
ss                                   # socket statistics
ss | where state == LISTEN           # listening ports
ping google.com                      # ping host
curl https://api.github.com          # HTTP requests
```

## Math {#math}
*Mathematical operations and calculations.*

```nu
# Basic math
3 + 4                                # addition
10 - 3                               # subtraction
6 * 7                                # multiplication
15 / 3                               # division
2 ** 8                               # power
17 mod 5                             # modulo

# Math functions
[1, 2, 3, 4, 5] | math sum           # sum
[1, 2, 3, 4, 5] | math avg           # average
[1, 2, 3, 4, 5] | math min           # minimum
[1, 2, 3, 4, 5] | math max           # maximum
[1, 2, 3, 4, 5] | math stddev        # standard deviation
[1, 2, 3, 4, 5] | math median        # median
[1, 2, 3, 4, 5] | math product       # product

# Rounding
3.14159 | math round                 # 3
3.14159 | math floor                 # 3
3.14159 | math ceil                  # 4
3.14159 | math round --precision 2   # 3.14

# Conversions
100 | into float                     # 100.0
"123" | into int                     # 123
```

## Filter {#filter}
*Advanced filtering and data manipulation.*

```nu
# Where conditions
ps | where cpu > 50                  # greater than
ps | where name == "chrome"          # equal
ps | where name != "chrome"          # not equal
ps | where name =~ "chro"            # regex match
ps | where name !~ "chro"            # regex not match
ps | where name in ["chrome", "firefox"]  # in list
ps | where name not-in ["chrome", "firefox"]  # not in list

# Complex conditions
ps | where cpu > 50 and mem > 100MB    # AND
ps | where name == "chrome" or name == "firefox"  # OR
ps | where not (cpu > 50)               # NOT
ps | where (cpu > 50) and (mem > 100MB or name =~ "important")  # grouped

# Null handling
data | where field != null           # exclude nulls
data | where field == null           # only nulls
data | default "N/A" field           # replace nulls

# Advanced filtering
data | find "search term"            # full-text search
data | find --columns [name, desc] "term"  # specific columns
data | where $in | str contains "text"     # custom conditions
```

## Import {#import}
*Data import/export in various formats.*

```nu
# File formats
open data.json                       # JSON
open data.csv                        # CSV
open data.yaml                       # YAML
open data.toml                       # TOML
open data.xml                        # XML
open data.xlsx                       # Excel

# Format conversion
open data.json | to csv              # JSON to CSV
open data.csv | to json              # CSV to JSON
open data.yaml | to toml             # YAML to TOML
{name: "test"} | to json             # record to JSON
[{a: 1}, {a: 2}] | to csv            # table to CSV

# URLs and APIs
http get https://api.github.com/users/octocat
fetch https://jsonplaceholder.typicode.com/posts
curl -s https://api.example.com/data | from json

# Database-like operations
open data.csv | query db "SELECT name, age FROM data WHERE age > 25"
```

# Programming

## Variables {#vars}
*Variable declaration and data types.*

```nu
# Immutable variables (default)
let name = "John"                    # string
let age = 30                         # integer
let pi = 3.14159                     # float
let active = true                    # boolean
let items = [1, 2, 3]               # list
let person = {name: "Alice", age: 25}  # record

# Mutable variables
mut counter = 0                      # mutable integer
$counter = $counter + 1              # update value
mut list = []                        # mutable list
$list = ($list | append "new item")  # update list

# Variable scoping
def example [] {
  let local_var = "inside function"  # function scope
  $local_var
}

# Pattern matching in assignments
let [first, second, ..rest] = [1, 2, 3, 4, 5]  # destructure list
let {name, age} = {name: "Bob", age: 35}        # destructure record
```

## Control {#control}
*Conditionals, loops, and control flow.*

```nu
# If conditions
if $age >= 18 { "adult" } else { "minor" }
if $score > 90 { "A" } else if $score > 80 { "B" } else { "C" }

# Match expressions
match $value {
  1 => "one",
  2 | 3 => "two or three",
  4..10 => "four to ten",
  _ => "other"
}

# Loops with each
[1, 2, 3] | each { |x| $x * 2 }     # transform each item
1..10 | each { |x| $x ** 2 }        # squares of 1-10
ls | each { |file| $file.name | str upcase }  # uppercase names

# For loops
for x in 1..10 { print $x }         # iterate range
for item in [a, b, c] { print $item }  # iterate list
for file in (ls) { print $file.name }  # iterate command output

# While loops
mut x = 0
while $x < 10 { $x = $x + 1; print $x }

# Loop control
for x in 1..100 {
  if $x mod 10 == 0 { continue }    # skip iteration
  if $x > 50 { break }              # exit loop
  print $x
}
```

## Functions {#funcs}
*Custom functions and commands.*

```nu
# Simple function
def greet [name: string] {
  $"Hello, ($name)!"
}

# Function with multiple parameters
def add [x: int, y: int] {
  $x + $y
}

# Optional parameters with defaults
def greet_with_title [name: string, title: string = "Mr/Ms"] {
  $"Hello, ($title) ($name)!"
}

# Functions with flags
def process [
  input: string,
  --verbose (-v),          # boolean flag
  --output (-o): string,   # flag with value
  --count (-c): int = 1    # flag with default
] {
  if $verbose { print $"Processing ($input)" }
  # ... function logic
}

# Return types
def get_user_info []: record<name: string, age: int> {
  {name: "Alice", age: 30}
}

# Closures
let double = { |x| $x * 2 }
[1, 2, 3] | each $double

# Pipeline functions
def filter_large_files [] {
  where size > 1MB
}
ls | filter_large_files
```

## Errors {#errors}
*Error handling and debugging.*

```nu
# Try-catch blocks
try {
  open non_existent_file.txt
} catch { |err|
  print $"Error: ($err.msg)"
  "default content"
}

# Error propagation
def safe_divide [x: float, y: float] {
  if $y == 0 {
    error make {msg: "Division by zero"}
  }
  $x / $y
}

# Default values for errors
let content = try { open file.txt } catch { "default content" }
let value = ($data | get field | default "N/A")

# Debugging
debug $variable                      # inspect variable
print $"Debug: value is ($variable)" # debug prints
$variable | describe                 # type information

# Assertions
assert ($x > 0) "x must be positive"
assert length $list > 0 "list cannot be empty"
```

## External {#ext}
*Working with external commands and tools.*

```nu
# Run external commands
^ls -la                              # explicit external
git status                           # implicit external
python script.py                     # run scripts
make build                           # build tools

# Capture output
let result = ^git status --porcelain
let files = ^find . -name "*.txt" | lines

# Pass data to external commands
ls | get name | ^head -5             # pipe to external
"hello world" | ^wc -w               # count words

# Environment for external commands
with-env {RUST_LOG: "debug"} { cargo test }
$env.PATH = ($env.PATH | append "/usr/local/bin")

# Command substitution
let current_branch = ^git branch --show-current
cd (^pwd)                            # command in expression

# Handle external errors
try {
  ^command_that_might_fail
} catch { |err|
  print $"Command failed: ($err.msg)"
}
```

## Advanced {#adv}
*Advanced patterns and techniques.*

```nu
# Closures and higher-order functions
let filter_fn = { |x| $x mod 2 == 0 }
[1, 2, 3, 4, 5] | where $filter_fn   # even numbers

let transform = { |x| {value: $x, double: ($x * 2)} }
[1, 2, 3] | each $transform

# Partial application
def multiply [x: int, y: int] { $x * $y }
let double = { |n| multiply $n 2 }
[1, 2, 3] | each $double

# Custom completions
def my_command [
  file: string@complete_files,       # file completion
  --format: string@complete_formats  # custom completion
] {
  # command implementation
}

def complete_files [context: string, position: int]: list<string> {
  ls | get name
}

# Generators and infinite sequences
def fibonacci [] {
  mut a = 0
  mut b = 1
  loop {
    yield $a
    let temp = $a + $b
    $a = $b
    $b = $temp
  }
}

# Module system
export def public_function [] {
  "This is public"
}

def private_function [] {
  "This is private"
}

# Advanced pipeline patterns
ls
| where type == file
| each { |file|
    let stats = ^stat $file.name | from ssv
    $file | insert permissions $stats.permissions
}
| where permissions =~ "r--"
| sort-by modified
| reverse
```

---

*Nushell combines the power of traditional Unix tools with modern language features. Master these commands and patterns to become highly productive with structured data processing.*