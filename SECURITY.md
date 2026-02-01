# 🔒 Security Notice - Critical Action Required

## ⚠️ URGENT: Exposed API Keys in Git History

This repository previously contained exposed Tavily API keys that have been removed from the current files. However, **the keys are still accessible in the git commit history** and must be addressed immediately.

### 🚨 Immediate Actions Required

1. **Revoke the Exposed API Keys Immediately**
   - The following Tavily API keys were exposed and MUST be revoked/rotated:
     - `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`
     - `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`
   - Log into your [Tavily Dashboard](https://tavily.com) and revoke these keys
   - Generate new API keys to replace them

2. **Clean Git History**
   - The exposed keys exist in commit `a28793e` and possibly earlier commits
   - See the "Cleaning Git History" section below for detailed instructions

### 📋 How to Clean Git History

Anyone with access to this repository can still see the exposed API keys by running:
```bash
git show a28793e:"Tool_ Travily API.json"
```

To permanently remove sensitive data from git history, you have two main options:

#### Option 1: Using git-filter-repo (Recommended)

1. **Install git-filter-repo:**
   ```bash
   pip install git-filter-repo
   ```

2. **Backup your repository:**
   ```bash
   git clone https://github.com/RobinMillford/My_n8n_workflows.git backup
   ```

3. **Clean the file from history:**
   ```bash
   cd My_n8n_workflows
   git filter-repo --path "Tool_ Travily API.json" --invert-paths
   ```
   
   Or to replace the content in history:
   ```bash
   git filter-repo --replace-text <(echo "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==>YOUR_TAVILY_API_KEY_HERE
   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==>YOUR_TAVILY_API_KEY_HERE")
   ```

4. **Force push the cleaned history:**
   ```bash
   git push origin --force --all
   git push origin --force --tags
   ```

#### Option 2: Using BFG Repo Cleaner

1. **Download BFG:**
   ```bash
   wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar
   ```

2. **Backup your repository:**
   ```bash
   git clone --mirror https://github.com/RobinMillford/My_n8n_workflows.git
   ```

3. **Create a file with strings to replace:**
   ```bash
   echo "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" > passwords.txt
   echo "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" >> passwords.txt
   ```

4. **Run BFG:**
   ```bash
   java -jar bfg-1.14.0.jar --replace-text passwords.txt My_n8n_workflows.git
   ```

5. **Clean up and push:**
   ```bash
   cd My_n8n_workflows.git
   git reflog expire --expire=now --all
   git gc --prune=now --aggressive
   git push --force
   ```

### ⚠️ Important Notes After Cleaning History

- **All collaborators** must delete their local clones and re-clone the repository
- Any forks of this repository will still contain the exposed keys
- GitHub may cache the old commits for a while - contact GitHub support if needed
- Consider making the repository private temporarily during the cleanup process

### 🔐 Best Practices Going Forward

1. **Never commit API keys, tokens, or secrets** to git repositories
2. Use **environment variables** or **secret management tools** for sensitive data
3. Add sensitive file patterns to `.gitignore` before committing
4. Use **pre-commit hooks** to scan for secrets (e.g., `detect-secrets`, `git-secrets`)
5. For n8n workflows, always use credential nodes rather than hardcoded values

### 📚 Additional Resources

- [GitHub: Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [git-filter-repo Documentation](https://github.com/newren/git-filter-repo)
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [Tavily API Documentation](https://docs.tavily.com)

### 🆘 Need Help?

If you need assistance with cleaning the git history or have questions about this security issue, please:
1. Contact the repository owner
2. Review GitHub's documentation on removing sensitive data
3. Consider consulting with a security professional if handling sensitive production systems

---

**Last Updated:** 2026-02-01  
**Severity:** Critical  
**Status:** Remediation in Progress
