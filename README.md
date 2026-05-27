# [MICCAI 2026 Early Accept Top 9%] FetalAgents: A Multi-Agent System for Fetal Ultrasound Image and Video Analysis

*FetalAgents* is a multi-agent system for comprehensive fetal ultrasound (US) image and video analysis. Through a lightweight, agentic coordination framework, *FetalAgents* dynamically orchestrates specialized vision experts to maximize performance across diagnosis, measurement, and segmentation. *FetalAgents* advances beyond static image analysis by supporting end-to-end video stream summarization, where keyframes are automatically identified across multiple anatomical planes, analyzed by coordinated experts, and synthesized with patient metadata into a structured clinical report. Extensive multi-center external evaluations demonstrate that *FetalAgents* consistently delivers the most robust and accurate performance when compared against specialized models and multimodal large language models (MLLMs), ultimately providing an auditable, workflow-aligned solution for fetal ultrasound analysis and reporting.

<p align="center">
  <img src="figures/pipeline.png" alt="FetalAgents pipeline" width="85%">
</p>

## ✨ Highlights

- **Multi-agent orchestration**  
  A flexible and robust agent pipeline that dynamically coordinates specialized experts for fetal ultrasound analysis.

- **Comprehensive image understanding**  
  Supports diverse tasks including classification, segmentation, biometry measurement, and report generation.

- **Video stream summarization**  
  Enables continuous video report by automated key frame identification and image-wise analysis.

<p align="center">
  <img src="figures/demo.png" alt="Qualitative examples of FetalAgents" width="92%">
</p>

### 📋 Supported Tasks

| Task Category | Supported Tasks |
|---|---|
| Classification | Standard plane classification, brain sub-plane classification |
| Segmentation | Fetal abdomen, fetal head, fetal stomach, PSFH |
| Biometry | AC measurement, HC measurement, gestational age estimation, AoP estimation |
| Reporting | Comprehensive image captioning, video stream summarization |

## 🗂️ Project Structure

```text
FetalAgents/
├── main.py                    # Core orchestration script
├── tools/                     # Agent-side tool wrapper scripts
├── external_tools/            # External model inference scripts
│   ├── AoP_SAM/               # AoP-SAM model code
│   ├── UperNet/               # UPerNet model code
│   ├── USFM_aop/              # USFM for AoP segmentation
│   ├── USFM_hc/               # USFM for HC segmentation
│   ├── CSM_hc/                # CSM for HC measurement
│   ├── ga_radimagenet/        # GA estimation (RadImageNet-based)
│   ├── ga_fetalclip/          # GA estimation (FetalCLIP-based)
│   ├── ga_convnext/           # GA estimation (ConvNeXt-based)
│   ├── plane_fetalclip/       # Plane classification (FetalCLIP)
│   ├── plane_fulora/          # Plane classification (FU-LoRA)
│   ├── brain_subplane_fetalclip/  # Brain sub-plane classification
│   └── keyframe_cls6/         # Video key-frame detection
├── reference/                 # Growth reference tables
│   ├── HC_GA_reference.csv
│   └── AC_GA_reference.csv
├── example_images/            # Example test data
├── requirements.txt
├── LICENSE
└── README.md
````

## 🛠️ Installation

### 1. Clone the repository

```bash
git clone https://github.com/huang-jw22/FetalAgents.git
cd FetalAgents
```

### 2. Set up the main Python environment

```bash
conda create -n fetalagents python=3.10
conda activate fetalagents
pip install -r requirements.txt
```

### 3. Set up auxiliary environments

This project relies on multiple model backends with different dependencies. In our internal setup, we use separate environments for different tool families.

Example environment groups:

* **fetal_base**: AoP-SAM, UPerNet, nnU-Net, GA-RadImageNet, GA-ConvNeXt
* **fetalclip**: FetalCLIP-based plane / sub-plane / GA tools
* **fetalclip2**: SAMUS-based segmentation, video key-frame detection
* **experiment_aaai**: CSM HC measurement, FU-LoRA plane classification
* **USFM**: USFM-based AoP / HC tools

Example setup commands for these auxiliary environments are provided in ENVIRONMENTS.md. If you already have compatible research environments, you can reuse them and only set the environment variables below.If you already have compatible research environments, you can reuse them and only set the environment variables below.

### 4. Configure tool environments

Set the Python executables for the corresponding environments:

```bash
export FETALAGENTS_FETAL_BASE_PYTHON=/path/to/envs/fetal_base/bin/python
export FETALAGENTS_FETALCLIP_PYTHON=/path/to/envs/fetalclip/bin/python
export FETALAGENTS_FETALCLIP2_PYTHON=/path/to/envs/fetalclip2/bin/python
export FETALAGENTS_EXPERIMENT_AAAI_PYTHON=/path/to/envs/experiment_aaai/bin/python
export FETALAGENTS_USFM_PYTHON=/path/to/envs/USFM/bin/python
export FETALAGENTS_NNUNET_PREDICT=/path/to/envs/fetal_base/bin/nnUNetv2_predict
```

If everything is installed into a single environment, the project can fall back to the default `python` executable.

### 5. Set up API access

The orchestration layer uses an OpenAI-compatible API:

```bash
export OPENAI_API_KEY="your-api-key-here"
export OPENAI_MODEL="gpt-5-mini"
export OPENAI_BASE_URL="https://api.openai.com/v1"
```

## 📦 Model Weights

**Trained model weights will be released once the paper is accepted.**

At the moment, checkpoints are not included in this repository for anonymity reasons.

## 🚀 Usage

### Single-image analysis

```bash
# Estimate gestational age
python main.py \
    --inquiry "Estimate gestational age of this fetal ultrasound scan image." \
    --case_dir example_images/brain_thalamic

