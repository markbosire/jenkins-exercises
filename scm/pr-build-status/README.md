Here's a concise and clear `README.md` section for displaying a Jenkins build status badge on GitHub:

---

## Jenkins Build Status Badge

To display a Jenkins build status badge on your GitHub project, follow these steps:

1. **Install the Required Plugin**

   * Navigate to **Jenkins → Manage Jenkins → Manage Plugins**
   * Under the **Available** tab, search for and install **Embeddable Build Status Plugin**

2. **Set Permissions for Anonymous Access**

   * Go to **Manage Jenkins → Configure Global Security**
   * Ensure **"Enable security"** is checked
   * Under **Authorization**, assign **"ViewStatus"** permission to **Anonymous Users** (at either the global or project level)

3. **Get the Badge Markdown**

   * Open your specific project or branch build page in Jenkins
   * Click on **Embeddable Build Status** in the left sidebar
   * Copy the value from the **Markdown (unprotected)** section

4. **Add Badge to GitHub**

   * Paste the copied Markdown badge into your project's `README.md` on GitHub

