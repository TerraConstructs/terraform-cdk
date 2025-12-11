# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **tcons** (Terraform Constructs), a maintained fork of HashiCorp's CDK for Terraform (CDKTF). It allows developers to define cloud infrastructure using familiar programming languages (TypeScript, Python, Java, C#, Go) instead of HCL, while still leveraging the entire Terraform ecosystem.

**Key Context**: HashiCorp discontinued CDKTF development on December 10, 2025. This fork continues active maintenance and development with the same interface, requiring no migration for existing users.

## Repository Structure

This is a Lerna monorepo with multiple interconnected packages:

### Core Packages

- **`packages/tcons`**: Main library package (JSII-compiled to support all languages)

  - Defines core constructs: `TerraformStack`, `TerraformResource`, `TerraformModule`, etc.
  - Built using JSII to generate bindings for Python, Java, C#, and Go
  - Located in `lib/` directory

- **`packages/tcons-cli`**: Command-line interface for users

  - Entry point: `src/bin/cdktf.ts`
  - Commands in `src/bin/cmds/`: init, get, deploy, destroy, diff, synth, watch, convert, etc.
  - Built with esbuild for bundling (not JSII)

- **`packages/@tcons/cli-core`**: Internal CLI logic shared between CLI and other tools
  - Core deployment logic, synth stack, Terraform interaction
  - Not meant for direct external use

### Supporting Packages

- **`packages/@tcons/commons`**: Shared utilities (config, logging, Terraform interaction)
- **`packages/@tcons/hcl2json`**: Converts HCL to JSON (Go-based WASM)
- **`packages/@tcons/hcl2cdk`**: Converts HCL to CDK constructs (powers `convert` command)
- **`packages/@tcons/hcl-tools`**: HCL manipulation tools (Go-based WASM)
- **`packages/@tcons/provider-generator`**: Generates provider bindings from Terraform schemas
- **`packages/@tcons/provider-schema`**: Schema definitions for providers

### Examples

Located in `examples/` organized by language (typescript, python, java, csharp, go). Each example must have a `package.json` with at minimum `build` and `synth` scripts. Examples are run in CI integration tests.

## Development Commands

### Initial Setup

```bash
yarn install
yarn build
```

### Building

```bash
yarn build              # Build all packages
yarn watch              # Watch mode for all packages (recommended for development)
yarn build-and-package  # Build and create distribution packages
```

### Testing

```bash
# Unit tests
yarn test                           # Run all unit tests
yarn test:update                    # Update snapshots for unit tests

# Integration tests (requires building packages first)
yarn package                        # Build dist packages
yarn integration                    # Run all integration tests
yarn integration:update             # Update integration test snapshots
yarn integration:single -- typescript/synth-app  # Run single integration test
yarn integration:typescript         # Run TypeScript integration tests only
yarn integration:python             # Run Python integration tests only
yarn integration:csharp             # Run C# integration tests only
yarn integration:java               # Run Java integration tests only
yarn integration:go                 # Run Go integration tests only
```

### Linting and Formatting

```bash
yarn lint:workspace    # Lint all TypeScript files
yarn lint:prettier     # Check Prettier formatting
yarn format            # Auto-format all files with Prettier
```

### Package-Specific Commands

```bash
# Build/test individual packages
cd packages/tcons && yarn build && yarn test
cd packages/tcons-cli && yarn build && yarn test

# Watch individual package
cd packages/@tcons/hcl2cdk && yarn watch
```

### Running Tests for a Single Package

```bash
cd packages/tcons
yarn test              # Run all tests in this package
npx jest path/to/test.test.ts  # Run specific test file
npx jest --watch       # Run tests in watch mode
```

### Local Development with tcons-cli

For testing CLI changes locally, you can create an alias (CLI is bundled with esbuild):

```bash
alias tconsl='/path/to/terraform-cdk/packages/tcons-cli/bundle/bin/tcons'
alias tconsld='node --inspect-brk /path/to/terraform-cdk/packages/tcons-cli/bundle/bin/tcons.js'
```

Or use yarn link for full integration (see CONTRIBUTING.md for complete setup).

## Architecture Overview

### Build System

