[![CircleCI](https://circleci.com/gh/flowerinthenight/lsdy/tree/master.svg?style=svg)](https://circleci.com/gh/flowerinthenight/lsdy/tree/master)

## Overview

`lsdy` is a tool for querying [DynamoDB](https://aws.amazon.com/dynamodb/) tables. It will attempt to display the values in a tabular form using all available attributes (by default, alphabetical order, left to right), unless specified. If `--pk` is specified, it will query the table with that specific primary key. For tables with sort keys, only string-based sort keys are supported at the moment. When the `--sk` flag is supplied, it will query all sort keys that begins with the flag value. An empty primary key implies a table scan.

The output uses Go's builtin [tabwriter](https://golang.org/pkg/text/tabwriter/) instead of the more sophisticated terminal-based UI libraries (i.e. [tcell](https://github.com/gdamore/tcell), [goncurses](https://github.com/rthornton128/goncurses)) for easy piping into other cmdline tools (i.e. `grep`, `awk`, `wc`, etc).

## Installation

Using [Homebrew](https://brew.sh/):
```bash
$ brew tap flowerinthenight/tap
$ brew install lsdy
```

If you have a Go environment:
```bash
$ go get -u -v github.com/flowerinthenight/lsdy
```

## Usage
```bash
# Minimal usage:
$ lsdy TABLE_NAME

# For a more updated help information:
$ lsdy -h
```

To authenticate to AWS, this tool looks for the following environment variables (can be set by cmdline args as well):
```bash
AWS_REGION
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY

# Optional:
ROLE_ARN

# Authenticate using id/secret flags:
$ lsdy --region=xxx --key=xxx --secret=xxx

# Authenticate by assuming a role ARN, in which case, id/secret should have the
# AssumeRole permissions. Using flags:
$ lsdy --region=xxx --key=xxx --secret=xxx --rolearn=xxx
```

To query a table using a primary key:
```bash
# Query table with primary key 'id' value of 'ID0001':
$ lsdy TABLE_NAME --pk "id:ID0001"
```

To query a table using both a primary key and a sort key:
```bash
# Query table with primary key 'id' value of 'ID0001' and sort key 'sortkey' of SK002:
$ lsdy TABLE_NAME --pk "id:ID0001" --sk "sortkey:SK002"
```

To query a table using multiple primary keys and optional sort key pair(s):
```bash
# Multiple primary keys only:
$ lsdy TABLE_NAME --pk "id:ID0001" --pk "id:ID0002" --pk "id:ID9999"

# Multiple primary keys with corresponding sort keys:
$ lsdy TABLE_NAME --pk "id:ID0001,id:ID0002,id:ID9999" --sk "sortkey:AAA,sortkey:BBB,sortkey:CCC"

# Multiple primary keys with only the first pk having a sortkey pair:
$ lsdy TABLE_NAME --pk "id:ID0001,id:ID0002,id:ID9999" --sk "sortkey:AAA"
```

To scan a table:
```bash
# All attributes (columns) will be queried:
$ lsdy TABLE_NAME

# If you want specific attributes (unsorted columns):
$ lsdy TABLE_NAME --attr "col1,col2,col3" --nosort

# or you can write it this way (sorted columns):
$ lsdy TABLE_NAME --attr col1 --attr col2 --attr col3
```

If you want to describe a table:
```bash
# Will output the table details and all its attributes/columns:
$ lsdy TABLE_NAME --describe
```
_Warning!_ At the moment, `--describe` will cause a table scan if the `--pk` flag is not set. For massive tables, it's probably a good idea to supply the `--pk` flag, in which case, it will only query the attributes from that key.

By default, the length of all cell items in the output table are concatenated up to `--maxlen`.

## Need help
PR's are welcome!

- [ ] Handling data tabulation for fullwidth characters (i.e. Japanese, Chinese, etc.)
- [ ] Better handling of JSON, maps, and base64-encoded(?) values in cells
- [ ] Query secondary indeces
- [ ] Support for other sort key types
- [ ] Config file support
- [ ] Package for Windows
- [ ] Output to CSV (ongoing)
- [x] Add `--delete` option to delete the queried data
