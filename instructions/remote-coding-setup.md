# Remote Coding Setup

This guide will show you how to set up your main computer to work with your `homelab` server.

Create an ssh key for your `homelab` server:
`ssh-keygen -t ed25519 -C "your@email.com"`

---

## 1. Update your SSH Config (Local Machine)

First, let's make sure your computer knows exactly who `homelab` is. Open your config:
`nano ~/.ssh/config`

Update or add the following block:

```text
Host homelab
    HostName <your-static-ip>  # Static IP of your homelab server
    User <your-ubuntu-username>
    IdentityFile ~/.ssh/id_ed25519_hl
    # Port Forwarding: Maps server port 3000 to your local machine
    LocalForward 3000 127.0.0.1:3000
    LocalForward 8000 127.0.0.1:8000

```

_Note: I added port 8000 as well, as you'll likely use it for FastAPI._

Copy the public key to your `homelab` server:
`ssh-copy-id -i ~/.ssh/id_ed25519_hl homelab`

_Note: You can use any name you want for your key, but I recommend using the same name as your server._

---

## 2. Mount the Files (The SSHFS Method)

This allows your local Neovim to "see" the remote files as if they were on your own hard drive.

1. **Create the local folder:**
   `mkdir -p ~/remote-projects/homelab`
2. **Mount it:**
   `sshfs homelab:/home/<your-username>/ ~/remote-projects/homelab`

**Now, you can simply run:**
`nvim ~/remote-projects/homelab/your-project-folder`
All your local Neovim themes, custom keybinds, and snippets will work perfectly.

---

## 3. The Web Preview Workflow

Since the code is running on your Ubuntu server but you are looking at it on your main computer:

1. **Start the server:** Open a terminal, `ssh homelab`, and start your app (e.g., `npm run dev`).
2. **View it:** Open your browser on your main computer and go to `http://localhost:3000`.
3. **Why it works:** The `LocalForward` in your config creates a secure "pipe" for that specific web traffic.

---

## 4. Pro-Level Cloud Setup: The "Sync" Plugin

If you find SSHFS a bit slow (since it fetches data over the network every time you move the cursor), you can use the **remote-nvim.nvim** plugin with your `homelab` host.

In your local Neovim, run:
`:RemoteStart`
Select **homelab** from the list. The plugin will detect your `~/.ssh/config` settings and use your `id_ed25519_hl` key automatically.

---

## Summary Checklist

| Action                  | Command                                      |
| ----------------------- | -------------------------------------------- |
| **Connect to Terminal** | `ssh homelab`                                |
| **Mount Filesystem**    | `sshfs homelab:~/ ~/remote-projects/homelab` |
| **Open Code**           | `nvim ~/remote-projects/homelab`             |
| **Preview Website**     | Open `localhost:3000` in your browser        |

### A Quick Tip for a Beginner

If you reboot your main computer, the "Mount" will disappear. You'll need to run the `sshfs` command again.

Would you like me to write a tiny **alias** for your `.bashrc` or `.zshrc` so you can just type `mount-lab` to reconnect everything instantly?
