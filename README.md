# Summarize Stage Action

[![CI](https://github.com/optivem/summarize-stage-action/actions/workflows/ci.yml/badge.svg)](https://github.com/optivem/summarize-stage-action/actions/workflows/ci.yml)
[![Example Usage](https://github.com/optivem/summarize-stage-action/actions/workflows/example.yml/badge.svg)](https://github.com/optivem/summarize-stage-action/actions/workflows/example.yml)

[![GitHub release](https://img.shields.io/github/release/optivem/summarize-stage-action.svg)](https://github.com/optivem/summarize-stage-action/releases)
[![GitHub](https://img.shields.io/github/license/optivem/summarize-stage-action)](LICENSE)

A GitHub Action for generating comprehensive summaries of workflow stage results with customizable success content and emojis.

## Features

- 🎯 **Flexible Stage Summaries**: Generate summaries for any workflow stage (build, test, deploy, etc.)
- ✨ **Customizable Emojis**: Use different emojis for success, failure, cancelled, and unknown states
- 📝 **Rich Content Support**: Add custom markdown content for successful stages
- 🔧 **Easy Integration**: Simple composite action that works with any workflow
- 📊 **GitHub Step Summary**: Automatically adds formatted summaries to your workflow run summary

## Usage

### Basic Usage

```yaml
- name: Summarize Build Stage
  uses: optivem/summarize-stage-action@v1
  with:
    stage-result: ${{ needs.build.result }}
    stage-name: 'Build'
```

### Advanced Usage with Custom Content

```yaml
- name: Summarize Test Stage
  uses: optivem/summarize-stage-action@v1
  with:
    stage-result: ${{ needs.test.result }}
    stage-name: 'Test Suite'
    stage-success-emoji: '🧪'
    stage-content: |
      ## General Information
      
      This stage validates the application quality and functionality.
    stage-success-content: |
      ## Test Results
      
      - ✅ Unit Tests: 45/45 passed
      - ✅ Integration Tests: 12/12 passed
      - ✅ Code Coverage: 95.2%
      
      ### Performance Metrics
      - Test execution time: 2m 34s
      - Memory usage: 512MB peak
```

### Complete Workflow Example

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build application
        run: |
          echo "Building application..."
          # Your build steps here

  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: |
          echo "Running tests..."
          # Your test steps here

  deploy:
    needs: [build, test]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Deploy application
        run: |
          echo "Deploying application..."
          # Your deployment steps here

  summary:
    needs: [build, test, deploy]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - name: Summarize Build
        uses: optivem/summarize-stage-action@v1
        with:
          stage-result: ${{ needs.build.result }}
          stage-name: 'Build'
          stage-content: |
            🏗️ **Build Process Overview**
            
            This stage compiles the application and prepares artifacts.
          stage-success-content: |
            ✅ **Build completed successfully**
            
            The application has been compiled and artifacts are ready for testing.

      - name: Summarize Tests
        uses: optivem/summarize-stage-action@v1
        with:
          stage-result: ${{ needs.test.result }}
          stage-name: 'Test Suite'
          stage-success-emoji: '🧪'
          stage-success-content: |
            🧪 **All tests passed!**
            
            Quality gates have been met and the code is ready for deployment.

      - name: Summarize Deployment
        uses: optivem/summarize-stage-action@v1
        with:
          stage-result: ${{ needs.deploy.result }}
          stage-name: 'Deployment'
          stage-success-emoji: '🚀'
          stage-success-content: |
            🚀 **Deployment successful!**
            
            The application is now live and available to users.
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `stage-result` | Result of the stage job (success, failure, cancelled, etc.) | ✅ | - |
| `stage-name` | Name of the stage being summarized | ✅ | - |
| `stage-success-emoji` | Emoji to display for successful stages | ❌ | `✅` |
| `stage-failure-emoji` | Emoji to display for failed stages | ❌ | `❌` |
| `stage-cancelled-emoji` | Emoji to display for cancelled stages | ❌ | `🛑` |
| `stage-unknown-emoji` | Emoji to display for unknown stage status | ❌ | `⚠️` |
| `stage-content` | General content to display for any stage result (supports markdown) | ❌ | `` |
| `stage-success-content` | Custom content to display on success (supports markdown) | ❌ | `` |

## Outputs

This action doesn't produce outputs but adds formatted content to the GitHub Step Summary that's visible in the workflow run summary.

## Stage Results

The action handles the following stage result values:

- **success**: Displays success emoji and optional custom content
- **failure**: Displays failure emoji with error message
- **cancelled**: Displays cancelled emoji with cancellation notice
- **skipped**: Displays unknown emoji with status information
- **Other values**: Displays unknown emoji with the actual result value

## Examples

### Basic CI Pipeline Summary

```yaml
- name: Summarize CI Results
  uses: optivem/summarize-stage-action@v1
  with:
    stage-result: ${{ needs.ci.result }}
    stage-name: 'Continuous Integration'
```

### Deployment with Rich Content

```yaml
- name: Summarize Deployment
  uses: optivem/summarize-stage-action@v1
  with:
    stage-result: ${{ needs.deploy.result }}
    stage-name: 'Production Deployment'
    stage-success-emoji: '🚀'
    stage-success-content: |
      ## Deployment Details
      
      - **Environment**: Production
      - **Version**: ${{ github.sha }}
      - **Deployment Time**: $(date)
      - **Status**: Live ✅
      
      [View Application](https://your-app.com)
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you encounter any issues or have questions, please [open an issue](https://github.com/optivem/summarize-stage-action/issues) on GitHub.
