# MLR Exercise 8 — leaf classifier in the browser

**Live page: <https://Laura-CMOS.github.io/mlr-ex08/>**

Open the link on a phone (or scan the QR code at the bottom of the page) and point the
rear camera at a leaf. The page shows the probability of each of the four species,
updated live. There is also a photo-upload button as a fallback.

## What runs where

| step | where | what |
| --- | --- | --- |
| training | Colab, `ex08_student.ipynb` Task 2 | `mobilenet_v3_small` pretrained on ImageNet, last layer replaced by 4 outputs, 15 epochs of Adam (lr 1e-3) on 244 photographs, validation on 62 |
| export | the same notebook, step 2e | `torch.onnx.export` → `leaf_model.onnx` (6.3 MB, opset `ai.onnx` v20, input `image` [1,3,96,96], output `logits`), with the classes and the preprocessing stored inside as metadata |
| inference | **your phone's browser** | `onnxruntime-web` 1.31.1 in WebAssembly. No server, no Python, nothing installed |

The exported file holds the weights of the **last** (15th) epoch — the notebook's Task 2
loop does not keep the best epoch. Validation accuracy in that epoch was **0.774**, the
best of the run was 0.839 in epoch 13; "keeping the weights of the best epoch" is one of
the improvements measured in Task 4.

## The preprocessing, which has to match the notebook

Exactly the two transforms of the notebook, in this order:

1. central **square** crop of the frame, resized to `input_size` × `input_size` (96 × 96);
2. `ToTensor`: RGB bytes **÷ 255**, then channels first — `(H, W, 3)` → `(3, H, W)`;
3. `Normalize`: `(value − mean) / std` per channel, with the ImageNet
   `mean = [0.485, 0.456, 0.406]`, `std = [0.229, 0.224, 0.225]`;
4. fed as one `float32` tensor of shape `1 × 3 × 96 × 96`;
5. the network returns **logits** (no softmax — `nn.CrossEntropyLoss` applied it during
   training), so the page applies the softmax itself.

All numbers come from `model_info.json`, which was written next to the model, so the page
follows the model rather than a hardcoded copy of its settings.

## Files

| file | what it is |
| --- | --- |
| `index.html` | the whole page: camera, preprocessing, inference, results, QR code |
| `leaf_model.onnx` | the trained network (Task 2e of the notebook) |
| `model_info.json` | classes, input size, mean, std — what the page reads |
| `check_image.jpg` | a validation photo (96 × 96) used for the numeric check |
| `check_expected.json` | the probabilities the notebook computed for that photo |
| `qrcode.js` | Kazuhiko Arase's QR generator (MIT), vendored so the QR is drawn locally |
| `REVIEW.md` | my review of the page, written by me (Task 2f) |
| `REPORT.md` | my report on the improvements, written by me (Task 4) |

## Checks already made

* The **Run the check** button runs `check_image.jpg` through the page and compares it
  with `check_expected.json`. Loaded from the published address in a desktop browser, the
  largest difference is **0.000000** — the page reproduces the notebook to three decimals.
  (A 96 × 96 source is copied without resampling, so the pixels are identical to the ones
  the notebook saw.)
* The page asks for **10 resources, every one a download**: 6 from this repository
  (the page, the model, `model_info.json`, `qrcode.js`, and the two check files once the
  check has been run), 3 from the jsDelivr CDN (the library, its WebAssembly glue and the ~15 MB
  engine, cached after the first visit), plus the tab icon the browser requests itself.
  No photograph is sent anywhere — there is no upload call in `index.html`, and the browser
  console stays empty (0 messages, 0 exceptions).
* `ort.env.wasm.numThreads = 1` because GitHub Pages sends no cross-origin isolation
  headers, so `SharedArrayBuffer` is unavailable and a threaded engine would never start.

## Run it locally

GitHub Pages aside, any static server works — `getUserMedia` needs `http://localhost` or
HTTPS, not `file://`:

```
python -m http.server 8765
```

then open <http://localhost:8765/>.
