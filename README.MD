# Orange ID by Bedrock – Script Tag (HTML) Integration Guide

This guide explains how to integrate the Bedrock Passport authentication widget into a standard HTML website without requiring React knowledge or build tools.

## Quick Start

Add the following code to your HTML page:

<!-- 1. Create a container for the widget -->
<div id="bedrock-login-widget"></div>

<!-- 2. Load the required scripts -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>

<!-- 3. Initialize the widget -->
<script>
  (function () {
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com', // Base API URL – no need to change this. Leave as is.
      authCallbackUrl: window.location.origin, // For production, ensure you have a dedicated auth callback page (e.g. /auth/callback)
      tenantId: 'orange-abc123', // Your assigned tenant ID - you can request one at https://vibecodinglist.com/orange-id-integration
    };

    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget with grouped options:
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          // Content options
          title: 'Sign in to',
          logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
          logoAlt: 'Orange Web3',
          walletButtonText: 'Connect Wallet',
          showConnectWallet: false,
          separatorText: 'OR',

          // Feature toggles
          features: {
            enableWalletConnect: false,
            enableAppleLogin: true,
            enableGoogleLogin: true,
            enableEmailLogin: true,
          },

          // Style options
          titleClass: 'text-xl font-bold',
          logoClass: 'ml-2 md:h-8 h-6',
          panelClass: 'container p-2 md:p-8 rounded-2xl max-w-[480px]',
          buttonClass: 'hover:border-orange-500',
          separatorTextClass: 'bg-orange-900 text-gray-500',
          separatorClass: 'bg-orange-900',
          linkRowClass: 'justify-center',
          headerClass: 'justify-center',
        })
      )
    );
  })();
</script>

## Detailed Integration Guide

### Step 1: Prepare Your HTML File

Create an HTML file with a container element where the widget will be rendered:

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My App with Bedrock Passport</title>
    <style>
      /* Optional: Add some basic styling for the widget container */
      #bedrock-login-widget {
        max-width: 400px;
        margin: 50px auto;
      }
    </style>
  </head>
  <body>
    <h1>Welcome to My App</h1>
    <!-- This div will contain the login widget -->
    <div id="bedrock-login-widget"></div>
  </body>
</html>

### Step 2: Add Required Dependencies

Add the React and ReactDOM libraries, along with the Bedrock Passport widget, before the closing </body> tag:

<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>

### Step 3: Initialize the Widget

Add the following initialization script to your HTML file:

<script>
  (function () {
    // Ensure required libraries are loaded
    if (!window.React || !window.ReactDOM || !window.Bedrock) {
      console.error('Error: Required libraries failed to load.');
      document.getElementById('bedrock-login-widget').innerText =
        'Error loading authentication widget. Please check your internet connection.';
      return;
    }

    // Configuration for Bedrock Passport
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com', // Base API URL – no need to change this. Leave as is.
      authCallbackUrl: window.location.origin, // For production, ensure you have a dedicated auth callback page (e.g. /auth/callback)
      tenantId: 'orange-abc123', // Your assigned tenant ID - you can request one at https://vibecodinglist.com/orange-id-integration
    };

    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget with grouped options:
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          // Content options
          title: 'Sign in to',
          logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
          logoAlt: 'Orange Web3',
          walletButtonText: 'Connect Wallet',
          showConnectWallet: false,
          separatorText: 'OR',

          // Feature toggles
          features: {
            enableWalletConnect: false,
            enableAppleLogin: true,
            enableGoogleLogin: true,
            enableEmailLogin: true,
          },

          // Style options
          titleClass: 'text-xl font-bold',
          logoClass: 'ml-2 md:h-8 h-6',
          panelClass: 'container p-2 md:p-8 rounded-2xl max-w-[480px]',
          buttonClass: 'hover:border-orange-500',
          separatorTextClass: 'bg-orange-900 text-gray-500',
          separatorClass: 'bg-orange-900',
          linkRowClass: 'justify-center',
          headerClass: 'justify-center',
        })
      )
    );
  })();
</script>

### Step 4: Handle Authentication Callbacks

When a user completes authentication, they will be redirected back to your site with `token` and `refreshToken` parameters in the URL. Add this script to handle the callback:

