# Client Project 12
## Chicago DSI 11
5/15/2020

Marina Baker, Upasana Mahanta, Lee Littlejohn

<strong>Provided Prompt:</strong>

Problem 12: Extracting Flood Depths from Imagery

Problem Statement: 

Floods cause damage to infrastructure and homes. The depth of flood waters is a good indicator of the severity of damage. Floods are incredibly difficult to model, and while model outputs are useful to emergency managers, it is crucial to know the actual depth. Social media and news outlets often present pictures of floods. How can this imagery be used to estimate the depth of water in a given area?

Proposed Deliverables:
- A short write up describing the project, results, and next steps or proposal to scale
- Open source code for estimating flood depths from ground-based imagery
- Example from flooding after a recent hurricane (e.g. Imelda, Florence, Harvey)

Descriptions of input data:
- Social media
- News (TV, internet, etc.)
- Traffic cameras
- Google Street View (“normal” imagery)

#### Initial Investigation

Some similar computer vision projects we investigated first are listed below:
- [FLOOD-WATER LEVEL ESTIMATION FROM SOCIAL MEDIA IMAGES](https://www.isprs-ann-photogramm-remote-sens-spatial-inf-sci.net/IV-2-W5/5/2019/isprs-annals-IV-2-W5-5-2019.pdf)
- [Detecting floodwater on roadways from image data with handcrafted features and deep transfer learning](https://arxiv.org/ftp/arxiv/papers/1909/1909.00125.pdf)
- [Flood Depth Estimation from Web Images](https://dl.acm.org/doi/pdf/10.1145/3356395.3365542)

After reading these papers, a major roadblock became apparent: access to good data. These papers use manually collected images and train models for flood depth using either object-detection and relative above-water visibility, by manually annotation by subjective human inference. In order to make more accurate predictions without the need for a complex and time-intensive deep learning framework, we chose to focus on flooding from Hurricane Harvey in Houston, TX due to the available flood depth data from the USGS and the large area that was under water, hopefully providing a higher likelihood of locating images.

We collected images from Google, Getty, the Associated Press, Instagram, and Reddit, looking specifically for any images that could be manually tagged with spatial coordinates by matching up landmarks or street signs, hoping to use the locations with the USGS data to get a more accurate assessment of depth. After a few days of collecting and annotating, we decided that collecting and geolocating enough images to effectively model flood depth would require more time and resources than we had available.

What to do, then? Pivot!

# Client Project 12B
## Chicago DSI 11
5/15/2020

Marina Baker, Upasana Mahanta, Lee Littlejohn

### Problem Statement

The federal response to Hurricane Katrina was widely criticised, and since then three major pieces of legislation aimed at improving the response of FEMA have been passed: the Post-Katrina Emergency Management Reform Act of 2006, the Sandy Recovery Improvement Act of 2013, and the Disaster Recovery Reform Act of 2018. With access to FEMA grant information, USGS flood depth data, and AGI numbers from the IRS, can the efficacy of these pieces of legislation be seen in the data? If flood depth is an indicator of damage, can flood depth be used to study financial assistance from the federal government? Does the data show patterns of funding discrepencies? 

### Data Acquisition and Cleaning

To begin exploring this problem, we wanted to find as much data as we could that may be relevant to the allotment of federal funding after flooding damage.

First, we gathered flood depth measurements from the United States Geological Survey [Flood Event Viewer](https://stn.wim.usgs.gov/FEV/). These tables included many measurements that we did not need, such as the names of the employees that took the measurements. We wanted, in particular, the depth, spatial coordinates, and "eventName" per observation. The "eventName" from the USGS is effectively the name of each hurricane; however, many other features were kept as well for potential future analysis. For aggregate statistics and matching to other sources of data, we used the Geolocating API from Google Maps to collect the zipcode of each observation from a latitude and longitude.

The FEMA (Federal Emergency Management Agency) hosts records of financial assistance applications and grants online. We used the [applicants](https://www.fema.gov/openfema-dataset-public-assistance-applicants-v1) data and the Geolocating API to generate spatial coordinates for financial assistance, as well as [funded projects](https://www.fema.gov/openfema-dataset-public-assistance-funded-projects-details-v1). This data is presented by FEMA as raw, in that the organization admits to the presence of human error. For instance, in this case, many entries for grants and funding were negative. These entries followed patterns consistent to their positive counterparts and were thus inverted to preserve the observations.

Population Data per zipcode from the 2010 Census was retrieved from the public datasets hosted on Google Bigquery.

Adjusted gross income data by zipcode is available from the IRS for 2010 at the IRS [website](https://www.irs.gov/statistics/soi-tax-stats-individual-income-tax-statistics-zip-code-data-soi).

After joining the FEMA, census, and IRS data, aggregate statistics were generated for flood depths, population, grants, and AGI, resulting in two dataframes saved to CSV files in this repository. Detailed descriptions follow.

#### Data Dictionary for depths_and_hurricanes.csv

|Column|Description|
|---|---|
|latitude|Latitude of measurement|
|longitude|Longitude of measurement|
|site_latitude|Assumed to be identical to latitude, although minor discrepencies exist|           
|site_longitude|Assumed to be identical to longitude, although minor discrepencies exist|         
|eventName|Name of hurricane|                             
|stateName|State in which measurement was taken|                            
|countyName|County in which measurement was taken|                                                       
|hwm_id|Internal USGS id|                                
|hwm_locationdescription|Plain description of measurement site|               
|elev_ft|Measurement site elevation above sea level| 
|height_above_gnd|Flood depth measurement|
|hwm_environment|Measurement site category|
|zip|Zipcode in which measurement was taken
|height_above_gnd_decile_by_eventName|Flood depth decile aggregated by hurricane|  
|height_above_gnd_scaled_by_eventName|Flood depth scaled (z-score) aggregated by hurricane|  
|elev_ft_decile_by_eventName|Measurement site elevation above sea level decile aggregated by hurricane|
|height_above_gnd_decile_by_zip|Flood depth decile aggregated by zipcode|
|height_above_gnd_mean_by_zip|Flood depth scaled (z-score) aggregated by zipcode|        


#### Data Dictionary for grants_money_pop.csv

|Column|Description|
|---|---|
|disasterNumber|FEMA reference number for disaster classification|
|declarationDate|date the disaster was declared|
|applicantId|Unique Public Assistance applicant identification number|
|damageCategory|The category code of the damage location|
|projectSize|Projects are designated as Large or Small|
|state|The name of a U.S. state or territory|
|projectAmount|The estimated total cost of the Public Assistance grant project in dollars, without administrative costs|
|federalShareObligated|The Public Assistance grant funding available to the grantee (State) in dollars, for sub-grantee's approved Project Worksheets|
|totalObligated|The federal share of the Public Assistance grant eligible project amount in dollars, plus grantee (State) and sub-grantee (applicant) administrative costs. The federal share is typically 75% of the total cost of the project|
|obligatedDate|Date the grant was obligated|
|applicantName|Name of the entity requesting Public Assistance Grant funding|
|addressLine1|A location at which a particular organization is found, Line 1; Applicant in this dataset|
|addressLine2|A location at which a particular organization is found, Line 2; Applicant in this dataset|
|city|A center of population; Applicant in this dataset|
|zipCode|Unique geographical identifier for a U.S. postal address; Applicant in this dataset|
|latitude|Latitude of applicant|
|longitude|Longitude of applicant|
|FEMA Event Name|FEMA event name, corresponds to unique disasterNumber|
|USGS Flood Event Name|Name of hurricane|
|Timeline_Category|Number designating location between legislation, 0: before PKEMRA, 1: before SRIA, 2: before DRRA, 3: after DRRA|
|projectAmount_decile_by_USGS Flood Event Name|projectAmount decile aggregated by hurricane|
|projectAmount_scaled_by_USGS Flood Event Name|scaled projectAmount aggregated by hurricane|
|federalShareObligated_decile_by_USGS Flood Event Name|federalShareObligated decile aggregated by hurricane|
|federalShareObligated_scaled_by_USGS Flood Event Name|scaled projectAmount aggregated by hurricane|
|totalObligated_decile_by_USGS Flood Event Name|totalObligated decile aggregated by hurricane|
|totalObligated_scaled_by_USGS Flood Event Name|scaled totalObligated aggregated by hurricane|
|zipcode|Zipcode of applicant|
|population|total population aggregated by zipcode|
|population_decile_by_zip|Population decile calculated aggregated by zipcode|
|population_scaled_by_zip|Population decile calculated aggregated by hurricane|
|total_agi_per_zip|Total AGI aggregated by zipcode|
|agi_per_capita_per_zip|AGI per capita aggregated by zipcode|

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
