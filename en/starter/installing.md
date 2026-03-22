--const express = require("express");
const fetch = require("node-fetch");
const session = require("express-session");

const app = express();
const PORT = 3000;

// Replace with your actual App ID and redirect URI
const APP_ID = "32Mm0f7hVzUo4t2eSJCBD";
const REDIRECT_URI = "https://MRdestroyer.com/callback";

app.use(session({
  secret: "supersecretkey",
  resave: false,
  saveUninitialized: true
}));

// Step 1: Login route
app.get("/login", (req, res) => {
  const oauthUrl = `https://oauth.deriv.com/oauth2/authorize?app_id=${APP_ID}&l=EN&brand=deriv&redirect_uri=${REDIRECT_URI}`;
  res.redirect(oauthUrl);
});

// Step 2: Callback route
app.get("/callback", async (req, res) => {
  const code = req.query.code;

  if (!code) return res.send("No authorization code received.");

  const response = await fetch("https://oauth.deriv.com/oauth2/token", {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: `grant_type=authorization_code&code=${code}&redirect_uri=${REDIRECT_URI}&client_id=${APP_ID}`
  });

  const data = await response.json();
  req.session.accessToken = data.access_token;

  res.send("Login successful! You can now call /accounts to see your data.");
});

// Step 3: Example API call
app.get("/accounts", async (req, res) => {
  if (!req.session.accessToken) return res.send("Not logged in.");

  const response = await fetch("https://api.derivws.com/trading/v1/options/accounts", {
    headers: { Authorization: `Bearer ${req.session.accessToken}` }
  });

  const accounts = await response.json();
  res.json(accounts);
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});

layout: page
title: Installing Express
description: Learn how to install Express.js in your Node.js environment, including setting up your project directory and managing dependencies with npm.
menu: starter
order: 1
redirect_from: "/starter/installing.html"
---

# Installing

Assuming you've already installed [Node.js](https://nodejs.org/), create a directory to hold your application, and make that your working directory.

* [Express 4.x](/{{ page.lang }}/4x/api.html) requires Node.js 0.10 or higher.
* [Express 5.x](/{{ page.lang }}/5x/api.html) requires Node.js 18 or higher.

```bash
$ mkdir myapp
$ cd myapp
```

Use the `npm init` command to create a `package.json` file for your application.
For more information on how `package.json` works, see [Specifics of npm's package.json handling](https://docs.npmjs.com/files/package.json).

```bash
$ npm init
```

This command prompts you for a number of things, such as the name and version of your application.
For now, you can simply hit RETURN to accept the defaults for most of them, with the following exception:

```
entry point: (index.js)
```

Enter `app.js`, or whatever you want the name of the main file to be. If you want it to be `index.js`, hit RETURN to accept the suggested default file name.

Now, install Express in the `myapp` directory and save it in the dependencies list. For example:

```bash
$ npm install express
```

To install Express temporarily and not add it to the dependencies list:

```bash
$ npm install express --no-save
```

<div class="doc-box doc-info" markdown="1">
By default with version npm 5.0+, `npm install` adds the module to the `dependencies` list in the `package.json` file; with earlier versions of npm, you must specify the `--save` option explicitly. Then, afterwards, running `npm install` in the app directory will automatically install modules in the dependencies list.
</div>
