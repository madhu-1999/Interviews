+ [I] Combine weather data with flight delays data for a more interesting dashboard.
# Tasks
+ [x]   Pick atleast 3 cities.
	According to this [source](https://www.oag.com/blog/most-popular-flight-routes-in-the-us) , the busiest domestic routes are:
	1. Los Angeles - San Francisco
	2. Las Vegas - Los Angeles
	Picking these to account for weather delays at both source and destination. Weather delays due to en-route weather conditions are not accounted for. Whether a delay was potentially due to source, destination or en-route weather conditions can be analyzed.
	They also seem to have very diverse weather conditions
	Lat-long data taken from [here](https://www.latlong.net/place/san-francisco-international-airport-usa-4273.html) (LAX, SFO, )
+ [ ] Flight data
	Can be downloaded from [here](https://www.transtats.bts.gov/DL_SelectFields.aspx?gnoyr_VQ=FGJ&QO_fu146_anzr=b0-gvzr) 
+ [ ] config db 
# DB Migration
To generate changes
```bash
alembic revision --autogenerate -m "add profile column"
```
To apply changes
```bash
alembic upgrade head
```
