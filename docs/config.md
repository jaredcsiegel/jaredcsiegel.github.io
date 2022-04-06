# Configuration file

This page introduces the configuration file. This file contains all the input parameters for processing stellar spectra using <tt>linebyline</tt>. The default setting are defined in:

    linebyline/configs/extraction_config.ini

Users can specify alternative input parameters by either changing the above file or providing <tt>linebyline</tt> with a path to a different configuration file.

Below, we outline each parameter in the configuration file (divided between the <tt>Instrument</tt> and <tt>Specbatch</tt> objects), as well as the default values.


### Instrument Object

    airmass_lim = 1.5 
    # float, sets the maximum allowed airmass for the 
    # input spectra (e.g., all spectra with a higher airmass are not processed)

    snr10_lim = 10
    # float, sets the minimum SNR for the 10th order

    verbose = False
    # bool, sets whether to provide printout statements

### Specbatch.filter_for_continuum

    omin  = 20
    # int, minimum order index (inclusive) considered for filtering

    omax  = 20
    # int, maximum order index (inclusive) considered for filtering
    
    sigma = 5
    # float, number of standard deviations away from the mean
    # a slope must be in order to be rejected 

    verbose = False
    # bool, sets whether to provide printout statements

### Specbatch.normalize_spectral_orders

    window_size = 100
    # int, size of the window for the rolling maximum continuum function
    # in units of 0.1 A (e.g., size = 100 gives a 10 A wide window)

### Specbatch.fetch_template

    template  = auto
    # str, sets the method of deriving the line list; auto yields
    # native line identification (using local minimums and maximums
    # in the reference spectrum), otherwise this is treated as the path
    # to a premade template

    sampling_per_order = 10000
    # int, number of points to evaluate the reference spectrum on
    # for a given order; the points are uniformly separated.

    bis_sampling       = 100
    # int, number of points to evaluate the BIS for each identified line

    min_width = 10
    # int, minimum allowed spectral line width (in pixels)

    min_depth = 0.05
    # float, minimum allowed spectral line depth

### Specbatch.flag_telluric_TAPAS

    buffer    = 48
    # int, a line is rejected if it is within this many 
    # pixels of a telluric feature

    threshold = 0.99
    # float, threshold for identifying telluric contamination 
    # in the TAPAS spectrum (if a feature has a normalized depth
    # below the threshold it is considered telluric contamination)

    verbose   = False
    # bool, sets whether to provide printout statements

    drop      = False
    # bool, sets whether to remove telluric contaminated
    # lines for the line list

    filter_window = 100000
    # int, width of the window for continuum normalizing the TAPAS 
    # spectrum (this depends on the density of the input TAPAS model)

### Specbatch.flag_crossover

    multiple = 512
    # int, the separation (in pixels) between CCF stitchings 

    buffer   = 48
    # int, a line is rejected if it is within this many 
    # pixels of a stitching

    verbose  = True
    # bool, sets whether to provide printout statements

### Specbatch.fetch_LBL

    [fetch_LBL]
    nproc   = 1
     # int, number of processors to use

    width   = auto
     # str or int, if auto the width of a line is determined 
     # natively (based on the nearest local maximas); if an
     # integer is passed, that value is the adopted line with
     # in units of pixels

    interp  = False
    # bool, if True the line depth in inferred via cubic spline
    # interpolation, otherwise the minimum pixel flux is used 

    verbose = False
    # bool, sets whether to provide printout statements

    sampling_per_line = 100
    # int, the number of points evaluated if interp = True

### Specbatch.filter_LBL

    sigma_rv        = 4
    sigma_rv_err    = 4
    sigma_depth     = 4
    sigma_depth_err = 4
    # float, number of standard deviations away from the mean
    # a RV, RV uncertainty, depth, or depth uncertainty must 
    # be in order to be rejected 

    niter_rv        = 4
    niter_rv_err    = 4
    niter_depth     = 4
    niter_depth_err = 4
    # int, number of iterations for the sigma-clipping

    rejection_fraction = 0.9
    # float, if less than fraction of measurements are maintained
    # for a given line, all measurements for that line are rejected

    drop  = False
    # bool, sets whether to remove filtered lines for the line list

### Specbatch[save_functions]

    npoints = pixel
    # str or int, density of points to save for the reference spectrum;
    # if npoints = pixel, the pixel values are used; if npoints an
    # integer, that number of points is used

    save_master_name   = master.csv
    save_lbl_data_name = LBL_data.csv
    save_template_name = template.csv
    # str, to save the data to