## Steps to Create a GitHub Personal Access Token (Classic or Fine-Grained)

### **Option 1: Classic Token (for general access)**

1. **Log in to GitHub**

   * Go to [https://github.com](https://github.com) and log in.

2. **Navigate to Developer Settings**

   * Click your profile photo (top-right) → **Settings**
   * Scroll down in the left menu → **Developer settings**

3. **Go to Personal Access Tokens**

   * Click **Personal access tokens** → **Tokens (classic)**

4. **Generate New Token**

   * Click **Generate new token** → **Generate new token (classic)**

5. **Token Details**

   * **Note**: Give your token a name (e.g., `repo-access-token`)
   * **Expiration**: Choose expiration (recommended: 90 days or custom)
   * **Select Scopes** (Permissions):

     * `repo` – Full control of private repositories (includes read/write access to code, issues, pull requests)
     * Or just `repo:read` if you only need read access

6. **Generate Token**

   * Click **Generate token** at the bottom

7. **Copy and Save the Token**

   * IMPORTANT: This token will only be shown once. **Copy and store it securely**

---