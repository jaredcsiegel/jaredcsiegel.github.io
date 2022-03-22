# Configuration file

This page introduces the configuration file. This file contains all the input parameters for processing stellar spectra using <tt>linebyline</tt>. The default setting are defined in:

    linebyline/configs/extraction_config.ini

Users can specify alternative input parameters by either changing the above file or providing <tt>linebyline</tt> with a path to a different configuration file.

Below, we all outline each parameter in the configuration file (divided between the <tt>Instrument</tt> and <tt>Specbatch</tt> objects). 

### Instrument Object

    [instrument]
    airmass_lim = 1.5
    snr10_lim = 10
    verbose = False

### Specbatch.filter_for_continuum

    [filter_for_continuum]
    omin  = 20
    omax  = 20
    sigma = 20
    verbose = False

### Specbatch.normalize_spectral_orders

    [normalize_spectral_orders]
    window_size = 100

### Specbatch.fetch_template

    [fetch_template]
    template  = auto
    sampling_per_order = 10000
    bis_sampling       = 100
    min_width = 10
    min_depth = 0.05

### Specbatch.flag_telluric_TAPAS

    [flag_telluric_TAPAS]
    buffer    = 48
    threshold = 0.99
    verbose   = False
    drop      = False
    filter_window = 100000

### Specbatch.flag_crossover

    [flag_crossover]
    multiple = 512
    buffer   = 48
    verbose  = True

### Specbatch.fetch_LBL

    [fetch_LBL]
    nproc   = 1
    width   = auto
    interp  = False
    verbose = False
    sampling_per_line = 100

### Specbatch.filter_LBL

    [filter_LBL]
    sigma_rv        = 4
    sigma_rv_err    = 4
    sigma_depth     = 4
    sigma_depth_err = 4

    niter_rv        = 4
    niter_rv_err    = 4
    niter_depth     = 4
    niter_depth_err = 4

    rejection_fraction = 0.9

    drop  = False

### Specbatch[save_functions]

    [save_functions]
    npoints = pixel

    save_master_name   = master.csv
    save_lbl_data_name = LBL_data.csv
    save_template_name = template.csv