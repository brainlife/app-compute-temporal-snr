<h1 align="center">Compute tSNR of DMRI Images</h1>

<p align="center">
  A brain-masked temporal signal-to-noise ratio (tSNR) map from the b0 volumes of a diffusion-weighted image, computed with FSL.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.378"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.378-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

## 🧠 At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Computes a temporal signal-to-noise ratio (tSNR) map from the b0 volumes of a DWI with FSL, following the HCP minimal preprocessing pipeline, and masks it to the brain.**

Extracts the b=0 volumes, divides their temporal mean by their temporal standard deviation and masks the result with the given brain mask, or with an FSL bet mask of the mean b0 if none is given. Use it as a DWI quality-control measure; it needs several b0 volumes for a meaningful standard deviation.

| | |
|---|---|
| 📥 **Inputs** | `neuro/mask` (optional) · `neuro/dwi` |
| 📤 **Outputs** | `neuro/snr` · `raw` |
| 🐳 **Container** | `brainlife/fsl:5.0.9` |
| ⚙️ **Resources** | 4 CPUs · 0.33 h walltime |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.378](https://doi.org/10.25663/brainlife.app.378) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `brainmask` | `neuro/mask` | no | Optional brain mask in DWI space; if absent, one is made with FSL bet from the mean b0. |
| `dwi` | `neuro/dwi` | yes | DWI image with bvals and bvecs. Needs more than one b=0 volume. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `outdir` | `neuro/snr` | Brain-masked tSNR map (snr.nii.gz): mean b0 signal divided by its standard deviation. |
| `raw` | `raw` | Intermediate images (b0 volumes, mean, standard deviation, unmasked SNR, mask). |

<!-- brainlife-app:end -->

## 🚀 Running the App

### ☁️ On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.378](https://doi.org/10.25663/brainlife.app.378), choose a `neuro/dwi` dataset (and, optionally, a brain mask in DWI space) from your project and press **Execute**. The App follows the tSNR computation of the Human Connectome Project minimal preprocessing pipeline and archives a `neuro/snr` dataset tagged `tSNR` and a `raw` dataset with the intermediate images. Use it as a quality-control measure; the DWI needs several b0 volumes for a meaningful standard deviation.

### 💻 Locally

You need [Singularity/Apptainer](https://apptainer.org/) and [`jq`](https://jqlang.github.io/jq/). The App runs FSL from the `brainlife/fsl:5.0.9` container.

1. Clone this repository and check out the App branch: `git checkout v1.0`.
2. Create `config.json` with the paths to your inputs (omit `brainmask`, or set it to `null`, to have a mask made with FSL `bet`):

   ```json
   {
       "brainmask": "/input/brainmask/mask.nii.gz",
       "dwi": "/input/dwi/dwi.nii.gz",
       "bvals": "/input/dwi/dwi.bvals",
       "bvecs": "/input/dwi/dwi.bvecs"
   }
   ```

3. Run the App:

   ```bash
   ./main
   ```

### 🧪 Sample data

Download sample datasets from brainlife.io with the [brainlife CLI](https://github.com/brainlife/cli):

```bash
npm install -g brainlife
bl login
mkdir input
bl dataset download <dataset-id>
```

## 📦 Outputs

The App extracts the b=0 volumes (`select_dwi_vols`), divides their temporal mean by their temporal standard deviation (`fslmaths -Tmean`, `-Tstd`, `-div`) and masks the result.

```text
outdir/snr.nii.gz   brain-masked tSNR map
raw/                intermediate images: nodif (b0 volumes), nodif_mean, nodif_std,
                    unmasked snr and, when no mask is given, the bet mask
```

## 🩺 Troubleshooting

- **`temporal snr calculation failed. see logs and derivatives`**: the masked tSNR image was not produced. Check that the DWI has b0 volumes and that the bvals match the DWI.

## 📚 Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- Woolrich MW, Jbabdi S, Patenaude B, Chappell M, Makni S, Behrens T, Beckmann C, Jenkinson M, Smith SM. Bayesian analysis of neuroimaging data in FSL. *NeuroImage* 45:S173–186 (2009).
- Smith SM, Jenkinson M, Woolrich MW, Beckmann CF, Behrens TEJ, Johansen-Berg H, et al. Advances in functional and structural MR image analysis and implementation as FSL. *NeuroImage* 23(S1):208–219 (2004).
- Jenkinson M, Beckmann CF, Behrens TE, Woolrich MW, Smith SM. FSL. *NeuroImage* 62:782–790 (2012).
- Glasser MF, Sotiropoulos SN, Wilson JA, et al.; WU-Minn HCP Consortium. The minimal preprocessing pipelines for the Human Connectome Project. *NeuroImage* 80:105–124 (2013). [doi:10.1016/j.neuroimage.2013.04.127](https://doi.org/10.1016/j.neuroimage.2013.04.127)
- Compute  tSNR of DMRI Images. brainlife.io App. [doi:10.25663/brainlife.app.378](https://doi.org/10.25663/brainlife.app.378)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)
- Woolrich MW, Jbabdi S, Patenaude B, et al. Bayesian analysis of neuroimaging data in FSL. NeuroImage 45:S173-86 (2009).
- Jenkinson et al. FSL. *NeuroImage* 62:782-790 (2012). [doi:10.1016/j.neuroimage.2011.09.015](https://doi.org/10.1016/j.neuroimage.2011.09.015)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)

## 👥 Authors

- Brad Caron
- Franco Pestilli (project director)

**Contributors:** Soichi Hayashi

## 💰 Funding

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

## ⚖️ License

[MIT](LICENSE). FSL itself is distributed under its own [license](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Licence).
