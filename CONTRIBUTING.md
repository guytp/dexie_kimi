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

## License

By contributing to Dexie, you agree that your contributions will be licensed under the project's license (to be determined). We appreciate your contributions and commitment to the UK Pokémon community!

---

**Thank you for contributing to Dexie!** 🎉

Questions? Check the [README.md](README.md) or reach out in #engineering.
