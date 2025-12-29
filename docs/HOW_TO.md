# U-Manager - How To Guide

## Table of Contents

- [Creating an API Key](#creating-an-api-key)
- [Troubleshooting](#troubleshooting)
- [Connection Settings](#connection-settings)
- [Custom Headers (Reverse Proxy)](#custom-headers-reverse-proxy)
- [File Browser Setup](#file-browser-setup)
- [Additional Resources](#additional-resources)

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

U-Manager can browse files on your Unraid server using the **File Core API** plugin.

### What is File Core API?

File Core API is a lightweight file manager that runs as Docker containers on your Unraid server. It provides a web interface and API for browsing, downloading, and managing files.

### Installation (Recommended: Unraid Templates)

The easiest way to install on Unraid is using the Docker templates:

1. **Download the templates** from:
   - [File Core API Repository](https://github.com/jandrop/file_core_api_unraid)

2. **Copy the XML templates** to your Unraid templates directory:
   ```
   /boot/config/plugins/dockerMan/templates-user/
   ```

3. **Install from Unraid Docker tab**:
   - Go to **Docker → Add Container**
   - Select **file_core_api** template
   - Configure and apply
   - Repeat for **file_core_ui** template

4. **Docker Network** (Important):
   - Both containers **must be on the same Docker network** to communicate
   - By default, use `bridge` or create a custom network
   - In Unraid, set the same "Network Type" for both containers

5. **Default Ports**:
   - Web UI: `8764`
   - API: `8000`

> **Note**: Using Unraid templates is preferred over Docker Compose as it integrates better with Unraid's container management.

### Configure in U-Manager

1. Open U-Manager and go to **Settings**
2. Find the **File Browser** section
3. Enter the **Port** (default: `8764`)
4. Tap on any Share to browse its files

### Troubleshooting

- **"File browser is not configured"**: Set the port in Settings
- **Cannot connect**: Verify the Docker containers are running in the Unraid Docker tab
- **UI can't reach API**: Ensure both containers are on the same Docker network
- **Permission errors**: Check PUID/PGID settings in the container config (typically `99` and `100` for Unraid)

For detailed setup and configuration, visit:
- [File Core API Documentation](https://github.com/jandrop/file_core_api_unraid/blob/main/README.md)

---

## Additional Resources

- [Unraid API Documentation](https://docs.unraid.net/API/how-to-use-the-api)
