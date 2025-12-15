
# U-Manager - How To Guide

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

## Additional Resources

- [Unraid API Documentation](https://docs.unraid.net/API/how-to-use-the-api)
