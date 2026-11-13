# Task 4 — what each change brought

The files behind the table in [`../REPORT.md`](../REPORT.md). The report holds the
numbers and what I make of them; this folder holds the raw evidence, so the table can
be checked line by line.

How they were made: one Colab notebook on a T4, starting from the Task 2 pipeline
unchanged — the baseline row is that network re-trained (`mobilenet_v3_small`
pretrained, new last layer, Adam lr 1e-3, 15 epochs, no augmentation, last epoch), and
every other row changes one thing against it. Same training images, same validation
images, a fixed seed for the starting weights and for the shuffling. 8 training runs in
all, 3.6 s (the frozen one) to 26 s (the 30-epoch combination) each.

| file | what it is |
| --- | --- |
| `task4_results.csv` | the 8 runs, one row per run |
| `leaf_model_best.onnx` | the model I would put on the phone: augmentation + colour jitter + cosine + 30 epochs, seed 0, the weights of epoch 15 — the best epoch of that run |
| `model_info_best.json` | its classes and preprocessing, the same shape as `../model_info.json` |
| `check_expected_best.json` | what this model gives for `../check_image.jpg` (the notebook saved the same photo again and the two files are byte-identical), so the page's **Run the check** photo can be read against either expected file |

`leaf_model_best.onnx` is a different model from `../leaf_model.onnx` (same
architecture and size, different weights, `md5` `75e298e7…` against `53a99a8b…`), and it
reads the same way: input `image` 1×3×96×96, output `logits`, the classes and
preprocessing in the metadata.

The page next to this folder still runs `../leaf_model.onnx`, the Task 2 model. That is
deliberate: `../REVIEW.md` checks that page against `../check_expected.json`, and
swapping the model under it would make that check and the phone screenshots describe a
page that no longer exists.

## The columns of `task4_results.csv`

| column | what it is |
| --- | --- |
| `config` | the variant, `baseline (Task 2)` is the reference |
| `seed` | the training seed; the baseline and the combination ran with two, the rest with one |
| `val_last`, `val_best` | accuracy on the 62 validation photographs, on the weights of the last and of the best epoch |
| `cam_last`, `cam_best` | the same two weight sets on a camera-like set: the same 62 photographs, each copied 3 times through a random crop, a rotation and new colours, generated once with seed 1234 and reused unchanged for every run — 186 images |
| `best_epoch` | the epoch `val_best` came from |
| `seconds` | the wall time of the run |
