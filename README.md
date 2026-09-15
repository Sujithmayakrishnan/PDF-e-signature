# NSR-Esign



A small, portable Windows application for stamping a **signature
image** plus an editable **Name / Place / Date / Time** text block
onto a PDF — similar to a simple e-signature tool, but it runs
entirely on your own computer. Your PDFs and signature image are
never uploaded anywhere, and no account is needed. The one optional
exception is described below under "Internet-based location & time".

## What it does

1. Open any PDF and preview its pages.
2. Load an image of your signature once (a PNG with a transparent
   background looks best, but any image works).
3. Drag the signature box and the Name/Place/Date/Time text box
   directly on the page preview to exactly where you want them —
   resize them by dragging the small square at the bottom-right
   corner of each box.
4. Choose which page(s) to stamp: the current page, all pages, or a
   custom list like `1,3,5-7`.
5. Click **Export Signed PDF** to save a new PDF with everything
   burned in. Your original PDF file is never changed.

The name, date/time and location can all be edited freely before
exporting, and the app remembers your last-used signature image, name
and default location between sessions (stored in a small settings
file next to the app — nothing is written anywhere else on your
computer).


## Check 

Fix on that laptop (pick one):

If Python isn't actually installed there: install it from python.org, not the Microsoft Store link Windows suggests:
https://www.python.org/downloads/
On the first install screen, tick "Add python.exe to PATH".
If Python IS already installed there and you still see this: the Store shortcut is intercepting the command anyway. Turn it off:
Settings → Apps → Advanced app settings → App execution aliases
Switch OFF the toggles for python.exe and python3.exe.

Install Download the free Microsoft Visual C++ Redistributable (x64) installer directly from Microsoft: https://aka.ms/vs/17/release/vc_redist.x64.exe

## 1. Building the app (one-time setup, on Windows)

You need Python installed once to *build* the app. After that, you
don't need Python (or an internet connection) to run it.

1. Install Python 3.10 or newer from <https://www.python.org/downloads/>
   if it isn't already installed. On the first setup screen, tick
   **"Add python.exe to PATH"** before clicking Install.
2. Put these files in the same folder:
   - `pdf_signer_app.py`
   - `requirements.txt`
   - `build_portable.bat`
   - `installer.iss`
3. Double-click **`build_portable.bat`**. It will install the
   required libraries (PyMuPDF, Pillow, PyInstaller), build the app,
   and then automatically build a proper Windows installer too
   (downloading and silently installing **Inno Setup** — a free,
   widely-used tool for building Windows installers — if it isn't
   already on this computer; this needs internet access on this build
   computer only, and if it can't complete for any reason the portable
   `.exe` below is still a complete result on its own). This takes a
   few minutes the first time.
4. When it finishes, you'll have two things:
   ```
   dist\NSR-Esign.exe            <- portable, just double-click to run
   Output\NSR-Esign Setup.exe    <- proper installer, for sharing
   ```
   The **portable .exe** needs no installation — copy it anywhere
   (a USB drive, another PC, a shared folder) and double-click it.
   The **installer** is the nicer way to hand the app to other people:
   it adds a Start Menu entry and a clean uninstaller listed in
   "Add or remove programs", and needs no administrator rights to
   install (it installs just for the person running it, under their
   own user profile).

You only need to repeat this build step if you want to change or
update the app later. Everyday use only needs one of the two files
above.

## 2. Using the app

1. Double-click **`NSR-Esign.exe`**.
   - The very first launch can take a few seconds longer than usual
     (Windows is unpacking the app into a temp folder) — this is
     normal for a single-file portable app.
   - Windows SmartScreen may show a blue "Windows protected your PC"
     notice the first time, because the app isn't digitally signed.
     Click **More info → Run anyway**. This is expected for any
     unsigned app you build yourself and is safe for something you
     built from your own source code.
2. Click **Open PDF** and choose the document you want to sign.
3. Click **Load Signature Image** and choose your signature file
   (PNG/JPG). It will appear as a draggable box on the page.
4. Fill in the **Name** field (the signer's name — it's stamped as
   its own line above Place/Date/Time) and the **Location** field
   (e.g. "Kopargaon"), then click **Insert / Refresh Stamp Text** —
   this fills in today's date and the current time automatically.
   You can also edit the text box freely by hand.
5. Drag the blue box (signature) and the red dashed box (text) to
   where you want them on the page. Drag the small square at each
   box's bottom-right corner to resize it. Click a box once and use
   the arrow keys for fine, pixel-level adjustments (hold Shift for
   bigger steps).
6. Use **Prev / Next** to check other pages — your placement is
   remembered and applied proportionally across pages.
7. Under **Pages to stamp**, choose whether to stamp only the current
   page, all pages, or a custom list (e.g. `1,3,5-7`).
8. Click **Export Signed PDF**, choose where to save, and you're done.
   The original file is left untouched.

## 3. Internet-based location & time (optional)

Instead of typing the location by hand and trusting this computer's
own clock, you can click **Detect Location & Time (Internet)** in the
side panel. If this computer currently has internet access, it will:

- fill in the **Location** field with an approximate place name
  (city/region/country) based on this computer's public IP address;
