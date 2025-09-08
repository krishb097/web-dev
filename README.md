<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Sign In / Sign Up</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }

    body {
      height: 100vh;
      background: linear-gradient(135deg, #facc7e, #ff913d);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .container {
      position: relative;
      width: 1000px;
      height: 520px;
      background: #fff;
      border-radius: 20px;
      overflow: hidden;
      box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3);
    }

    .form-wrapper {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      z-index: 1;
    }

    .form-section {
      width: 50%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      background: white;
      transition: all 0.5s ease;
      opacity: 0;
    }

    .form-section.active {
      opacity: 1;
      z-index: 1;
    }

    .form-section h2 {
      color: orange;
      margin-bottom: 20px;
    }

    .form-section input {
      width: 70%;
      padding: 10px;
      margin: 10px 0;
      border: 1.5px solid #ccc;
      border-radius: 8px;
    }

    .form-section button {
      margin-top: 15px;
      padding: 10px 20px;
      border: none;
      background: orange;
      color: #fff;
      font-weight: 600;
      border-radius: 8px;
      cursor: pointer;
    }

    .toggle-wrapper {
      position: relative;
      width: 100%;
      height: 100%;
      display: flex;
      z-index: 2;
      transition: transform 0.6s ease-in-out;
    }

    .toggle-panel {
      width: 50%;
      height: 100%;
      background: orange;
      color: white;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      padding: 40px;
      text-align: center;
      transition: transform 0.6s ease-in-out;
    }

    .toggle-panel h1 {
      font-size: 26px;
      font-weight: 600;
    }

    .toggle-panel p {
      margin: 10px 0;
    }

    .toggle-panel a {
      color: #fff;
      text-decoration: underline;
      font-weight: 500;
      cursor: pointer;
    }

    .container.signin-active .toggle-wrapper {
      transform: translateX(-50%);
    }

    .container.signup-active .toggle-wrapper {
      transform: translateX(50%);
    }

    .container.signin-active .form-section.signin,
    .container.signup-active .form-section.signup {
      opacity: 1;
      z-index: 2;
    }
  </style>
</head>
<body>

  <div class="container signin-active" id="mainContainer">
    <!-- Forms -->
    <div class="form-wrapper">
      <!-- Sign Up Form -->
      <div class="form-section signup">
        <h2>Create Account</h2>
        <input type="text" id="signupName" placeholder="Full Name" required />
        <input type="email" id="signupEmail" placeholder="Email Address" required />
        <input type="password" id="signupPassword" placeholder="Password" required />
        <button type="button" id="signupBtn">Sign Up</button>
      </div>

      <!-- Sign In Form -->
      <div class="form-section signin active">
        <h2>Sign In</h2>
        <input type="email" id="signinEmail" placeholder="Email Address" required />
        <input type="password" id="signinPassword" placeholder="Password" required />
        <button type="button" id="signinBtn">Sign In</button>
      </div>
    </div>

    <!-- Toggle Panels -->
    <div class="toggle-wrapper">
      <div class="toggle-panel left">
        <h1>Join Us!</h1>
        <p>Already have an account?🚀</p>
        <a id="goSignIn">Go to Sign In</a>
      </div>

      <div class="toggle-panel right">
        <h1>Welcome Back!</h1>
        <p>Don't have an account? Sign up now! 🚀</p>
        <a id="goSignUp">Go to Sign Up</a>
      </div>
    </div>
  </div>

<script>
  const container = document.getElementById('mainContainer');
  const goSignUp = document.getElementById('goSignUp');
  const goSignIn = document.getElementById('goSignIn');
  const signupBtn = document.getElementById('signupBtn');
  const signinBtn = document.getElementById('signinBtn');

  // ✅ Redirect if already logged in
  if (localStorage.getItem("isLoggedIn") === "true") {
    window.location.href = "dashboard.html"; // you can create this page
  }

  // 🔁 UI Toggle
  goSignUp.addEventListener('click', () => {
    container.classList.add('signup-active');
    container.classList.remove('signin-active');
  });

  goSignIn.addEventListener('click', () => {
    container.classList.add('signin-active');
    container.classList.remove('signup-active');
  });

  // ✅ Sign Up Logic
  signupBtn.addEventListener('click', () => {
    const name = document.getElementById('signupName').value.trim();
    const email = document.getElementById('signupEmail').value.trim();
    const password = document.getElementById('signupPassword').value.trim();

    if (name === "" || email === "" || password === "") {
      alert("⚠ Please fill all signup fields!");
      return;
    }

    // Check if user already exists
    const users = JSON.parse(localStorage.getItem("users")) || {};
    if (users[email]) {
      alert("⚠ This email is already registered. Please sign in.");
      container.classList.add('signin-active');
      container.classList.remove('signup-active');
      return;
    }

    // Save new user
    users[email] = { name, password };
    localStorage.setItem("users", JSON.stringify(users));
    localStorage.setItem("lastSignupEmail", email); // auto-fill later
    alert("✅ Account created successfully! Please sign in.");

    // Auto switch to signin
    container.classList.add('signin-active');
    container.classList.remove('signup-active');

    // Auto-fill email in signin
    document.getElementById('signinEmail').value = email;
  });

  // ✅ Sign In Logic
  signinBtn.addEventListener('click', () => {
    const email = document.getElementById('signinEmail').value.trim();
    const password = document.getElementById('signinPassword').value.trim();

    if (email === "" || password === "") {
      alert("⚠ Please fill both email and password!");
      return;
    }

    const users = JSON.parse(localStorage.getItem("users")) || {};
    if (!users[email]) {
      alert("⚠ No account found with this email. Please sign up.");
      container.classList.add('signup-active');
      container.classList.remove('signin-active');
      return;
    }

    if (users[email].password !== password) {
      alert("❌ Incorrect password. Try again.");
      return;
    }

    // Login success
    localStorage.setItem("isLoggedIn", "true");
    localStorage.setItem("loggedInUser", email);
    window.location.href = "loader.html"; // change to your page
  });

  // ✅ Auto-fill last signup email in signin form
  document.addEventListener("DOMContentLoaded", () => {
    const lastEmail = localStorage.getItem("lastSignupEmail");
    if (lastEmail) {
      document.getElementById('signinEmail').value = lastEmail;
    }
  });
</script>
</body>
</html>
