# Locus DataSource SDK

This package provides the core interface for creating external data source integrations with Locus.

## Overview

A `DataSource` represents any external API or service that can provide:
- **Topics**: High-level items like questions, articles, or videos
- **Data**: Detailed content associated with topics (answers, excerpts, transcripts)

Common examples include Stack Exchange, Wikipedia, YouTube, or custom knowledge bases.

## Contributing Data Sources

The official implementations of this SDK are maintained in the [`datasource-implementations`](https://github.com/locus-search/datasource-implementations) repository, which contains data sources used in the private Locus core software.

### Creating a New Data Source

If you wish to contribute a new data source to Locus:

1. **Start with the template**: Use the [`datasource-template`](https://github.com/locus-search/datasource-template) repository to create your implementation
2. **Build your integration**: Implement the `DataSource` interface following best practices (see below)
3. **Submit for review**: Open a pull request on the [`datasource-implementations`](https://github.com/locus-search/datasource-implementations) repository

### Acceptance Criteria

Your implementation must meet the following requirements:

- **Correctly implements desired behavior**: Accurately fetches topics and data from the target source
- **Efficient and well-implemented**: Follows Go best practices and performs optimally
- **Secure**: No vulnerabilities or malicious code
- **Minimal dependencies**: Uses as few third-party imports as possible (see Security Considerations)
- **Novel or improved**: Not already implemented, or demonstrably better than existing implementations

Implementations that do not meet these criteria will be rejected.

### Security Considerations

**Important**: Locus is designed to run on-premises at high-stakes enterprise environments. To maintain security and trust:

- **Minimize third-party dependencies**: Use the Go standard library whenever possible
- **Justify external imports**: If you must use third-party packages, provide clear rationale for their necessity
- **Vet dependencies**: Only use well-maintained, security-audited packages from trusted sources
- **Avoid unnecessary complexity**: Simpler implementations are easier to audit and maintain

This approach reduces the attack surface and ensures our software can be thoroughly vetted for enterprise deployment.

## Interface

```go
type DataSource interface {
    Init() error
    CheckAvailability() bool
    FetchTopics(count int, input NewQuestionInput) ([]DataSourceTopic, error)
    FetchData(count int, topicID int64) ([]DataSourceData, error)
}
```

## Quick Start

```go
package main

import (
    "fmt"
    datasource "github.com/locus-search/datasource-sdk"
)

type MyDataSource struct {
    // Your fields here
}

func (ds *MyDataSource) Init() error {
    // Initialize API clients, fetch config, etc.
    return nil
}

func (ds *MyDataSource) CheckAvailability() bool {
    // Quick health check
    return true
}

func (ds *MyDataSource) FetchTopics(count int, input datasource.NewQuestionInput) ([]datasource.DataSourceTopic, error) {
    // Search your API for relevant topics
    return []datasource.DataSourceTopic{
        {
            Topic:     "Example Topic",
            SourceURL: "https://example.com/topic/1",
            TopicID:   1,
        },
    }, nil
}

func (ds *MyDataSource) FetchData(count int, topicID int64) ([]datasource.DataSourceData, error) {
    // Fetch detailed content for the topic
    return []datasource.DataSourceData{
        {
            DataText:  "Detailed answer or content...",
            SourceURL: "https://example.com/topic/1#answer-1",
            AnswerID:  1,
        },
    }, nil
}
```

## Installation

```bash
go get github.com/locus-search/datasource-sdk
```

## Documentation

### Types

#### `DataSourceTopic`
Represents a high-level item that may contain relevant information.

| Field | Type | Description |
|-------|------|-------------|
| `Topic` | string | Title or main text |
| `SourceURL` | string | Canonical URL |
| `Site` | string | Optional site identifier |
| `TopicID` | int64 | Unique identifier |

#### `DataSourceData`
Represents specific content associated with a topic.

| Field | Type | Description |
|-------|------|-------------|
| `DataText` | string | The actual content |
| `SourceURL` | string | Canonical URL |
| `Site` | string | Optional site identifier |
| `AnswerID` | int64 | Unique identifier |

#### `NewQuestionInput`
Provides search context for fetching topics.

| Field | Type | Description |
|-------|------|-------------|
| `QuestionText` | string | Search query |
| `Tags` | []string | Optional topic tags |
| `AskedBy` | *int64 | Optional user ID |
| `Embedding` | []float64 | Optional semantic vector |

## Best Practices

### 1. Handle Timeouts
Set reasonable timeouts for external API calls:

```go
client := &http.Client{
    Timeout: 8 * time.Second,
}
```

### 2. Return Empty Slices, Not Errors
When no results are found, return an empty slice with `nil` error:

```go
if len(results) == 0 {
    return []DataSourceTopic{}, nil
}
```

### 3. Use Context for Cancellation
Support graceful cancellation in long-running operations:

```go
func (ds *MyDataSource) FetchTopics(count int, input NewQuestionInput) ([]DataSourceTopic, error) {
    ctx, cancel := context.WithTimeout(context.Background(), 8*time.Second)
    defer cancel()
    
    // Use ctx in API calls
}
```

### 4. Limit External API Calls
Respect rate limits and implement backoff strategies.

### 5. Validate Input
Check for required fields before making API calls:

```go
if strings.TrimSpace(input.QuestionText) == "" {
    return nil, errors.New("question text is required")
}
```

## Examples

See the following reference implementations:
- [datasource-wikipedia](https://github.com/locus-search/datasource-wikipedia) - Simple REST API integration
- [datasource-stackexchange](https://github.com/locus-search/datasource-stackexchange) - Advanced multi-site support with embedding-based selection

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - see [LICENSE](LICENSE) for details.