<script>
  (function () {
    // Configuration (same as above)
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com',
      authCallbackUrl: window.location.origin,
      tenantId: 'orange-abc123',
    };

    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);
    const params = new URLSearchParams(window.location.search);
    const token = params.get('token');
    const refreshToken = params.get('refreshToken');

    if (token && refreshToken) {
      root.render(
        React.createElement(
          Bedrock.BedrockPassportProvider,
          bedrockConfig,
          React.createElement(function CallbackProcessor() {
            const { loginCallback } = Bedrock.useBedrockPassport();
            React.useEffect(() => {
              async function go() {
                const success = await loginCallback(token, refreshToken);
                if (success) {
                  window.location.href = '/dashboard.html'; // Redirect to your dashboard page
                } else {
                  container.innerHTML = '<p>Login failed.</p>';
                }
              }
              go();
            }, []);
            return React.createElement('div', null, 'Processing...');
          })
        )
      );
    } else {
      root.render(
        React.createElement(
          Bedrock.BedrockPassportProvider,
          bedrockConfig,
          React.createElement(Bedrock.LoginPanel, {
            // Content options
            title: 'Sign in to',
            logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
            logoAlt: 'Orange Web3',
            walletButtonText: 'Connect Wallet',
            showConnectWallet: false,
            separatorText: 'OR',

            // Feature toggles
            features: {
              enableWalletConnect: false,
              enableAppleLogin: true,
              enableGoogleLogin: true,
              enableEmailLogin: true,
            },

            // Style options
            titleClass: 'text-xl font-bold',
            logoClass: 'ml-2 md:h-8 h-6',
            panelClass: 'container p-2 md:p-8 rounded-2xl max-w-[480px]',
            buttonClass: 'hover:border-orange-500',
            separatorTextClass: 'bg-orange-900 text-gray-500',
            separatorClass: 'bg-orange-900',
            linkRowClass: 'justify-center',
            headerClass: 'justify-center',
          })
        )
      );
    }
  })();
</script>

## 📦 User Object Returned After Login

When a user logs in successfully using Bedrock Passport, you can access a structured user object like the following:

```json
{
  "id": "clf8138x000045o01a4p2ajpi",
  "email": "testuser@example.com",
  "name": "Test User",
  "displayName": "testname",
  "role": "user",
  "ethAddress": "0x1234567890abcdef1234567890abcdef12345678",
  "picture": "https://cdn.example.com/images/sample_user.png",
  "provider": "google",
  "createdAt": "2025-04-07T06:21:36.674Z"
}
```

### Explanation of Fields

| Key           | Description                                                 |
|---------------|-------------------------------------------------------------|
| `id`          | Unique Orange ID for the user                               |
| `email`       | User's email address                                         |
| `name`        | Full name from the provider                                  |
| `displayName` | User-chosen display name                                       |
| `role`        | User role (e.g., `user`, `admin`)                            |
| `ethAddress`  | Ethereum wallet address (if connected)                       |
| `picture`     | URL to the user’s profile picture                            |
| `provider`    | Login method used (e.g., `google`, `apple`, `wallet`)        |
| `createdAt`   | Timestamp of account creation                                |

## ⚙️ Features Configuration

You can customize which login methods to show by passing a `features` object to the `<LoginPanel>` component.

### Example (React / NPM)

```tsx
<LoginPanel
  ...
  features={{
    enableWalletConnect: true,
    enableAppleLogin: true,
    enableGoogleLogin: true,
    enableEmailLogin: true,
  }}
/>
```

### Example (HTML / Script Tag)

```js
React.createElement(Bedrock.LoginPanel, {
  ...
  features: {
    enableWalletConnect: true,
    enableAppleLogin: true,
    enableGoogleLogin: true,
    enableEmailLogin: true,
  }
})
```

### Available Feature Flags

| Feature               | Description                              |
|-----------------------|------------------------------------------|
| `enableWalletConnect` | Enables Web3 wallet login                |
| `enableAppleLogin`    | Enables Apple ID login                   |
| `enableGoogleLogin`   | Enables Google login                     |
| `enableEmailLogin`    | Enables email-based login                |

## Styling Options for the Panel

The widget accepts several CSS class names for styling. Below is a summary of the available options:

| Parameter            | Description                                                      |
|----------------------|------------------------------------------------------------------|
| `panelClass`         | Panel styling                                                    |
| `buttonClass`        | All button styling                                               |
| `headerClass`        | Header styling                                                   |
| `title`              | Title of the panel                                               |
| `titleClass`         | Title styling                                                    |
| `logo`               | Logo URL                                                         |
| `logoAlt`            | Logo alt text                                                    |
| `logoClass`          | Logo styling                                                     |
| `showConnectWallet`  | Boolean; false by default                                        |
| `walletButtonText`   | Text for the wallet button                                       |
| `walletButtonClass`  | Wallet button styling (defaults to following button styling)     |
| `separatorText`      | Text displayed in the separator                                  |
| `separatorTextClass` | Separator text styling                                           |
| `separatorClass`     | Separator styling                                                |
| `linkRowClass`       | Styling for the row containing login links                       |

## 🛠 Troubleshooting

- **Widget not appearing?**  
  Open your browser's developer tools and check for missing scripts or React errors.

- **Authentication callback not working?**  
  Verify that `authCallbackUrl` matches the one registered on the platform and that URL parameters (`token` and `refreshToken`) are handled correctly.

- **Styling issues?**  
  Ensure Tailwind CSS is loaded or replace the class names with your custom CSS definitions.

---

You're now ready to integrate Orange ID by Bedrock into your HTML project using only script tags!
