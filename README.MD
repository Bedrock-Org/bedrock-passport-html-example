# Orange ID by Bedrock – Script Tag (HTML) Integration Guide

This guide explains how to integrate the Bedrock Passport authentication widget into a standard HTML website without requiring React knowledge or build tools.

> ⚠️ **Before You Start**  
>  
> To use the Orange ID widget, you must [create a Project ID and API Key](http://developer.orangeweb3.com) and **add it as your `tenantId` & `subscriptionKey`** in the configuration.  
> You'll also need to **whitelist your project URLs via the link above** to enable authentication on your domain.

## Quick Start

Add the following code to your HTML page:

```html
<!-- 1. Create a container for the widget -->
<div id="bedrock-login-widget"></div>

<!-- 2. Load the required scripts -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>

<!-- Optional: Include Tailwind CSS for styling -->
<script src="https://cdn.tailwindcss.com"></script>

<!-- 3. Initialize the widget -->
<script>
  (function () {
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com', // Base API URL – no need to change this. Leave as is.
      authCallbackUrl: window.location.origin, // For production, ensure you have a dedicated auth callback page (e.g. /auth/callback)
      tenantId: 'orange-abc123', // Your assigned tenant ID - you can request one at the developer portal
      subscriptionKey: 'your_API_Key', // Your assigned API Key - request in the developer portal
    };

    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget with grouped options:
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          // Content options — No changes needed unless specific customization is required
          title: 'Sign in to',
          logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
          logoAlt: 'Orange Web3',
          walletButtonText: 'Connect Wallet',
          showConnectWallet: false,
          separatorText: 'OR',

          // Feature toggles — Adjust these based on which login methods you want to support
          features: {
            enableWalletConnect: false,
            enableAppleLogin: true,
            enableGoogleLogin: true,
            enableEmailLogin: false,
          },

          // Style options — Keep as-is unless UI tweaks are needed
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
```

## Detailed Integration Guide

### Step 1: Prepare Your HTML File

Create an HTML file with a container element where the widget will be rendered:

```html
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
```

### Step 2: Add Required Dependencies

Add the React and ReactDOM libraries, along with the Bedrock Passport widget, before the closing </body> tag:

```html
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>
<!-- Optional: Include Tailwind CSS for styling -->
<script src="https://cdn.tailwindcss.com"></script>
```

### Step 3: Initialize the Widget

Add the following initialization script to your HTML file:

```html
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
      tenantId: 'orange-abc123', // Your assigned tenant ID - request in the developer portal
      subscriptionKey: 'your_API_Key', // Your assigned API Key - request in the developer portal
    };

    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget with grouped options:
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          // Content options — No changes needed unless specific customization is required
          title: 'Sign in to',
          logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
          logoAlt: 'Orange Web3',
          walletButtonText: 'Connect Wallet',
          showConnectWallet: false,
          separatorText: 'OR',

          // Feature toggles — Adjust these based on which login methods you want to support
          features: {
            enableWalletConnect: false,
            enableAppleLogin: true,
            enableGoogleLogin: true,
            enableEmailLogin: false,
          },

          // Style options — Keep as-is unless UI tweaks are needed
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
```

### Step 4: Handle Authentication Callbacks

When a user completes authentication, they will be redirected back to your site with `token` and `refreshToken` parameters in the URL. Add this script to handle the callback:

```html
<script>
  (function () {
    // Configuration (same as above)
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com',
      authCallbackUrl: window.location.origin,
      tenantId: 'orange-abc123',
      subscriptionKey: 'your_API_Key', 
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
                  // Persist tokens for later logout
                  try {
                    localStorage.setItem('bedrock:accessToken', token);
                    localStorage.setItem('bedrock:refreshToken', refreshToken);
                  } catch (_) {}
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
            // Content options — No changes needed unless specific customization is required
            title: 'Sign in to',
            logo: 'https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg',
            logoAlt: 'Orange Web3',
            walletButtonText: 'Connect Wallet',
            showConnectWallet: false,
            separatorText: 'OR',

            // Feature toggles — Adjust these based on which login methods you want to support
            features: {
              enableWalletConnect: false,
              enableAppleLogin: true,
              enableGoogleLogin: true,
              enableEmailLogin: false,
            },

            // Style options — Keep as-is unless UI tweaks are needed
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
```

## Complete Example
For a complete working example with authentication callback handling, see the full HTML file below:

```html

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Orange ID Demo</title>
  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
        sans-serif;
      background-color: #f5f5f5;
      margin: 0;
      padding: 0;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 2rem;
    }

    .header {
      text-align: center;
      margin-bottom: 2rem;
    }

    #bedrock-login-widget {
      max-width: 400px;
      margin: 0 auto;
      background-color: white;
      border-radius: 8px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      padding: 1.5rem;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="header">
      <h1>Orange ID Demo</h1>
      <p>
        This demonstrates integrating the Orange ID widget in a plain
        HTML page.
      </p>
    </div>

    <div id="bedrock-login-widget"></div>
  </div>

  <!-- Required Dependencies -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  <script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>
  <!-- Optional: Include Tailwind CSS for styling -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Initialization Script -->
  <script>
    (function () {
      // Ensure required libraries are loaded
      if (!window.React || !window.ReactDOM || !window.Bedrock) {
        console.error('Error: Required libraries failed to load.');
        document.getElementById('bedrock-login-widget').innerHTML =
          '<div style="color: red; text-align: center;">Error loading authentication widget. Please check your internet connection.</div>';
        return;
      }

      // Configuration
      const bedrockConfig = {
        baseUrl: 'https://api.bedrockpassport.com', // Base API URL – no need to change this. Leave as is.
        authCallbackUrl: window.location.origin, // For production, ensure you have a dedicated auth callback page (e.g. /auth/callback)
        tenantId: 'orange-abc123', // Your assigned tenant ID - request in the developer portal
        subscriptionKey: 'your_API_Key', // Your assigned API Key - request in the developer portal
      };

      // Container for widget
      const container = document.getElementById('bedrock-login-widget');
      const root = ReactDOM.createRoot(container);

      // Check if we're handling a callback
      const params = new URLSearchParams(window.location.search);
      const token = params.get('token');
      const refreshToken = params.get('refreshToken');

      if (token && refreshToken) {
        // We're handling a callback
        // Create the callback processor component
        function AuthCallbackProcessor() {
          const {loginCallback} = Bedrock.useBedrockPassport();
          const [message, setMessage] = React.useState(
            'Processing authentication...'
          );

          React.useEffect(() => {
            async function processLogin() {
              try {
                setMessage('Verifying tokens...');
                const success = await loginCallback(token, refreshToken);

                if (success) {
                  // Persist tokens for later logout
                  try {
                    localStorage.setItem('bedrock:accessToken', token);
                    localStorage.setItem('bedrock:refreshToken', refreshToken);
                  } catch (_) {}
                  setMessage('Login successful! Redirecting...');
                  // Remove token parameters from URL and redirect
                  window.history.replaceState(
                    {},
                    document.title,
                    window.location.pathname
                  );
                  setTimeout(() => {
                    // Redirect or refresh page as needed
                    window.location.reload();
                  }, 1000);
                } else {
                  setMessage('Authentication failed. Please try again.');
                }
              } catch (error) {
                console.error('Login error:', error);
                setMessage('An error occurred during login.');
              }
            }

            processLogin();
          }, [loginCallback]);

          return React.createElement(
            'div',
            {style: {textAlign: 'center', padding: '1rem'}},
            message
          );
        }

        // Render callback processor
        root.render(
          React.createElement(
            Bedrock.BedrockPassportProvider,
            bedrockConfig,
            React.createElement(AuthCallbackProcessor)
          )
        );
      } else {
        // Normal login flow
        root.render(
          React.createElement(
            Bedrock.BedrockPassportProvider,
            bedrockConfig,
            React.createElement(Bedrock.LoginPanel, {
              // Content options — No changes needed unless specific customization is required
              title: "Sign in to",
              logo: "https://irp.cdn-website.com/e81c109a/dms3rep/multi/orange-web3-logo-v2a-20241018.svg",
              logoAlt: "Orange Web3",
              walletButtonText: "Connect Wallet",
              showConnectWallet: false,
              separatorText: "OR",

              // Feature toggles — Adjust these based on which login methods you want to support
              features: {
                enableWalletConnect: false,
                enableAppleLogin: true,
                enableGoogleLogin: true,
                enableEmailLogin: false,
              },

              // Style options — Keep as-is unless UI tweaks are needed
              titleClass: "text-xl font-bold",
              logoClass: "ml-2 md:h-8 h-6",
              panelClass: "container p-2 md:p-8 rounded-2xl max-w-[480px]",
              buttonClass: "hover:border-orange-500",
              separatorTextClass: "bg-orange-900 text-gray-500",
              separatorClass: "bg-orange-900",
              linkRowClass: "justify-center",
              headerClass: "justify-center",
            })
          )
        );
      }
    })();
  </script>
</body>

</html>

```


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

## Example: Signing Out a User
 
Use this minimal sign-out button. It calls the Logout API with the stored access token, then clears local auth state.
 
```html
<!-- place this wherever you want the button to appear -->
<div id="signout-button"></div>
 
<script>
  (function () {
    // grab your existing config object
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com',
      authCallbackUrl: window.location.origin,
      tenantId: 'orange-abc123',
      subscriptionKey: 'your_API_Key',
    };
 
    // find the container and create a React root
    const container = document.getElementById('signout-button');
    const root = ReactDOM.createRoot(container);
 
    // a small component to show a Sign Out button
    function SignOutButton() {
      const { user, signOut } = Bedrock.useBedrockPassport();
      return React.createElement(
        'button',
        {
          className: 'px-4 py-2 bg-red-600 text-white rounded',
          onClick: async () => {
            try {
              const accessToken = localStorage.getItem('bedrock:accessToken');
              if (accessToken) {
                await fetch('https://api.bedrockpassport.com/api/v1/auth/logout', {
                  method: 'POST',
                  headers: { Authorization: `Bearer ${accessToken}` }
                });
              }
            } catch (_) {}
            await signOut();
            try {
              localStorage.removeItem('bedrock:accessToken');
              localStorage.removeItem('bedrock:refreshToken');
            } catch (_) {}
            window.location.reload();
          }
        },
        user ? 'Sign Out' : 'Sign In'
      );
    }
 
    // wrap in your provider
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(SignOutButton)
      )
    );
  })();
</script>
```

### Logout Implementation with JWT Token

To log out, **POST** `https://api.bedrockpassport.com/api/v1/auth/logout` with  
`Authorization: Bearer <accessToken>`, then call `signOut()` to clear local auth state. Store `token`/`refreshToken` on the callback so the access token is available for the logout call.

## 📦 User Object Returned After Login

When a user logs in successfully using Bedrock Passport, you will receive a user object similar to the following:

```json
{
  "id": "clf8138x000045o01a4p2ajpi",
  "email": "testuser@example.com",
  "name": "Test User",
  "displayName": "testname",
  "bio": "This is a sample biography.",
  "picture": "https://cdn.example.com/images/sample_user.png",
  "banner": "https://cdn.example.com/images/sample_user_banner.png",
  "ethAddress": "0x1234567890abcdef1234567890abcdef12345678",
  "provider": "google",
  "createdAt": "2025-04-07T06:21:36.674Z"
}
```

### Explanation of Fields

| Key           | Description                                                                                                                      |
|---------------|----------------------------------------------------------------------------------------------------------------------------------|
| `id`          | Unique Orange ID for the user.                                                                                                   |
| `email`       | User's email address.                                                                                                            |
| `name`        | Full name from the provider.                                                                                                     |
| `displayName` | User-chosen display name. (Can be updated at vibecodinglist.com/profile)                                                         |
| `bio`         | A brief biography of the user.                                                                                                   |
| `picture`     | URL to the user's profile picture. (Can be updated at vibecodinglist.com/profile)                                                |
| `banner`      | URL to the user's banner image. (Can be updated at vibecodinglist.com/profile)                                                   |
| `ethAddress`  | Ethereum wallet address (if connected).                                                                                          |
| `provider`    | Login method used (e.g., `google`, `apple`, `wallet`).                                                                           |
| `createdAt`   | Timestamp of account creation.                                                                                                   |


## Server-Side Token Verification (Important for Custom Backends)

After a user logs in with Orange ID, your app receives a **JWT (token)** that represents the authenticated user. If you're passing this token to a backend service (e.g. to fetch user-specific data), it's important to **verify that the token is valid and untampered**.

To do this, your backend can call:

```
GET https://api.bedrockpassport.com/api/v1/auth/user
```

Pass the JWT as a Bearer token in the `Authorization` header:

```
Authorization: Bearer <token>
```

If the token is valid, the endpoint will return the user’s profile. If it's invalid or forged, it will return a `401 Unauthorized` response.

> This is the recommended approach for verifying tokens until Bedrock publishes a **JWKS endpoint** that will allow local verification using standard JWT libraries (coming soon).


## 🛠 Troubleshooting

- **Widget not appearing?**  
  Open your browser's developer tools and check for missing scripts or React errors.

- **Authentication callback not working?**  
  Verify that `authCallbackUrl` matches the one registered on the platform and that URL parameters (`token` and `refreshToken`) are handled correctly.

- **Styling issues?**  
  Ensure Tailwind CSS is loaded or replace the class names with your custom CSS definitions.

---

You're now ready to integrate Orange ID by Bedrock into your HTML project using only script tags!
