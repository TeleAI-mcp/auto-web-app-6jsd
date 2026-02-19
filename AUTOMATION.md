# Label-Based Comment Automation

This repository implements an automated issue comment system that responds to specific labels with predefined messages.

## How It Works

When an issue is labeled with specific keywords, the system automatically posts a corresponding comment:

### Current Label Mappings

| Label | Automated Comment |
|-------|------------------|
| `bug` | "Thank you for reporting this bug. Our team will investigate and fix it." |
| `completed` | "This issue has been resolved. Thank you for your contribution!" |
| `feature` | "Thank you for the feature request. We will consider including this in a future release." |

## Adding New Labels

To add a new automated response:

1. **Create the label** in the repository settings
2. **Add the mapping** to this documentation
3. **Update the automation logic** (see implementation details below)

## Implementation Details

The automation can be implemented through:

### Option 1: GitHub Actions Workflow

Create `.github/workflows/label-automation.yml`:

```yaml
name: Label Automation
on:
  issues:
    types: [labeled]

jobs:
  auto-comment:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            const label = context.payload.label.name;
            const issueNumber = context.issue.number;
            
            const responses = {
              'bug': 'Thank you for reporting this bug. Our team will investigate and fix it.',
              'completed': 'This issue has been resolved. Thank you for your contribution!',
              'feature': 'Thank you for the feature request. We will consider including this in a future release.'
            };
            
            if (responses[label]) {
              await github.rest.issues.createComment({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: issueNumber,
                body: responses[label]
              });
            }
```

### Option 2: Webhook Integration

For more complex automation, consider using a webhook service that:
1. Listens for `issues.labeled` events
2. Matches the label name to predefined responses
3. Posts the appropriate comment via GitHub API

## Maintenance

- **Review responses quarterly** to ensure they remain relevant
- **Test new labels** before deploying to production
- **Monitor issue comments** to ensure automation is working correctly
- **Update this documentation** whenever changes are made

## Testing

The following test issues demonstrate the automation:

- Issue #1: "Issue: mobile layout" (label: `bug`) ✅
- Issue #2: "Issue: login flow" (label: `completed`) ✅  
- Issue #3: "Issue: dark mode" (label: `feature`) ✅

## Troubleshooting

If automation isn't working:
1. Check GitHub Actions logs for errors
2. Verify label names match exactly (case-sensitive)
3. Ensure repository secrets/permissions are properly configured
4. Test with a new issue to rule out caching issues

---

Last updated: 2026-02-19
Maintained by: Development Team