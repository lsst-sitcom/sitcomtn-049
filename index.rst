:tocdepth: 1

.. sectnum::

.. Metadata such as the title, authors, and description are set in metadata.yaml

.. TODO: Delete the note below before merging new content to the main branch.

.. note::

   **This technote is a work-in-progress. It does not yet include the calculations for the tunable laser.**

Abstract
========

Provides a description of the Exposure Time Calculator for the Flat Field Calibration Screen, both for the tunable laser and the white light source (LEDs). Includes a table of expected exposure times to meet the requirements. 

System Description
==================
A light projector, either a tunable laser or white light source (e.g. LED) will be used to illuminate the white calibration screen to provide the camera with a flat field calibration source. The light source will be placed in the center of the calibration screen and will reflect off of a custom made optic that delivers light back to the calibration screen mounted to the Rubin Obs. dome.

Below are assumptions about the system made in this exposure time calculator:

- Outer diameter of calibration screen: 9.27 m
- Diameter of obscuration of calibration screen (i.e. inner diameter): 4.18 m
- Plate Scale of camera: 0.2 arcsec/pixel
- Total Number of Pixels: :math:`3.2\textrm{x}10^{9}` pixels

Throughput curves for the 6 filters used (u, g, r, i, z, y) are taken from `Docushare Collection-1777: Baseline Design Throughput <https://docushare.lsst.org/docushare/dsweb/View/Collection-1777>`__

Requirements
============

Key requirements can be found in `LSE-60 <https://docushare.lsst.org/docushare/dsweb/Get/LSE-60>`__.
The most important for the White Light Source are that the illumination from the calibration screen have a spatial uniformity of 10%. 

Additionally, according to TLS-REQ-0096, the intensity of the white light (e.g. LED) exitance from the screen should produce a spectral radiance of 3 milli-Jansky's per arcsec^2, as measured on the detector.

This can be converted to spectral exitance (M) in W/m2/Hz:

.. math:: 1 Jy = 10^{-26} \frac{W}{m^{2}\, Hz}

.. math:: E = W/m^{2}/\mu m/arcsec^{2}/s = 3.3 \frac{mJ}{arcsec^{2}} * 10^{-26} * \frac{c}{\lambda^{2}} * 10^{-6},

where :math:`\lambda` is measured in m.

To measure the required photon rate for a given wavelength:

.. math:: \textrm{photon rate} = \textrm{E} (W/m^{2}/\mu m/arcsec^{2}/s) * \textrm{bandpass} (\mu m) * \textrm{platescale} (arcsec) ^ 2 * (\frac{\lambda}{h\,c}),


If we assume that we want a SNR = 1000, 

.. math:: \textrm{exptime}= \frac{SNR^{2}}{\textrm{photon rate}}


.. table:: With a white light source centered at 500nm, the following photon rates would be required for each filter.

   +----+----------+-----------------+----------------------------+---------------+
   |    | Filter   |   Bandpass (um) |   Photon Rate (ph/pix/sec) |   ExpTime (s) |
   +----+----------+-----------------+----------------------------+---------------+
   |  0 | u        |           0.071 |                    3762.47 |      265.783  |
   +----+----------+-----------------+----------------------------+---------------+
   |  1 | g        |           0.147 |                   10368.5  |       96.4463 |
   +----+----------+-----------------+----------------------------+---------------+
   |  2 | r        |           0.139 |                   12734.2  |       78.5288 |
   +----+----------+-----------------+----------------------------+---------------+
   |  3 | i        |           0.127 |                   14124.7  |       70.7981 |
   +----+----------+-----------------+----------------------------+---------------+
   |  4 | z        |           0.103 |                   13201.5  |       75.7492 |
   +----+----------+-----------------+----------------------------+---------------+
   |  5 | y4       |           0.075 |                   10607.7  |       94.2711 |
   +----+----------+-----------------+----------------------------+---------------+

For the tunable laser, besides the requirement on uniformity, TLS-REQ-0098 states that the FWHM of the luminous exitance from the calibration screen be no wider that 1 nm and be adjustable by 1 nm step size with an accuracy of 1 nm. For the purposes of this document, we will assume that requirement is met. We will assume a desired median SNR of 100 for all bands using the tunable laser for flat field calibration.


Exposure Time Calculator
========================
Throughput
----------
There are several sources of throughput loss:

1. Calibration System Optics

- Light source mask: 0.3 throughput

(Note: for LED only; estimate was 0.2 but measured to be closer to 0.3)

- Fiber Coupling: 0.5 throughput

(For tunable laser only)

- Reflector and calibration screen reflectance: 0.01/100 @ 600nm

Needs filter dependence to be removed

2. Fiber Attenuation

Data from http://www.ceramoptec.de/ in units of dB/km. We are assuming a fiber length (distance) of 15m.

.. math:: \textrm{T} = 10^{\frac{-db/km}{distance(km)/10}}

.. figure:: /_static/fiber_att.png
   :name: fiber_att
   :target: ../_images/fiber_att.png
   :alt: fiber_att

3. Filter Transmission

- Using ideal filter curves from `Docushare Collection-1777: Baseline Design Throughput <https://docushare.lsst.org/docushare/dsweb/View/Collection-1777>`__

.. figure:: /_static/ideal_filters.png
   :name: filter_throughput
   :target: ../_images/ideal_filters.png
   :alt: ideal_filters

   Ideal filter throughput.

