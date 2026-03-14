# Contributing to M365 Security Baseline

Thanks for your interest in improving this baseline. Security is a moving target, and community input keeps this guide current.

## How to Contribute

### Adding or Updating Controls

1. Fork the repository
2. Find the appropriate section in README.md
3. Add your control following the existing format
4. Submit a pull request

### Control Format

Every control must follow this format:

```markdown
- [ ] **[Critical|High|Medium|Low]** Description of what to do and why it matters.
  [Microsoft Docs - Relevant Topic](https://learn.microsoft.com/...)
```

### Severity Guidelines

| Level | Use When |
|-------|----------|
| **Critical** | Direct path to account compromise or data exposure |
| **High** | Significant security gap, should be fixed soon |
| **Medium** | Defense-in-depth, reduces attack surface |
| **Low** | Good hygiene, implement when you can |

### What We Accept

- Controls based on CIS Benchmarks, Microsoft security baselines, or real-world hardening experience
- Updated Microsoft documentation links
- Corrections to existing controls
- New service areas with at least 5 controls

### What We Won't Merge

- Vendor-specific product recommendations
- Controls without Microsoft documentation references
- Duplicate controls already covered

## Questions?

Open an issue or reach out to the maintainers.
