# Network activity and permissions: FreexStudio Connector 2.2.0

FreexStudio Connector has no account, no analytics, no crash reporting and no update check.
It makes no HTTP or HTTPS requests.

## What it sends

| When | What | To | You can change it |
|---|---|---|---|
| After it changes the route, and when it refreshes status, if **Verify connection** is on (default on) | One ICMP echo request (`/sbin/ping -c 1`) | `8.8.8.8` by default | Preferences → Network → Ping target, or turn off Verify connection |

Nothing else leaves your Mac. The app reads local network state with the macOS tools
`netstat`, `ipconfig`, `ifconfig` and the SystemConfiguration and CoreWLAN frameworks; those
reads stay on your Mac.

## What it asks for

| Permission | Why | When |
|---|---|---|
| Administrator password (macOS prompt) | To change the default route with `/sbin/route`. The command runs through `osascript ... with administrator privileges`, so macOS asks you (it may remember the password for a few minutes) | Only when you start a connection |
| Location (Wi-Fi network name) | macOS only reveals the current Wi-Fi name to apps with location permission. The app does not collect or store your location | Only if you use Wi-Fi hotspot mode and grant it in Preferences |
| Apple Events | Needed for the administrator prompt above | With the route change |
| Login item | Starts the app at login | Only if you turn on Launch at login |
| Notifications | Connection status messages | Only if you allow notifications |

## Confirm it yourself

With an outbound firewall such as LuLu or Little Snitch running, use the app normally. You
should see **no** outgoing TCP or UDP connection from FreexStudio Connector. The ping is ICMP,
which app firewalls usually do not list; to watch it, run this while you connect:

```sh
sudo tcpdump -n icmp
```

You should see one echo request to your ping target per check, and nothing else from the app.
If you see anything else, report it privately as described in [SECURITY.md](SECURITY.md).
