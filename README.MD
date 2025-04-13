# Bedrock Passport Widget - HTML Integration Guide

This guide explains how to integrate the Bedrock Passport authentication widget into a standard HTML website without requiring React knowledge or build tools.

## Quick Start

Add the following code to your HTML page:

```html
<!-- 1. Create a container for the widget -->
<div id="bedrock-login-widget"></div>

<!-- 2. Load the required scripts -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>

<!-- 3. Initialize the widget -->
<script>
  (function () {
    // Configuration for Bedrock Passport
    const bedrockConfig = {
      baseUrl: 'https://api.bedrockpassport.com', // Replace with your endpoint
      authCallbackUrl: window.location.origin, // Current site URL
      tenantId: 'dev', // Replace with your tenant ID
      walletConnectId: '591d21ef88b24c6837599a5c2a0ce03d', // Replace with your wallet connect ID
    };

    // Get container and create root
    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          title: 'Sign in to',
          logo: 'https://path-to-your-logo.svg', // Replace with your logo URL
          logoAlt: 'Your App',
          showConnectWallet: true,
          walletButtonText: 'Connect Wallet',
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

    <!-- Scripts will be added here -->
  </body>
</html>
```

### Step 2: Add Required Dependencies

Add the React and ReactDOM libraries, along with the Bedrock Passport widget:

```html
<!-- Add these scripts before the closing </body> tag -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>
```

### Step 3: Initialize the Widget

Add the initialization script:

```html
<script>
  (function () {
    // Make sure all required libraries are loaded
    if (!window.React || !window.ReactDOM || !window.Bedrock) {
      console.error('Error: Required libraries failed to load.');
      document.getElementById('bedrock-login-widget').innerText =
        'Error loading authentication widget. Please check your internet connection.';
      return;
    }

    // Configuration for Bedrock Passport
    const bedrockConfig = {
      baseUrl: 'https://apim-dev-bedrockpassport.azure-api.net/', // Replace with your endpoint
      authCallbackUrl: window.location.origin, // Current site URL
      tenantId: 'dev', // Replace with your tenant ID
      walletConnectId: '591d21ef88b24c6837599a5c2a0ce03d', // Replace with your wallet connect ID
    };

    // Get container and create root
    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    // Render login widget
    root.render(
      React.createElement(
        Bedrock.BedrockPassportProvider,
        bedrockConfig,
        React.createElement(Bedrock.LoginPanel, {
          title: 'Sign in to',
          logo: 'https://path-to-your-logo.svg', // Replace with your logo URL
          logoAlt: 'Your App',
          titleClass: 'text-xl font-bold',
          logoClass: 'ml-2 h-8',
          panelClass: 'p-6 rounded-lg shadow-md',
          showConnectWallet: true,
          walletButtonText: 'Connect Wallet',
          separatorText: 'OR',
          separatorTextClass: 'text-gray-500 text-sm',
          buttonClass: 'py-2 px-4 rounded hover:bg-gray-100',
        })
      )
    );
  })();
</script>
```

### Step 4: Handle Authentication Callbacks

When a user completes authentication, they will be redirected back to your site with `token` and `refreshToken` parameters in the URL. You need to handle these to complete the login process:

