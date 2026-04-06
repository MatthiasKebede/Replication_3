Loading weights: 100% 199/199 [00:00<00:00, 1273.96it/s, Materializing param=pooler.dense.weight]
testing on project:  dubbo
Class Weights: [1. 1.]

 Epoch 1 / 20

Evaluating..

Training Loss: 0.681
Validation Loss: 0.637

 Epoch 2 / 20

Evaluating..

Training Loss: 0.568
Validation Loss: 0.500

 Epoch 3 / 20

Evaluating..

Training Loss: 0.434
Validation Loss: 0.450

 Epoch 4 / 20

Evaluating..

Training Loss: 0.353
Validation Loss: 0.446

 Epoch 5 / 20

Evaluating..

Training Loss: 0.305
Validation Loss: 0.466

 Epoch 6 / 20

Evaluating..

Training Loss: 0.273
Validation Loss: 0.475

 Epoch 7 / 20

Evaluating..

Training Loss: 0.249
Validation Loss: 0.455

 Epoch 8 / 20

Evaluating..

Training Loss: 0.216
Validation Loss: 0.421

 Epoch 9 / 20

Evaluating..

Training Loss: 0.194
Validation Loss: 0.415

 Epoch 10 / 20

Evaluating..

Training Loss: 0.162
Validation Loss: 0.435

 Epoch 11 / 20

Evaluating..

Training Loss: 0.140
Validation Loss: 0.465

 Epoch 12 / 20

Evaluating..

Training Loss: 0.128
Validation Loss: 0.434

 Epoch 13 / 20

Evaluating..

Training Loss: 0.111
Validation Loss: 0.476

 Epoch 14 / 20

Evaluating..

Training Loss: 0.089
Validation Loss: 0.474

 Epoch 15 / 20
Traceback (most recent call last):
  File "/content/Flakify/src/Flakify_predictor_per_project.py", line 452, in <module>
    train_loss, _ = train()
                    ^^^^^^^
  File "/content/Flakify/src/Flakify_predictor_per_project.py", line 247, in train
    preds = preds.detach().cpu().numpy()
            ^^^^^^^^^^^^^^^^^^^^
KeyboardInterrupt
