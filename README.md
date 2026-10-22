# fin-card-issuer-api

[![API Quality Gates](https://github.com/cam-ai-lab/fin-card-issuer-api/actions/workflows/quality-gates.yml/badge.svg)](https://github.com/cam-ai-lab/fin-card-issuer-api/actions/workflows/quality-gates.yml)

A Fastify-based REST API for managing credit card accounts with comprehensive API governance using Spectral linting.

## What it is

A deliberately dummy credit-card account API used to practise API governance rather than card-issuing logic. One Fastify + TypeScript endpoint (`GET /credit-cards/{accountId}`) plus a health check is described in `openapi.yaml`, and a Spectral ruleset (`.spectral.yml`) enforces naming and design rules in the `API Quality Gates` GitHub Actions workflow. `sample-issue.md` is a feature request (a `minimumDue` field) drafted to exercise that workflow.

## Status

active (dormant). All work happened on 2025-07-14 (initial API, Spectral workflow fixes, badge, Copilot instructions, sample issue); idle since. There is no test suite (`npm test` is the npm placeholder).

## Layout

```
src/index.ts                    Fastify server and the credit-card endpoint
openapi.yaml                    API contract linted by Spectral
openapi-test*.yaml              fixtures with and without rule violations
.spectral.yml                   governance ruleset
.github/workflows/quality-gates.yml  Spectral CI
scripts/setup-branch-protection.sh   gh-based branch protection helper
sample-issue.md                 example feature request (minimumDue)
```

## 🚀 Features

- **Credit Card Account Management**: Retrieve account details including balance, reward points, and payment due dates
- **API Governance**: Spectral-powered linting ensures consistent API design and naming conventions
- **Type Safety**: Full TypeScript implementation with strict type checking
- **Documentation**: Auto-generated OpenAPI/Swagger documentation
- **Quality Gates**: Comprehensive CI/CD pipeline with linting, testing, and security scanning

## 📋 API Endpoints

### Get Credit Card Account Details
```
GET /credit-cards/{accountId}
```

**Parameters:**
- `accountId` (path): Credit card account identifier (format: XXXX-XXXX-XXXX-XXXX)

**Response:**
```json
{
  "accountId": "4532-1234-5678-9012",
  "totalBalance": 1250.75,
  "membershipRewardPoints": 15420,
  "paymentDueDate": "2025-08-15"
}
```

### Health Check
```
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2025-07-14T10:30:00.000Z"
}
```

## 🛠 Development Setup

### Prerequisites
- Node.js 20+
- npm or yarn

### Installation
```bash
# Clone the repository
git clone <your-repo-url>
cd fin-card-issuer-api

# Install dependencies
npm install

# Start development server
npm run dev
```

The API will be available at `http://localhost:3000`
API documentation at `http://localhost:3000/docs`

### Available Scripts

```bash
# Development
npm run dev          # Start development server with hot reload
npm run build        # Build for production
npm start           # Start production server

# Code Quality
npm run lint        # Run ESLint
npm run lint:fix    # Fix ESLint issues automatically
npm run format      # Format code with Prettier
npm run type-check  # TypeScript type checking

# API Governance
npm run spectral:lint     # Lint OpenAPI spec with Spectral
npm run spectral:lint:ci  # Lint for CI (fails on warnings)
```

## 📐 API Governance with Spectral

This project uses Spectral to enforce API design standards and naming conventions:

### Enforced Rules

1. **Naming Conventions**:
   - Property names: `camelCase`
   - Schema names: `PascalCase`
   - Operation IDs: `camelCase`
   - Path parameters: `camelCase`

2. **Documentation Requirements**:
   - All operations must have summaries and descriptions
   - All schemas must have descriptions
   - All properties should have descriptions
   - Operations must have appropriate tags

3. **Response Standards**:
   - All operations must have 2xx responses
   - Operations should have 4xx responses for error cases
   - Error responses must use consistent schema

4. **Metadata Requirements**:
   - API must have contact information
   - API should have license information

### Example Spectral Violations

❌ **Invalid (will fail CI)**:
```yaml
# Property names not in camelCase
properties:
  total_balance: # Should be: totalBalance
    type: number
  membership_reward_points: # Should be: membershipRewardPoints
    type: integer
```

✅ **Valid**:
```yaml
# Correct camelCase naming
properties:
  totalBalance:
    type: number
    description: Current total balance on the account
  membershipRewardPoints:
    type: integer
    description: Available membership reward points
```

## 🔄 CI/CD Pipeline

The project includes comprehensive GitHub Actions workflows:

### Quality Gates Workflow
- **TypeScript Type Checking**: Ensures type safety
- **ESLint**: Code quality and style enforcement
- **Prettier**: Code formatting validation
- **Spectral Linting**: API specification governance
- **OpenAPI Validation**: Schema validation
- **Contract Testing**: API endpoint testing
- **Security Scanning**: Dependency and secret scanning

### Branch Protection
- Required status checks (all CI jobs must pass)
- Required pull request reviews
- No direct pushes to main branch
- No force pushes or deletions

## 🛡 Setting Up GitHub Branch Protection

1. **Create GitHub Repository**:
   ```bash
   # Initialize git repository
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/yourusername/fin-card-issuer-api.git
   git push -u origin main
   ```

2. **Set up Branch Protection** (choose one method):

   **Method A: Using the provided script**
   ```bash
   # Set your GitHub token
   export GITHUB_TOKEN="your_personal_access_token"
   
   # Update the script with your GitHub username and repo name
   nano scripts/setup-branch-protection.sh
   
   # Run the setup script
   chmod +x scripts/setup-branch-protection.sh
   ./scripts/setup-branch-protection.sh
   ```

   **Method B: Manual setup via GitHub UI**
   1. Go to your repository on GitHub
   2. Navigate to Settings → Branches
   3. Click "Add rule" for the main branch
   4. Configure:
      - ✅ Require status checks to pass before merging
      - ✅ Require branches to be up to date before merging
      - ✅ Select status checks: "Code Quality & API Validation", "API Contract Validation", "Security Scan"
      - ✅ Require pull request reviews before merging
      - ✅ Dismiss stale pull request approvals when new commits are pushed
      - ✅ Require review from code owners (if you have a CODEOWNERS file)
      - ✅ Include administrators
      - ✅ Restrict pushes that create files (prevent force pushes)

## 🔑 Required GitHub Secrets

For the CI/CD pipeline to work properly, set up these repository secrets:

1. Go to repository Settings → Secrets and variables → Actions
2. Add any required secrets (currently none needed for basic setup)

## 🧪 Testing the Setup

1. **Create a Feature Branch**:
   ```bash
   git checkout -b feature/test-api-governance
   ```

2. **Make a Change that Violates Spectral Rules**:
   ```yaml
   # In openapi.yaml, change a property to snake_case
   total_balance:  # This will fail Spectral linting
     type: number
   ```

3. **Push and Create PR**:
   ```bash
   git add .
   git commit -m "Test: violate naming convention"
   git push origin feature/test-api-governance
   ```

4. **Observe CI Failure**: The PR will be blocked due to Spectral linting failure

5. **Fix the Issue**:
   ```yaml
   # Correct the naming back to camelCase
   totalBalance:
     type: number
   ```

6. **Push Fix**: CI should now pass and allow merging

## 🔧 Customizing Spectral Rules

Edit `.spectral.yml` to customize API governance rules:

```yaml
# Add custom rule
rules:
  my-custom-rule:
    description: Custom validation rule
    message: "Custom error message"
    given: "$.paths[*][*]"
    severity: error
    then:
      function: truthy
```

## 📚 API Documentation

- **Local Development**: http://localhost:3000/docs
- **OpenAPI Spec**: `/openapi.yaml`
- **Interactive Documentation**: Swagger UI available at `/docs` endpoint

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes following the established patterns
4. Ensure all quality gates pass
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.
