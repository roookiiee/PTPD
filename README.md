# PTPD: Unsupervised Prompt Distillation for Vision-Language Models

<!-- Official PyTorch implementation of "PTPD: Unsupervised Prompt Distillation for Vision-Language Models" -->

## Getting Started

### 1. Environment Setup

Begin by setting up the environment and installing the necessary dependencies for Dassl.pytorch.  
Please follow the steps outlined in [`docs/INSTALL.md`](docs/INSTALL.md) for detailed guidance.

---

### 2. Preparing the Teacher Model (Recommended: Pretrained)

You can either:

- **Use pretrained ViT-L/14 teacher models** (recommended):  
  Download them from [PromptKD](https://github.com/zhengli97/PromptKD), unzip, and place the model files in the `./teacher_model` folder.

- **Train your own teacher model** (optional):  
  Instructions are provided in the section [Training Your Own Teacher Model](#training-your-own-teacher-model-optional).

> Model accuracies are reported in Table 1 in the supplementary materials of the paper.

---

### 3. Download CLIP Base Model Weights

Download the original CLIP weights and place them in the `./clip` directory:

- [ViT-B/16](https://openaipublic.azureedge.net/clip/models/5806e77cd80f8b59890b7e101eabd078d9fb84e6937f9e85e4ecb61988df416f/ViT-B-16.pt)  
- [ViT-L/14](https://openaipublic.azureedge.net/clip/models/b8cca3fd41ae0c99ba7e8951adf17d267cdb84cd88be6f7c2e0eca1737a03836/ViT-L-14.pt)

---

### 4. Prepare the Dataset

Follow the dataset preparation guide in [`docs/DATASETS.md`](docs/DATASETS.md) to structure and load datasets correctly.

---

## Training Your Own Teacher Model (Optional)

We use the PromptSRC method by default to pretrain the ViT-L/14 teacher.  
You’ll find the corresponding config here:

```
configs/trainers/PromptSRC/vit_l14_c2_ep20_batch8_4+4ctx.yaml
```

To train the model:

1. Edit the script `scripts/promptsrc/base2new_train.sh`

2. Replace line 11:

   ```bash
   CFG=vit_b16_c2_ep20_batch4_4+4ctx
   ```

   with:

   ```bash
   CFG=vit_l14_c2_ep20_batch8_4+4ctx
   ```

3. Then follow the training steps in [`docs/PromptSRC.md`](docs/PromptSRC.md)

> **Note:** Your model's performance may vary by hardware. Refer to Appendix Table 10 to verify it’s suitable for use in distillation. If training is too resource-intensive, we recommend using the pretrained teacher.

---

## Running PTPD

### Base-to-Novel Experiments

1. Main config file:

   ```
   configs/trainers/ptpd/vit_b16_c2_ep20_batch8_4+4ctx.yaml
   ```

   Modify hyperparameters if needed.

2. Update the dataset path:  
   In `scripts/ptpd/base2new_train.sh`, edit **line 4** to point to your dataset location.

3. Run training commands:

   ```bash
   # For ImageNet with seed 1
   sh scripts/ptpd/base2new_train.sh imagenet 1
   
   # Other seeds
   sh scripts/ptpd/base2new_train.sh imagenet 2
   sh scripts/ptpd/base2new_train.sh imagenet 3
   
   # Using DTD dataset
   sh scripts/ptpd/base2new_train.sh dtd 1
   ```

---

### Output Directory

Training results will be saved to:

```
output/base2new/train_base/${DATASET}/shots_${SHOTS}/${TRAINER}/${CFG}/seed_${SEED}
```

Where:

- `${DATASET}` = dataset name (e.g., `imagenet`, `dtd`)  
- `${SHOTS}` = number of shots per class  
- `${TRAINER}` = usually `ptpd`  
- `${CFG}` = config file name  
- `${SEED}` = random seed used

---

Feel free to reach out if you'd like this converted to a PDF or need additional customization!