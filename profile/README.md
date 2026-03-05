<table align="center"><tr><td align="center" width="9999">
<img src="https://github.com/user-attachments/assets/95afed09-0e3d-4cc6-8759-1ae5a42ed603" align="center" style="width:50%; height:auto;">

## Open Threat-Informed Detection Engineering

</td></tr></table>

OpenTide is the open-source reference for Detection Engineering teams — whether just starting out or already advanced — looking to adopt Detection-as-Code, Threat & Detection Modelling, and modern DevOps workflows.

### MyTIDE Quick Start

Get your private **Tide** repository running on your **GitHub** or **GitLab** instance in just a few steps.

The step-by-step instructions below use GitHub to help you quickly test the creation or import of models from [ShareTIDE]() and get hands-on experience with the framework.

Dedicated guidelines are also available for GitLab. The **main advantage of using GitLab** is that documentation (Markdown wiki pages) is created and maintained automatically by the pipelines — on each merge for TVMs and DOMs, and on every commit for MDRs.

---

### GitHub Quick Start for MyTIDE

#### Minimum steps to work with TVMs and DOMs

Under the OpenTideHQ project, we have prepared a template repository: [InitTide](https://github.com/OpenTideHQ/InitTide).

- Use this template to create a new repository in your GitHub space.
- Fill in the required information and, after a few seconds, your repository will be ready.
- You can now add OpenTide objects — either by using objects available on ShareTide (TLP:CLEAR), objects shared via MISP, or by creating new objects using the templates provided in the schema.

> You can safely ignore any error messages shown under Actions immediately after the repository is created. From the first commit onwards, the pipelines will begin validating YAML files against the schema.

#### Additional configuration to deploy MDRs

### Self-Managed GitLab Instance

##### 🧩 Prepare your GitLab instance

Before starting, ensure you have:

- A running **GitLab instance**
- A **registered GitLab Runner**
- The ability to **create a repository by URL**
  *(Settings → General → Import Sources → Repository by URL)*

Create a new group — for example, **OpenTIDE** — to host your repositories.

---

##### 📥 Import the GitHub template *InitTide*

1. Go to **New Project → Import Project**
   <img width="1260" height="66" alt="image" src="https://github.com/user-attachments/assets/49d3bd07-7d4a-436f-a60a-47d0f52c610b" />

2. On [InitTide](https://github.com/OpenTideHQ/InitTide), copy the **repository clone URL**
   <img width="429" height="167" alt="image" src="https://github.com/user-attachments/assets/a8af2f93-941b-4e75-933e-17c3b64f43a7" />

3. In GitLab, select **Repository by URL** and paste the details for your new project
   <img width="646" height="469" alt="image" src="https://github.com/user-attachments/assets/1baa0422-829f-45c9-9aee-4894181b4db8" />

4. Click **Create Project**.
   After a few seconds, you will see the full **Tide repository structure**:
   <img width="641" height="451" alt="image" src="https://github.com/user-attachments/assets/6d5c0308-1d64-4e90-81dc-d9de5237c887" />

5. *(Optional)* Delete all other branches and keep only `main`.

---

##### ⚙️ Initial Configuration

1. If you have not already done so, [add an SSH key](https://docs.gitlab.com/ee/user/ssh.html#add-an-ssh-key-to-your-gitlab-account) to your GitLab profile.
   *Git over SSH is the recommended method.*

2. On the `main` branch, open the **`.gitlab-ci.yml`** file to review or adjust the CI/CD configuration.

---

##### 🔐 Configure SSH Keys for CI/CD

Tide uses SSH for authentication and commit signing. You will need to create and configure a dedicated key pair for your GitLab Runner.

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
       * Recommended settings:
           * Username: `gitlab-runner`
           * Name: `OpenTide GitLab Runner`
           * Email: `gitlab-runner@example.com` (or internal)
           * Access level: **Regular user (not admin)**
           * Optional: Disable password sign-in if you will only use SSH.
     - Add the **public** key under **User Settings → SSH Keys**
     - Grant the user access to your Tide project (and CoreTIDE if hosted locally).

> 💡 These keys are also used for commit signing, ensuring that pipeline commits are trusted and verifiable.

---

##### 🌐 Configure CoreTIDE Access

**CoreTIDE** is the foundational framework upon which Tide is built.
By default, it is fetched dynamically from the **public repository**:

```
https://code.europa.eu/ec-digit-s2/opentide/coretide
```

Your GitLab Runner must have **network access** to this URL in order to fetch the latest version.

If you are working in an **air-gapped environment**, or developing custom extensions, you can host **CoreTIDE locally** within your own instance instead.

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

##### ✅ Next Steps

- Verify that your GitLab Runner can connect to your repository via SSH.
- Trigger a test pipeline to confirm CoreTIDE integration.
- You are now ready to start building with **Tide**!

#### Resources
- [OpenTide Whitepaper](https://github.com/OpenTideHQ/.github/blob/main/profile/OpenTide%20White%20Paper.pdf)
- [Introducing CoreTide — FIRST TC Amsterdam 2024](https://www.first.org/resources/papers/amsterdam24/Benson-Housmann-Seguy-CoreTIDE-FIRST-TC-Amsterdam-2024.pdf)

All projects released under OpenTide are open-sourced under the [European Union Public Licence (EUPL) 1.2](https://eupl.eu/1.2/en/).