4. Detector Quantum Efficiency

.. figure:: /_static/det_tput.png
   :name: detector_throughput
   :target: ../_images/det_tput.png
   :alt: det_tput

   Detector Quantum Efficiency

Light Output
------------
We expect to use Thorlabs LEDs and the Ekspla Tunable Laser for flat field calibration. The Energetiq 99x FC data is shown for comparison.

.. figure:: /_static/thorlabs_leds.png
   :name: thorlabs_leds
   :target: ../_images/thorlabs_leds.png
   :alt: thorlabs_leds

   Thorlab LED Flux

.. figure:: /_static/energetiq.png
   :name: energetiq_99xFC
   :target: ../_images/energetiq.png
   :alt: energetiq

   Energetiq 99x FC flux with fiber multiplier of 2.25



.. figure:: /_static/nt242_output.png
   :name: nt242_output
   :target: ../_images/nt242_output.png
   :alt: nt242_output

   Ekspla NT242 Output


Calculator
----------
Details can be found in  ``LED_Throughput_Calc.ipynb`` and ``Laser_Throughput_Calc.ipynb`` found in the ``_static/`` folder at ``https://github.com/lsst-sitcom/sitcomtn-049``.

1. Start with the flux profile :math:`f(\lambda)` from the light source in W/nm
2. Calculate photons/sec from light source:

.. math:: ph/s/nm = f(\lambda) * \frac{\lambda (m)}{h\, c}

3. Multiply by throughput of calibration system optics
4. Multiply by fiber attenuation, filter efficiency and detector curves
5. Integrate all photons within a given bandpass
6. Divide by total number of pixels
7. Calculate exposure time to get SNR=1000


Results
=======
.. table:: Thorlabs LEDs

   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |    | LED        | Filter   |   Bandpass (um) |   Ph Rate (ph/pix/sec) |   Req. Ph Rate |   Exptime (s) |   Req. Exptime (s) | Pass   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  0 | M365L2-C1  | u        |           0.071 |                1243.92 |        7060.36 |      803.909  |           141.636  | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  1 | M365LP1-C1 | u        |           0.071 |                4743.25 |        7060.36 |      210.826  |           141.636  | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  2 | M385L2-C1  | u        |           0.071 |                1400.27 |        6345.87 |      714.148  |           157.583  | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  3 | M385LP1-C1 | u        |           0.071 |                6485.86 |        6345.87 |      154.182  |           157.583  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  4 | M455L3-C1  | g        |           0.147 |                8965.87 |       12520.8  |      111.534  |            79.8672 | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  5 | M470L3-C1  | g        |           0.147 |                6600.87 |       11734.3  |      151.495  |            85.2199 | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  6 | M505L3-C1  | g        |           0.147 |                3993.4  |       10164.2  |      250.413  |            98.3848 | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  7 | M530L3-C1  | g        |           0.147 |                3540.21 |        9227.9  |      282.469  |           108.367  | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  8 | M590L3-C1  | r        |           0.139 |                1941.87 |        9145.49 |      514.967  |           109.343  | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   |  9 | M617L3-C1  | r        |           0.139 |                8078.68 |        8362.59 |      123.783  |           119.58   | False  |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 10 | M625L3-C1  | r        |           0.139 |                9715.54 |        8149.88 |      102.928  |           122.701  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 11 | M660L4-C1  | r        |           0.139 |               15923    |        7308.42 |       62.8023 |           136.829  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 12 | M730L4-C1  | i        |           0.127 |                7055.02 |        6626.32 |      141.743  |           150.913  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 13 | M780L3-C1  | i        |           0.127 |                6454.12 |        5804.02 |      154.94   |           172.294  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 14 | M810L3-C1  | i        |           0.127 |                6462.9  |        5382.06 |      154.729  |           185.803  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 15 | M850L3-C1  | z        |           0.103 |               15919.3  |        4567.98 |       62.8169 |           218.915  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+
   | 16 | M940L3-C1  | y4       |           0.075 |                7524.54 |        3001.28 |      132.898  |           333.192  | True   |
   +----+------------+----------+-----------------+------------------------+----------------+---------------+--------------------+--------+

.. table:: Energetiq

   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |    | LED                     | Filter   |   Ph Rate (ph/pix/sec) |   Req. Ph Rate |   Exptime (s) |   Req. Exptime (s) | Pass   |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  0 | energetiq-99xfc@359.5nm | u        |                135.908 |        7278.04 |       7357.94 |           137.4    | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  1 | energetiq-99xfc@478.5nm | g        |                526.756 |       11321.2  |       1898.41 |            88.3302 | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  2 | energetiq-99xfc@621.5nm | r        |                535.776 |        8241.93 |       1866.45 |           121.331  | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  3 | energetiq-99xfc@754.5nm | i        |                540.748 |        6202.97 |       1849.29 |           161.213  | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  4 | energetiq-99xfc@869.5nm | z        |                765.297 |        4365.39 |       1306.68 |           229.075  | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+
   |  5 | energetiq-99xfc@959.5nm | y4       |                222.625 |        2880.52 |       4491.87 |           347.159  | False  |
   +----+-------------------------+----------+------------------------+----------------+---------------+--------------------+--------+

.. figure:: /_static/laser_snr_plots.png
   :name: laser_snr_plots
   :target: ../_images/laser_snr_plots.png
   :alt: laser_snr_plots

   Ekspla Tunable Laser SNR plots 