- **Monorepo**: Managed by Lerna with Yarn workspaces
- **TypeScript**: All packages written in TypeScript
- **JSII**: `tcons` package uses JSII to generate multi-language bindings (Python, Java, C#, Go)
- **esbuild**: `tcons-cli` uses esbuild for fast bundling (type checking via pre-commit hook)
- **Go/WASM**: `hcl2json` and `hcl-tools` packages include Go code compiled to WASM

### Package Dependencies

```
tcons-cli
  ├─ @tcons/cli-core
  │   ├─ @tcons/commons
  │   ├─ @tcons/hcl2cdk
  │   ├─ @tcons/hcl2json
  │   ├─ @tcons/hcl-tools
  │   └─ @tcons/provider-schema
  ├─ @tcons/hcl2cdk
  ├─ @tcons/hcl2json
  ├─ @tcons/hcl-tools
  ├─ @tcons/commons
  └─ tcons

tcons (standalone, JSII-compiled)
  └─ constructs (peer dependency)
```

### Key Workflows

1. **`cdktf init`**: Creates new project from templates (in `packages/@tcons/cli-core/templates/`)
2. **`cdktf get`**: Downloads and generates provider bindings using `@tcons/provider-generator`
3. **`cdktf synth`**: Executes user code to generate Terraform JSON
4. **`cdktf deploy/destroy/diff`**: Wraps Terraform CLI commands with state management
5. **`tcons convert`**: Uses `@tcons/hcl2cdk` to convert HCL to CDK code

### State Management

The CLI core uses XState for complex deployment state machines (see `packages/@tcons/cli-core/src/lib/models/deploy-machine.ts`).

### Language Support

- **TypeScript/JavaScript**: Direct usage of the `tcons` package
- **Python**: Generated via JSII to `cdktf` PyPI package
- **Java**: Generated via JSII to Maven Central
- **C#**: Generated via JSII to NuGet
- **Go**: Generated via JSII to Go modules

Each language has examples in `examples/<language>/` and templates in `packages/@tcons/cli-core/templates/<language>/`.

## Important Development Notes

### Versioning

All packages use `0.0.0` in development. The `tools/align-version.sh` script synchronizes versions for releases.

For local Python development with package hashing issues:

```bash
./tools/align-version.sh -dev.111212112 && yarn build && yarn package
```

### Testing Strategy

- **Unit tests**: Located in each package (e.g., `packages/tcons/lib/__tests__/`)
- **Integration tests**: In `test/` directory, organized by language
- **Edge provider tests**: Special provider in `packages/@tcons/provider-generator/lib/__tests__/edge-provider-schema` with edge cases

### Feature Flags

New breaking changes are introduced via feature flags (see `packages/tcons/lib/features.ts`). Feature flags are enabled by default for new projects created via `cdktf init` but disabled for existing projects to maintain compatibility.

When adding a feature flag:

1. Define constant in `features.ts`
2. Check with `node.tryGetContext(FEATURE_FLAG)`
3. Add to `FUTURE_FLAGS` map
4. Add `(under feature flag)` suffix to PR title

### JSII Constraints (for `tcons` package only)

- Cannot use TypeScript-specific features not supported by target languages
- Must use JSII-compatible types
- Build with `jsii --silence-warnings reserved-word`
- Package with `jsii-pacmak` to generate language bindings

### CLI Build Process (tcons-cli)

1. `yarn compile-build-config` - Compile build.ts
2. `tsc --noEmit` - Type check
3. `node build.js` - Run esbuild bundler
4. Pre-commit hook runs `tsc` for type validation (esbuild doesn't type-check)

### Integration Testing

Integration tests in `test/` run against the built `dist/` artifacts:

```bash
cd test
yarn edge:install           # Install edge provider test fixtures
./run-against-dist npx jest --runInBand
```

### Working with Go Modules

The repository includes Go code in `hcl2json` and `hcl-tools`. A `go.work` file at the root coordinates Go modules.

## Debugging

Enable detailed logging:

```bash
export CDKTF_LOG_LEVEL=debug
export JSII_DEBUG=1
export JSII_DEBUG_TIMING=1
```

## Commit Style

Use [conventional commits](https://www.conventionalcommits.org/):

- `feat(cli): add new watch command`
- `fix(lib): resolve stack dependency issue`
- `refactor(provider-generator): simplify schema parsing`
- `chore: update dependencies`

## Package Naming Note

While the repository is being renamed from `cdktf` to `tcons`, there may be references to both names:

- Package names: `tcons`, `tcons-cli`, `@tcons/*`
- Legacy references: Some internal code may still reference `cdktf`
- Published packages maintain compatibility with original naming in some language targets (e.g., Python package is still `cdktf`)
