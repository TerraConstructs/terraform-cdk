# TerraConstructs TCONS - TODO List

Last Updated: December 11, 2024

---

## 🚀 High Priority - Package Publishing

### [ ] 1. Configure NPM Publishing Workflow

**Files to modify:**

- `.github/workflows/release.yml`
- `.github/workflows/release_next.yml`

**Tasks:**

- [ ] Add npm authentication token to GitHub Secrets (`NPM_TOKEN`)
- [ ] Update package scope references from `@cdktf/*` to `@tcons/*`
- [ ] Configure npm publish step for these packages:
  - [ ] `tcons` (main library)
  - [ ] `tcons-cli` (CLI tool)
  - [ ] `@tcons/cli-core`
  - [ ] `@tcons/commons`
  - [ ] `@tcons/hcl-tools`
  - [ ] `@tcons/hcl2json`
  - [ ] `@tcons/hcl2cdk`
  - [ ] `@tcons/provider-generator`
  - [ ] `@tcons/provider-schema`
- [ ] Remove or update Sentry references (change `cdktf-cli` to `tcons-cli` or remove if not using)
- [ ] Update job names from `cdktf-cli` to `tcons-cli`
- [ ] Test publish workflow in dry-run mode

**Acceptance Criteria:**

- GitHub Actions workflow publishes packages to npm on release
- Packages are accessible via `npm install tcons` and `npm install -g tcons-cli`
- Version numbers are correctly synchronized across all packages

---

### [ ] 2. Setup Go Module Publishing

**Target Repository:** https://github.com/TerraConstructs/terraform-cdk-go

**Tasks:**

- [ ] Create GitHub Actions workflow to sync Go module
  - Source: `packages/tcons/dist/go/tcons/` (after JSII build)
  - Destination: https://github.com/TerraConstructs/terraform-cdk-go
- [ ] Configure authentication between repositories (GitHub token or deploy key)
- [ ] Setup automated tagging for Go module versions (e.g., `v0.21.0`)
- [ ] Ensure go.mod uses correct module path: `github.com/TerraConstructs/terraform-cdk-go`
- [ ] Add copyright headers to generated Go files (already scripted in `go-copyright-header.sh`)
- [ ] Test installation: `go get github.com/TerraConstructs/terraform-cdk-go/tcons@v0.21.0`

**Acceptance Criteria:**

- On release, Go module is automatically published to target repository
- Go developers can install with standard `go get` command
- Module follows Go versioning conventions (semantic versioning with `v` prefix)

---

### [ ] 3. Test Package Installation & Functionality

**NPM Packages:**

```bash
# Test library installation
npm install tcons

# Test CLI installation
npm install -g tcons-cli

# Verify CLI works (entrypoint remains cdktf for compatibility)
cdktf --version
cdktf init --help
```

**Go Module:**

```bash
# Test Go module installation
go get github.com/TerraConstructs/terraform-cdk-go/tcons@v0.21.0

# Test import in Go code
# import "github.com/TerraConstructs/terraform-cdk-go/tcons"
```

**Tasks:**

- [ ] Create test project using published `tcons` package
- [ ] Verify `tcons-cli` binary works globally
- [ ] Test `tcons init` command creates valid project
- [ ] Test `tcons get` command downloads providers
- [ ] Test Go import and basic usage
- [ ] Document any issues or limitations found

---

## 📦 Deferred - Other Package Registries

### [ ] 4. Python Package Publishing (PyPI)

**Status:** Deferred until needed

**When ready:**

- [ ] Configure PyPI authentication
- [ ] Update `.github/workflows/release.yml` for Python publishing
- [ ] Package name: `cdktf` (keeping backward compatible name)
- [ ] Test: `pip install cdktf`

---

### [ ] 5. Java Package Publishing (Maven Central)

**Status:** Deferred - `mvn` not currently installed

**Prerequisites:**

- [ ] Install Maven on build environment
- [ ] Fix packaging error: `yarn package` currently fails on Java

**When ready:**

- [ ] Setup Maven Central account and GPG keys
- [ ] Configure Maven publishing in workflows
- [ ] Package: `com.hashicorp:cdktf`
- [ ] Test: Maven dependency resolution

---

### [ ] 6. C#/.NET Package Publishing (NuGet)

**Status:** Deferred until needed

**When ready:**

