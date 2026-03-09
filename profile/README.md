<table align="center"><tr><td align="center" width="9999">
<img src="https://github.com/user-attachments/assets/95afed09-0e3d-4cc6-8759-1ae5a42ed603" align="center" style="width:50%; height:auto;">

## Open Threat-Informed Detection Engineering

</td></tr></table>

OpenTide is the open-source reference for Detection Engineering teams — whether just starting out or already advanced — looking to adopt Detection-as-Code, Threat & Detection Modelling, and modern DevOps workflows.

### Setting Up an OpenTide Instance

Get your own **OpenTide Instance** running in just a few steps. An OpenTide Instance is your organisation's private repository built on the [InitTide](https://github.com/OpenTideHQ/InitTide) template, where you store your OpenTide objects and configurations.

**Supported platforms:** **GitHub** (recommended), **Azure DevOps**, and **GitLab** are all supported. GitHub is the preferred platform due to its ease of setup and the availability of managed runners. Refer to the section for your chosen platform below.

> For full details on the CI/CD files shipped with InitTide, refer directly to the relevant files in the [InitTide repository](https://github.com/OpenTideHQ/InitTide): `.github/workflows/main.yml` for GitHub Actions, `.azure-pipeline.yml` for Azure DevOps, and `.gitlab-ci.yml` for GitLab CI.

---

### GitHub (Recommended)

GitHub is the simplest way to get an OpenTide Instance running. No self-hosted infrastructure is required, and pipelines are preconfigured to pull the latest CoreTide definitions automatically.

#### 🚀 Create your OpenTide Instance

1. Open [InitTide](https://github.com/OpenTideHQ/InitTide) and click **Use this template → Create a new repository**.
2. Fill in the repository name and visibility settings, then click **Create repository**.
3. After a few seconds, your OpenTide Instance will be ready. You can now add OpenTide objects — either from [ShareTide](https://github.com/OpenTideHQ/ShareTide) (TLP:CLEAR), via MISP, or by creating new objects from the schemas provided in the repository.

> You can safely ignore any pipeline error messages shown immediately after the repository is created. From the first commit onwards, the pipelines will begin validating YAML files against the schema.

#### ⚙️ CI/CD Configuration

The pipeline is defined in **`.github/workflows/main.yml`**. By default it points to the `development` branch of CoreTide on GitHub — no changes are needed to get started.

**Passing secrets to the pipeline**

If you use Detection-as-Code and need to pass API tokens or other credentials, declare them in your repository under **Settings → Secrets and variables → Actions**, then expose them to the pipeline by editing the `Secrets` job in `.github/workflows/main.yml`:

```yaml
jobs:
  Secrets:
    # ...
    steps:
      - name: Export secrets as env vars and stringify
        id: export_env
        # env:
          # SECRET: ${{ secrets.SECRET }}
```

Uncomment and add each secret you need. In your configuration files (`.toml`), prefix the variable name with `$` so the pipeline resolves it from the environment.

**Wiki / documentation**

To enable automatic documentation generation, fill in the `Documentation` job parameters in `.github/workflows/main.yml`:

```yaml
Documentation:
  with:
    wiki_enabled: true
    wiki_repo_owner: <your-org>
    wiki_repo_name: <your-wiki-repo>
    wiki_repo_branch: main
  secrets:
    wiki_token: ${{ secrets.YOUR_GITHUB_TOKEN }}
```

Create a fine-grained Personal Access Token (or a classic token) with write access to the wiki repository and store it as `YOUR_GITHUB_TOKEN` in your repository secrets.

---

### Azure DevOps

Azure DevOps is fully supported and recommended for organisations already running on the Microsoft stack.

#### 🚀 Create your OpenTide Instance

1. In Azure DevOps, create a new project (or use an existing one).
2. Import the InitTide repository: **Repos → Import repository** and provide the URL `https://github.com/OpenTideHQ/InitTide`.
3. The pipeline definition is in **`.azure-pipeline.yml`** at the root of the repository. Create a new pipeline in Azure Pipelines pointing to this file.

#### ⚙️ CI/CD Configuration

**GitHub service connection**

The pipeline fetches CoreTide pipeline definitions from GitHub. Create a service connection to GitHub under **Project Settings → Service connections** and name it **`GitHub OpenTide`** (this name is expected by the pipeline). Grant it access to the `OpenTideHQ/CoreTide` repository.

**Variable group**

Create a variable group named **`OpenTide`** under **Pipelines → Library**. Add the required variables listed at the top of `.azure-pipeline.yml`, including committer details, wiki settings, and deployment environment names.

**Deployment environments**

Create two environments in Azure Pipelines (**Pipelines → Environments**) matching the names you set in `OpenTide.Deployment.Environment.Production` and `OpenTide.Deployment.Environment.Staging`. Grant the pipeline access to each environment under **Security → Pipeline permissions**.

**Handling secrets**

When a variable in a variable group is marked as secret (value hidden), Azure Pipelines will not expose it directly as an environment variable to scripts. To work around this, use **`.azure/set_secrets.yml`** — edit that file to declare your secrets explicitly so they are broadcast to the pipeline:

```yaml
# .azure/set_secrets.yml
steps:
  - script: |
      # ...
    env:
      MY_SECRET: $(MY_SECRET)   # Reference your variable group secret here
```

Secrets exposed this way are still masked in pipeline logs.

**Local CoreTide (optional)**

By default the pipeline points to `OpenTideHQ/CoreTide` on GitHub. To use a locally mirrored copy, update the `resources.repositories.coretide` section in `.azure-pipeline.yml` to reference your own repository and adjust the `endpoint` service connection accordingly.

---

### GitLab

GitLab is supported and offers native wiki integration — documentation (Markdown wiki pages) is created and maintained automatically by the pipelines on each merge for TVMs and DOMs, and on every commit for MDRs.

#### 🧩 Prepare your GitLab instance

Before starting, ensure you have:

- A running **GitLab instance**
- A **registered GitLab Runner**
- The ability to **create a repository by URL**
  *(Settings → General → Import Sources → Repository by URL)*

Create a new group — for example, **OpenTIDE** — to host your repositories.

---

#### 📥 Import the InitTide template

1. Go to **New Project → Import Project**
   <img width="1260" height="66" alt="image" src="https://github.com/user-attachments/assets/49d3bd07-7d4a-436f-a60a-47d0f52c610b" />

2. On [InitTide](https://github.com/OpenTideHQ/InitTide), copy the **repository clone URL**
   <img width="429" height="167" alt="image" src="https://github.com/user-attachments/assets/a8af2f93-941b-4e75-933e-17c3b64f43a7" />

3. In GitLab, select **Repository by URL** and paste the details for your new project
   <img width="646" height="469" alt="image" src="https://github.com/user-attachments/assets/1baa0422-829f-45c9-9aee-4894181b4db8" />

4. Click **Create Project**.
   After a few seconds, you will see the full **OpenTide Instance repository structure**:
   <img width="641" height="451" alt="image" src="https://github.com/user-attachments/assets/6d5c0308-1d64-4e90-81dc-d9de5237c887" />

5. *(Optional)* Delete all other branches and keep only `main`.

---

#### ⚙️ Initial Configuration

1. If you have not already done so, [add an SSH key](https://docs.gitlab.com/ee/user/ssh.html#add-an-ssh-key-to-your-gitlab-account) to your GitLab profile.
   *Git over SSH is the recommended method.*

2. On the `main` branch, open **`.gitlab-ci.yml`** to review or adjust the CI/CD configuration.

---

#### 🔐 Configure SSH Keys for CI/CD

The pipeline uses SSH for authentication and commit signing. You will need to create and configure a dedicated key pair for your GitLab Runner.

1. **Generate a key pair**
   ```bash
   ssh-keygen -t ed25519 -C "tide-runner"
   ```
   This produces two files, typically:
   - `id_ed25519` (private key)
   - `id_ed25519.pub` (public key)

2. **Add CI variables** to your GitLab project under **Settings → CI/CD → Variables → Add**
   - `TIDE_RUNNER_SSH_PRIVATE_KEY` → contents of the private key
     *(Visible — untick both `Protect variable` and `Expand variable reference`)*
   - `TIDE_RUNNER_SSH_PUBLIC_KEY` → contents of the public key
     *(Visible — untick both `Protect variable` and `Expand variable reference`)*

3. **Authorise the key pair**
   You have two options:
   - **Deploy Token method**
     Add the private key under
     *Settings → Repository → Deploy Tokens*
     *(Repeat this in the local CoreTIDE project if you are using one.)*
   - **Programmatic user method**
     - Create a dedicated GitLab user for automation.
       Recommended settings:
         - Username: `gitlab-runner`
         - Name: `OpenTide GitLab Runner`
         - Email: `gitlab-runner@example.com` (or internal)
         - Access level: **Regular user (not admin)**
         - Optional: Disable password sign-in if you will only use SSH.
     - Add the **public** key under **User Settings → SSH Keys**
     - Grant the user access to your OpenTide Instance project (and CoreTIDE if hosted locally).

> 💡 These keys are also used for commit signing, ensuring that pipeline commits are trusted and verifiable.

---

#### 🌐 Configure CoreTIDE Access

**CoreTIDE** is the foundational framework upon which the OpenTide Instance is built.
By default, the `.gitlab-ci.yml` in InitTide fetches CoreTide pipeline definitions from the **public GitHub repository** (`github.com/OpenTideHQ/CoreTide`). Your runner must be able to reach `github.com`.

If you are working in an **air-gapped environment**, or developing custom extensions, you can host **CoreTIDE locally** within your own GitLab instance instead.

1. Clone or mirror the CoreTIDE repository:
   ```bash
   git clone git@github.com:OpenTideHQ/CoreTide.git
   ```
   Alternatively, use GitLab's [Repository Push Mirroring](https://docs.gitlab.com/ee/user/project/repository/mirror/push.html) to keep your local copy in sync with upstream.

2. In `.gitlab-ci.yml`, adjust the following variables:
   ```yaml
   TIDE_CORE_SETUP: LOCAL
   TIDE_CORE_REPO: CoreTide
   TIDE_CORE_ACCESS: $TIDE_CORE_LOCAL
   ```

3. Update the `include` section to reference your local repository path:
   ```yaml
   include:
     - project: 'group/coretide'
       ref: core
       file: '/Pipelines/Gitlab/tide.yml'
   ```

> 💡 Use a **separate development branch** (not `core`) for internal modifications.
> This keeps your local CoreTIDE aligned with upstream whilst allowing safe customisation.

---

#### ✅ Next Steps

- Verify that your GitLab Runner can connect to your repository via SSH.
- Trigger a test pipeline to confirm CoreTIDE integration.
- You are now ready to start building your OpenTide Instance!

---

### Resources
- [OpenTide Whitepaper](https://github.com/OpenTideHQ/.github/blob/main/profile/OpenTide%20White%20Paper.pdf)
- [Introducing CoreTide — FIRST TC Amsterdam 2024](https://www.first.org/resources/papers/amsterdam24/Benson-Housmann-Seguy-CoreTIDE-FIRST-TC-Amsterdam-2024.pdf)

All projects released under OpenTide are open-sourced under the [European Union Public Licence (EUPL) 1.2](https://eupl.eu/1.2/en/).
