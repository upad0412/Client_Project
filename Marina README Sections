### Methods

#### Geocoding and Revese Geocoding

The USGS Hurricane High Water Marks dataset is logged by latitude and longitude.
The FEMA Relief Applications and Grants datasets are logged by postal address of the applicant.
In order to join these datasets together, we need to establish a ZIP code for the entries in the flood data, and a coordinate pair for the entires in the grants data. For the grants data, the street address and zip code were passed to the Google Maps geo-location API which returned the appropriate latitude-longitude pair. Documentation for this API is available at https://developers.google.com/maps/documentation/geocoding/start.  For the flood data, the latitude and longitude for each entry was passed to the reverse geocoding function and the appropriate ZIPcode was returned from the API.

#### ZIP Code Aggregation

The flooding and funding data were aggregated by ZIP code for purposes of comparison and ratios. The flood data was saved as average height (column 'height_above_gnd') per zip code and the funding data was saved as average FEMA grant amount (column 'totalObligated') per zip code. The aggregation also included the storm event, since the same ZIP code could be involved in multiple storms. 
These features, along with population and income per ZIP code, can be used to illustrate the disparity or equality of distribution of data.

#### Hypothesis Testing

We were most interested in inequalities and disparity in distribution of FEMA grant funding across multiple areas, so we wanted to examine how well the FEMA funding scales with flood severity, i.e. do the areas most impacted by flooding receive higher funding and the inverse? We found that there were three major reform bills passed in the last two decades that are intended to improve the distribution of funding. The Congressional Research Service published the following details in their report 'The Disaster Relief Fund:
Overview and Issues' (https://fas.org/sgp/crs/homesec/R45484.pdf):

1. The Post Katrina Emergency Reform Act of 2006 (PKEMRA)51—Enacted as
a sixth title to the FY2007 DHS Appropriations Act, PKREMRA reauthorized
and restructured FEMA, and made amendments to the Stafford Act, including
allowing federal assistance to be provided in the absence of a specific request,
improved assistance for individuals with disabilities, and expanded availability of
public assistance to non-governmental organizations.

2. The Sandy Recovery Improvement Act (SRIA)52—Enacted as a part of the
FY2013 supplemental appropriations act, SRIA included alternative procedures
for the Stafford Act Public Assistance program to allow disaster impacted area to
get assistance on the basis of cost estimates rather than reimbursement of costs,
among other reforms.

3. The Disaster Recovery Reform Act of 2018 (DRRA)53—Enacted through
language that was attached to an FAA reauthorization measure in the wake of
wildfires in California as well as Hurricanes Harvey, Irma, and Maria, DRRA has
provisions to broaden federal investments from the DRF into mitigation efforts to
protect public infrastructure, as well as making improvements to the Public
Assistance and Individual Assistance programs. For additional information on
these reforms, see CRS Report R45819, The Disaster Recovery Reform Act of
2018 (DRRA): A Summary of Selected Statutory Provisions.

We wanted to examine if any of these reforms had a significant impact on the distribution of FEMA Funding for disaster relief. The ratio of interest is the ratio of mean FEMA grants in a ZIP code to the mean flood depth in a ZIP code. If the funding is scaled closely with flood severity, this ratio will have a relatively low variance across the ZIP codes. We wanted to test if this variance changes before and after each one of these reform bills. The null hypothesis is that the variance in funding ratio is equal before and after this reform bill, and the alternative hypothesis is that these variances are not equal and we can infer that the reform bill had an effect on the distribution of funding. There are several statistical tests available to test equality of variances, however many require a normal distribution, which is not present in this case. For this case, the Levene test for equality of variances was selected because this test is suggested to perform better on highly skewed data. Details for this test are available at https://www.itl.nist.gov/div898/handbook/eda/section3/eda35a.htm. None of the p-values comparing before and after each reform were significant at alpha = 0.05, so we cannot conclude with statistical certainty that these reforms had a measureable effect on the variance of the ratio between funding and severity.