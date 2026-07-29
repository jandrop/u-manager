# U-Manager - How To Guide

## Table of Contents

- [Before You Start: Keep the Unraid API Updated](#before-you-start-keep-the-unraid-api-updated)
- [Creating an API Key](#creating-an-api-key)
- [Troubleshooting](#troubleshooting)
- [Connection Settings](#connection-settings)
- [Custom Headers (Reverse Proxy)](#custom-headers-reverse-proxy)
- [File Browser Setup](#file-browser-setup)
- [Push Notifications Setup](#push-notifications-setup)
- [Companion Plugin (Optional)](#companion-plugin-optional)
- [Additional Resources](#additional-resources)
- [Discord server](#discord-server)

---

## Before You Start: Keep the Unraid API Updated

> **This is the single most important thing for using U-Manager.** Keep your Unraid API on the latest stable version.

U-Manager talks to the GraphQL API that ships with Unraid. Older API versions have bugs and missing fields that make parts of the app misbehave, and the **U-Manager Companion** plugin also needs a recent API to apply its patches correctly.

The easiest way to keep the API up to date is the official **Unraid Connect** plugin, which updates the Unraid API to the latest stable version for you:

1. Open your Unraid WebGUI and go to the **Apps** tab (Community Apps).
2. Search for **Unraid Connect** and install it.
3. Once installed, it keeps your Unraid API updated to the latest stable release.

> You do not need to sign in to an Unraid Connect account to benefit from the updated API. Installing the plugin is enough to get the latest API version.

---

## Creating an API Key

To connect U-Manager to your Unraid server, you need to create an API key.

### Steps

1. Open your Unraid WebGUI in a browser
2. Navigate to **Settings → Management Access → API Keys**
3. Click **Create API Key**
4. Configure the key:
    - **API Key NAME**: Enter a name to identify this key (e.g., "UManager")
    - **Description**: Optional description for reference
    - **Roles**: Select **admin** (recommended)
5. Click **Create**

6. Copy the generated API key

> **Recommendation**: Use the **admin** role to avoid permission issues. The app needs access to various system information and using a restricted role may cause features to not work properly.

> **Important**: Keep your API key secure. Treat it like a password.

### Alternative: Command Line

You can also generate a key via SSH:

```bash
unraid-api apikey --create
```

---

## Troubleshooting

### Important Notice

U-Manager is a client application that consumes the Unraid API.  
If the API returns incorrect data or fails, the app will reflect that behavior.
Issues caused by the Unraid API itself should be reported to Unraid.

### First Step (Important): Restart the API

In many cases, API key or connection issues are resolved by restarting the Unraid API service.

1. Open your Unraid WebGUI
2. Go to **Settings → Management Access**
3. Find **Unraid API Status**
4. Click **RESTART API**
5. Wait for the API to restart
6. Try using U-Manager again

If the problem is resolved after restarting the API, the issue was most likely related to the Unraid API and not the app itself.

### Disks Wake Up When Opening the App

This is a bug in the Unraid API itself, not in U-Manager. When the app reads disk information, the official `unraid-api` runs a full SMART read (`smartctl --xall`) on every disk without the `-n standby` flag, which forces idle drives to spin up and leave standby. The app only asked for disk identifiers, but the API wakes the platters anyway.

It is being tracked upstream here: [unraid/api#2018](https://github.com/unraid/api/issues/2018).

This is not an in-app setting. Until the fix reaches the official API, you can fix it on the server side today by installing the **U-Manager Companion** plugin, which patches your local Unraid API so disk reads no longer wake spun-down disks while the app is open. The companion reads disk identity from the system and only consults SMART for drives that are already spinning, matching what Unraid's own web UI does.

> Make sure your Unraid API is on the latest stable version first, otherwise the companion may not patch correctly. See [Before You Start: Keep the Unraid API Updated](#before-you-start-keep-the-unraid-api-updated).

To install it, follow the steps in [Companion Plugin (Optional)](#companion-plugin-optional).

### Known Unraid API Issues

Before reporting a bug, check if your issue is a known problem with the Unraid API:

- **Unraid API Issues**: [https://github.com/unraid/api/issues](https://github.com/unraid/api/issues)

Many issues that appear to be app problems are actually API bugs that need to be reported to Unraid directly.

### Reporting U-Manager Bugs

If after restarting the API the problem persists, and you've verified it's not a known Unraid API issue, please open a bug report:

1. Check existing issues first: [https://github.com/jandrop/u-manager/issues](https://github.com/jandrop/u-manager/issues)
2. If your issue doesn't exist, create a new one with:
    - Description of the problem
    - Steps to reproduce
    - Your Unraid version
    - Screenshots if applicable

---

## Connection Settings

### Host

Enter your Unraid server's IP address or hostname:
- Example: `http://192.168.1.100` or `https://192.168.1.100`

---

## Custom Headers (Reverse Proxy)

If you access your Unraid server through a reverse proxy with authentication (like Cloudflare Access, Authelia, or Authentik), you may need to add custom headers to authenticate with the proxy.

### When Do You Need Custom Headers?

- **Cloudflare Access**: Requires `CF-Access-Client-Id` and `CF-Access-Client-Secret` headers
- **Other reverse proxies**: May require custom authentication headers

### Adding Custom Headers

1. On the login screen, tap **Custom Headers** to expand the section
2. Choose a header type:
   - **Cloudflare Client ID**: Pre-fills the `CF-Access-Client-Id` key
   - **Cloudflare Secret**: Pre-fills the `CF-Access-Client-Secret` key
   - **Custom**: Enter any custom header key
3. Enter the header value
4. Tap **Add** to add the header
5. Repeat for additional headers if needed
6. Complete the login with your server URL and API key

### Cloudflare Access Setup

To use Cloudflare Access with U-Manager:

1. In Cloudflare Zero Trust dashboard, go to **Access → Service Auth**
2. Create a new **Service Token**
3. Copy the **Client ID** and **Client Secret**
4. In U-Manager:
   - Add a **Cloudflare Client ID** header with the Client ID value
   - Add a **Cloudflare Secret** header with the Client Secret value

> **Security Note**: Custom headers are stored securely on your device alongside your API key.

### Troubleshooting

- **401 Unauthorized**: Check that your header values are correct
- **403 Forbidden**: Verify your service token has the correct permissions in Cloudflare
- **Connection timeout**: Ensure your reverse proxy is correctly forwarding requests to Unraid

---

## File Browser Setup

U-Manager can browse files on your Unraid server using the **U-Manager Files** plugin — a native file browser that installs from Community Applications and runs on your server's port `8740`, authenticated with your Unraid API key.

### Install

In the Unraid WebGUI, open **Plugins → Install Plugin** and paste:

```
https://github.com/jandrop/u-manager-files-releases/releases/latest/download/UManagerFiles.plg
```

Click **INSTALL**. The plugin starts on port `8740` with `/mnt/user` as the exposed root.

### Configure in the app

Open the **File Browser settings** in U-Manager and tap the **?** in the top bar for the full setup guide. In short: the host and port (`8740`) point at your server, and under **Authentication** you paste an Unraid API key to use the SSO same authentication as unraid does — it's recommended to create a new, dedicated key (**Settings → Management Access → API Keys → Create API Key**) so you can revoke just this access later.

### Troubleshooting

- **"File browser is not configured"** — set it up in File Browser settings.
- **Cannot connect** — confirm the U-Manager Files plugin is installed and running on the server (port `8740`).

---

## Push Notifications Setup

U-Manager can deliver Unraid notifications to your phone using the **U-Manager Notifications** plugin.

### Step 1: Install the plugin in Unraid

1. Open your Unraid WebGUI and go to **Plugins** → **Install Plugin**.
2. In the field labelled **"Enter URL of remote plugin file or local plugin file"**, paste this URL:
   ```
   https://raw.githubusercontent.com/jandrop/u-manager-unraid-plugin/main/UManager.plg
   ```
3. Click **INSTALL** and wait for the plugin to finish installing.

![Install Plugin screen in Unraid](https://raw.githubusercontent.com/jandrop/u-manager-unraid-plugin/main/docs/screenshots/install-plugin.png)

> Plugin source: [github.com/jandrop/u-manager-unraid-plugin](https://github.com/jandrop/u-manager-unraid-plugin)

### Step 2: Get your push token in U-Manager

1. Open U-Manager and go to **Settings** → **Notifications**.
2. Enable the **Push notifications** toggle.
3. Copy the token shown below the toggle.

### Step 3: Configure the plugin in Unraid

1. In Unraid, go to **Settings** → **Notification Settings**.
2. Scroll to the bottom — you will see a **UManager** section with these fields:
   - **Agent function**: set to `Enabled`.
   - **Push token**: paste the token you copied from U-Manager.
   - **Notification title**: leave as `Subject`.
   - **Notification message**: leave as `Description`.
3. Click **APPLY**.
4. Click **TEST** to send a test notification — it should arrive on your phone within a few seconds.

![UManager agent settings in Unraid Notification Settings](https://raw.githubusercontent.com/jandrop/u-manager-unraid-plugin/main/docs/screenshots/plugin-settings.png)

### Important notes

- **Token is regenerated when you toggle off and on.** If you turn push notifications OFF in U-Manager and turn them back ON, a new token is issued and the old one stops working. You must paste the new token into the plugin again.
- **Only one device at a time.** A given token only delivers to one phone. If you set up the same token on a second phone, the first phone stops receiving notifications. To use a different phone, regenerate the token there and update the plugin with it.

---

## Companion Plugin (Optional)

U-Manager talks to the official Unraid GraphQL API. A few features depend on fixes that haven't reached upstream yet — the **U-Manager Companion** Unraid plugin patches your local `unraid-api` so those features work today. Without the plugin the app still works; the affected sections just stay dark or fall back to safe defaults.

### What it enables

- **Parity check resume**: lets you resume a paused parity check from the app. The official API doesn't expose this.
- **Disks stay spun down**: works around an Unraid API bug ([unraid/api#2018](https://github.com/unraid/api/issues/2018)) that wakes idle disks whenever the app reads disk info. See [Disks Wake Up When Opening the App](#disks-wake-up-when-opening-the-app).

The other patches the companion installs are infrastructure for upcoming app features and don't change anything visible today.

### Install

1. Open your Unraid WebGUI → **Plugins** → **Install Plugin**.
2. Paste this URL:
   ```
   https://raw.githubusercontent.com/jandrop/u-manager-companion/main/UManagerCompanion.plg
   ```
3. Click **INSTALL**. The plugin re-applies its patches at every boot, so you only install it once.

> Plugin source, technical details and the upstream tracking issues: [github.com/jandrop/u-manager-companion](https://github.com/jandrop/u-manager-companion)

### Remove

In Unraid: **Plugins** → **u-manager-companion** → **Remove**. The patches stay live until the next reboot; after that, the pristine API bundle is loaded from squashfs.

---

## Additional Resources

- [Unraid API Documentation](https://docs.unraid.net/API/how-to-use-the-api)

---

## Discord Server

You can join this Discord server if you need more help: https://discord.gg/N7fxabZzXN
