# MLR Exercise 08 — Leaf Classifier in the Browser (onnxruntime-web)

**Live app: <https://hykacek.github.io/mlr-ex08/>**

A single, fully client-side `index.html` that runs a trained 4-class leaf
classifier (apple / cherry / chestnut / maple) entirely inside a mobile phone
browser — Safari on iOS and Chrome on Android. There is **no server-side
processing**: the ONNX model runs on the device via
[onnxruntime-web](https://onnxruntime.ai/docs/tutorials/web/) (WASM backend,
loaded from jsDelivr CDN).

Point the page at this URL (QR code at the bottom of the page does it for you):

## What it does

| Feature | Implementation |
|---|---|
| Rear camera stream | `getUserMedia` with `facingMode: { exact: "environment" }`, falling back to `environment` → `ideal: environment` → any camera. `playsinline muted` for iOS Safari. |
| Preprocessing (identical for camera frames & uploaded photos) | central-square crop → resize to **96×96** (`model_info.json` `input_size`) → RGB ÷ 255 → per-channel `(x − mean)/std` with `mean=[0.485, 0.456, 0.406]`, `std=[0.229, 0.224, 0.225]` in RGB order (PyTorch `transforms.Normalize`) → `1×3×96×96` NCHW `float32` tensor |
| Inference | `onnxruntime-web` WASM (1 thread — Pages has no COOP/COEP headers). The model's output tensor (`logits`) is **not** softmaxed, so the app applies a numerically-stabilised softmax. |
| Live output | real-time-updated probability bar + percentage for **every** class, top class starred, fps counter |
| Photo upload check | any photo goes through the identical pipeline; uploading `check_image.jpg` compares against `check_expected.json` and shows a per-class table with PASS/FAIL (required agreement: 3 decimal places; actual max deviation ≈ 1e-7) |
| QR code | generated **locally in the browser** with `qrcode-generator` (CDN) as inline SVG — no third-party QR image APIs — next to a clickable link, both encoding the Pages URL above |

## Files

- `index.html` — the entire app (HTML + CSS + JS, single file)
- `leaf_model.onnx` — trained model, input `image` `[1,3,96,96] float32`, output `logits` `[1,4]`
- `model_info.json` — class names, input size, normalization mean/std (fetched at runtime)
- `check_image.jpg` — 96×96 verification image
- `check_expected.json` — expected probabilities for `check_image.jpg`

## Verification

With `check_image.jpg` uploaded, the page prints a table of
page-computed vs. expected probabilities per class and
`max |Δ|`, passing when every class agrees to at least three decimal places.
Reference pipeline (numpy + onnxruntime, same crop/resize/normalize/softmax
order) reproduces `check_expected.json` to `max |Δ| ≈ 9.6e-11`, and the
in-browser result matches at `max |Δ| ≈ 1e-7`.

## Run locally (optional)

Any static server works, e.g. `python3 -m http.server` in this folder, then open
`http://localhost:8000`. Camera access needs a secure context — `localhost` or
the https Pages URL.
