# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

MegaMind IT is an Azure Static Web App that provides an AI-powered IT assistant interface with Microsoft Azure AD authentication. The application uses n8n webhooks for backend orchestration and includes voice capabilities and admin role management.

## Architecture

### Frontend Structure
- **Single Page Application**: Pure HTML/CSS/JavaScript (no build process)
- **Authentication**: Microsoft Authentication Library (MSAL) for Azure AD integration
  - Client ID: `675b5149-fad2-4c8e-a587-097e9fff59dd` (MegaMind IT for saxadvisorygroup.com)
  - Tenant ID: `a33e9b66-a6ef-43bf-9702-7cb4301d0a16` (saxadvisorygroup.com)
- **Backend Integration**: n8n webhook at `https://workflows.saxtechnology.com/webhook/megamind-it-chat`
- **Voice Features**: Web Audio API for recording and text-to-speech playback

### Key Files
- `index.html` - Main application with embedded CSS and JavaScript
- `robot.png` - Application logo/mascot image
- `staticwebapp.config.json` - Azure Static Web App routing configuration
- `test-webhook.js` - Node.js script for testing n8n webhook integration

## Development Commands

### Testing Webhook Integration
```bash
node test-webhook.js
```

### Local Development
Since this is a static site with no build process, open `index.html` directly in a browser. Note that authentication features require the proper domain for redirect URIs.

### Deployment
This project uses GitHub Actions for automatic deployment to Azure Static Web Apps:

```bash
# Push changes to production
git add .
git commit -m "Your commit message"
git push origin main
```

The GitHub Action will automatically deploy to Azure Static Web Apps on push to main branch.

## Azure Resources

### Static Web App
- **Resource**: Azure Static Web Apps
- **Deployment Token**: Stored in GitHub secrets as `AZURE_STATIC_WEB_APPS_API_TOKEN_CALM_TREE_040AB6B0F`
- **GitHub Actions**: `.github/workflows/azure-static-web-apps-calm-tree-040ab6b0f.yml`

### Azure AD Application
- **App Registration**: MegaMind IT
- **Scopes**: `openid`, `profile`, `User.Read`
- **Redirect URI**: Must match the deployment domain

## n8n Integration

The application communicates with n8n workflows hosted at `https://workflows.saxtechnology.com`. The webhook expects the following payload structure:

```javascript
{
  message: string,
  sessionId: string,
  selectedVoice: string,
  userContext: {
    userProfile: {
      name: string,
      email: string,
      jobTitle: string,
      department: string,
      contextualRole: string,
      permissions: object
    }
  },
  userProfile: object
}
```

## User Roles & Permissions

Admin permissions are automatically granted to users in these departments or with these titles:
- **Departments**: IT, Information Technology, HR, Human Resources
- **Titles**: Director, Manager, Administrator, Admin, Lead

## Important Notes

1. **MSAL Library**: Loaded from Microsoft CDN with integrity checking
2. **Session Storage**: Authentication tokens stored in sessionStorage (not localStorage)
3. **Voice Selection**: Multiple voice options available for text-to-speech
4. **Admin Badge**: Visual indicator for users with elevated permissions
5. **Build Directory**: Contains fallback files for Azure Static Web Apps routing

## Troubleshooting

### Authentication Issues
- Verify the Azure AD app registration has correct redirect URIs
- Check browser console for MSAL initialization errors
- Ensure popup blockers are disabled for authentication

### Webhook Connectivity
- Use `test-webhook.js` to verify n8n webhook is responding
- Check network tab for failed requests to `workflows.saxtechnology.com`
- Verify CORS settings on n8n instance

### Static Asset Issues
- Ensure `staticwebapp.config.json` properly configures MIME types
- Check that `robot.png` and other assets are in the correct location
- Verify GitHub Actions deployment completed successfully
