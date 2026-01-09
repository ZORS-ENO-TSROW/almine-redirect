<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AL Mine - Reset Password</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 20px;
            padding: 40px;
            max-width: 450px;
            width: 100%;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
        }

        .logo {
            text-align: center;
            margin-bottom: 30px;
        }

        .logo h1 {
            font-size: 32px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 10px;
        }

        .logo p {
            color: #666;
            font-size: 14px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #333;
            font-weight: 600;
        }

        input[type="password"] {
            width: 100%;
            padding: 14px;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-size: 16px;
            transition: all 0.3s;
        }

        input[type="password"]:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        button {
            width: 100%;
            padding: 16px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.2s;
        }

        button:hover {
            transform: translateY(-2px);
        }

        button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .message {
            padding: 14px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
            font-weight: 600;
        }

        .message.error {
            background: #ffebee;
            color: #c62828;
        }

        .message.success {
            background: #e8f5e9;
            color: #2e7d32;
        }

        .message.info {
            background: #e3f2fd;
            color: #1565c0;
        }

        .password-requirements {
            font-size: 12px;
            color: #666;
            margin-top: 8px;
        }

        .password-requirements ul {
            margin-top: 8px;
            padding-left: 20px;
        }

        .password-requirements li {
            margin-bottom: 4px;
        }

        .success-container {
            text-align: center;
        }

        .success-container h2 {
            color: #2e7d32;
            margin-bottom: 20px;
        }

        .success-container p {
            color: #666;
            margin-bottom: 30px;
            line-height: 1.6;
        }

        .success-icon {
            font-size: 64px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">
            <h1>AL Mine</h1>
            <p>Reset Your Password</p>
        </div>

        <div id="form-container">
            <div id="message-container"></div>

            <form id="reset-form">
                <div class="form-group">
                    <label for="new-password">New Password</label>
                    <input 
                        type="password" 
                        id="new-password" 
                        placeholder="Enter new password"
                        required
                        minlength="6"
                    >
                    <div class="password-requirements">
                        <ul>
                            <li>At least 6 characters</li>
                            <li>Mix of letters and numbers recommended</li>
                        </ul>
                    </div>
                </div>

                <div class="form-group">
                    <label for="confirm-password">Confirm Password</label>
                    <input 
                        type="password" 
                        id="confirm-password" 
                        placeholder="Confirm new password"
                        required
                        minlength="6"
                    >
                </div>

                <button type="submit" id="submit-btn">Reset Password</button>
            </form>
        </div>

        <div id="success-container" style="display: none;">
            <div class="success-icon">✅</div>
            <h2>Password Updated!</h2>
            <p>Your password has been successfully changed. You can now close this page and log in to AL Mine with your new password.</p>
        </div>
    </div>

    <script>
        // Supabase configuration - REPLACE THESE WITH YOUR ACTUAL VALUES
        const SUPABASE_URL = 'https://vghcyfarsjzresjglfzd.supabase.co';
        const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InZnaGN5ZmFyc2p6cmVzamdmZnpkIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MzQyNzUzOTAsImV4cCI6MjA0OTg1MTM5MH0.w-vUZnZkxlR6RzXMlWzZiLXBFSK6FDv7J6hPsUGOQ9o';

        const form = document.getElementById('reset-form');
        const submitBtn = document.getElementById('submit-btn');
        const messageContainer = document.getElementById('message-container');
        const formContainer = document.getElementById('form-container');
        const successContainer = document.getElementById('success-container');

        // Get access token from URL hash
        const hash = window.location.hash;
        const params = new URLSearchParams(hash.substring(1)); // Remove '#' and parse
        const accessToken = params.get('access_token');
        const type = params.get('type');

        // Check if this is a valid password recovery link
        if (!accessToken || type !== 'recovery') {
            showMessage('Invalid or expired reset link. Please request a new password reset email.', 'error');
            submitBtn.disabled = true;
        }

        function showMessage(text, type) {
            messageContainer.innerHTML = `<div class="message ${type}">${text}</div>`;
        }

        form.addEventListener('submit', async (e) => {
            e.preventDefault();

            const newPassword = document.getElementById('new-password').value;
            const confirmPassword = document.getElementById('confirm-password').value;

            // Validate passwords match
            if (newPassword !== confirmPassword) {
                showMessage('Passwords do not match!', 'error');
                return;
            }

            // Validate password length
            if (newPassword.length < 6) {
                showMessage('Password must be at least 6 characters long', 'error');
                return;
            }

            // Disable button and show loading
            submitBtn.disabled = true;
            submitBtn.textContent = 'Updating Password...';
            showMessage('Updating your password...', 'info');

            try {
                // Call Supabase API to update password
                const response = await fetch(`${SUPABASE_URL}/auth/v1/user`, {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json',
                        'apikey': SUPABASE_ANON_KEY,
                        'Authorization': `Bearer ${accessToken}`
                    },
                    body: JSON.stringify({
                        password: newPassword
                    })
                });

                const data = await response.json();

                if (response.ok) {
                    // Success!
                    formContainer.style.display = 'none';
                    successContainer.style.display = 'block';
                } else {
                    // Error
                    showMessage(data.error_description || data.message || 'Failed to update password. Please try again.', 'error');
                    submitBtn.disabled = false;
                    submitBtn.textContent = 'Reset Password';
                }

            } catch (error) {
                console.error('Error:', error);
                showMessage('Network error. Please check your connection and try again.', 'error');
                submitBtn.disabled = false;
                submitBtn.textContent = 'Reset Password';
            }
        });
    </script>
</body>
</html>
