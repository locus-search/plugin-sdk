# Contributing to Locus DataSource SDK

Thank you for your interest in contributing to the Locus DataSource SDK!

## How to Contribute

### Contributing to the SDK Interface

This repository defines the core `DataSource` interface. For changes to the SDK itself:

#### Reporting Issues

- Check if the issue already exists
- Provide clear steps to reproduce
- Include Go version and SDK version
- Share relevant code snippets

#### Proposing SDK Changes

1. **Open an issue first** - Interface changes affect all implementations
2. **Fork the repository**
3. **Create a feature branch**: `git checkout -b feature/my-feature`
4. **Make your changes**
5. **Add tests** for new functionality
6. **Run tests**: `go test ./...`
7. **Commit with clear messages**: `git commit -m "Add feature X"`
8. **Push to your fork**: `git push origin feature/my-feature`
9. **Open a Pull Request**

### Contributing a New Data Source Implementation

Official data source implementations are maintained in the [`datasource-implementations`](https://github.com/locus-search/datasource-implementations) repository and are used in the private Locus core software.

#### Implementation Workflow

1. **Use the template**: Start with [`datasource-template`](https://github.com/locus-search/datasource-template)
   ```bash
   # Create a new repository from the template
   # Then clone and develop your implementation
   git clone https://github.com/YOUR-USERNAME/YOUR-DATASOURCE-NAME
   ```

2. **Implement the interface**: Follow the `DataSource` interface specification
   - Implement all required methods: `Init()`, `CheckAvailability()`, `FetchTopics()`, `FetchData()`
   - Follow the code guidelines below
   - Write comprehensive tests

3. **Submit to datasource-implementations**: Once your implementation is complete:
   - Fork the [`datasource-implementations`](https://github.com/locus-search/datasource-implementations) repository
   - Add your implementation to the appropriate directory
   - Open a pull request with a clear description

#### Acceptance Criteria

Your implementation will be reviewed against the following criteria:

**Correct Behavior**
- Accurately implements the `DataSource` interface
- Correctly fetches and formats topics and data from the target source
- Handles edge cases and errors appropriately

**Code Quality**
- Efficient implementation with optimal performance
- Follows Go best practices and idioms
- Well-structured, readable, and maintainable code
- Comprehensive test coverage

**Security**
- No security vulnerabilities or malicious code
- **Minimal third-party dependencies** (see Security Requirements below)
- Properly handles authentication and sensitive data
- Input validation and sanitization where applicable

**Novelty or Improvement**
- Data source is not already implemented, OR
- Your implementation is demonstrably better than the existing one (faster, more features, more reliable, etc.)

**Implementations will be rejected if they:**
- Do not correctly implement the desired behavior
- Are inefficient or poorly implemented
- Contain security vulnerabilities or malicious code
- Have excessive third-party dependencies without justification
- Duplicate existing implementations without meaningful improvements

## Code Guidelines

### Security Requirements

**Critical**: Locus is designed to run on-premises in high-stakes enterprise environments. Security is paramount.

#### Minimal Third-Party Dependencies

- **Prefer the Go standard library**: Use built-in packages whenever possible
- **Justify external dependencies**: If you must import third-party packages:
  - Explain why the standard library is insufficient
  - Demonstrate the dependency is essential for functionality
  - Choose well-maintained, security-audited packages from trusted sources
- **Avoid dependency bloat**: Each import increases the attack surface
- **Document all dependencies**: Include rationale in your PR description

**Examples:**
- Use `net/http` for HTTP requests
- Use `encoding/json` for JSON parsing
- Use `time` for date/time operations
- Only use specialized libraries if absolutely necessary (e.g., official API SDKs)
- Avoid utility libraries that duplicate standard library functionality

### Interface Stability

The `DataSource` interface is considered **stable**. Any changes must:
- Maintain backward compatibility
- Be discussed in an issue first
- Include migration guide if breaking

### Code Style

- Follow standard Go formatting (`gofmt`)
- Use meaningful variable names
- Add comments for exported types and functions
- Keep functions focused and testable
- Handle errors explicitly (don't ignore errors)
- Use context for cancellation where appropriate

### Documentation

- Update README.md for new features
- Add godoc comments for public APIs
- Include code examples where helpful
- Update CHANGELOG.md

### Testing

- Write tests for new functionality
- Maintain or improve code coverage
- Test edge cases and error conditions
- Use table-driven tests where appropriate

Example:
```go
func TestMyFeature(t *testing.T) {
    tests := []struct {
        name    string
        input   string
        want    string
        wantErr bool
    }{
        {"valid input", "test", "result", false},
        {"empty input", "", "", true},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := MyFunction(tt.input)
            if (err != nil) != tt.wantErr {
                t.Errorf("error = %v, wantErr %v", err, tt.wantErr)
            }
            if got != tt.want {
                t.Errorf("got %v, want %v", got, tt.want)
            }
        })
    }
}
```

## Questions?

Open an issue with the "question" label or reach out to the maintainers.

Thank you for contributing!