<table align="center"><tr><td align="center" width="9999">
<img src="https://github.com/user-attachments/assets/95afed09-0e3d-4cc6-8759-1ae5a42ed603" align="center" style="width:50%; height:auto;">

## Open Threat Informed Detection Engineering

</td></tr></table>

OpenTide is the open source reference for starting and advanced Detection Engineering team looking to adopt Detection-as-Code, Threat & Detection Modelling, and modern DevOps workflows.

### Quick Start

Get your private **Tide** repository running on your **Github** repository or **GitLab** instance in just a few steps.

---

#### Github quick-start

Under OpenTideHQ project, we have prepared a template [InitTide](https://github.com/OpenTideHQ/InitTide).
 - Simply use this template to create a new repository on your Github space.
 - Provide the information and after a few seconds, the repository is created
 - You can now add OpenTide objects, either using objects available on ShareTide (TLP:CLEAR) or shared via MISP or you could create new objects using the templates available in the schema.

#### Self-managed Gitlab instance
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

3. In GitLab, select **Repository by URL** and paste the information for your new project  
   <img width="646" height="469" alt="image" src="https://github.com/user-attachments/assets/1baa0422-829f-45c9-9aec-4894181b4db8" />

4. Click **Create Project**.  
   After a few seconds, you’ll see the full **Tide repository structure**:  
   <img width="641" height="451" alt="image" src="https://github.com/user-attachments/assets/6d5c0308-1d64-4e90-81dc-d9de5237c887" />

5. *(Optional)* Delete all other branches and keep only `main`.

---

##### ⚙️ Initial Configuration

1. If not already done, [add an SSH key](https://docs.gitlab.com/ee/user/ssh.html#add-an-ssh-key-to-your-gitlab-account) to your GitLab profile.  
   *Git over SSH is the recommended method.*

2. On the `main` branch, open the file **`.gitlab-ci.yml`** to review or adjust the CI/CD configuration.

---

##### 🔐 Configure SSH Keys for CI/CD

Tide uses SSH for authentication and commit signing.  
You’ll need to create and configure a dedicated key pair for your GitLab Runner.

1. **Generate a key pair**  
   ```bash
   ssh-keygen -t ed25519 -C "tide-runner"
   ```
   This produces two files, typically:
   - `id_ed25519` (private key)
   - `id_ed25519.pub` (public key)

2. **Add CI variables** to your GitLab project **Settings → CI/CD → Variables → CI/CD Variables → Add** 
   - `TIDE_RUNNER_SSH_PRIVATE_KEY` → contents of the private key  
   *(Visible, untick both flags `Protect variable` & `Expand variable reference`)*
   - `TIDE_RUNNER_SSH_PUBLIC_KEY` → contents of the public key  
     *(Visible, untick both flags `Protect variable` & `Expand variable reference`)*

3. **Authorize the key pair**  
   You have two options:
   - **Deploy Token method**  
     Add the private key under  
     *Settings → Repository → Deploy Tokens*  
     *(Repeat in the local CoreTIDE project if you’re using one.)*
   - **Programmatic user method** 
     - Create a dedicated GitLab user for automation.
       * Recommended settings:
           * Username: `gitlab-runner`
           * Name: “OpenTide GitLab Runner”
           * Email: `gitlab-runner@example.com (or internal)`
           * Access level: **Regular user (not admin)**
           * Optional: Disable sign-in with password if you’ll only use SSH.
     - Add the **public** key under **User Settings → SSH Keys** 
     - Grant the user access to your Tide project (and CoreTIDE if local).

> 💡 These keys are also used for commit signing, ensuring pipeline commits are trusted and verifiable.

---

##### 🌐 Configure CoreTIDE Access

**CoreTIDE** is the foundational framework Tide builds upon.  
By default, it’s fetched dynamically from the **public repository**:

```
https://code.europa.eu/ec-digit-s2/opentide/coretide
```

Your GitLab Runner must have **network access** to this URL to fetch the latest version.

If you’re in an **air-gapped environment** or developing custom extensions, you can host **CoreTIDE locally** in your instance instead.

1. Clone or mirror the CoreTIDE repository:
   ```bash
   git clone git@github.com:OpenTideHQ/CoreTide.git
   ```
   or use GitLab’s [Repository Push Mirroring](https://docs.gitlab.com/ee/user/project/repository/mirror/push.html) to keep your local copy in sync with upstream.

2. In `.gitlab-ci.yml`, adjust these variables:
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
> This keeps your local CoreTIDE aligned with upstream while allowing safe customization.

---

##### ✅ Next Steps

- Verify that your GitLab Runner can connect to your repository via SSH.  
- Trigger a test pipeline to confirm CoreTIDE integration.  
- You’re now ready to start building with **Tide**!


#### Resources
- [OpenTide Whitepaper](https://github.com/OpenTideHQ/.github/blob/main/profile/OpenTide%20White%20Paper.pdf)
- [Introducing CoreTide FIRST TC Amsterdam 2024](https://www.first.org/resources/papers/amsterdam24/Benson-Housmann-Seguy-CoreTIDE-FIRST-TC-Amsterdam-2024.pdf)

All projects released within OpenTide is Open Sourced under the [European Union Public License (EUPL) 1.2](https://eupl.eu/1.2/en/).
