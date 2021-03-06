# Translation testing

## What's the deal

Before release, new translations should be checked, to prevent instances of translations breaking page layout.

Such visual checks for new translations can not be automated, but we can at least automate opening all of those pages in different locales.


## Scripts

Located here: https://github.com/ursakacar/scripts-and-scraps/tree/master/translation-testing

`setup-test-translations.sh` clones abpui repo, switches to selected branch and starts up the test server.
`/test-translations.sh` script prompts the user if they want to open translated pages for each extension page. If yes, a tab is opened for each core locale in Chrome and Firefox.


### How to run

1. Copy both scripts to a folder
1. Make them executable with `chmod +x name-of-the-script.sh`
1. Run `./setup-test-translations.sh`
1. After the test serves is running, run `./test-translations.sh` in a new terminal

