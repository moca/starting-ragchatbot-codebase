# GitHub Actions for Claude Code with AWS Bedrock

This directory contains GitHub Actions workflows that use Claude Sonnet 4.5 via AWS Bedrock.

## Workflows

### 1. Claude Code Review (`claude-code-review.yml`)

Automatically reviews pull requests using Claude Sonnet 4.5.

**Triggers:**
- Automatically on pull request events (opened, synchronized, reopened)
- Manually via workflow_dispatch

**Features:**
- Reviews code changes in pull requests
- Provides feedback on:
  - Code quality
  - Potential bugs
  - Security concerns
  - Best practices
  - Suggestions for improvement
- Posts review as a PR comment

**Usage:**
1. The workflow runs automatically on PRs
2. Or trigger manually from Actions tab with a PR number

### 2. Claude Assistant (`claude-assistant.yml`)

General-purpose Claude assistant for answering questions or performing tasks.

**Triggers:**
- When an issue is labeled with `claude-assist`
- Manually via workflow_dispatch with a custom query

**Features:**
- Answers questions about the codebase
- Provides assistance with coding tasks
- Can be used for general AI-powered help

**Usage:**

**Option 1: Via Issue Label**
1. Create a GitHub issue with your question in the body
2. Add the label `claude-assist` to the issue
3. Claude will respond with a comment and remove the label

**Option 2: Via Workflow Dispatch**
1. Go to Actions tab
2. Select "Claude Assistant" workflow
3. Click "Run workflow"
4. Enter your query
5. View results in the workflow run logs

## Setup

### Required GitHub Secrets

Configure the following secrets in your repository settings:

- `AWS_ACCESS_KEY_ID` - Your AWS access key ID
- `AWS_SECRET_ACCESS_KEY` - Your AWS secret access key
- `AWS_SESSION_TOKEN` - (Optional) AWS session token for temporary credentials
- `AWS_REGION` - (Optional) AWS region, defaults to `us-east-1`

**To add secrets:**
1. Go to repository Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Add each required secret

### AWS Bedrock Requirements

1. Your AWS account must have access to AWS Bedrock
2. The Claude Sonnet 4.5 model must be enabled in your region
3. IAM permissions must allow `bedrock:InvokeModel` for the Claude model

### Model Information

These workflows use:
- **Model ID:** `global.anthropic.claude-sonnet-4-20250514-v1:0`
- **Model Name:** Claude Sonnet 4.5 (Global)

## Permissions

The workflows require the following GitHub permissions:
- `contents: read` - To read repository code
- `pull-requests: write` - To comment on pull requests (code review workflow)
- `issues: write` - To comment on issues (assistant workflow)

## Examples

### Example Code Review Output
```
🤖 Claude Sonnet 4.5 Code Review

Summary of Changes:
- Added new GitHub Actions workflows
- Configured AWS Bedrock integration

Potential Issues:
- None identified

Security Concerns:
- Ensure AWS credentials are properly secured as GitHub secrets

Suggestions:
- Consider adding error handling for API failures
- Add timeout configurations for long-running operations

---
Powered by AWS Bedrock with Claude Sonnet 4.5
```

### Example Assistant Query
**Issue body:** "How can I improve error handling in the ai_generator.py file?"

**Claude's response:** Detailed suggestions for improving error handling with code examples.

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Verify AWS credentials are correctly set in GitHub secrets
   - Check IAM permissions for Bedrock access
   - Ensure session token is provided if using temporary credentials

2. **Model Not Available**
   - Verify Claude Sonnet 4.5 is enabled in your AWS region
   - Check the model ID matches your Bedrock configuration

3. **No Response Posted**
   - Check workflow logs for errors
   - Verify the workflow has required permissions
   - Ensure the trigger conditions are met

## Customization

### Adjusting Model Parameters

Edit the workflow files to customize:
- `max_tokens`: Maximum response length
- `temperature`: Response creativity (0 = deterministic, 1 = creative)
- `model`: Switch to different Claude models if needed

### Changing Triggers

Modify the `on:` section in workflow files to change when they run.

### Custom Prompts

Update the prompt text in the Python scripts to customize Claude's behavior.
