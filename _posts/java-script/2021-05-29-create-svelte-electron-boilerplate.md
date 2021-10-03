---
layout:     post
title:      "svelte-boilerplate"
subtitle:   " \"ansible ingo\""
date:       2021-05-29 16:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - svelte
    - electron
---



```bash
cd $HOME/Documents/servid-github/sandbox &&\
export APP_NM="svelte-electron-test" ;\
npx degit sveltejs/template $APP_NM && \
cd $APP_NM &&\
node scripts/setupTypeScript.js &&\
npm install &&\
npm run build &&\
npm install --save-dev electron &&\
npm install --save-dev cross-env &&\
npm install --save-dev npm-run-all
```

## in src/electron.js

```javascript
const { app, BrowserWindow } = require("electron")
const path = require("path")

let mainWindow

function createWindow() {
    mainWindow = new BrowserWindow({
        width: 900,
        height: 680,
    })

    const mode = process.env.NODE_ENV
    const url =
        mode === "production"
            // in production, use the statically build version of our application
            ? `file://${path.join(__dirname, "../public/index.html")}`
            // in dev, target the host and port of the local rollup web server
            : "http://localhost:5000"
    mainWindow.loadURL(url)
    mainWindow.on("closed", () => {
        mainWindow = null
    })
}

app.on("ready", createWindow)

// those two events completely optional to subscrbe to, but that's a common way to get the
// user experience people expect to have on macOS: do not quit the application directly
// after the user close the last window, instead wait for Command + Q (or equivalent).
app.on("window-all-closed", () => {
    if (process.platform !== "darwin") app.quit()
})
app.on("activate", () => {
    if (mainWindow === null) createWindow()
})
```

```bash
cd $HOME/Documents/servid-github/sandbox &&\
export APP_NM="svelte-electron-test" &&\
mkdir -p $HOME/Documents/servid-github/sandbox/${APP_NM}/src/components/TopMenu &&\
touch $HOME/Documents/servid-github/sandbox/${APP_NM}/src/components/TopMenu/SearchBar.svelte
```

```javascript
<!-- src/components/TopMenu/SearchBar.svelte -->

<header id="topmenu">
    <div class="actions">
        <input placeholder="Type your search term" />
        <button>Let's find out 🕵️‍♀️</button>
    </div>
</header>

<style>
    .actions {
        text-align: center;
        padding-top: 4px;
        padding-bottom: 4px;
        background-color: rgb(239, 239, 239);
        border-bottom: 1px solid lightgrey;
        box-shadow: 0 0 4px #00000038;
    }

    .actions input,
    .actions button {
        margin: 0;
    }
</style>
```