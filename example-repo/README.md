# APort SDK Generator Example

This repository demonstrates how to use the APort SDK Generator GitHub Action to generate SDKs and client libraries for APort agents in multiple programming languages.

## 🚀 Quick Start

### 1. Fork this Repository

Click the "Fork" button to create your own copy of this repository.

### 2. Set up APort Agent

1. Go to [APort Dashboard](https://aport.io)
2. Create a new agent
3. Configure the agent with capabilities and policies
4. Note the Agent ID

### 3. Configure GitHub Secrets

Add the following secrets to your repository:

1. Go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add the following secrets:

| Secret Name | Description | Example Value |
|-------------|-------------|---------------|
| `APORT_AGENT_ID` | Your APort Agent ID | `agent_1234567890abcdef` |

### 4. Test the Action

1. Push changes to the main branch
2. The action will automatically generate SDKs
3. Or trigger manually via **Actions** → **APort SDK Generation** → **Run workflow**

## 🔧 Configuration

### Supported Languages

| Language | Description | Output |
|----------|-------------|--------|
| `python` | Python SDK with type hints | Complete Python package |
| `nodejs` | Node.js SDK with TypeScript | npm package with types |
| `go` | Go SDK with structs | Go module |

### Workflow Configuration

The workflow is configured to run on:

- **Push Events:** When changes are pushed to main branch
- **Manual Dispatch:** Can be triggered manually with custom parameters

## 📊 Usage Examples

### Basic SDK Generation

```yaml
name: Basic SDK Generation
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

### Multi-language SDK Generation

```yaml
name: Multi-language SDK
on:
  workflow_dispatch:
    inputs:
      languages:
        description: "Languages to generate"
        required: true
        default: "python,nodejs,go,rust"

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

1. **"Agent not found" error**
   - Verify `APORT_AGENT_ID` secret is correct
   - Check agent exists in APort dashboard

2. **"Language not supported" error**
   - Check supported languages list
   - Verify language name spelling

3. **"Generation failed" error**
   - Check agent capabilities
   - Verify API connectivity

### Debug Mode

To enable debug logging, modify the workflow:

```yaml
- name: Generate SDK
  uses: aporthq/sdk-generator-action@v1
  with:
    agent-id: ${{ secrets.APORT_AGENT_ID }}
    languages: 'python,nodejs'
    debug: true  # Add this line
```

## 📚 Advanced Examples

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

### SDK Versioning

```yaml
name: SDK Versioning
on:
  push:
    tags:
      - 'v*'

jobs:
  generate-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SDK
        uses: aporthq/sdk-generator-action@v1
        with:
          agent-id: ${{ secrets.APORT_AGENT_ID }}
          languages: 'python,nodejs,go'
          output-dir: './generated-sdk'
      
      - name: Create Release
        uses: actions/create-release@v1
        with:
          tag_name: ${{ github.ref }}
          release_name: SDK Release ${{ github.ref }}
          body: |
            Generated SDKs for agent ${{ secrets.APORT_AGENT_ID }}
            
            Languages: ${{ steps.generate.outputs.languages_generated }}
          files: generated-sdk/*
```

### SDK Testing

```yaml
name: SDK Testing
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
          languages: 'python,nodejs,go'
          output-dir: './generated-sdk'
      
      - name: Test Python SDK
        if: contains(steps.generate.outputs.languages_generated, 'python')
        run: |
          cd generated-sdk/python
          pip install -e .
          python -c "import aport_sdk; print('Python SDK imported successfully')"
      
      - name: Test Node.js SDK
        if: contains(steps.generate.outputs.languages_generated, 'nodejs')
        run: |
          cd generated-sdk/nodejs
          npm install
          npm run build
          node -e "console.log('Node.js SDK built successfully')"
      
      - name: Test Go SDK
        if: contains(steps.generate.outputs.languages_generated, 'go')
        run: |
          cd generated-sdk/go
          go mod tidy
          go build
          echo "Go SDK built successfully"
```

## 🤝 Contributing

1. Fork this repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

MIT License - see LICENSE file for details.

## 🆘 Support

- 📖 [APort Documentation](https://aport.io/docs)
- 💬 [Discord Community](https://discord.gg/aport)
- 🐛 [Issue Tracker](https://github.com/aporthq/sdk-generator-action/issues)
- 📧 [Email Support](mailto:support@aport.io)

## 🔗 Related

- [APort Dashboard](https://aport.io)
- [SDK Generator Action](https://github.com/aporthq/sdk-generator-action)
- [APort Documentation](https://aport.io/docs)
