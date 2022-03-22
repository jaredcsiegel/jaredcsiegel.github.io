# Tutorials

Here we walk through the <tt>linebyline</tt> pipeline and highlight available features. A Jupyter notebook version of this tutorial is available at:

	linebyline/docs/tutorials/

For more details on each step and what parameters the user can change, see the configuration file page.

## Step 0: Imports and the configuration file
We first must navigate to the following directory:

	linebyline/docs/tutorials

For this tutorial, the following imports are necessary.

	import os
	from linebyline.instruments import instrument
	from linebyline.specbatch import SpecBatch
	from linebyline.configs import config
	from linebyline.parse import ParseSpec

To specify the default parameters for the pipeline, we also need to define a configuration file (see the configuration file page for details on the different parameters):

	cfg = config.get_config('../../linebyline/configs/extraction_config.ini')

## Step 1: Correcting for known systematics

In this first step, the user provides a list of paths to stellar spectra and specifies the corresponding instrument; depending on the instrument, the user may also need to provide paths to the blaze correction files and the cross-correlation function (CCF) files. Given these user inputs, <tt>linebyline</tt> will load in the provided spectra and correct each for known systematics, including blaze corrections and Doppler shifting the wavelength solution into the heliocentric frame. 

In this step, we also specify filters on airmass and activity level (see the configuration file page for details on the available filters).

Below we outline this step for each supported instrument.

### HARPS
Once in the , we first gather the stellar spectra:

	ps    = os.listdir('../example_data/HARPS/alphaCenB/spectra')
	paths = ['../example_data/HARPS/alphaCenB/spectra/' + p for p in ps]

We then build two dictionaries that point to each spectra's corresponding blaze and CCF file:

	blaze_key = {}
	for p in paths:
		blaze_key[p] = '../example_data/HARPS/alphaCenB/blaze/' + fits.open(p)[0].header['HIERARCH ESO DRS BLAZE FILE']
	ccf_key = {}
	for p in paths:
		ccf_key[p] = '../example_data/HARPS/alphaCenB/ccf/' + p.split('/')[-1].replace('_e2ds_A','_ccf_K5_A')

Lastly, we must initialize the instrument object with these paths and dictionaries,

	obs = instrument.HARPS(paths, blaze_key, cfg, ccf_key)

The instrument object <tt>obs</tt> can now be passed to the next step in the pipeline and will facilitate loading in the stellar spectra and correcting for known systematics. 

### MAROON-X

For MAROON-X, we initialize the instrument object as,

	obs = instrument.MAROONX(paths, blaze_key, cfg)

Notice, <tt>ccf_key</tt>  is no longer required.

## Step 2: Line-by-line measurements

Next, we implement the line-by-line technique outlined by <a href="https://ui.adsabs.harvard.edu/abs/2018A%26A...620A..47D/abstract" target="_blank">Dumusque (2018)</a>.  Note, for here on, the pipeline is agnostic to the chosen instrument (the instrument object we defined in Step 1 will handle all instrument specific concerns).

To begin, we define the <tt>SpecBatch</tt> object,

	batch = SpecBatch(
		obs,
		target = target,
		config = cfg
	)

where <tt>obs</tt> and <tt>cfg</tt>  were defined above, and <tt>target</tt> is a string that will be the name of the directory for saving the results. 

We must then load all the input stellar spectra,

	batch.load_spectra()

and normalize their continuums (using a rolling maximum),
	
	batch.normalize_spectral_orders()

#### a. Filtering the spectra

Before proceeding further, we must remove outlier observations from the input stellar spectra. <tt>linebyline</tt> filters observations based on the slope of each spectral orders continuum,

	batch.filter_for_continuum()

The user can specify additional filters, by controlling the list of paths passed to <tt>linebyline</tt> . 

#### b. Building a reference spectrum

Next, we build a reference spectrum form the input stellar spectra. 

	batch.create_master()

To help with memory usage, next call

	batch.flush()

to remove the loaded spectra from active memory (we don't need to have them all loaded at once now that we have the reference spectrum).

#### c. Generating (or importing) a catalog of spectral lines

To conduct the line-by-line measurements, we need a catalog of spectral lines. This catalog can either be generated based on the reference spectrum or imported from a user specified file.

To specify a list of spectral lines, set the <tt>template</tt> parameter for <tt>batch.fetch_template </tt> to a path to the line list.  User defined line lists must have the same format as the ESPRESSO line lists. Examples for the K5 and G2 stellar types are provided in,

	linebyline/docs/example_data/synthetic_templates 
	
For example, to use the ESPRESSO K5 template,

	batch.fetch_template(template='../example_data/synthetic_templates/K5.mas')

Alternatively, to have the catalog generated from the reference spectrum, set <tt>template='auto'</tt> and call,

	batch.fetch_template(template='auto')

Regardless of whether the line list is generated from the reference spectra or user specified, we next want to flag potentially contaminated lines, including those near tellurics or near stichings in the CCD:


	batch.flag_telluric_TAPAS('../example_data/TAPAS/TAPAS_WMKO_NORAYLEIGH_SPEC')
	batch.flag_crossover()

where we have pointed to an example TAPAS atmospheric spectrum for  <tt>batch.flag_telluric_TAPAS</tt>.

#### d. Making the measurements

We're now ready to conduct the line-by-line measurements (yay!). This is handled by the following call,

	 batch.fetch_LBL()

To help reduce run-times, the user can specify the number of processors to use.

#### e. Filtering

Lastly, we flag outlier line-by-line measurements,

	batch.filter_LBL()

#### f. Saving

To save our results, we run,

	batch.save_template()
	batch.save_LBL_data()
	batch.save_master()

## Step 3: Processing the measurements

Now that we have made the line-by-line measurements, what can we do with them? 

To start, we load the saved results into the <tt>ParseSpec</tt> object. 

	dat = ParseSpec(target + '/')

where <tt>target</tt> is the same specified in Step 2.

The line-by-line measurements are stored as <tt>pandas.DataFrame</tt> in <tt>dat.data</tt>.

We next reject flagged measurements (including lines contaminated by tellurics or rejected by the sigma clipping in Step 2f),

	dat.enforce_filter()


To calculate the bulk RV for each spectra, we run

	t,rv,er = dat.fetch_bulk_RVs(log=True)

where <tt>log=True</tt> saves the bulk RVs to <tt>dat.data</tt>.

