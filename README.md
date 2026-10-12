# MarketBridge POS

A Windows desktop POS built for a small supermarket LAN: one computer owns the central database and API; every till and inventory station talks to that API over the local network. Wi-Fi, Ethernet, and a mixed LAN are all supported—the app binds to all IPv4 interfaces and discovers the server with UDP broadcast. Internet access is not used for normal checkout.

## What is in this repository

- Electron desktop shell (Windows installer target; the user does not install Node or Python).
- Server-owned SQLite database, accessed only through the API (never a shared network database file).
- TLS local API with a pinned server certificate after discovery/pairing.
- Local-network discovery, PIN-based till pairing, sessions, roles, audit records, and device blocking.
- POS sales, stock movements, barcode keyboard-wedge input, mixed tenders, hold/resume, refunds, reports, products, suppliers, customers, purchases, backup/restore, and offline sale outbox.
- Browser demo mode for reviewing the screens without installing the Windows application.

## Windows installer build

The Windows x64 NSIS installer has been built and is in `deliverables/MarketBridge-POS-Setup-1.0.0-x64.exe` (about 117 MB / 112 MiB). It includes Electron, the application and runtime libraries; the end user does **not** install Python, Node, Java, a database server, Docker, or development tools. The installer is per-machine and requests Windows administrator approval to install and add Private/LocalSubnet firewall rules.

Developers can rebuild with Node.js 22.12+ and `npm install && npm run build:win`. Cross-building the Windows NSIS installer from Linux also uses Wine. This repository does not contain a production code-signing certificate. Windows SmartScreen may therefore show an “unrecognized publisher” warning until a trusted vendor certificate is used to sign the installer.

## First run

Choose **Main server / manager computer**, enter the shop and administrator details, and keep that computer powered on. Use the pairing code on the Network screen to enroll each till or inventory computer. Discovery works on the same broadcast domain; the manual host/IP option is available if the router blocks UDP broadcast. The installer opens the service ports on Windows' private firewall profile.

The first-run administrator password is chosen by the user; no universal default password is installed. KSh and Africa/Nairobi are the initial locale defaults and are editable.

## Important deployment notes

- The central database is stored under the Windows profile's MarketBridge application data folder on the server computer. Tills do not open that file; all sales and inventory writes go through the server API. Use the same Windows account for the server at each login.
- The database engine is embedded SQLite rather than a separately installed PostgreSQL service. It keeps deployment self-contained and is serialized behind one local API process. It is **not** a database file to place on a network share.
- The server API runs in the desktop app's tray/background process and is enabled to start at that Windows user's logon. This release does not register a separate Windows service; tills are disconnected while the server app or Windows user session is stopped.
- Offline till sales are kept in the till's local outbox and submitted with a unique client transaction ID when the server returns. Offline sales can temporarily oversell stock; the server records them and flags resulting negative stock for manager review.
- Use a wired LAN or a dedicated/secured Wi-Fi network. The app provides application authentication and TLS certificate pinning, but the first device pairing PIN should be exchanged in person. Do not forward the API ports to the public internet.
- Backups are local SQLite snapshots. Copy a second backup to removable media or another computer regularly. Test restores before relying on a backup as your only recovery plan.
- Receipt printing uses installed Windows printers and the system print dialog when no printer is selected. Verify the target thermal printer and paper width before live trading.

## Demo

`npm run web:demo` launches a seeded, disposable browser demo. It is for evaluation only and is not a production deployment. The demo login is shown on its sign-in screen.

## Scope and release status

This is a tested functional release candidate/source deliverable, not a certified commercial accounting or fiscal-receipt product. The installer has been cross-built, but was not executed on a physical Windows/shop-network test bed in this workspace. Before live use, install it on the target Windows version, test wired/Wi-Fi discovery, printer drivers, failure/recovery and backup restore, set employee accounts, and validate VAT/fiscal receipt rules with a local accountant or tax authority. The app does not claim fiscal-device or KRA eTIMS integration.
Download EXE file from https://drive.google.com/file/d/1HhEu984MnOkhRDgz2rlQRZY0R2Sn0D3N/view?usp=sharing
