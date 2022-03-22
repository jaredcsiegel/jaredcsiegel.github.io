# Welcome to <tt>linebyline</tt> 

## About

<tt>linebyline</tt> is a public python package for conducting high-precision radial velocity (RV) analysis, using the "line-by-line" technique of <a href="https://ui.adsabs.harvard.edu/abs/2018A%26A...620A..47D/abstract" target="_blank">Dumusque (2018)</a>. 

This package is a complete RV analysis pipeline, with the ability to process a series of 1D spectra and return corresponding line-by-line radial velocity measurements and stellar activity indices; <tt>linebyline</tt> also facilitates several different methods of stellar activity mitigation, including:

* "Line selection": calculating RVs using spectral lines shown to be insensitive to stellar activity <a href="https://ui.adsabs.harvard.edu/abs/2018A%26A...620A..47D/abstract" target="_blank">(Dumusque, 2018)</a>.
* "Formation Depth": using the difference between the RVs of deep and shallow spectral lines as an activity proxy <a href="https://ui.adsabs.harvard.edu/abs/2020A%26A...633A..76C/abstract" target="_blank">(Cretignier et al. 2020)</a>.
* FF': tracking stellar activity via a spot model and the star's photometric light curve (or an activity index) <a href="https://ui.adsabs.harvard.edu/abs/2012MNRAS.419.3147A/abstract" target="_blank">(Aigrain et al. 2012)</a>.
* Gaussian process (GP) regression: do we want to include these?

Currently, HARPS, HARPS-N, and MAROON-X are supported instruments.

## Installation

To install, download the package, change directories into the downloaded folder, and run:

	pip install .

## Authors 
Siegel, J, Rubenzahl, R, Halverson, S, Howard, A