- [ ] Configure NuGet authentication
- [ ] Update workflows for NuGet publishing
- [ ] Package: `HashiCorp.Cdktf`
- [ ] Test: `dotnet add package HashiCorp.Cdktf`

---

## 🔧 CI/CD Improvements

### [ ] 7. Update GitHub Workflows

**File:** `.github/workflows/pr-unit.yml`

- [ ] Update job name: `"cdktf-cli Unit Tests"` → `"tcons-cli Unit Tests"`
- [ ] Update labels: `'ci/run-unit/cdktf-cli'` → `'ci/run-unit/tcons-cli'`
- [ ] Update concurrency groups: `pr-cdktf-cli-core` → `pr-tcons-cli-core`

**File:** `.github/workflows/release.yml`

- [ ] Review all references to `cdktf` and update where appropriate
- [ ] Ensure Sentry integration uses `tcons-cli` or is removed

**File:** `.github/workflows/release_next.yml`

- [ ] Same updates as release.yml

---

## 🧹 Code Quality (Optional)

### [ ] 8. Fix ESLint Warnings

**Status:** Optional - 396 warnings, 0 errors (pre-existing)

**Most common warnings:**

- Unused variables (`no-unused-vars`)
- `require()` style imports (`@typescript-eslint/no-require-imports`)
- Unused eslint-disable directives

**Recommendation:** Address incrementally, not blocking for release

---

### [ ] 9. Update Documentation Examples

**Status:** Optional

**Files:**

- `packages/@tcons/cli-core/src/lib/dependencies/package-manager.ts`
  - Lines 31, 33, 54: Update examples from `@cdktf/provider-random` to `@tcons/provider-random`

---

## 🎯 Future Enhancements

### [ ] 10. Prebuilt Provider Publishing

**Current State:**

- Using `@cdktf/provider-*` from archived HashiCorp repository for testing
- Code returns `@cdktf/provider-${name}` in prebuilt-providers.ts:100

**When ready to publish own providers:**

- [ ] Update `packages/@tcons/cli-core/src/lib/dependencies/prebuilt-providers.ts:100`
  - Change: `return \`@cdktf/provider-\${name}\`;`
  - To: `return \`@tcons/provider-\${name}\`;`
- [ ] Setup provider generation and publishing pipeline
- [ ] Publish providers to npm under `@tcons` scope
- [ ] Update documentation

---

### [ ] 11. Migration Documentation

**Create migration guide for users:**

- [ ] Document package.json changes
- [ ] Document import statement changes
- [ ] Provide codemod or migration script
- [ ] Create comparison table of old vs new package names
- [ ] Document CLI compatibility (binary name remains `cdktf`)

---

### [ ] 12. Integration Testing

**Test with real providers:**

- [ ] Test with AWS provider
- [ ] Test with Azure provider
- [ ] Test with GCP provider
- [ ] Test multi-language project generation
- [ ] Test HCL conversion functionality

---

## ✅ Completed

- [x] Rename core packages from `cdktf` to `tcons`
- [x] Fix node-pty package issue (upgrade to Node v22)
- [x] Fix code generator import statements
- [x] Update package dependencies
- [x] Fix build errors (JSII compilation)
- [x] Fix formatting issues
- [x] Verify `yarn build` succeeds
- [x] Verify `yarn test` passes
- [x] Create CLAUDE.md for repository context
- [x] Create SESSION_SUMMARY.md documenting the work

---

## 📊 Progress Tracking

| Category         | Completed | Total | Progress |
| ---------------- | --------- | ----- | -------- |
| Package Rename   | 11        | 11    | ✅ 100%  |
| Build Fixes      | 3         | 3     | ✅ 100%  |
| Publishing Setup | 0         | 3     | ⏳ 0%    |
| CI/CD Updates    | 0         | 3     | ⏳ 0%    |
| Documentation    | 2         | 4     | 🟡 50%   |

---

## 🔗 Useful Links

- **Main Repository:** https://github.com/terraconstructs/terraform-cdk
- **Go Module Repository:** https://github.com/TerraConstructs/terraform-cdk-go
- **Original CDKTF:** https://github.com/hashicorp/terraform-cdk (archived)
- **NPM Registry:** https://www.npmjs.com/
- **Go Package Index:** https://pkg.go.dev/

---

**Note:** This TODO list is a living document. Update it as tasks are completed or new requirements emerge.