```html
<script>
  (function () {
    // Configuration (same as above)
    const bedrockConfig = {
      baseUrl: 'https://apim-dev-bedrockpassport.azure-api.net/',
      authCallbackUrl: window.location.origin,
      tenantId: 'dev',
      walletConnectId: '591d21ef88b24c6837599a5c2a0ce03d',
    };

    // Check if we're handling a callback
    const params = new URLSearchParams(window.location.search);
    const token = params.get('token');
    const refreshToken = params.get('refreshToken');

    // Container for widget
    const container = document.getElementById('bedrock-login-widget');
    const root = ReactDOM.createRoot(container);

    if (token && refreshToken) {
      // We're handling a callback, render the callback processor
      root.render(
        React.createElement(
          Bedrock.BedrockPassportProvider,
          bedrockConfig,
          React.createElement(CallbackProcessor, { token, refreshToken })
        )
      );
    } else {
      // Normal login flow, render the login panel
      root.render(
        React.createElement(
          Bedrock.BedrockPassportProvider,
          bedrockConfig,
          React.createElement(Bedrock.LoginPanel, {
            // Login panel props (as shown above)
            title: 'Sign in to',
            logo: 'https://path-to-your-logo.svg',
            // ...other props
          })
        )
      );
    }

    // Component to handle the callback
    function CallbackProcessor(props) {
      const { token, refreshToken } = props;

      // Get the loginCallback function from Bedrock
      const hookResult = Bedrock.useBedrockPassport();
      const loginCallback = hookResult.loginCallback;

      // Process the login
      React.useEffect(() => {
        async function processLogin() {
          try {
            // Show loading message
            container.innerHTML = '<p>Processing login...</p>';

            // Call the loginCallback function
            const success = await loginCallback(token, refreshToken);

            if (success) {
              // Login successful
              window.location.href = '/dashboard.html'; // Redirect to your dashboard page
            } else {
              // Login failed
              container.innerHTML = '<p>Login failed. Please try again.</p>';
            }
          } catch (error) {
            console.error('Login error:', error);
            container.innerHTML = '<p>An error occurred during login.</p>';
          }
        }

        processLogin();
      }, []);

      return React.createElement('div', null, 'Processing authentication...');
    }
  })();
</script>
```

## Customization Options

### Styling

The widget accepts several CSS class names for styling:

```javascript
React.createElement(Bedrock.LoginPanel, {
  // Content customization
  title: 'Sign in to',
  logo: 'https://path-to-your-logo.svg',
  logoAlt: 'Your App',
  showConnectWallet: true,
  walletButtonText: 'Connect Wallet',
  separatorText: 'OR',

  // Styling classes
  buttonClass: 'py-2 px-4 rounded hover:bg-gray-100',
  headerClass: 'flex justify-center items-center mb-6',
  titleClass: 'text-xl font-bold',
  logoClass: 'ml-2 h-8',
  panelClass: 'p-6 rounded-lg shadow-md bg-white',
  separatorTextClass: 'text-gray-500 text-sm',
  separatorClass: 'border-t border-gray-300',
});
```

You can define these classes in your CSS stylesheet or include a CSS framework like Tailwind CSS.

### Complete Example

For a complete working example with authentication callback handling, see the full HTML file below:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Bedrock Passport Demo</title>
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
        <h1>Bedrock Passport Demo</h1>
        <p>
          This demonstrates integrating the Bedrock Passport widget in a plain
          HTML page.
        </p>
      </div>

      <div id="bedrock-login-widget"></div>
    </div>

    <!-- Required Dependencies -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://public-cdn-files.pages.dev/bedrock-passport.umd.js"></script>

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
          baseUrl: 'https://apim-dev-bedrockpassport.azure-api.net/',
          authCallbackUrl: window.location.origin,
          tenantId: 'dev',
          walletConnectId: '591d21ef88b24c6837599a5c2a0ce03d',
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
            const { loginCallback } = Bedrock.useBedrockPassport();
            const [message, setMessage] = React.useState(
              'Processing authentication...'
            );

            React.useEffect(() => {
              async function processLogin() {
                try {
                  setMessage('Verifying tokens...');
                  const success = await loginCallback(token, refreshToken);

                  if (success) {
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
              { style: { textAlign: 'center', padding: '1rem' } },
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
                title: 'Welcome to',
                logo: 'https://example.com/your-logo.svg',
                logoAlt: 'Your App',
                showConnectWallet: true,
                walletButtonText: 'Connect Wallet',
                separatorText: 'OR',
              })
            )
          );
        }
      })();
    </script>
  </body>
</html>
```

## Troubleshooting

### Widget Not Appearing

- Check browser console for errors
- Verify all script tags are loading properly
- Ensure the container element exists with the correct ID

### Authentication Callback Not Working

- Verify the `authCallbackUrl` matches your site's URL
- Check that you're properly handling the token and refreshToken URL parameters
- Test with console.log statements to debug the callback flow

### Styling Issues

- The widget uses minimal default styling
- Apply custom styles using the class props or global CSS
- For complete styling control, you may need to add a CSS framework
