# CV-Inspector-adblockpluschrome
Custom changes to Adblock Plus Chrome extension for CV-Inspector.

The changes do the following:
1. It annotates DOM elements that are hidden with the attribute `abp-blocked-element: true`
1. It forces the extension to load EasyList and Anti-CV list from a different local web proxy: `127.0.0.1:5000` (see the patch file for details). This allows us to freeze the filter list that we are using during data collection for CV-Inspector.
1. Minor: It also keeps track of when a JS snippet filter rule is matched. See patch for details: `notifyBodyWithSnippetBlock`. This is then picked up when using CV-Inspector.

For more details, we will refer to the paper.

**This should be used in combination with [CV-Inspector](https://github.com/UCI-Networking-Group/cv-inspector).**

Visit our [CV-Inspector Project page](https://athinagroup.eng.uci.edu/projects/cv-inspector/) for more information, including datasets that we utilized in the paper.


# Citation
If you create a publication (including web pages, papers published by a third party, and publicly available presentations) using this repository or CV-Inspector, please cite the corresponding paper as follows:
```
@inproceedings{le2021cvinspector,
  title={{CV-Inspector: Towards Automating Detection of Adblock Circumvention}},
  author={Le, Hieu and Markopoulou, Athina and Shafiq, Zubair},
  booktitle={The Network and Distributed System Security Symposium (NDSS)},
  url = {https://dx.doi.org/10.14722/ndss.2021.24055},
  doi = {10.14722/ndss.2021.24055},
  year={2021}
}
```

## Contact
We also encourage you to provide us [athinagroupreleases@gmail.com](mailto:athinagroupreleases@gmail.com) with a link to your publication. We use this information in reports to our funding agencies.

# Setup Overview

This setup was tested using Amazon Machine Image: `Ubuntu Server 18.04 LTS (HVM), SSD Volume Type`

1. Install basic dependencies
    1. python-pip, virtualenv
    1. Install specific versions of npm and node. (I could not get it to work with nodejs 15)
    1. `curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -`
    1. `sudo apt-get install -y nodejs`
1. Create a virtual environment for python2: we will call this `adp`
    1. `virtualenv --python=python2.7 [path_to_your_envs]/adp`
1. Activate the virtual environment
    1. `source [path_to_your_envs]/adp/bin/activate`
1. Install python dependencies
    1. `pip install jinja2 fonttools brotli pycrypto mercurial`
1. Try to build adblockpluschrome for the first time:
    1. Go to path you keep your projects
    1. `git clone https://github.com/adblockplus/adblockpluschrome.git`
    1. `cd adblockpluschrome`
    1. `git checkout 7f8c1fb82d3f14515ba09c248f5aaa339246064c` ([version 3.7, ~Jan 2020](https://github.com/adblockplus/adblockpluschrome/commit/7f8c1fb82d3f14515ba09c248f5aaa339246064c))
    1. `./build.py devenv -t chrome`
        1. if this breaks for tensor related reasons, then install it yourself using:
        `npm install "@tensorflow/tfjs-core"`
        1. re-run the build command
1. Add in our changes and rebuild:
    1. Go to root directory our project and copy over our version of `include.preload.js` to where you saved `adblockpluschrome`
        1. `cp include.preload.js [path to adblockpluschrome]/.`
        1. `cp adblockpluscore_path.diff [path to adblockpluschrome]/adblockpluscore/.`
        1. `cp contentFiltering.js [path to adblockpluschrome]/lib/.`
        1. `cd [path to adblockpluschrome]/adblockpluscore/`
        1. `git apply adblockpluscore_path.diff`
    1. rebuild the extension without dependencies
        1. `SKIP_DEPENDENCY_UPDATES=true ./build.py devenv -t chrome`
1. The resulting build will be in `[path to adblockpluschrome]/devenv.chrome`. You will use this directory when running CV-Inspector


# Dependencies
- `npm 6, nodejs 14`: to build this project
- `Adblock Plus 3.7 for Chrome`: https://github.com/adblockplus/adblockpluschrome
- `Python 2.7`

# CV-Inspector

See [CV-Inspector](https://github.com/UCI-Networking-Group/cv-inspector) repository for the main component to CV-Inspector.

# License
Adblock Plus Chrome Extension for CV-Inspector is licensed under [GPL-3.0 License](https://www.gnu.org/licenses/gpl-3.0.en.html).
