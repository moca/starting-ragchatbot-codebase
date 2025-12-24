# GitHub Actions for Claude Code with AWS Bedrock

This directory contains GitHub Actions workflows that use Claude Sonnet 4.5 via AWS Bedrock.

## Workflows

### 1. Claude Code Review (`claude-code-review.yml`)

Automatically reviews pull requests using Claude Sonnet 4.5.

**Triggers:**
- Automatically on pull request events (opened, synchronize, reopened)
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

### Required Configuration

#### 1. AWS IAM Role Setup

These workflows use AWS IAM roles with OpenID Connect (OIDC) for secure authentication without storing AWS credentials.

**Step 1: Create IAM OIDC Identity Provider**
1. Go to AWS IAM Console → Identity Providers → Add provider
2. Provider type: OpenID Connect
3. Provider URL: `https://token.actions.githubusercontent.com`
4. Audience: `sts.amazonaws.com`
5. Click "Add provider"

**Step 2: Create IAM Role**
1. Go to IAM → Roles → Create role
2. Trusted entity type: Web identity
3. Identity provider: `token.actions.githubusercontent.com`
4. Audience: `sts.amazonaws.com`
5. Add condition (optional but recommended):
   - Key: `token.actions.githubusercontent.com:sub`
   - Value: `repo:YOUR_ORG/YOUR_REPO:*` (replace with your repo)
6. Attach policy with Bedrock permissions (see below)
7. Name the role (e.g., `GitHubActions-Claude-Bedrock-Role`)
8. Note the Role ARN

**Step 3: Create IAM Policy for Bedrock**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel"
      ],
      "Resource": [
        "arn:aws:bedrock:*::foundation-model/anthropic.claude-*"
      ]
    }
  ]
}
```

#### 2. GitHub Repository Configuration

**Required Secret:**
- `AWS_ROLE_ARN` - The ARN of the IAM role created above (e.g., `arn:aws:iam::123456789012:role/GitHubActions-Claude-Bedrock-Role`)

**Optional Variable:**
- `AWS_REGION` - AWS region for Bedrock (defaults to `us-east-1`)

**To add secret:**
1. Go to repository Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `AWS_ROLE_ARN`
4. Value: Your IAM role ARN
5. Click "Add secret"

**To add variable (optional):**
1. Go to repository Settings → Secrets and variables → Actions → Variables tab
2. Click "New repository variable"
3. Name: `AWS_REGION`
4. Value: Your preferred region (e.g., `us-east-1`)

### AWS Bedrock Requirements

1. Your AWS account must have access to AWS Bedrock
2. The Claude Sonnet 4.5 model must be enabled in your region
3. The IAM role must have permissions to invoke the Claude model

### Model Information

These workflows use:
- **Model ID:** `global.anthropic.claude-sonnet-4-20250514-v1:0`
- **Model Name:** Claude Sonnet 4.5 (Global)

## Permissions

The workflows require the following GitHub permissions:
- `contents: read` - To read repository code
- `pull-requests: write` - To comment on pull requests (code review workflow)
- `issues: write` - To comment on issues (assistant workflow)
- `id-token: write` - To request OIDC token for AWS authentication

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
- Ensure AWS IAM role has minimal required permissions
- Review trust policy to restrict access to your repository

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
   - Verify `AWS_ROLE_ARN` secret is correctly set in GitHub repository settings
   - Check IAM role trust policy allows your GitHub repository
   - Ensure OIDC identity provider is configured in AWS IAM
   - Verify the role has `bedrock:InvokeModel` permissions

2. **OIDC Token Errors**
   - Ensure `id-token: write` permission is set in workflow
   - Verify the OIDC provider audience is `sts.amazonaws.com`
   - Check that the repository matches the trust policy condition

3. **Model Not Available**
   - Verify Claude Sonnet 4.5 is enabled in your AWS region
   - Check the model ID matches your Bedrock configuration
   - Ensure the IAM role has access to the specific model ARN

4. **No Response Posted**
   - Check workflow logs for errors
   - Verify the workflow has required GitHub permissions
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
