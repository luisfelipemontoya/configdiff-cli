# configdiff-cli

Compare JSON and YAML configuration files from the command line.

[![Node CI](https://github.com/luisfelipemontoya/configdiff-cli/actions/workflows/nodejs.yml/badge.svg)](https://github.com/luisfelipemontoya/configdiff-cli/actions/workflows/nodejs.yml)
[![Hexlet checks](https://github.com/luisfelipemontoya/configdiff-cli/actions/workflows/hexlet-check.yml/badge.svg)](https://github.com/luisfelipemontoya/configdiff-cli/actions/workflows/hexlet-check.yml)

**configdiff-cli** provides the `gendiff` command to identify added, removed, and updated properties in configuration files. It supports nested objects and offers three output formats for terminal inspection or further processing.

## Features

- Compare `.json`, `.yml`, and `.yaml` files.
- Detect changes recursively in nested objects.
- Display results as a structured diff, a plain-text summary, or JSON.
- Use relative or absolute file paths.
- Produce consistently ordered results with alphabetically sorted keys.

## Installation

Prerequisites: Git, Node.js 20 or later, and npm.

Install from source and register the `gendiff` command locally:

```bash
git clone https://github.com/luisfelipemontoya/configdiff-cli.git
cd configdiff-cli
npm ci
npm link
```

Check that the command is available:

```bash
gendiff --help
```

You can also run the CLI directly from the repository without `npm link`:

```bash
node bin/gendiff.js --help
```

## Quick start

From the repository root, compare the included YAML fixtures:

```bash
gendiff __fixtures__/file1.yml __fixtures__/file2.yml
```

Output:

```text
{
  - follow: false
    host: hexlet.io
  - proxy: 123.234.53.22
  - timeout: 50
  + timeout: 20
  + verbose: true
}
```

A `-` marks a property removed from the first file, and a `+` marks a property added in the second file. Updated properties appear twice: the old value with `-` and the new value with `+`. Properties without a marker are unchanged.

## CLI reference

```text
gendiff [options] <filepath1> <filepath2>
```

The first file is the baseline; the second file is the configuration to compare against it.

| Option | Description |
| --- | --- |
| `-f, --format <type>` | Output format: `stylish` (default), `plain`, or `json`. |
| `-h, --help` | Show usage information. |
| `-V, --version` | Show the CLI version. |

File formats are detected from their extensions. Both files must contain valid configuration objects. Unsupported extensions and unknown output formats raise an error.

## Output formats

### Stylish

The default format displays a structured diff, preserving the hierarchy of nested objects:

```bash
gendiff __fixtures__/file1.json __fixtures__/file2.json
```

See the [expected nested output](__fixtures__/expected.txt) for the included JSON fixtures.

### Plain

Use `plain` for a concise description of each change. Nested properties use dot-separated paths, unchanged properties are omitted, and object values are represented as `[complex value]`.

```bash
gendiff --format plain __fixtures__/file1.yml __fixtures__/file2.yml
```

Output:

```text
Property 'follow' was removed
Property 'proxy' was removed
Property 'timeout' was updated. From 50 to 20
Property 'verbose' was added with value: true
```

### JSON

Use `json` to serialize the diff tree for further processing:

```bash
gendiff --format json __fixtures__/file1.json __fixtures__/file2.json
```

The result is an array of diff nodes, rather than a merged configuration or a JSON Patch document. Each node includes a `key` and a `type`:

| Node type | Additional fields |
| --- | --- |
| `added`, `removed`, `unchanged` | `value` |
| `changed` | `oldValue`, `newValue` |
| `nested` | `children` (an array of diff nodes) |

Save the result to a file:

```bash
gendiff --format json __fixtures__/file1.json __fixtures__/file2.json > diff.json
```

## Demonstrations

Recorded walkthroughs:

- [YAML comparison](https://asciinema.org/a/6qlomRR7fKkiTF62)
- [Nested configuration comparison](https://asciinema.org/a/KcjcaSbfjsiqvXsL)
- [Stylish and plain output](https://asciinema.org/a/o2Mm6eBRwy17qjLU)
- [JSON output](https://asciinema.org/a/ozteB5C8PUTciccZ)

## Development

Install dependencies with `npm ci`, then run:

```bash
npm test
npm run lint
```

Additional commands:

```bash
npm run test:watch
npm test -- --coverage
```

### Project structure

| Path | Purpose |
| --- | --- |
| `bin/gendiff.js` | CLI entry point and argument handling with Commander.js. |
| `src/parsers.js` | Read and parse JSON and YAML files. |
| `src/buildDiff.js` | Build the recursive diff tree. |
| `src/formatters/` | Render stylish, plain, and JSON output. |
| `src/index.js` | Coordinate parsing, comparison, and formatting. |
| `__fixtures__/` | Sample configurations and expected output. |
| `__tests__/` | Jest tests. |

## Contributing

Bug reports and improvements are welcome through [issues](https://github.com/luisfelipemontoya/configdiff-cli/issues) and pull requests. For bug reports, include the input files, the command you ran, and the expected and actual output. Run the tests and linter before submitting code changes.

## License

The package declares the ISC license in [package.json](package.json).
