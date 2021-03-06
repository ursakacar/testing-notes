# Notifications

## Prepare notification files

1. Serve the content of the folder where you have notification files by running: `python -m SimpleHTTPServer 8080` or `python3 -m http.server 8080`
1. Use http://localhost.run/ to share localhost (run `ssh -R 80:localhost:8080 ssh.localhost.run`)
1. Copy the https url from the previous command (it will be something like https://ursa-6d4aba57.localhost.run)
1. **note!** it has to be served over HTTPS!

## Way 1 (no code changes)

### Chrome and Opera

1. Install the build (You now have 60 seconds before **notifications.json** is requested so act fast)
1. Open the popup UI from the ABP icon. Right click, inspect.
1. In that console run: `browser.runtime.sendMessage({type: "prefs.set", key: "notificationurl", value: "https://url-where-you-serve-notifications/notification.json"})` (replace value with your own url)
1. Wait for the 60s mark.
1. The notification should appear in ABP.

If you want to trigger another notification, you need to remove the extension and reinstall it, then repeat the steps above.

### Firefox

1. Same as for Chrome
1. Just do black magic and hope for the best. Maybe follow steps here: https://discourse.mozilla.org/t/how-can-i-inspect-the-popup-of-an-addon/34134/2
1. Same as for Chrome from here on


## Way 2 (code changes)

Originally, the **notifications.json** is first requested after 1 minute of installing the extension, and then again after 24 hours, when it expires.

Here, we will serve the notification files ourselves, change the url from where the extension fetches the **notification.json** file and change the delay intervals, so the notifications are triggered instantly.


### Prepare the ABP build

* in unpacked build, open the `background.js file`
* change the notification url to the url where your notifications are served

**Before:**
```yml title=original
defaults.notificationurl = "https://notification.adblockplus.org/notification.json";
```

**After:**
```yml title=changed
defaults.notificationurl = "https://your-url.localhost.run/notification.json";
```

* seach for **@fileOverview Handles notifications**
* add `MILLIS_IN_SECOND`:

**Before:**
```yml title=original
const {MILLIS_IN_MINUTE, MILLIS_IN_HOUR, MILLIS_IN_DAY} = __webpack_require__(15);
```

**After:**
```yml title=changed
const {MILLIS_IN_SECOND, MILLIS_IN_MINUTE, MILLIS_IN_HOUR, MILLIS_IN_DAY} = __webpack_require__(15);
```

* change the delays:

**Before:**
```yml title=original
const INITIAL_DELAY = 1 * MILLIS_IN_MINUTE;
const CHECK_INTERVAL = 1 * MILLIS_IN_HOUR;
const EXPIRATION_INTERVAL = 1 * MILLIS_IN_DAY;
```

**After:**
```yml title=changed
const INITIAL_DELAY = 2 * MILLIS_IN_SECOND;
const CHECK_INTERVAL = 5 * MILLIS_IN_SECOND;
const EXPIRATION_INTERVAL = 5 * MILLIS_IN_SECOND;
```

* Load the extension- notification should be triggered within seconds
* Change the content(by simply copy pasting) of the **notification.json** file, to trigger different notifications
* The extension will only show a notification with unique **id**, that hasn't been shown before, so make sure the **id** on each **notification.json** save is unique
* Notification requests can be observed from the terminal running the http server

## Copy paste for testing notifications

```
// PLACEHOLDER
defaults.notificationurl = "https://ursa-1d4ffb95.localhost.run/placeholder-notification.json";
// DEFAULT
//defaults.notificationurl = "https://ursa-1d4ffb95.localhost.run/default.json";
// NEWTAB
// defaults.notificationurl = "https://ursa-1d4ffb95.localhost.run/newtab.json";
```

## Bubble UI notifications

Inspect the popup, paste code in console:

```
dispatchEvent(new CustomEvent("extension:notification", {
  detail: {
    type: "information", // or "critical" or "normal" or whatever it is you want
    texts: {
      title: "Title for a notification",
      message: "There is something to read here"
    }
  }
}));
```

**Some more examples I use often:**
```
dispatchEvent(new CustomEvent("extension:notification", {
  detail: {
    type: "normal",
    links: ["contribute", "abp:day1"],
    texts: {
      title: "Title for a notification",
      message: "Message for <a>Link1</a> notification <a>day1</a>",
    }
  }
}));
```

```
dispatchEvent(new CustomEvent("extension:notification", {
  detail: {
    type: "information",
    texts: {
      title: "Title for a long long long very long and not very useful and so on notification",
      message: "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc scelerisque in tellus et consequat. Praesent scelerisque55 congue felis at faucibus. Vestibulum sit Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc scelerisque in tellus et consequat. Praesent scelerisque congue felis at faucibus. Vestibulum sit Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc scelerisque in tellus et consequat. Praesent scelerisque congue felis at faucibus. Vestibulum sit"
    }
  }
}));
```
