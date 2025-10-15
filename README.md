# AWS Infrastructure Diagram Generator

Automated AWS infrastructure diagram generation using Amazon Q Developer CLI agents with Model Context Protocol (MCP) servers.

## Overview

This project provides specialized agents that automatically discover and visualize AWS infrastructure:

- **graph-mermaid**: Generates Mermaid diagrams of AWS resources
- **graph-aws**: Creates AWS architecture diagrams using the AWS Diagram MCP server

Both agents can connect to AWS accounts, discover resources, and generate comprehensive infrastructure documentation with visual diagrams.

## Project Structure

```
q-agents-diagram/
├── agent-mermaid/          # Mermaid diagram generation agent
│   ├── .amazonq/
│   │   └── cli-agents/
│   │       ├── graph-mermaid.json    # Agent configuration
│   │       ├── system-prompt.md      # Agent instructions
│   │       └── aws-credentials.md    # AWS credentials reference
│   ├── output/             # Generated diagrams directory
└── agent-aws/              # AWS diagram generation agent
    ├── .amazonq/
    │   └── cli-agents/
    │       ├── graph-aws.json        # Agent configuration
    │       ├── system-prompt.md      # Agent instructions
    │       └── aws-credentials.md    # AWS credentials reference
    └── output/             # Generated diagrams directory
```

## Features

### graph-mermaid Agent
- Generates Mermaid syntax diagrams
- Supports multiple AWS services (EC2, VPC, S3, Lambda, RDS, etc.)
- Validates Mermaid syntax using MCP validator
- Outputs structured markdown documentation
- Configurable white background theme
- Specialized for VPC-focused diagrams

### graph-aws Agent
- Uses AWS Diagram MCP server for native AWS diagrams
- Comprehensive AWS service support
- Professional AWS architecture visualization
- Automated resource discovery
- Structured output with account identity verification

### investigator Agent
- General-purpose AWS investigation and analysis
- File system operations
- Bash command execution
- AWS CLI integration

### Common Features
- Multi-profile AWS credential support
- Cross-account and assume role capabilities
- Automated output directory creation (`./output/{account-id}-{region}/`)
- Timestamped documentation
- AWS caller identity verification
- Atomic file writing
- Readonly operations auto-approved for security
- Automated output directory creation
- Timestamped documentation
- Caller identity verification

## Prerequisites

- Amazon Q Developer CLI installed
- Node.js (for MCP servers)
- Python with uvx (for AWS MCP servers)
- Valid AWS credentials

## Installation

1. Clone the repository:

```bash
git clone https://github.com/mercoglianoisidoro/q-agents-diagram.git
cd q-agents-diagram
```

2. Ensure Amazon Q Developer CLI is installed:

```bash
q --version
```

3. Install required MCP dependencies:

```bash
# For Mermaid validator (Node.js)
npm install -g @rtuin/mcp-mermaid-validator

# For AWS MCP servers (Python)
pip install uvx
```


## Usage

`q chat --agent graph-mermaid|graph-aws`


## Usage Examples

### Basic Infrastructure Diagram

Generate a complete infrastructure diagram for Ireland region:

```bash
# Using Mermaid agent
q chat --agent graph-mermaid --no-interactive --trust-all-tools \
  "create an infrastructure diagram of all the resources in the region Ireland"

# Using AWS diagram agent
q chat --agent graph-aws --no-interactive --trust-all-tools \
  "create an infrastructure diagram of all the resources in the region Ireland"
```


### Interactive Mode

For more complex requirements, use interactive mode:

```bash
q chat --agent graph-mermaid
```

### Custom Profile and Region

Use specific AWS profile and region:

```bash
q chat --agent graph-aws --no-interactive --trust-all-tools \
  "create a diagram of the aws account showing only the s3 buckets: use 'isi' profile and ireland region"
```




## Configuration

### AWS Credentials

The agents use standard AWS credential resolution in order of precedence:

1. Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`)
2. AWS profiles from `~/.aws/credentials`

You can configure available profiles in the `aws-credentials.md` files within each agent directory. In this case the agent will update your profiles.


### Agent Configuration

Each agent has a JSON configuration file that defines:
- MCP servers and their commands
- Allowed tools and services
- File system permissions
- Model settings (Claude Sonnet 4.5)



## Output Structure

Generated diagrams are saved in organized directories:

```
output/
└── {account-id}-{region}/
    └── infrastructure.md    # Contains diagrams and AWS account info
```

Each output file includes:
- AWS caller identity information
- Generated diagrams (Mermaid or AWS native)
- Timestamp and file path footer


## MCP Servers Used

### Mermaid Agent
- `@rtuin/mcp-mermaid-validator`: Mermaid syntax validation
- `awslabs.aws-documentation-mcp-server`: AWS service documentation

### AWS Agent
- `awslabs.aws-diagram-mcp-server`: Native AWS diagram generation
- `awslabs.aws-documentation-mcp-server`: AWS service documentation

## Security Considerations

- AWS credentials follow standard AWS credential resolution (environment variables or profiles)
- Agents have configurable AWS service access permissions (default: all services with readonly auto-approved)
- File system write access is restricted to `./output/**` directory
- Cross-account access and assume-role capabilities require explicit configuration
- MCP servers run with auto-approval for configured request types
- Bash command execution is restricted to explicitly allowed commands

**Production Deployment Recommendations:**

- Use IAM roles with least-privilege policies
- Enable AWS CloudTrail for audit logging
- Restrict file system paths in production environments
- Review and limit allowed tools
- Use AWS Organizations SCPs for cross-account guardrails


## Repository

- **GitHub**: [mercoglianoisidoro/q-agents-diagram](https://github.com/mercoglianoisidoro/q-agents-diagram)
- **Issues**: Report bugs or request features via GitHub Issues
