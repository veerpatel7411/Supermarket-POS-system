# MarketBridge POS — quick start for the shop floor

## 1. Install the app

Run `MarketBridge-POS-Setup-1.0.0-x64.exe` on the manager computer and every till/inventory computer. The installer is approximately 117 MB (112 MiB), installs the app for the computer, creates Start Menu/Desktop shortcuts and an uninstaller, and adds private-network firewall rules for local discovery/API traffic. Windows will request administrator approval. This build is not code-signed, so SmartScreen may say the publisher is unknown; only continue if you obtained the installer from your trusted supplier.

## 2. Set up the main server first

1. Open MarketBridge POS and choose **Main server / manager computer**.
2. Enter your supermarket name, administrator name, username and a strong password. The default currency/tax are KSh and 16%; review these before trading.
3. Keep the displayed six-digit pairing code for the first till. It expires after 15 minutes. The app keeps the API in the notification area while it is running and starts it again when the same Windows user signs in.
4. Sign in as the administrator. To enroll each additional device, use **Network & tills → New code** to issue another short-lived code.

The main computer must remain powered on, signed into the Windows account used during setup, and connected to the shop LAN while tills are trading. Internet access is not required.

## 3. Pair tills and stockroom computers

On each computer, choose **Till** or **Inventory computer**. Click **Search** and select the store found on the LAN, then enter its current pairing code. Discovery works on Wi-Fi and Ethernet on the same broadcast domain. A till ID such as `TILL-01` is auto-assigned; managers can rename/change it later in Network & tills.

If discovery finds nothing:

- Make sure the server app is open and both computers use the same router/VLAN.
- Disable router “AP/client isolation” for the shop's devices.
- Make sure Windows classifies the shop LAN as **Private**; installer firewall rules are limited to Private / LocalSubnet.
- Use **Enter server address manually** and type the server computer name or its current LAN IP (not its public internet address). The normal API port is `47832`.
- Never port-forward the POS service to the internet.

## 4. Ring up a sale

1. Sign in with the cashier's employee account.
2. In **Point of sale**, click a product or scan its barcode with a USB keyboard-wedge scanner. A scanner that types the barcode and presses Enter needs no special driver.
3. Change quantity with `+`/`−`. Use the discount field if permitted by shop policy.
4. Choose cash, card, M-Pesa or a split payment. Enter the M-Pesa reference when relevant; cash change is calculated by the server.
5. Confirm payment. The sale receives a unique receipt number, is written to the central database and updates stock for every connected till.

Use **Hold** to park a basket and **Resume** to reopen a held basket on its permitted till. A completed receipt can be reprinted from Sales history. Install the printer driver in Windows and choose the till's 58 mm/80 mm/standard printer in **Settings → Receipts & printer**. Verify the actual paper width and test print before opening.

## 5. If the LAN drops

The till shows **Connection to supermarket server lost** instead of implying that shared stock is current. If this till had a signed-in cashier and a recent product cache, a sale can be queued on that computer and retried when the server returns. Offline sales are idempotent and retain a unique client ID. They may oversell stock; the server records the sale and managers should review any negative quantities after synchronization. Keep the till powered on until the outbox has synced.

A brand-new login, product change, refund, supplier purchase, or central stock adjustment requires the server. Do not use an offline till as a substitute for a working LAN.

## 6. Users, permissions and backups

Create cashier, manager and inventory accounts in **Team & permissions**. Cashiers have checkout/own-sales access; managers administer reports, refunds and network operations; inventory users manage stock/catalogue/purchases. Server API authorization enforces the role.

The server keeps daily database snapshots. Use **Settings → Backup & restore → Back up database** to save a copy to another folder or USB drive. Keep at least one copy off the server and periodically test a restore. Restore replaces the current server database; close tills and preserve a separate pre-restore backup first.

## Before live trading

This release does not provide KRA/eTIMS or other fiscal-device integration and is not a certified accounting/tax system. Confirm the correct VAT setup and receipt wording locally. Pilot the installer, sales, refunds, offline sync, printer, network isolation and backup/restore on the actual shop's Windows computers and router before using it for real stock or money.
