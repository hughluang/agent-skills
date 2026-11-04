# Open the prototype

Get the file onto a screen the user can click before the first question.

`SSH_CONNECTION` unset: the file is on the same machine as the IDE. The user opens the editor tab and chooses Open in Browser.

`SSH_CONNECTION` set: the file is on the remote host and the IDE browser is on the client. A double-click, a `file://` URL, and `127.0.0.1` reach the client, not this file. For the rest of the run, this step replaces the prototype skill's double-click / no-server rule.

Serve only the directory that contains the HTML, on one free port, bound to all interfaces:

```bash
python3 -m http.server <port> --bind 0.0.0.0
```

Keep that process for later rounds. The URL host is field 3 of `SSH_CONNECTION`, the address the client already used to connect. The path is the file name. Open `http://<that-host>:<port>/<file>` in the IDE browser, and give the user the same URL. After each round, the user refreshes that URL.

Done when that URL loads in the IDE browser. Stop this server when the spec is written.
