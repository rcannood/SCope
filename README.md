# SCope v1.3.4: Visualization of large-scale and high dimensional single cell data
<img src="images/SCope_Logo.png" width="640">

SCope is a fast visualization tool for large-scale and high dimensional scRNA-seq datasets.
Currently the data format supported by SCope is `.loom`. This file format for very large omics datasets is maintained by the Linnarsson Lab through the `loompy` Python package (https://github.com/linnarsson-lab/loompy).

## Version History

November 16, 2018
* Version 1.3.4
   * Fix some installation bugs.
   * Fix bug Compare tab disabled when a .loom does not contain any meta data information (e.g.: annotations).

August 22, 2018
* Version 1.3.2
   * Fix bug downloading sub loom when parent loom is a public one.
   * Fix bug downloading sub loom when parent loom has been created with SCopeLoomR version < 0.0.5.6558.
* Version 1.3.1
   * Fix bug when cluster markers not present in .loom.
* Version 1.3.0
   * Add feature to download subset of looms. Currently it is only possible to subset the active loom based on cluster information.

August 10, 2018
* Version 1.2.1
   * Fix bug when trajectory data not well defined in .loom.

July 6, 2018

* Version 1.2.0
   * Add feature to display trajectory data in the viewer. Currently the only way we provide to add trajectory data to .loom files is through [SCopeLoomR](https://github.com/aertslab/SCopeLoomR/).

July 4, 2018

* Version 1.1.0
   * Add feature to display metrics in the viewer. Currently the only way we provide to add metrics to .loom files is through [SCopeLoomR](https://github.com/aertslab/SCopeLoomR/).

## Demo

Visit [http://scope.aertslab.org](http://scope.aertslab.org) to test out SCope on several published datasets! Personal loom file files can be uploaded but will only be kept for 5 days.

## Loom File Generation

Currently there are two packages to generate extended loom files compatible with SCope.
- R: [SCopeLoomR](https://github.com/aertslab/SCopeLoomR) - Dedicated R package
- Python: [pySCENIC](https://github.com/aertslab/pySCENIC) - Single function for generation from SCENIC results

Eventually the functionality from pySCENIC will be expanded and put in its own python package.


## Run SCope

### Standalone apps

Standalone apps for **macOS** and **Linux** can be downloaded from [the releases page.](https://github.com/aertslab/SCope/releases).

A **Windows** app is under development, but currently has no ETA.

### Development
Requirements should be fulfilled (see `Requirements` section).

#### Clone repository

```bash
# Define where you want to clone the SCope repository.
LOCAL_SCOPE_REPO="${HOME}/repos/SCope"

# Clone SCope git repository.
git clone https://github.com/aertslab/SCope "${LOCAL_SCOPE_REPO}"
```

### Install miniconda and create SCope environment.

We recommend using miniconda to install SCope and its dependencies in a clean environment.

Download miniconda3 from https://conda.io/miniconda.html or use the command line:

```bash
wget --content-disposition http://bit.ly/miniconda3
```

Install Minconda:

```bash
bash Miniconda3-latest-[...].sh
```

Create miniconda virtual environment with python (3.6 or higher) and nodejs (9 or higher) for SCope:

```bash
# Install python and nodejs with conda.
conda create -n scope 'python>=3.6' 'nodejs>=9'
```

#### Install SCope

Activate miniconda virtual environment with python (3.6 or higher) and nodejs (9 or higher) and install SCope:

```bash
# Activate SCope environment.
conda activate scope

# Go to your local cloned SCope repository.
cd "${LOCAL_SCOPE_REPO}"

# Install SCope.
npm install
```

#### Run

- One Command Run:

```bash
# Go to your local cloned SCope repository.
cd "${LOCAL_SCOPE_REPO}"

npm run scope
```

- Debug Run in 2 terminals:

```bash
# Go to your local cloned SCope repository.
cd "${LOCAL_SCOPE_REPO}"

# Start SCope Server (terminal 1).
scope-server

# Start SCope Client (terminal 2).
npm run dev
```

#### 1. Packaging SCope Data Server

Activate SCope environment (see `Install miniconda and create SCope environment.` section if you do not have one yet):

```bash
# Activate SCope environment.
conda activate scope

# Go to your local cloned SCope repository.
cd "${LOCAL_SCOPE_REPO}"
```

Install the SCope Server as Python package:

```bash
cd opt
python setup.py develop
```

Install PyInstaller:

```bash
cd scopeserver/dataserver
pip install pyinstaller
```

Package the SCope Data Server:

```bash
cd ./opt/scopeserver/dataserver
LD_LIBRARY_PATH=${CONDA_PATH}/lib pyinstaller \
	--onedir \
	--hidden-import=scipy._lib.messagestream \
	--hidden-import=pandas._libs.tslibs.timedeltas  \
	--hidden-import=cytoolz.utils \
	--hidden-import=cytoolz._signatures __init__.py \
	--hidden-import=pandas._libs.tslibs.np_datetime \
	--hidden-import=pandas._libs.tslibs.nattype \
	--hidden-import=pandas._libs.skiplist
```

`${CONDA_PATH}` is the path where Miniconda has been installed.

#### 2. Packaging SCope

First install electron-packager node module:

```bash
sudo npm install electron-packager -g
```

Finally, bundle the SCope app:
- Linux (x64)

```bash
npm run package-linux-x64
tar -zcvf scope-linux-x64.tar.gz scope-linux-x64
```

- macOS (x64)

```bash
npm run package-macOS-x64
```

Run the binary:
- Linux

```bash
./release/scope-linux-x64/scope
```

- macOS

Run the .app file generated

#### 3. Creating Single Executable File

##### Debian package
For more details, follow https://www.christianengvall.se/electron-installer-debian-package/

```bash
npm run create-debian-installer
```

##### dmg for macOS

```bash
git clone https://github.com/andreyvit/yoursway-create-dmg.git
./yoursway-create-dmg/create-dmg \
	--volname "SCope Installer" \
	--volicon "images/SCope_Icon.icns" \
	--background "images/SCope_Background.png" \
	--window-pos 200 120 \
	--window-size 800 400 \
	--icon-size 100 \
	--icon release/scope-darwin-x64/scope.app 192 344 \
	--hide-extension scope.app \
	--app-drop-link 448 344 \
	${TRAVIS_BUILD_DIR}/release/scope-macOS-x64.dmg \
	release/scope-darwin-x64/scope.app/
```

All uploaded data from SCope will be put in the following folders by default:
- Linux
`~/.local/share/scope/`

- macOS
`~/Library/Application\ Support/scope/`

## Deploy a Cloud-based Instance

### Amazon Web Services

#### Public AMI

Coming soon.

#### Source

To create a SCope AWS instance from scratch please read the tutorial [aws-deployment-source](https://github.com/aertslab/SCope/tree/master/tutorials/aws-deployment-source).

## Architecture

SCope architecture can be visualized below:

![SCope architecture](/images/SCope_architecture.png)
