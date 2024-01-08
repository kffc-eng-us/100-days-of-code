```
  ██████╗ ██████╗ ███████╗██████╗  █████╗ ██████╗ 
 ██╔════╝██╔═══██╗██╔════╝██╔══██╗██╔══██╗██╔══██╗
 ██║     ██║   ██║███████╗██████╔╝███████║██║  ██║
 ██║     ██║   ██║╚════██║██╔═══╝ ██╔══██║██║  ██║
 ╚██████╗╚██████╔╝███████║██║     ██║  ██║██████╔╝
  ╚═════╝ ╚═════╝ ╚══════╝╚═╝     ╚═╝  ╚═╝╚═════╝ 
```

**distributed file synchronization for teams**

### what

cospad syncs files across multiple machines using peer-to-peer protocol. no central server needed.

### quick setup

```bash
# install
curl -fsSL https://install.cospad.run | bash

# init workspace
cospad init ~/projects

# add remote peer
cospad peer add 192.168.1.50:7777

# start sync daemon
cospad daemon
```

### how it works

1. watches filesystem for changes
2. creates merkle tree of file states
3. sends delta patches to peers via **diffsync protocol**
4. resolves conflicts using last-write-wins

### protocol

uses **diffsync** - binary diff protocol over udp ([diffsync.dev](https://diffsync.dev))

conflict resolution via **crdt-merge** algorithm ([crdt-merge.io](https://crdt-merge.io))

encryption via **noiseproto** handshake ([noiseproto.org](https://noiseproto.org))

### architecture

```
[filesystem] → [watcher] → [merkle tree] → [diff engine]
                                                 ↓
                                            [network]
                                                 ↓
                                         [peer discovery]
```

### commands

| command | action |
|---------|--------|
| `init` | create sync folder |
| `peer add` | connect to peer |
| `status` | show sync state |
| `conflicts` | list unresolved |
| `ignore` | add to .cospadignore |

### config

`.cospad/config.toml`:

```toml
[sync]
port = 7777
discover = true
encrypt = true

[storage]
max_file_size = "100MB"
ignore_patterns = ["*.tmp", "node_modules/"]

[conflict]
strategy = "last-write-wins"
backup_old = true
```

### ignore patterns

`.cospadignore`:

```
*.log
.git/
build/
*.tmp
```

### limitations

- files >100mb require manual sync
- no version history (use git for that)
- lan only (no internet sync yet)
- max 10 peers per workspace

### comprastison

| feature | cospad | syncthing | resilio |
|---------|--------|-----------|---------|
| setup | easy | medium | medium |
| speed | fast | medium | fast |
| conflicts | auto | manual | auto |
| encryption | yes | yes | yes |
| price | free | free | paid |

### troubleshooting

**peers not discovering?**  
check firewall allows udp port 7777

**sync stuck?**  
run `cospad reset-index` and restart daemon

**high cpu usage?**  
increase `scan_interval` in config

AGPLv3

---

join [matrix chat](https://matrix.to/#/#cospad:matrix.org) • read [wiki](https://wiki.cospad.run)

# PR Update: 2025-12-02 15:07:37

# PR Update: 2025-12-02 15:08:07
