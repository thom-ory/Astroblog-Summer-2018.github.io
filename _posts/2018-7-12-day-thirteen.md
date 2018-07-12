After the morning meeting with Dr. G, I have a list of tasks that I want to accomplish by this weekend: 
1. Correct my error estimates from yesterday, which applied Poisson statistics to the wrong data type
2. Create a new version of the code that is designed to work with drizzled HST images
3. Improve my source detection. Right now, I am detecting for more sources than are actually there. I will try to determine the average, maximum, and minimum FWHM of sources in the field and utilize that information to improve my search. I also may expand the field some to 300x300, possibly up to 500x500 to include more sources. 

Getting this done before the weekend would leave me feeling prepared to look at the black-box HST photometry software that we're actually using for this particular data next week. I do hope we still end up using the codes Rory and I are writing for some of the real data analysis on our sources of interest; I'm starting to become proud of this code. 

## Better Error Estimation
To correct yesterday's code, I went back to [aperture_photometry.py](https://github.com/GosnellResearchGroupSummer2018/NGC6819/blob/master/photometry%20codes/aperture_photometry.py) and tried to adjust the code to actually deal with the fact that this is a drizzled image. The first thing I tried was just multiplying the data array by the exposure time for that observation at the very beginning of the code so that I get total flux at the end indtead of flux rate. For some reason, doing this causes [aperture_photometry.py](https://github.com/GosnellResearchGroupSummer2018/NGC6819/blob/master/photometry%20codes/aperture_photometry.py) to give the error "invalid values encountered in np.sqrt()", even though the negatives have been replaced by zero like before. Also, this error doesn't happen if I just don't multiply by effective gain. But, the code still compiles and runs and gives me this out:  
![fluxes]({{ thom-ory.github.io }}/images/flux_rates_2.png) 
_note: The error bars are tiny and very hard too see. If you look closely, you can see a little bit of red on top of most of the blue circles; this is the error_

---
I'm shocked at how tiny the errors are. I thought there was a problem at first and they weren't getting drawn, but I changed the plot to have the error bars over the data points instead of underneath them (visually), and sure enough they're there, and tiny. You can also see the values of the error in the last column of [phot_table.txt](https://github.com/GosnellResearchGroupSummer2018/NGC6819/blob/master/first_attempt_at_HST_source_detection/day_five/phot_table.txt).

I then tried to lean into the drizzled images that I already have to work with, and did an entirely different method that allowed me to get counts/s out. I saved this version of the file as [aperture_photometry_drz.py](https://github.com/GosnellResearchGroupSummer2018/NGC6819/blob/master/photometry%20codes/aperture_photometry_drz.py), and instead of explicitly writing out `np.sqrt(data)` to get a Poission uncertainty estimate, I used `photutils.utils.calc_total_error()`, which requires a "background error" (bkg_err) array (which is the error associated with background objects or the instrument and does not include Poisson noise) and an effective gain. The effective gain (gain_eff) is a conversion factor that converts whatever data you have into counts. Bkg_err and the data must have the same array shape and if you write units explicitly they must have the same units, and gain_eff must have units that cancel to counts when multiplied with the data array and be either a scalar or an array of the same shape as data. Since the drizzled data is already flat-fielded, and since I subtract the background with localized aperture photometry, I can make bkg_err an array full of zeros; there's nothing that bkg_err corrects for that I don't account for somewhere else. I used 7058 seconds as gain_eff, since that was how long the exposure time was, this cancels to total counts when multiplied with data. With this method, I no longer need to explicitly multiply the data array by the gain, and the results I get are in counts/s:  
!{flux_rates}({{ thom-ory.github.io }}/images/flux_rates.png)
_note: This graph looks proportionally the same as the other graph, but is a factor of 7058 smaller in value and has different units. The errors are significantly smaller than the results I got yesterday, which were an incorrect application of Poisson statistics since counts/s are not integer values_

---