# /code-review:review-pr - Pull Request Review

Comprehensive pull request review using all specialized agents. Posts only high-confidence, high-value inline comments directly on PR lines - no overall review report.

- Purpose - Review PR changes before merge with minimal noise
- Output - Inline comments on specific lines (only issues that pass confidence/impact thresholds)

```bash
/code-review:review-pr ["PR number or review-aspects"]
```

## CI/CD Integration

You can intergreate this plugin with your CI/CD pipeline by using Offical Anthropics Claude Code Action. See [CI/CD Integration](../../guides/ci-integration.md) for more details.

## Arguments

PR number (e.g., #123, 123) and/or review aspects to focus on

## How It Works

1. **PR Context Loading**: Fetches PR details and diff
   - Changed files
   - Commit messages
   - PR description
   - Base branch context

2. **Parallel Agent Analysis**: Same six agents analyze the PR diff
   - Each agent examines changes from their specialty perspective
   - Considers PR context and commit messages

3. **Confidence & Impact Scoring**: Each issue is scored on two dimensions
   - **Confidence (0-100)**: How likely is this a real issue vs false positive?
   - **Impact (0-100)**: How severe is the consequence if left unfixed?
   - Progressive threshold: Critical issues (81-100 impact) need 50% confidence, Low issues (0-20 impact) need 95% confidence

4. **Inline Comment Posting**: Only issues passing thresholds get posted
   - Uses GitHub inline comments on specific PR lines
   - Low impact issues (0-20) are never posted, even with high confidence


## Usage Examples

```bash
# Review PR by number
> /code-review:review-pr #123

# Review PR with focus on security
> /code-review:review-pr #123 security

# Review current branch's PR
> /code-review:review-pr
```


