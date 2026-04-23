| rank_by_global_iou | report_label | model | loss | class_weights | learning_rate | epochs_run | checkpoint_epoch | best_threshold | global_iou_water | global_f1_water | precision | recall | true_water_frac | pred_water_frac | collapse_check |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Exp 6: Residual Attention U-Net, auto WCE + Dice 0.25 | Residual Attention U-Net | Weighted CE + Dice 0.25 | auto | 8e-4 | 74 | 54 | 0.450 | 0.548 | 0.708 | 0.724 | 0.693 | 0.110 | 0.106 | No |
| 2 | Exp 2: Small U-Net, manual WCE [1,3], LR=1e-3 | Small U-Net | Weighted CE | manual [1,3] | 1e-3 | 35 | 20 | 0.550 | 0.536 | 0.698 | 0.757 | 0.648 | 0.110 | 0.094 | No |
| 3 | Exp 1: Small U-Net, auto WCE, LR=1e-3 | Small U-Net | Weighted CE | auto | 1e-3 | 24 | 16 | 0.450 | 0.528 | 0.691 | 0.722 | 0.662 | 0.110 | 0.101 | No |
| 4 | Exp 5: Residual Attention U-Net, auto WCE | Residual Attention U-Net | Weighted CE | auto | 1e-3 | 80 | 74 | 0.550 | 0.521 | 0.685 | 0.737 | 0.639 | 0.110 | 0.096 | No |
| 5 | Exp 4: Small U-Net, auto WCE + Dice 0.25 | Small U-Net | Weighted CE + Dice 0.25 | auto | 1e-3 | 40 | 20 | 0.550 | 0.513 | 0.678 | 0.724 | 0.637 | 0.110 | 0.097 | No |
| 6 | Exp 3: Small U-Net, auto WCE, LR=5e-4 | Small U-Net | Weighted CE | auto | 5e-4 | 32 | 20 | 0.600 | 0.491 | 0.659 | 0.773 | 0.573 | 0.110 | 0.082 | No |
