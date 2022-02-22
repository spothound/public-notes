# Teams on Linux troubleshooting: memory leak

I have been using Teams on Linux for a few months and recently I have started to have troubles initializing it. 

I did this to find possible log files related to the service:
```
find / -name '*teams*' 2> /dev/null | grep -v "icons"
```

And found gold:

```
...
/var/lib/systemd/coredump/core.teams.1000.24dd6669b1b84c2ea684ebb7d4f2a3a0.3812.1645430810000000.zst
/var/lib/systemd/coredump/core.teams.1000.d771303a93524fda9c735db18f326638.2323.1645522605000000.zst
/var/lib/systemd/coredump/core.teams.1000.24dd6669b1b84c2ea684ebb7d4f2a3a0.2682.1645430748000000.zst
/var/lib/systemd/coredump/core.teams.1000.4066a1a450914e45a8cd89e4c18ac784.11093.1645519942000000.zst
/var/lib/systemd/coredump/core.teams.1000.4066a1a450914e45a8cd89e4c18ac784.10539.1645519906000000.zst
/var/lib/systemd/coredump/core.teams.1000.d771303a93524fda9c735db18f326638.2977.1645522642000000.zst
/var/lib/systemd/coredump/core.teams.1000.e38529d4044f43f28c8c80246b8e6a1d.2259.1645431541000000.zst
/var/lib/systemd/coredump/core.teams.1000.4066a1a450914e45a8cd89e4c18ac784.17364.1645522349000000.zst
...
```

There were lot of core-dumps reports on my system related to teams. I checked for logs as well:

```
/home/username/.config/teams
/home/username/.config/Microsoft/Microsoft Teams/media-stack/teams.msrtc-1-s516561805.blog
/home/username/.config/Microsoft/Microsoft Teams/media-stack/teams.msrtc-0-s286380614.blog
/home/username/.config/Microsoft/Microsoft Teams/logs/teams-startup.log
/home/username/.config/Microsoft/Microsoft Teams/IndexedDB/https_teams.microsoft.com_0.indexeddb.blob
/home/username/.config/Microsoft/Microsoft Teams/IndexedDB/https_teams.microsoft.com_0.indexeddb.leveldb
/home/username/.config/autostart/teams.desktop

```

So I checked this one:

```
cat "/home/username/.config/Microsoft/Microsoft Teams/logs/teams-startup.log"
Error while parsing hooks JSON. Error: "ENOENT: no such file or directory, open '/home/grg121/.config/Microsoft/Microsoft Teams/hooks.json'"
(node:3936) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.
(node:3936) MaxListenersExceededWarning: Possible EventEmitter memory leak detected. 11 ecsSettingsUpdated listeners added to [EventEmitter]. Use emitter.setMaxListeners() to increase limit
(node:3936) MaxListenersExceededWarning: Possible EventEmitter memory leak detected. 11 appInitialized listeners added to [EventEmitter]. Use emitter.setMaxListeners() to increase limit

```

I removed this configuration folder (backed up it) to check if teams would create a new one BUT THIS DIDN'T  SOLVE THE ISSUE

```
mv Microsoft\ Teams "MicrosoftTeamsBackup"
```

Teams does creates the Microsoft\ Teams folder again, but this doesn't fix the issue.

So I tried creating the missing file:

```
vi "/home/grg121/.config/Microsoft/Microsoft Teams/hooks.json"
```
With this:
```
{"enableProcessIntegrityLevel": false}
```

But still, the problem persiste.

I went to the AUR (arch linux user repository) entry for the teams package that I used to install this service on arch Linux and I saw that there are lot of people struggling with the same error. Even without having updated the service. It seems like something changed in the Microsoft Teams servers and now our clients are not working correctly on Linux by default.

The issue can be fixed adding  `--no-sandbox` to the execution of teams. In my case, I added it to the file:
```
~/.config/autostart/teams.desktop
```