- capture latitude/longitude for that location — tick **"Include
  latitude/longitude in stamp"** to add a `Lat/Long: ...` line to the
  stamp text;
- use an **internet-verified time** for the "Time:" line (taken from
  the timestamp returned by the lookup service, not from this
  computer's own clock) and mark it `(internet-verified)` in the
  stamped text, so it's clear at a glance the time wasn't just typed
  by hand or read off a possibly-wrong system clock.

If there is **no internet connection**, or the lookup fails for any
reason (blocked by a firewall, service temporarily down, etc.), the
app tells you so in the status line under the button and simply falls
back to manual location entry and this computer's system clock — the
rest of the app works exactly as before, fully offline.

**Privacy note:** clicking this button sends this computer's public IP
address to a third-party IP-geolocation lookup service (tries
`ipapi.co`, then `ipwho.is` as a fallback — both are free, no-account,
no-API-key services commonly used for this purpose) in order to look
up an approximate location. No PDF content, signature image, or file
name is ever sent — only the network request your computer makes to
that lookup service, the same as visiting any website. This only
happens when you explicitly click "Detect Location & Time (Internet)";
it is never triggered automatically. If your institution's network
blocks these domains, the button will simply fail over to manual entry.

If you edit the Location field by hand after a successful detection,
any previously-detected latitude/longitude is cleared automatically
(since it may no longer match what you typed) — the internet-verified
time is kept.

## 4. Exact GPS coordinates via your browser (optional)

The location above is IP-based, which is only ever approximate —
sometimes accurate to a city, sometimes off by a fair distance. For a
much more precise fix, click **Get Exact GPS via Browser**. When you
do:

1. The app briefly starts a small local helper page on this computer
   only (not reachable from outside it) and opens it in your normal
   web browser.
2. That page asks your browser for the device's exact location — the
   same permission prompt you'd see on any map or delivery website.
   **Click Allow.** Your browser uses GPS/Wi-Fi-based positioning where
   available, which is typically accurate to a few metres, not
   kilometres.
3. The page sends the result straight back to the app and tells you
   you can close the tab. Switch back to **NSR-Esign** — the
   Location field, coordinates, and accuracy radius are filled in
   automatically, and "Include latitude/longitude in stamp" is ticked
   for you. The app also looks up a place name for those exact
   coordinates in the background and fills that in once found.
4. The stamped text marks these as `(GPS, ±N m)` so it's clear at a
   glance they're a precise device fix, not an IP-based guess.

If you deny the permission prompt, or there's no internet connection,
or the browser doesn't respond within two minutes, the app tells you
in the status line and nothing changes — go back to typing the
location by hand, or use the approximate IP-based detection instead.

**Privacy note:** nothing about this step ever leaves your own
computer except two things, both sent only after you've clicked the
button and your browser has captured a location: (1) the exact
coordinates are sent to OpenStreetMap's free **Nominatim** service
purely to translate them into a place name — you can skip this by
simply not waiting for it, the coordinates are already filled in
immediately; (2) your browser's own request to fetch the small helper
page from `127.0.0.1` never leaves this computer at all. As always, no
PDF content or signature image is ever sent anywhere.

If your Windows Firewall shows a prompt the first time you click this
button (about the app listening on a local port), that's expected —
it's this computer talking to itself over `127.0.0.1` so your browser
can hand the location back to the app; it is not reachable from your
network or the internet.

## 5. Tips

- **Signature image**: for the cleanest look, use a PNG with a
  transparent background (e.g. a signature photographed on white
  paper, background removed). A plain photo on a white background
  also works — it will just show a small white rectangle behind the
  signature.
- **Name field**: type the signer's name here — it's stamped as its
  own "Name: ..." line above Place/Date/Time. Leave it blank to skip
  it (it's optional).
- **Font**: the stamped text uses Arial at size 8pt by default (a
  compact, professional size for a signature block) — change the size
  in the side panel if you'd like it larger or smaller.
- **Bold / size / ink color**: adjust these in the side panel before
  exporting — they apply to the Name/Place/Date/Time text.
- **Only need a date stamp, no signature?** Untick "Include signature
  image" and export — only the text will be stamped (and vice versa).
- Your last-used signature image, signer name, and default location
  are remembered automatically for next time (stored in
  `pdf_sign_place_settings.json`, created next to the `.exe`).
- **If the text box is too small for what's typed in it** (a long
  location, a 4th "Lat/Long" line, Bold text, a larger font size), the
  app automatically shrinks the exported text just enough to make it
  fit rather than silently leaving part of it out — after exporting
  you'll see a note if this happened. If the text box is so small that
  even the smallest size still doesn't fit, the app tells you exactly
  which page(s) and asks you to drag the box bigger before exporting
  again — it will never silently drop the date/time from your PDF.

## Notes for later changes

- All the app logic lives in one file, `pdf_signer_app.py`, built with
  Python's built-in Tkinter for the interface and
  [PyMuPDF](https://pymupdf.readthedocs.io/) for reading/writing PDFs.
- Antivirus tools sometimes flag PyInstaller `--onefile` executables
  as suspicious purely because of how they self-extract — this is a
  well-known false positive with PyInstaller apps in general, not a
  sign of anything wrong with this app's source code (which you can
  read yourself in `pdf_signer_app.py`).
