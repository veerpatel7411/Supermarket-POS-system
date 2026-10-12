# Build and verification report

Build target: Windows x64, Electron 44.6.0, NSIS installer, MarketBridge POS 1.0.0. Installer output: `deliverables/MarketBridge-POS-Setup-1.0.0-x64.exe` (~117 MB / 112 MiB). The installer is unsigned; no vendor code-signing certificate was supplied.

## Checks performed in this workspace

- `npm test`: 6/6 API/database tests pass.
  - Two tills attempt concurrent stock-sensitive sales; one is rejected rather than overselling.
  - Retrying a sale with the same client ID returns the same receipt without a second stock deduction.
  - Offline outbox sync is idempotent, preserves the sale timestamp, and records negative stock for review.
  - A cashier cannot open another cashier's sale or issue a refund; the cashier dashboard and product feed omit management-only financial/network data.
  - Partial refund puts returned units back into central inventory.
  - SQLite backup and restore recover previous records.
- HTTPS/API smoke check: generated TLS certificate, certificate-pinned client request, public status, and PIN pairing.
- Browser demo smoke test in headless Chromium: sign-in, product lookup, checkout/payment, and receipt-result modal.
- UI route sweep: dashboard, POS, products, inventory, suppliers, customers, sales, purchases, reports, team, network, settings and audit log rendered without browser-console/page errors.
- `npm audit`: 0 findings at the time of build.
- `npm run build:win` under Node 22.23.3: NSIS installer emitted successfully from Linux using Wine; PE installer type and size checked.

## Not verified here

- The `.exe` was not run on a physical Windows workstation. UAC, Start Menu/Desktop shortcuts, Windows Defender Firewall rules, tray/login startup, printer-driver compatibility and Windows SmartScreen need a real Windows pilot.
- Wi-Fi/Ethernet auto-discovery could not be validated across real routers/VLANs from this sandbox. UDP broadcast was answered on loopback; some routers isolate Wi-Fi clients or suppress broadcast. Manual LAN host/IP is provided as a fallback.
- No real USB barcode scanner, card terminal, M-Pesa settlement, 58 mm/80 mm printer or fiscal device was connected.
- The project does not implement KRA eTIMS/fiscal-device integration and is not an audited accounting system.

Treat the installer as a pilot/release-candidate build until the shop-specific Windows/network/printer and backup-restore checks are complete.
