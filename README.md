<h1 align="center">Nobrainer: Brain Parcellation</h1>

<p align="center">
  A FreeSurfer-style whole-brain parcellation of a T1-weighted image, with entropy and variance maps of prediction uncertainty, from the kwyk Bayesian neural network.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.218"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.218-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Segments a T1-weighted image into a FreeSurfer-style whole-brain parcellation with the kwyk Bayesian neural network (nobrainer), with entropy and variance maps of prediction uncertainty.**

Runs the neuronets/kwyk 0.4 GPU container on the T1w image with the chosen model and number of Monte Carlo samples, producing mean-label parcellations in the original and the conformed (FreeSurfer) space plus entropy and, except for the bwn model, variance maps. Needs a GPU node. Note that the code writes the original-space prediction to parc and the conformed-space prediction to parc_fs, which is the opposite of the registry descriptions (see report).

| | |
|---|---|
| **Inputs** | `neuro/anat/t1w` |
| **Outputs** | `neuro/parcellation/volume` ×2 · `raw` ×2 |
| **Container** | `neuronets/kwyk:version-0.4-gpu` |
| **Resources** | 1 CPUs · 4 GB memory · 0.5 h walltime · GPU |
| **Run it** | [doi.org/10.25663/brainlife.app.218](https://doi.org/10.25663/brainlife.app.218) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t1` | `neuro/anat/t1w` | yes | T1-weighted image to parcellate. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `parc` | `neuro/parcellation/volume` | Predicted parcellation/volume in freesurfer_space. |
| `parc_fs` | `neuro/parcellation/volume` | Predicted parcellation/volume in subject space. |
| `output` | `raw` | contains entrypy and variance (optional) for prediction uncertainty. |
| `output_fs` | `raw` | contains entrypy and variance (optional) for prediction uncertainty. |

<details>
<summary><b>Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `samples` | number | `10` | Number of samples you wish to run (ignored for bwn/MAP) |
| `model` | enum |  | kwyk model passed to -m: bvwn_multi_prior, bwn_multi or bwn (bwn is the MAP model without variance output). Options: `bvwn_multi_prior`, `bwn_multi`, `bwn`. |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.218](https://doi.org/10.25663/brainlife.app.218), choose a T1-weighted image (`neuro/anat/t1w`), pick a `model` and the number of Monte Carlo `samples`, and press **Execute**. The App needs a GPU node. It archives two `neuro/parcellation/volume` datasets and two `raw` datasets with the uncertainty maps.

| `model` | Method | Uncertainty outputs |
|---|---|---|
| `bvwn_multi_prior` | spike-and-slab dropout | entropy and variance |
| `bwn_multi` | MC Bernoulli dropout | entropy and variance |
| `bwn` | MAP (`samples` is ignored) | entropy only |

[Nobrainer](https://github.com/neuronets/nobrainer#readme) is a deep learning framework for 3D image processing. It implements 3D convolutional models, loading and augmentation of volumetric data for TensorFlow and Keras, losses and metrics for 3D data, and utilities for training, evaluation, prediction and transfer learning. It also provides pre-trained models for brain extraction, brain segmentation and other tasks; this App runs the kwyk parcellation model from the `neuronets/kwyk:version-0.4-gpu` container.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) with GPU support (`--nv`), an NVIDIA GPU and [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository.
2. Create `config.json` (see `config.json.example`):

   ```json
   {
       "samples": 2,
       "model": "bvwn_multi_prior",
       "t1": "testdata/t1.nii.gz"
   }
   ```

3. Run the App:

   ```bash
   ./main
   ```

### Sample data

Download a T1-weighted sample from brainlife.io with the [brainlife CLI](https://github.com/brainlife/cli):

```bash
npm install -g brainlife
bl login
bl dataset download 5a050966eec2b300611abff2 && mv 5a050966eec2b300611abff2 testdata
```

## Outputs

```text
parc/parc.nii.gz            mean-label parcellation in the original T1 space, with key.txt
parc_fs/parc.nii.gz         mean-label parcellation in the conformed (FreeSurfer) space, with key.txt
output/entropy.nii.gz       prediction entropy, original space (variance.nii.gz too, except for bwn)
output_fs/entropy.nii.gz    prediction entropy, conformed space (variance.nii.gz too, except for bwn)
```

`key.txt` maps each FreeSurfer label value to the network's label index and structure name.

## Troubleshooting

- **Which parcellation is in which space**: the code writes the original-space prediction to `parc` and the conformed-space prediction to `parc_fs`, while the registered output tags and descriptions on brainlife.io say the opposite (`parc` is tagged `freesurfer_space`). Check the image header before combining a parcellation with other data.
- **The container fails to start**: the App runs `singularity run --nv` and needs a node with an NVIDIA GPU.

## Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- Nobrainer - BrainParcellation. brainlife.io App. [doi:10.25663/brainlife.app.218](https://doi.org/10.25663/brainlife.app.218)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)
- Fischl. FreeSurfer. *NeuroImage* 62:774-781 (2012). [doi:10.1016/j.neuroimage.2012.01.021](https://doi.org/10.1016/j.neuroimage.2012.01.021)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)

## Authors

- Soichi Hayashi
- Franco Pestilli (project director)

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io is publicly funded, and acknowledging that support helps keep it running. This App was developed with support from Wellcome, the U.S. National Institutes of Health (including the BRAIN Initiative BRAIN CONNECTS program: The Axonal Projectome EXchange, U24NS140384, and the Center for Mesoscale Connectomics, UM1NS132207) and the U.S. National Science Foundation.</sub>

## License

[MIT](LICENSE). Nobrainer and the kwyk models are distributed under their own licenses.
