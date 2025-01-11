# Forge API

A secure and flexible headless CMS library for Go applications, providing robust content management with built-in security, audit trails, and customizable APIs.

## Features

### Core Features
- Dynamic schema management
- Custom API endpoints
- Flexible data relationships
- Lifecycle hooks
- Plugin system

### Security Features
- Role-based access control (RBAC)
- Multiple authentication methods (JWT, API Key, OAuth)
- Field-level encryption
- Security audit logging
- Rate limiting & DDoS protection
- GDPR compliance helpers

### API Features
- RESTful API generation
- GraphQL support (coming soon)
- Custom endpoints
- API documentation
- Rate limiting
- Access control

### Storage Features
- Multiple database support (PostgreSQL, MySQL, MongoDB)
- Caching layer (Memory, Redis)
- Data encryption
- Audit trails

## Installation

```bash
go get github.com/BimaPangestu28/forge-api
```

## Quick Start

```go
package main

import (
    forge "github.com/BimaPangestu28/forge-api"
)

func main() {
    // Initialize Forge with security config
    app := forge.New(forge.Config{
        Storage: forge.StorageConfig{
            Type: "postgres",
            DSN:  "postgres://user:pass@localhost:5432/dbname",
        },
        Security: forge.SecurityConfig{
            Auth: forge.AuthConfig{
                JWT: forge.JWTConfig{
                    Secret: "your-secret-key",
                    ExpiryTime: 24 * time.Hour,
                },
            },
            RBAC: forge.RBACConfig{
                DefaultRole: "user",
                SuperAdminRole: "admin",
            },
        },
    })

    // Define schema with security options
    app.DefineSchema("article", forge.Schema{
        Fields: map[string]forge.Field{
            "title": {
                Type:     forge.String,
                Required: true,
            },
            "content": {
                Type: forge.Text,
                Security: forge.FieldSecurity{
                    Encrypted: true,
                },
            },
        },
        Security: forge.SchemaSecurity{
            Roles: []string{"editor", "admin"},
            Audit: true,
        },
    })

    // Define secure API endpoint
    app.DefineAPI("article.publish", forge.APIConfig{
        Method: "POST",
        Path:   "/articles/:id/publish",
        Access: forge.Private,
        Security: forge.APISecurity{
            Roles: []string{"editor"},
            RateLimit: &forge.RateLimit{
                Max: 100,
                Duration: time.Hour,
            },
        },
        Handler: publishHandler,
    })
}
```

## Project Structure

```
forge-api/
├── core/                    # Core package functionality
│   ├── schema/             # Schema definitions & validation 
│   │   ├── field/          # Field types and handlers
│   │   ├── validation/     # Schema validation rules
│   │   └── types.go        # Common schema types
│   ├── query/              # Query building & execution
│   └── hooks/              # Lifecycle hooks system
│
├── security/               # Security components
│   ├── authentication/     # Authentication system
│   │   ├── jwt/           # JWT handling
│   │   ├── apikey/        # API key management
│   │   └── oauth/         # OAuth providers
│   ├── authorization/      # Authorization system
│   └── encryption/        # Data encryption
│
├── api/                    # API functionality
│   ├── rest/              # REST API implementation
│   └── graphql/           # GraphQL implementation
│
├── storage/               # Storage implementations
│   ├── drivers/          # Database drivers
│   └── cache/            # Caching layer
│
├── plugins/              # Plugin system
├── utils/               # Shared utilities
├── examples/            # Example implementations
├── tests/               # Test suites
└── docs/                # Documentation
```

### Key Directories Explained
- `core/`: Contains the fundamental business logic and domain models
- `security/`: Security-related implementations
- `api/`: API-related code
- `storage/`: Database and caching implementations

## Development Guide

### Local Development Setup

1. **Prerequisites Installation**
```bash
# Install Go 1.21 or later
go version

# Install development tools
go install golang.org/x/tools/cmd/goimports@latest
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
```

2. **Clone & Setup**
```bash
# Clone repository
git clone https://github.com/BimaPangestu28/forge-api.git
cd forge-api

# Install dependencies
go mod download

# Set up pre-commit hooks
cp scripts/pre-commit .git/hooks/
chmod +x .git/hooks/pre-commit
```

3. **Environment Setup**
```bash
# Copy example env file
cp .env.example .env

# Edit .env with your configurations
vim .env
```

## Code Style & Standards

### Go Code Style
- Follow official Go style guide
- Use `gofmt` formatted code
- Follow package naming conventions
- Use meaningful variable names

### Package Organization
```go
package schema

import (
    "context"
    
    "github.com/BimaPangestu28/forge-api/core"
    "github.com/BimaPangestu28/forge-api/utils"
)

// Types at the top
type Schema struct {
    // ...
}

// Constants and variables
const (
    DefaultPageSize = 20
)

// Public functions
func New() *Schema {
    // ...
}

// Private functions at the bottom
func validateField() error {
    // ...
}
```

### Coding Standards

1. **Error Handling**
```go
// Good
if err != nil {
    return fmt.Errorf("failed to create schema: %w", err)
}

// Bad
if err != nil {
    return err
}
```

2. **Comments and Documentation**
```go
// SchemaOption configures a schema instance
type SchemaOption func(*Schema)

// WithValidation adds validation rules to the schema
func WithValidation(rules []string) SchemaOption {
    return func(s *Schema) {
        // Implementation
    }
}
```

3. **Interface Design**
```go
// Keep interfaces small and focused
type Storage interface {
    Get(ctx context.Context, id string) (interface{}, error)
    Set(ctx context.Context, id string, value interface{}) error
    Delete(ctx context.Context, id string) error
}
```

## Adding New Features

1. **Planning**
   - Create an issue describing the feature
   - Discuss implementation approach
   - Define interface and types

2. **Implementation**
   - Create feature branch
   - Write tests first (TDD)
   - Implement feature
   - Update documentation

3. **Testing**
   - Unit tests
   - Integration tests
   - Performance tests if needed

4. **Documentation**
   - Update API documentation
   - Add example code
   - Update README if needed

## Development Prerequisites

### Required Software
- Go 1.21 or higher
- Git
- Make
- Docker & Docker Compose

### Databases
- PostgreSQL 14+
- Redis 6+
- MongoDB 5+ (optional)

### Tools
- golangci-lint
- goimports
- gotest
- mockgen

### Editor Setup
Recommended VS Code extensions:
- Go
- Go Test Explorer
- Go Outliner
- Docker

## Roadmap

- [ ] GraphQL Support
- [ ] Real-time Subscriptions
- [ ] Plugin System
- [ ] Multi-tenant Support
- [ ] Asset Management
- [ ] Workflow Engine
- [ ] Import/Export Tools
- [ ] Admin UI Generator

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Write tests
4. Implement feature
5. Run tests and linters
6. Update documentation
7. Submit a pull request

### Pull Request Guidelines
- Clear PR description
- Link to related issues
- Include tests
- Update documentation
- Follow code style guide

## Security

Please report security vulnerabilities to security@forge-api.com

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

- GitHub Issues: [Issues](https://github.com/BimaPangestu28/forge-api/issues)
- Documentation: [Docs](docs/)