# AR Hand-Controlled Model

Scan a QR code → phone camera opens → point at the printed target image →
your 3D model appears and locks onto it → move your open hand left/right/up/down
in the air to rotate the model, no screen touch needed.

## How it works

1. **MindAR** tracks your printed target image through the phone camera and
   anchors the 3D model to it.
2. **MediaPipe Hand Landmarker** reads the same camera feed (no second camera
   request) and tracks your hand position.
3. **Three.js** renders the model and applies rotation driven by hand position.

## Folder structure

```
ar-hand-project/
├── index.html
├── README.md
└── assets/
    ├── target.mind   ← you generate this (see below)
    └── model.stl     ← your 3D model (from Polycam or elsewhere)
```

Create the `assets/` folder and drop your two files in before deploying.

## Step 1 — Compile your target image

MindAR needs a compiled `.mind` file, not the raw photo.

1. Take a clear, well-lit, high-contrast photo of the item/artwork you want as
   the AR trigger (same guidance as your museum project: plain background,
   no glare).
2. Go to the MindAR image target compiler:
   https://hiukim.github.io/mind-ar-js-doc/tools/compile
3. Upload your photo, compile it, and download `target.mind`.
4. Place it at `assets/target.mind`.

## Step 2 — Add your 3D model

1. Scan your object with Polycam (Object mode) as discussed, export as `.stl`.
2. Rename it `model.stl` and place it at `assets/model.stl`.
3. STL files carry geometry only — no color or texture — so the model
   renders in a single flat color. Change it by editing the `color: 0x9fe1cb`
   line near the model loader in `index.html` (search for `MODEL_SRC`).
4. If the model looks too big/small, upside-down, or lying on its side,
   adjust the `scale` and `rotation.x` lines in the same block. STL exports
   are often Z-up rather than Y-up, which is why a 90° rotation is applied
   by default — tweak it if your model still looks wrong.

## Step 3 — Host it

This needs to run over HTTPS for camera access to work (a phone browser will
refuse camera permission on plain HTTP, except localhost).

Your repo is already set up: https://github.com/Tobythambi/LiveAR
1. Push this folder's contents (`index.html`, `README.md`, `assets/`) to the
   root of that repo.
2. In the repo, go to Settings → Pages, set Source to the `main` branch and
   `/` (root), then Save.
3. Your live URL will be:
   `https://tobythambi.github.io/LiveAR/`

## Step 4 — Generate the QR code

Once the page is live, generate a QR code pointing at that URL using any
free QR generator (e.g. https://www.qr-code-generator.com), or:

```bash
npm install -g qrcode
qrcode "https://tobythambi.github.io/LiveAR/" -o qr.png
```

Print the QR code next to your physical target image/object.

## Notes

- Hand tracking needs decent, even lighting — direct light on the hand,
  not backlit.
- If performance feels laggy on an older phone, this is the expected
  tradeoff of running MindAR + MediaPipe simultaneously (see earlier
  discussion) — reducing the model's triangle count helps.
- Adjust rotation sensitivity by changing the multipliers in `trackHands()`
  (`* 6` and `* 3` in `index.html`) — higher = more sensitive to hand movement.