# Generate a comprehensive caption for a fetal brain scan
python main.py \
    --inquiry "Write a comprehensive caption for this fetal ultrasound scan image." \
    --case_dir example_images/brain_thalamic

# Generate a comprehensive caption for a fetal abdomen scan
python main.py \
    --inquiry "Write a comprehensive caption for this fetal ultrasound scan image." \
    --case_dir example_images/fetal_abdomen
```

### Video summarization

```bash
python main.py \
    --inquiry "This folder contains continuous screenshots of a fetal US video. The Gestational Age estimated from last menstrual period (LMP) is 23w 0d. Please provide a comprehensive summary." \
    --case_dir example_images/video
```

## 📝 Input Format

Each `case_dir` should contain:

* One or more ultrasound images (`.png` or `.jpg`)
* A `pixel_size.csv` file with columns: `filename,pixel size(mm)`

Example:

```csv
filename,pixel size(mm)
315_HC.png,0.201985378458
```

## 🙏 Acknowledgments

This repository builds upon several excellent open-source projects and prior works. We thank the original authors for making their code and research publicly available.

* **FetalCLIP** — [BioMedIA-MBZUAI/FetalCLIP](https://github.com/BioMedIA-MBZUAI/FetalCLIP)

* **FU-LoRA** — [13204942/FU-LoRA](https://github.com/13204942/FU-LoRA)

* **USFM** — [openmedlab/USFM](https://github.com/openmedlab/USFM)

* **AoP-SAM** — [maskoffs/AoP-SAM](https://github.com/maskoffs/AoP-SAM)

* **SAMUS** — [xianlin7/SAMUS](https://github.com/xianlin7/SAMUS)

* **nnU-Net** — [MIC-DKFZ/nnUNet](https://github.com/MIC-DKFZ/nnUNet)

* **CSM for fetal HC measurement** — [ApeMocker/CSM-for-fetal-HC-measurement](https://github.com/ApeMocker/CSM-for-fetal-HC-measurement)

* **AutoGen** — [microsoft/autogen](https://github.com/microsoft/autogen)
