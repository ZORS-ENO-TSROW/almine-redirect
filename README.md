<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ALMine - Opening App...</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            text-align: center;
            padding: 20px;
        }
        .container {
            max-width: 500px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        h1 {
            font-size: 28px;
            margin-bottom: 20px;
            animation: pulse 1.5s ease-in-out infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.6; }
        }
        p {
            font-size: 16px;
            line-height: 1.6;
            margin: 15px 0;
        }
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid white;
            border-radius: 50%;
            width: 50px;
            height: 50px;
            animation: spin 1s linear infinite;
            margin: 30px auto;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .fallback {
            display: none;
            margin-top: 20px;
        }
        .fallback.show {
            display: block;
        }
        .button {
            display: inline-block;
            margin-top: 20px;
            padding: 12px 30px;
            background: white;
            color: #667eea;
            text-decoration: none;
            border-radius: 25px;
            font-weight: bold;
            transition: transform 0.2s;
        }
        .button:hover {
            transform: scale(1.05);
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 id="title">🔐 Opening ALMine App...</h1>
        <div class="spinner" id="spinner"></div>
        <p id="message">Please wait while we redirect you to the app.</p>
        
        <div class="fallback" id="fallback">
            <p><strong>App didn't open automatically?</strong></p>
            <p>Please open the ALMine app manually to complete your password reset.</p>
            <p style="font-size: 14px; margin-top: 30px; opacity: 0.8;">
                If you don't have the app installed, please download it from Google Play Store.
            </p>
        </div>
    </div>

    <script>
        // Get the full URL fragment (everything after #)
        const fragment = window.location.hash.substring(1); // Remove the leading '#'
        
        console.log('🔍 Redirect page loaded');
        console.log('📋 URL fragment:', fragment);
        
        if (fragment) {
            // Construct the deep link with the full fragment
            const deepLink = `almine://reset-password#${fragment}`;
            console.log('🔗 Attempting to open:', deepLink);
            
            // Try to open the app
            window.location.href = deepLink;
            
            // Show fallback message after 3 seconds
            setTimeout(() => {
                document.getElementById('spinner').style.display = 'none';
                document.getElementById('title').textContent = '⚠️ Manual Action Required';
                document.getElementById('message').style.display = 'none';
                document.getElementById('fallback').classList.add('show');
            }, 3000);
        } else {
            // No token in URL
            document.getElementById('spinner').style.display = 'none';
            document.getElementById('title').textContent = '❌ Invalid Reset Link';
            document.getElementById('message').textContent = 'This password reset link is invalid or expired. Please request a new one from the app.';
        }
    </script>
</body>
</html>
