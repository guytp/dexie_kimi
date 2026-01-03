# Contributing to Dexie Platform

Thank you for your interest in contributing to Dexie! This guide will help you understand our development workflow and get you started quickly. Dexie is a first-of-its-kind Pokémon card pricing platform for the UK market, built with modern technologies and best practices.

## Overview

Dexie follows **trunk-based development** optimized for a small, fast-moving team. Our goal is to maintain high code quality while enabling rapid development and deployment.

## Table of Contents

- [Branching Strategy](#branching-strategy)
- [Branch Naming Conventions](#branch-naming-conventions)
- [Pull Request Workflow](#pull-request-workflow)
- [Development Setup](#development-setup)
- [Commit Guidelines](#commit-guidelines)
- [Code Review Expectations](#code-review-expectations)
- [Breaking Changes in Shared Libraries](#breaking-changes-in-shared-libraries)
- [Getting Help](#getting-help)

## Branching Strategy

### Trunk-Based Development

We use **trunk-based development** with a single long-lived `main` branch:

- **`main`** - The only long-lived branch, always deployable
- **Short-lived feature branches** - Created from `main`, merged back via PR
- **No `develop` branch** - The overhead of GitFlow is not justified at this stage

### Key Principles

1. **Never commit directly to `main`** - All changes must go through pull requests
2. **Keep branches short-lived** - Ideally under 2 days, maximum 1 week
3. **Small, focused changes** - Each branch should address one story or bug
4. **Frequent integration** - Merge to `main` frequently to avoid conflicts
5. **Backward compatibility** - Changes to shared libraries must be backward compatible where possible

### Repository Structure Flexibility

While we currently use a mono-repo, our structure is designed to support extraction to separate repositories if needed:

- Each service is self-contained in its own directory
- Shared libraries are versioned and have clear contracts
- No tight coupling between services
- Service boundaries are well-defined

This means your contributions should respect these boundaries and avoid creating cross-service dependencies that would complicate future extraction.

## Branch Naming Conventions

Use clear, descriptive branch names following these patterns:

### Feature Branches

For new features and user stories:

```
feature/STORY_NUMBER-brief-description
```

**Examples:**
- `feature/45-price-aggregation-endpoints`
- `feature/53-git-structure-and-branching`
- `feature/67-card-image-upload`

### Bugfix Branches

For bug fixes:

```
bugfix/STORY_NUMBER-brief-description
```

**Examples:**
- `bugfix/102-mobile-crash-on-card-scan`
- `bugfix/118-price-calculation-error`
- `bugfix/125-memory-leak-in-scraper`

### Chore Branches

For maintenance, refactoring, and tooling updates:

```
chore/STORY_NUMBER-brief-description
```

**Examples:**
- `chore/89-update-dotnet-sdk-8.0.200`
- `chore/92-refactor-health-checks`
- `chore/ci-update-deployment-pipeline`

### Spike Branches

For experimental or research work that may not be merged:

```
spike/description-of-experiment
```

**Examples:**
- `spike/ai-model-evaluation`
- `spike/react-vs-vue-for-admin-ui`
- `spike/new-scraping-approach`

**Important:** Spike branches are typically short-lived and may be deleted without merging. Document your findings in the branch and associated PR before closing.

## Pull Request Workflow

Follow this workflow for all contributions:

### 1. Create a Branch from `main`

```bash
# Ensure you're on main and have the latest changes
git checkout main
git pull origin main

# Create and switch to your feature branch
git checkout -b feature/53-git-structure-and-branching
```

### 2. Implement Your Changes

- Work on your feature/bugfix
- Commit frequently with clear messages (see [Commit Guidelines](#commit-guidelines))
- Keep commits small and focused
- Run tests and builds locally before pushing

### 3. Build and Test Locally

**Build all affected services:**

```bash
# Build the specific service
dotnet build backend/services/cards-service/CardsService.sln

# Build shared libraries if you changed them
dotnet build backend/libs/dexie-domain/Dexie.Shared.Domain.sln
dotnet build backend/libs/dexie-infra/Dexie.Shared.Infrastructure.sln

# Build all affected services
./scripts/build-all.sh  # If available, or build individually
```

**Run tests:**
```bash
# Run tests for the specific service
dotnet test backend/services/cards-service/CardsService.sln

# Run tests for shared libraries
dotnet test backend/libs/dexie-domain/Dexie.Shared.Domain.sln
```

**Verify the service runs:**
```bash
cd backend/services/cards-service/src/Dexie.Cards.Api
dotnet run

# In another terminal, test the health endpoint
curl http://localhost:5001/health
```

### 4. Push Your Branch

```bash
# Push your branch to the remote
git push -u origin feature/53-git-structure-and-branching
```

### 5. Create a Pull Request

1. Go to the repository on GitHub/GitLab
2. Click "New Pull Request"
3. Select your branch as the source and `main` as the target
4. Fill in the PR template with all required information

**PR Title Format:**
```
[Type StoryNumber] Brief description
```

**Examples:**
- `[Feature 53] Establish Git Repository Structure and Branching`
- `[Bugfix 102] Fix mobile crash on card scan`
- `[Chore 89] Update .NET SDK to 8.0.200`

**PR Description Template:**
```markdown
## Summary
Brief description of what this PR does

## Story
Closes #[Story Number]

## Changes Made
- List of major changes
- Another significant change
- Any other important modifications

## Testing
- [ ] Built and ran locally
- [ ] Unit tests pass
- [ ] Integration tests pass (if applicable)
- [ ] Health endpoint responds correctly

## Notes for Reviewers
Anything specific you'd like reviewers to focus on
```

### 6. Code Review

**Requirements:**
- At least **one approving review** from a different engineer
- No blocking review comments
- All status checks must pass

**Code Review Guidelines:**
Reviewers should focus on:
- **Correctness** - Does the code work as intended?
- **Architecture alignment** - Does it fit the system design?
- **Simplicity** - Can it be simpler or more maintainable?
- **Test coverage** - Are non-trivial parts tested?
- **Security** - Any data privacy or security concerns?
- **Error handling** - Are edge cases handled properly?

**As a Reviewer:**
- Be constructive and helpful
- Prefer comments that suggest concrete improvements
- Approve once concerns are addressed
- Don't block PRs unnecessarily

**As an Author:**
- Respond to all feedback
- Implement suggestions or explain why not
- Keep PRs small and focused
- Don't take feedback personally

### 7. Status Checks

Before merging, ensure:
- ✅ All builds pass
- ✅ Unit tests pass
- ✅ No linting errors
- ✅ Static analysis passes (if configured)
- ✅ Code coverage meets thresholds (future)

### 8. Merge the PR

**We use squash merge by default:**
- Keeps main branch history clean
- Each PR becomes a single, atomic commit
- Easier to revert if needed
- Clear commit messages from PR title and description

**To merge:**
1. Ensure all reviews are approved
2. Ensure all status checks pass
3. Click "Squash and merge"
4. Edit the commit message if needed
5. Confirm the merge

### 9. Clean Up

After merging, delete your branch:

```bash
# Delete local branch
git branch -d feature/53-git-structure-and-branching

# Delete remote branch
git push origin --delete feature/53-git-structure-and-branching
```

## Development Setup

Get your development environment ready:

### Quick Start

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-org/dexie-platform.git
   cd dexie-platform
   ```

2. **Set up environment variables:**
   ```bash
   # Copy the example env file (create if it doesn't exist)
   cp .env.example .env  # If you have one
   
   # Or set variables directly
   export MONGODB_CONNECTION_STRING="mongodb://localhost:27017/dexie-dev"
   export RABBITMQ_HOST="localhost"
   export RABBITMQ_USERNAME="guest"
   export RABBITMQ_PASSWORD="guest"
   ```

3. **Build shared libraries:**
   ```bash
   dotnet build backend/libs/dexie-domain/Dexie.Shared.Domain.sln
   dotnet build backend/libs/dexie-infra/Dexie.Shared.Infrastructure.sln
   ```

4. **Build and run a service:**
   ```bash
   dotnet build backend/services/cards-service/CardsService.sln
   dotnet run --project backend/services/cards-service/src/Dexie.Cards.Api/Dexie.Cards.Api.csproj
   ```

### Full Setup

See the [README.md](README.md) for complete setup instructions, including:
- Prerequisites (.NET 8, MongoDB, RabbitMQ)
- Docker Compose setup
- IDE configuration
- Testing all services

## Commit Guidelines

Write clear, concise commit messages that explain the "why" not just the "what":

### Commit Message Format

```
Type: Brief description (max 72 chars)

Detailed explanation of what and why, wrapped at 72 characters.
Include any important context, trade-offs, or decisions made.

Story: #STORY_NUMBER
```

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation changes
- `style` - Code style changes (formatting, etc.)
- `refactor` - Code refactoring
- `test` - Adding or updating tests
- `chore` - Maintenance tasks, build changes, etc.

**Examples:**

```
feat: add price aggregation endpoint

Implement new endpoint to calculate average prices across
different time periods. This supports the new dashboard
feature showing price trends over time.

Story: #45
```

```
fix: resolve memory leak in card image processor

The image processing pipeline was not disposing of streams
correctly, causing memory to accumulate. Fixed by adding
using statements and explicit disposal.

Story: #118
```

```
docs: update API documentation for card endpoints

Added request/response examples for all card endpoints,
including query parameters and status codes.

Story: #67
```

### Commit Frequency

- **Commit early and often** - Don't wait until the end
- **Keep commits focused** - Each commit should do one thing
- **Make commits atomic** - Each commit should build and pass tests
- **Use interactive rebase** - Clean up history before pushing (`git rebase -i main`)

## Code Review Expectations

### As an Author

Before requesting review:
- [ ] Build and test locally
- [ ] Write/update tests for new functionality
- [ ] Update documentation if needed
- [ ] Self-review your code
- [ ] Ensure PR is focused and not too large

**PR Size Guidelines:**
- **Small PR:** < 200 lines (ideal, quick review)
- **Medium PR:** 200-500 lines (acceptable, standard review)
- **Large PR:** > 500 lines (avoid if possible, may need multiple reviewers)

### As a Reviewer

**Your responsibilities:**
- Review within 24 hours when possible
- Be thorough but constructive
- Approve or request changes clearly
- Don't block without good reason
- Celebrate good work

**What to look for:**
- **Functionality** - Does it work correctly?
- **Architecture** - Does it fit the system design?
- **Code Quality** - Is it readable, maintainable?
- **Tests** - Are there appropriate tests?
- **Security** - Any security concerns?
- **Performance** - Any obvious performance issues?

**Review Comments:**
- Be specific and constructive
- Explain the "why" behind suggestions
- Use code suggestions when helpful
- Balance thoroughness with pragmatism

## Breaking Changes in Shared Libraries

Break the build? Here's how to handle breaking changes in shared libraries (`Dexie.Shared.Domain`, `Dexie.Shared.Infrastructure`):

### Before Making Breaking Changes

1. **Notify the team** - Post in #engineering channel
2. **Impact assessment** - Identify all affected services
3. **Migration plan** - Document how services should update
4. **Coordinate rollout** - Plan the order of PRs

### Process for Breaking Changes

1. **Create a feature branch** for the breaking change
2. **Update the shared library** with the changes
3. **Increment the version** if applicable
4. **Update all affected services** in the same PR if possible
5. **Document the changes** in the PR description with migration guide
6. **Get approvals** from all affected service owners

### Example PR Description for Breaking Change

```markdown
BREAKING CHANGE: Update Card model to support multiple variants

## Summary
Changes the Card model to support multiple variants instead of
a single variant string. This is required for the new card 
management features.

## Impact
Affects: cards-service, prices-service, gateway-api

## Migration Guide
1. Update Card model references
2. Use Card.Variants[] instead of Card.Variant
3. Update any variant-specific logic
4. Run database migration script

## Coordinated Deployment
1. Deploy shared library update
2. Deploy cards-service
3. Deploy prices-service  
4. Deploy gateway-api

## Testing
- [ ] cards-service builds and passes tests
- [ ] prices-service builds and passes tests
- [ ] gateway-api builds and passes tests
- [ ] Integration tests pass
```

### Backward Compatibility

Where possible, prefer backward-compatible changes:
- Add new fields instead of modifying existing ones
- Use default values for new properties
- Deprecate old methods before removing them
- Provide migration helpers

## Getting Help

### Resources

- **README.md** - Project overview and quickstart
- **Docs** - Architecture and runbooks in `/docs/`
- **Code Guidelines** - Follow best practices for .NET and React

### Questions or Issues

- **Technical Questions:** Ask in #engineering Slack channel
- **Story Clarification:** Contact the product owner
- **PR Review Issues:** Tag specific reviewers or ask in #pr-reviews
- **Urgent Issues:** Mention @here in #engineering or escalate to tech lead

### Common Issues

**Build failures:**
```bash
# Clean and rebuild
dotnet clean backend/services/cards-service/CardsService.sln
dotnet build backend/services/cards-service/CardsService.sln
```

**Test failures:**
- Check if dependencies are running (MongoDB, RabbitMQ)
- Verify environment variables are set
- Run tests individually: `dotnet test --filter "TestName"`

**Git issues:**
- Sync with main: `git pull --rebase origin main`
- Undo commits: `git reset --soft HEAD~1`
- Fix merge conflicts: resolve, then `git rebase --continue`

## Future Repository Structure

Our mono-repo structure is designed for extraction:

### Service Boundaries

Each service in `/backend/services/` can be extracted to its own repository with minimal changes:

1. **Copy service directory** to new repository
2. **Update project references** to shared libraries (use NuGet packages instead)
3. **Extract CI/CD** pipeline configuration
4. **Update deployment** configurations

### Shared Library Strategy

When extracting services, shared libraries will be published as NuGet packages:

- **Dexie.Shared.Domain** - Core domain models
- **Dexie.Shared.Infrastructure** - Infrastructure helpers
- **Dexie.Testing** - Testing utilities

Keep shared library changes backward-compatible to avoid breaking extracted services.

### Current Structure Benefits

- **Simplicity** - One repository to clone, build, and manage
- **Atomic changes** - Update multiple services in one PR
- **Shared tooling** - Common build scripts, CI/CD configs
- **Discovery** - Easy to explore and understand the whole system

### When to Extract

Consider extracting services when:
- Different teams need different review processes
- Services have different release cycles
- Build times become too long
- Repository size becomes unmanageable

Until then, enjoy the simplicity of the mono-repo!

## Non-Functional Requirements & Expectations

### Performance of Workflow

To maintain development velocity and minimize friction:

**Branch Lifetime:**
- **Ideal:** Less than 2 days from creation to merge
- **Maximum:** 1 week (requires justification and approval)
- **Rationale:** Short-lived branches reduce merge conflicts, keep changes focused, and enable faster feedback loops

**Pull Request Size:**
- **Ideal:** Under 200 lines of code changes
- **Acceptable:** 200-500 lines (may require multiple reviewers)
- **Avoid:** Over 500 lines (should be split into smaller PRs)
- **Rationale:** Small PRs review faster, catch bugs earlier, and reduce cognitive load on reviewers

**PR Review Time:**
- **Target:** Review within 24 hours of submission
- **Escalation:** Tag @here in #pr-reviews if no response after 48 hours
- **Rationale:** Fast reviews prevent context switching and keep work moving

**Commit Frequency:**
- **Commit often:** Every logical unit of work (typically 1-3 hours)
- **Push regularly:** At least once per day to enable CI and backup
- **Rationale:** Small commits make debugging easier and reduce work loss risk

### Security Implications

Security is everyone's responsibility. Consider these implications in every PR:

**Authentication & Authorization:**
- Are new endpoints properly protected with appropriate auth checks?
- Should this feature be available to all users or restricted?
- Are JWT tokens validated correctly? Are roles checked?

**Data Privacy:**
- Is any personally identifiable information (PII) being logged or exposed?
- Are we collecting only the data we need (data minimization)?
- Are card images and user data properly encrypted at rest?

**Input Validation:**
- Are all inputs validated and sanitized (SQL injection, XSS prevention)?
- Are file uploads scanned for malware?
- Are we protected against mass assignment vulnerabilities?

**Dependencies:**
- Are new dependencies necessary and from trusted sources?
- Have dependencies been scanned for known vulnerabilities (`dotnet list package --vulnerable`)?
- Are NuGet packages pinned to specific versions?

**Secrets Management:**
- No secrets in code, configuration files, or logs
- Use environment variables or Azure Key Vault for sensitive data
- Rotate credentials if there's any suspicion of exposure

**Security Review Checklist:**
- [ ] Authentication required where appropriate
- [ ] Authorization checks in place
- [ ] Input validation on all user inputs
- [ ] No secrets or credentials in code
- [ ] Dependencies scanned for vulnerabilities
- [ ] Sensitive data encrypted at rest and in transit
- [ ] Security headers configured (CORS, CSP, etc.)

### Reliability Expectations

Dexie must be reliable for Pokémon collectors and sellers who depend on accurate pricing data.

**Backward Compatibility:**
- **API Changes:** Never break existing API contracts without versioning (e.g., `/v1/`, `/v2/`)
- **Database Changes:** Always provide backward-compatible migrations; deploy schema changes before code changes
- **Shared Libraries:** Changes to `Dexie.Shared.*` must be backward compatible or coordinated across all services

**Error Handling:**
- All external calls (MongoDB, RabbitMQ, HTTP) must have timeout and retry logic
- Use circuit breakers for external dependencies
- Log errors with sufficient context for debugging
- Fail gracefully: return partial data rather than 500 errors where possible

**Monitoring & Observability:**
- Health endpoints must respond quickly (< 100ms)
- Log structured data (structured logging with Serilog)
- Track key metrics: error rates, latency, throughput
- Set up alerts for anomalies (future: Prometheus/Grafana)

**Graceful Degradation:**
- If AI analysis fails, still return basic pricing data
- If image upload fails, allow manual entry
- Cache frequently accessed data to survive DB outages

**Rollback Plan:**
- Every deployment must be reversible within 5 minutes
- Keep previous version ready for immediate rollback
- Test rollback procedures regularly

### Branch Protection Recommendations

Protect `main` with these GitHub settings:

**Required Status Checks:**
- [ ] Require status checks to pass before merging
  - Build must pass
  - All tests must pass
  - Code coverage threshold (future: minimum 70%)
- [ ] Require branches to be up to date before merging
- [ ] Require conversation resolution before merging

**Pull Request Restrictions:**
- [ ] Require pull request reviews before merging
  - Number of required approvals: 1
  - Require review from Code Owners (future)
- [ ] Dismiss stale pull request approvals when new commits are pushed
- [ ] Require approval from specific teams for shared library changes
- [ ] Require signed commits (future, not currently enforced)

**Administration:**
- [ ] Include administrators in restrictions (no one commits directly to main)
- [ ] Restrict pushes that create files larger than 10MB (prevent accidental commits of large binaries)

**Enforcement:**
These rules apply to everyone, including senior engineers and tech leads. If you need to bypass them (emergency hotfix), you must:
1. Get verbal approval from another senior engineer
2. Create PR immediately after hotfix deployment
3. Document the emergency in the PR description
4. Schedule post-mortem within 48 hours

### Performance Expectations

**Response Times:**
- **Health endpoints:** < 100ms
- **API endpoints:** < 500ms (p95), < 1s (p99)
- **Price calculations:** < 200ms
- **Card image processing:** < 5 seconds

**Resource Usage:**
- **Memory:** Services should stay under 512MB RSS in normal operation
- **CPU:** Efficient algorithms, avoid O(n²) where possible
- **Database:** Proper indexes on frequently queried fields, use projections
- **Caching:** Cache frequently accessed card data (TTL: 5 minutes)

**Optimization Checklist:**
- [ ] Database queries use appropriate indexes
- [ ] No N+1 query problems
- [ ] Async/await used for I/O operations
- [ ] Large collections paginated
- [ ] Expensive operations cached

### Documentation Requirements

Significant changes must be documented:

**Code Documentation:**
- All public classes and methods must have XML documentation
- Complex algorithms should have comments explaining the "why"
- Configuration options documented in appsettings.json comments

**Architecture Decisions:**
Add ADR (Architecture Decision Record) to `/docs/architecture/` for:
- New technology adoption
- Significant design pattern changes
- Major infrastructure changes

**Breaking Changes:**
Must document in:
1. PR description (clear migration guide)
2. `CHANGELOG.md` (when created)
3. Team Slack channel (#engineering)
4. Possibly email to affected team members

**Runbooks:**
Add to `/docs/runbooks/` for:
- New deployment procedures
- Troubleshooting guides
- Recovery procedures

### Continuous Improvement

We continuously refine our process:

**Sprint Retrospectives:**
- Discuss what's working and what's not
- Adjust branch protection rules if needed
- Update these guidelines based on learnings

**Metrics We Track:**
- PR review time (target: < 24 hours)
- Branch lifetime (target: < 2 days)
- Build failure rate (target: < 5%)
- Time from commit to production (target: < 1 day)

**Process Evolution:**
This document will evolve. Suggest improvements via PR to this file!

## License

By contributing to Dexie, you agree that your contributions will be licensed under the project's license (to be determined). We appreciate your contributions and commitment to the UK Pokémon community!

---

**Thank you for contributing to Dexie!** 🎉

Questions? Check the [README.md](README.md) or reach out in #engineering.
