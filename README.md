# APort SDK Generator Action

Generate SDKs and client libraries for APort agents in multiple programming languages.

## 🚀 Features

- **Multi-language Support** - Generate SDKs for Python, Node.js, Go, and more
- **Type Safety** - Include TypeScript definitions and type hints
- **Agent-specific** - Generate SDKs tailored to specific agent capabilities
- **GitHub Integration** - Seamless integration with GitHub Actions
- **Flexible Output** - Configurable output directories and formats

## 📋 Usage

### Basic Usage

```yaml
name: Generate APort SDK
on:
  push:
    branches: [main]

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SDK
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: 'python,nodejs'
          output-dir: './generated-sdk'
```

### Advanced Usage

```yaml
name: Advanced SDK Generation
on:
  workflow_dispatch:
    inputs:
      agent_id:
        description: 'Agent ID to generate SDK for'
        required: true
      languages:
        description: 'Languages to generate'
        required: false
        default: 'python,nodejs,go'

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SDK
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ github.event.inputs.agent_id }}
          languages: ${{ github.event.inputs.languages }}
          output-dir: './generated-sdk'
          include-types: 'true'
          api-base: 'https://api.aport.io'
```

## 🔧 Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `agent-id` | The APort Agent ID to generate SDK for | ✅ | - |
| `api-base` | The base URL for the APort API | ❌ | `https://api.aport.io` |
| `output-dir` | Directory to output generated SDK files | ❌ | `./generated-sdk` |
| `languages` | Comma-separated list of languages to generate | ❌ | `python,nodejs` |
| `include-types` | Whether to include TypeScript type definitions | ❌ | `true` |
| `github-token` | GitHub token for publishing generated SDKs | ❌ | `${{ github.token }}` |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `generated_files` | JSON array of generated file paths |
| `languages_generated` | JSON array of languages that were successfully generated |
| `success` | Boolean indicating if SDK generation was successful |

## 🏗️ Setup

### 1. Create APort Agent

1. Go to [APort Dashboard](https://aport.io)
2. Create a new agent
3. Configure capabilities and policies
4. Note the Agent ID

### 2. Set GitHub Secrets

Add the following secrets to your repository:

```bash
APORT_AGENT_ID=your-agent-id-here
```

## 🌐 Supported Languages

### Python

Generates a complete Python package with:

- **Client Class** - `APortClient` for API interactions
- **Type Definitions** - Pydantic models for type safety
- **Requirements** - Dependencies in `requirements.txt`
- **Package Structure** - Proper Python package layout

```python
from aport_sdk import APortClient, PolicyContext

client = APortClient(api_base="https://api.aport.io")
passport = client.get_passport()
result = client.verify_policy(PolicyContext(
    repo="owner/repo",
    base_branch="main",
    github_actor="user"
))
```

### Node.js

Generates a complete Node.js package with:

- **TypeScript Support** - Full type definitions
- **Modern JavaScript** - ES2020+ features
- **Package.json** - Proper npm package configuration
- **Build System** - TypeScript compilation setup

```typescript
import { APortClient, PolicyContext } from '@aport/sdk-agent-id';

const client = new APortClient('https://api.aport.io');
const passport = await client.getPassport();
const result = await client.verifyPolicy({
  repo: 'owner/repo',
  base_branch: 'main',
  github_actor: 'user'
});
```

### Go

Generates a complete Go module with:

- **Client Struct** - `APortClient` for API interactions
- **Type Definitions** - Structs for all data types
- **Go Modules** - Proper `go.mod` configuration
- **Error Handling** - Comprehensive error handling

```go
package main

import (
    "fmt"
    "github.com/aport/sdk-agent-id"
)

func main() {
    client := aport.NewClient("https://api.aport.io")
    passport, err := client.GetPassport()
    if err != nil {
        panic(err)
    }
    
    ctx := aport.PolicyContext{
        Repo: "owner/repo",
        BaseBranch: "main",
        GitHubActor: &[]string{"user"}[0],
    }
    
    result, err := client.VerifyPolicy(ctx)
    if err != nil {
        panic(err)
    }
}
```

## 📚 Examples

### Generate and Publish SDK

```yaml
name: Generate and Publish SDK
on:
  push:
    branches: [main]

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SDK
        id: generate
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: 'python,nodejs,go'
          output-dir: './generated-sdk'
      
      - name: Publish Python SDK
        if: contains(steps.generate.outputs.languages_generated, 'python')
        run: |
          cd generated-sdk/python
          pip install build
          python -m build
          # Add your publishing logic here
      
      - name: Publish Node.js SDK
        if: contains(steps.generate.outputs.languages_generated, 'nodejs')
        run: |
          cd generated-sdk/nodejs
          npm publish
          # Add your publishing logic here
```

### Multi-language SDK Generation

```yaml
name: Multi-language SDK
on:
  workflow_dispatch:
    inputs:
      languages:
        description: 'Languages to generate'
        required: true
        default: 'python,nodejs,go,rust'

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SDK
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: ${{ github.event.inputs.languages }}
          output-dir: './generated-sdk'
          include-types: 'true'
      
      - name: Upload SDK Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: generated-sdk
          path: generated-sdk/
```

### Conditional SDK Generation

```yaml
name: Conditional SDK Generation
on:
  push:
    branches: [main, staging]

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate Production SDK
        if: github.ref == 'refs/heads/main'
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: 'python,nodejs,go'
          output-dir: './production-sdk'
      
      - name: Generate Development SDK
        if: github.ref == 'refs/heads/staging'
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: 'python,nodejs'
          output-dir: './development-sdk'
```

## 🚨 Troubleshooting

### Common Issues

1. **Agent not found**
   - Verify `APORT_AGENT_ID` is correct
   - Check agent exists in APort dashboard

2. **Language not supported**
   - Check supported languages list
   - Verify language name spelling

3. **Generation failed**
   - Check agent capabilities
   - Verify API connectivity

### Debug Mode

Enable debug logging:

```yaml
- name: Generate SDK
  uses: aporthq/sdk-generator-action@v1
  with:
    agent-id: ${{ secrets.APORT_AGENT_ID }}
    languages: 'python,nodejs'
    debug: true
```

## 📚 Examples

See the `example-repo/` directory for complete working examples.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

MIT License - see LICENSE file for details.

## 🆘 Support

- 📖 [Documentation](https://aport.io/docs)
- 💬 [Discord Community](https://discord.gg/aport)
- 🐛 [Issue Tracker](https://github.com/aporthq/sdk-generator-action/issues)
- 📧 [Email Support](mailto:support@aport.io)
