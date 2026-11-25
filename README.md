[![Abcdspec-compliant](https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg)](https://github.com/brain-life/abcd-spec)

# app-NOBRAINER

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

**Segments a T1-weighted image into a FreeSurfer-style whole-brain parcellation with the kwyk Bayesian neural network (nobrainer), with entropy and variance maps of prediction uncertainty.**

Runs the neuronets/kwyk 0.4 GPU container on the T1w image with the chosen model and number of Monte Carlo samples, producing mean-label parcellations in the original and the conformed (FreeSurfer) space plus entropy and, except for the bwn model, variance maps. Needs a GPU node. Note that the code writes the original-space prediction to parc and the conformed-space prediction to parc_fs, which is the opposite of the registry descriptions (see report).

Run it on brainlife.io: [https://doi.org/10.25663/brainlife.app.218](https://doi.org/10.25663/brainlife.app.218)

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t1` | `neuro/anat/t1w` | yes | T1-weighted image to parcellate. |

| Output | Datatype | Description |
|---|---|---|
| `parc` | `neuro/parcellation/volume` | Predicted parcellation/volume in freesurfer_space. |
| `parc_fs` | `neuro/parcellation/volume` | Predicted parcellation/volume in subject space. |
| `output` | `raw` | contains entrypy and variance (optiona) for prediction uncertainty. |
| `output_fs` | `raw` | contains entrypy and variance (optiona) for prediction uncertainty. |

| Parameter | Type | Default | Description |
|---|---|---|---|
| `samples` | number | `10` | Number of samples you wish to run (ignored for bwn/MAP) |
| `model` | enum |  | kwyk model passed to -m: bvwn_multi_prior, bwn_multi or bwn (bwn is the MAP model without variance output). Options: `bvwn_multi_prior`, `bwn_multi`, `bwn`. |

Runtime: container `neuronets/kwyk:version-0.4-gpu`; 1 CPUs; 4 GB memory; 0.5 h walltime; GPU.

Please cite:
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

<!-- brainlife-app:end -->

This app executes https://github.com/neuronets/nobrainer/ - bayesian brain parcellation

Nobrainer is a deep learning framework for 3D image processing. It implements several 3D convolutional models from recent literature, methods for loading and augmenting volumetric data that can be used with any TensorFlow or Keras model, losses and metrics for 3D data, and simple utilities for model training, evaluation, prediction, and transfer learning.

Nobrainer also provides pre-trained models for brain extraction, brain segmentation, and other tasks. Please see the Nobrainer models repository for more information.

More information can be found [here](https://github.com/neuronets/nobrainer#readme)

### Authors
- [Soichi Hayashi](hayashis@iu.edu)

### Project Director
- [Franco Pestilli](pestilli@utexas.edu)

#### Copyright (c) 2021 brainlife.io The University of Texas at Austin and Indiana University

### Funding Acknowledgement
brainlife.io is publicly funded and for the sustainability of the project it is helpful to Acknowledge the use of the platform. We kindly ask that you acknowledge the funding below in your code and publications. Copy and past the following lines into your repository when using this code.

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB020470](https://img.shields.io/badge/NIH_NIBIB-R01EB020470-green.svg)](https://grantome.com/grant/NIH/R01-EB0220470-03)

### Citations
We ask that you the following articles when publishing papers that used data, code or other resources created by the brainlife.io community.

1. Avesani, P., McPherson, B., Hayashi, S. et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). [https://doi.org/10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)


## Running the App 

### On Brainlife.io

You can submit this App online at [https://doi.org/10.25663/bl.app.218](https://doi.org/10.25663/bl.app.218) via the "Execute" tab.

### Running Locally (on your machine)

1. git clone this repo.
2. Inside the cloned directory, create `config.json` with something like the following content with paths to your input files.

```json
{
  "t1": "t1.nii.gz"
}
```

3. Launch the App by executing `main`

```bash
./main
```

### Sample Datasets

If you don't have your own input file, you can download sample datasets from Brainlife.io, or you can use [Brainlife CLI](https://github.com/brain-life/cli).

```
npm install -g brainlife
bl login
mkdir input
bl dataset download 5a0f0fad2c214c9ba8624376#5a050966eec2b300611abff2 && mv 5a0f0fad2c214c9ba8624376#5a050966eec2b300611abff2 .
```

## Output

All output file (a resampled T1w NIFTI-1 file) will be generated inside the current working directory (pwd), inside a specifc directory called:

```
out_dir
```
