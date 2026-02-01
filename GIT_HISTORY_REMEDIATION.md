# Git History Remediation Guide

## Problem Statement

API keys were committed to this repository and then removed in a later commit. However, **removing files in a new commit does NOT remove them from git history**. Anyone can still access the old commits and retrieve the exposed secrets.

## Why This Matters

Git stores the complete history of all changes. When you commit a file containing secrets and later delete it, the secrets remain accessible through:
- Direct commit access: `git show <commit-hash>:<file-path>`
- Git history commands: `git log`, `git checkout <old-commit>`
- GitHub web interface: browsing commit history
- Cloned repositories: any existing clones have the full history

## Verification

To verify that secrets are still in history, run:

```bash
# Check if the file exists in an old commit
git show a28793e:"Tool_ Travily API.json"

# Search all history for a specific string
git log -S "tvly-dev-" --source --all

# List all commits that touched the file
git log --all --full-history -- "Tool_ Travily API.json"
```

## Complete Remediation Steps

### Phase 1: Revoke Compromised Credentials (IMMEDIATE)

1. **Log into Tavily Dashboard**
   - Navigate to https://tavily.com
   - Go to API Keys section
   - Revoke both exposed keys immediately

2. **Generate New Keys**
   - Create new API keys in Tavily dashboard
   - Store them securely (password manager, secrets vault)
   - Update your local n8n instance with new keys

3. **Audit for Unauthorized Use**
   - Check Tavily API usage logs if available
   - Look for any suspicious activity with the old keys
   - Document any unauthorized access

### Phase 2: Clean Git History

#### Prerequisites
- **Backup**: Create a complete backup before proceeding
- **Coordinate**: Notify all collaborators about the upcoming changes
- **Timing**: Plan for some downtime if this is a production workflow

#### Method 1: git-filter-repo (Recommended for most cases)

```bash
# 1. Install git-filter-repo
pip3 install git-filter-repo

# 2. Clone a fresh copy
git clone https://github.com/RobinMillford/My_n8n_workflows.git
cd My_n8n_workflows

# 3. Create backup
cd ..
cp -r My_n8n_workflows My_n8n_workflows_backup

# 4. Clean the repository
cd My_n8n_workflows

# Option A: Replace sensitive strings throughout history
git filter-repo --replace-text <(cat <<EOF
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==>***REMOVED***
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==>***REMOVED***
EOF
)

# Option B: Remove the file entirely from history (if preferred)
# git filter-repo --path "Tool_ Travily API.json" --invert-paths

# 5. Verify the cleaning worked
git log --all -S "tvly-dev-" --source
# Should return no results

# 6. Re-add the remote (filter-repo removes it)
git remote add origin https://github.com/RobinMillford/My_n8n_workflows.git

# 7. Force push to update remote
git push origin --force --all
git push origin --force --tags
```

#### Method 2: BFG Repo-Cleaner (Faster for large repos)

```bash
# 1. Download BFG
wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar

# 2. Create a passwords file
cat > passwords.txt <<EOF
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
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
EOF

# 3. Clone as a mirror
git clone --mirror https://github.com/RobinMillford/My_n8n_workflows.git

# 4. Run BFG
java -jar bfg-1.14.0.jar --replace-text passwords.txt My_n8n_workflows.git

# 5. Clean up
cd My_n8n_workflows.git
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# 6. Push changes
git push --force

# 7. Clone normally for continued work
cd ..
git clone https://github.com/RobinMillford/My_n8n_workflows.git
```

### Phase 3: Post-Cleanup Actions

1. **Notify All Collaborators**
   ```
   Subject: URGENT - Repository history rewritten

   The repository history has been rewritten to remove exposed API keys.
   
   Required actions:
   1. Delete your local clone
   2. Re-clone the repository
   3. Do NOT push old branches
   
   Commands:
   cd /path/to/old/clone
   cd ..
   rm -rf My_n8n_workflows
   git clone https://github.com/RobinMillford/My_n8n_workflows.git
   ```

2. **Handle Forks**
   - Contact fork owners to delete their forks
   - Forks will still contain the old history
   - Consider making the repo private temporarily

3. **GitHub Cache**
   - GitHub may cache commits for a while
   - Contact GitHub Support to request cache invalidation
   - Cached commits: https://github.com/RobinMillford/My_n8n_workflows/commit/a28793e

4. **Update Documentation**
   - ✅ SECURITY.md created with remediation steps
   - ✅ README.md updated with security notice
   - ✅ .gitignore added to prevent future issues

### Phase 4: Prevent Future Incidents

1. **Install Pre-commit Hooks**
   ```bash
   # Install detect-secrets
   pip install detect-secrets

   # Set up pre-commit hook
   cat > .git/hooks/pre-commit <<'EOF'
   #!/bin/bash
   detect-secrets-hook --baseline .secrets.baseline
   EOF
   chmod +x .git/hooks/pre-commit

   # Create baseline
   detect-secrets scan > .secrets.baseline
   ```

2. **Use Environment Variables**
   - Never hardcode credentials in workflow files
   - Use n8n's credential management system
   - Store sensitive values in environment variables

3. **Regular Security Audits**
   - Review commits before pushing
   - Use tools like `git-secrets` or `truffleHog`
   - Enable GitHub secret scanning alerts

## Verification After Cleanup

```bash
# 1. Check that secrets are gone from history
git log --all -S "tvly-dev-" --source
# Should return no results

# 2. Verify current files are clean
grep -r "tvly-dev-" .
# Should only match SECURITY.md or documentation

# 3. Check specific old commit is cleaned
git show a28793e:"Tool_ Travily API.json" 2>&1
# Should fail or show cleaned version

# 4. Verify repository size decreased
git count-objects -vH
```

## Troubleshooting

### Error: "refusing to update checked out branch"
- You're in a checked-out repository
- Use a bare/mirror clone for BFG

### Error: "remote contains work that you do not have"
- Expected after rewriting history
- Use `--force` flag (carefully!)

### Collaborators can still push old commits
- They must delete and re-clone
- Enable branch protection after cleanup

### Forks still have secrets
- Contact fork owners
- No automated way to clean forks
- Consider DMCA takedown if necessary

## Additional Resources

- [GitHub: Removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [git-filter-repo](https://github.com/newren/git-filter-repo/)
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [detect-secrets](https://github.com/Yelp/detect-secrets)
- [git-secrets](https://github.com/awslabs/git-secrets)

## Questions?

If you encounter issues during remediation:
1. Restore from backup
2. Review the documentation links above
3. Seek help from security professionals if needed
4. Don't skip steps - incomplete cleanup leaves you vulnerable

---

**Remember**: This is a critical security issue. Take it seriously and follow all steps carefully.
