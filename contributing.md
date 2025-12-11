# Contributing to AWS Security Infrastructure

Thank you for your interest in contributing to our AWS Security Infrastructure project!

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Process](#development-process)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Security Requirements](#security-requirements)

## Code of Conduct

### Our Standards

- Use welcoming and inclusive language
- Be respectful of differing viewpoints
- Accept constructive criticism gracefully
- Focus on what is best for the organization
- Show empathy towards other contributors

## Getting Started

### Prerequisites

1. Fork the repository
2. Clone your fork locally
3. Set up your development environment:

```bash
# Install required tools
brew install terraform
brew install tflint
brew install checkov
brew install pre-commit

# Install pre-commit hooks
pre-commit install
```

### Development Environment Setup

```bash
# Configure AWS credentials
aws configure --profile finserv-dev

# Initialize Terraform
cd terraform/modules/network
terraform init
```

## Development Process

### Branch Strategy

- `main` - Production-ready code
- `develop` - Integration branch for features
- `feature/*` - Feature branches
- `hotfix/*` - Emergency fixes
- `release/*` - Release preparation

### Creating a Feature Branch

```bash
git checkout develop
git pull origin develop
git checkout -b feature/your-feature-name
```

## Pull Request Process

### Before Submitting

1. **Test Your Changes**
```bash
# Terraform validation
terraform fmt -recursive
terraform validate

# Security scanning
checkov -d terraform/
tfsec terraform/

# Compliance checks
./scripts/compliance/validate.sh
```

2. **Update Documentation**
- Update relevant `.md` files in `/docs`
- Update `README.md` if needed
- Add inline comments to complex code

3. **Commit Message Format**
```
<type>(<scope>): <subject>

<body>

<footer>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

Example:
```
feat(network): add Transit Gateway route table

- Implement routing for inspection VPC
- Add route propagation for spoke VPCs
- Enable DNS support

Closes #123
```

### PR Requirements

1. **Title**: Clear, descriptive summary
2. **Description**: 
   - What changes were made
   - Why the changes were needed
   - How to test the changes
3. **Reviewers**: Minimum 2 approvals required
4. **Checks**: All CI/CD checks must pass
5. **Documentation**: Updated if applicable

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Security scans pass
- [ ] Manually tested

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests added/updated
- [ ] Dependent changes merged

## Security Impact
Describe any security implications

## Compliance Impact
Describe impact on PCI-DSS, SOX, SOC 2
```

## Coding Standards

### Terraform Standards

```hcl
# Use consistent naming
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = merge(
    var.common_tags,
    {
      Name = "${var.environment}-vpc"
    }
  )
}

# Use variables for all configurable values
variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  validation {
    condition     = can(cidrhost(var.vpc_cidr, 0))
    error_message = "Must be valid IPv4 CIDR."
  }
}

# Always include outputs
output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}
```

### Security Best Practices

1. **Never commit secrets**
   - Use AWS Secrets Manager
   - Use parameter store
   - Use environment variables

2. **Encryption everywhere**
   - Enable encryption at rest
   - Use TLS 1.2+ for transit
   - Use KMS customer-managed keys

3. **Least privilege**
   - Minimum required permissions
   - Resource-based policies where possible
   - Regular access reviews

### Documentation Standards

```markdown
# Component Name

## Purpose
Clear statement of what this does

## Architecture
High-level design explanation

## Usage
```hcl
module "example" {
  source = "./modules/example"
  
  vpc_id = "vpc-xxx"
  cidr   = "10.0.0.0/16"
}
```

## Inputs
| Name | Description | Type | Required |
|------|-------------|------|----------|
| vpc_id | VPC identifier | string | yes |

## Outputs
| Name | Description |
|------|-------------|
| subnet_id | Subnet identifier |

## Security Considerations
List any security implications
```

## Security Requirements

### Code Review Checklist

- [ ] No hardcoded credentials
- [ ] No overly permissive IAM policies
- [ ] Encryption enabled where applicable
- [ ] Security groups follow least privilege
- [ ] Logging enabled for all resources
- [ ] Tags applied consistently
- [ ] Data classification respected
- [ ] Compliance requirements met

### Testing Requirements

```bash
# Security scanning
checkov -d . --framework terraform

# Static analysis
tfsec .

# Policy validation
opa test policies/

# Compliance validation
./scripts/compliance/pci-dss-check.sh
./scripts/compliance/sox-check.sh
```

## Review Process

### Reviewer Responsibilities

1. **Code Quality**
   - Readable and maintainable
   - Follows style guide
   - Properly documented

2. **Security**
   - No vulnerabilities introduced
   - Follows security best practices
   - Complies with policies

3. **Compliance**
   - Meets regulatory requirements
   - Audit trail maintained
   - Change control followed

### Approval Requirements

- **Standard Changes**: 2 approvers
- **Security Changes**: 2 approvers + Security team
- **Production Changes**: 2 approvers + CAB approval

## Change Management

### Change Types

1. **Standard Change**: Pre-approved, low risk
2. **Normal Change**: Requires CAB review
3. **Emergency Change**: High priority, expedited review

### CAB (Change Advisory Board) Process

For production changes:
1. Submit change request 5 business days prior
2. Include rollback plan
3. Schedule maintenance window
4. Notify stakeholders
5. Execute with approval

## Questions?

- Technical Questions: devops@yourcompany.com
- Security Questions: security@yourcompany.com
- Process Questions: compliance@yourcompany.com

## Recognition

Contributors will be recognized in:
- Monthly team meetings
- Annual reviews
- Project documentation

Thank you for contributing to secure infrastructure! 🔒
