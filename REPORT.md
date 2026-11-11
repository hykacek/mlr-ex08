┌────────────────────────────────┬─────────────────────┬──────────────────
│            variant             │   val acc (mean,    │ camera-sim val   │   
│                                │       range)        │       acc        │     
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ baseline (Task 3)              │ 0.667 (0.621–0.690) │ 0.402            │ 
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ ColorJitter                    │ 0.713 (0.690–0.724) │ 0.506            │
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ 2a transfer, frozen backbone   │ 0.816 (0.793–0.828) │ 0.563            │ 
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ 2b transfer, full fine-tune    │ 0.724 (0.690–0.759) │ 0.632            │       
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ training kit, 40 epochs        │ 0.782 (0.759–0.828) │ 0.379            │        
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ GAP + Dropout(0.5)             │ 0.598 (0.586–0.621) │ 0.299            │           
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ last year's data (700          │ 0.782 (0.759–0.793) │ 0.529            │          
│ imgs)                          │                     │                  │                 
├────────────────────────────────┼─────────────────────┼──────────────────┼
│ combo: transfer + colour aug   │ 0.759 (0.724–0.793) │ 0.667            │              
│  + kit                         │                     │                  │             
└────────────────────────────────┴─────────────────────┴──────────────────┴

ColorJitter - the colour part of the augmentation is what moves the camera number
2a transfer - fewest trainable parameters, nothing to overfit — but camera-sim stays mid
2b transfer - higher camera-sim, augmentation during fine-tuning helps real photos
training kit - no camera gain
GAP - negative results
last year's data - more data helps to with the training
combo - best camera-sim

For the phone page the combo would be the best fit. It got the best results on the camera-simulated test.

