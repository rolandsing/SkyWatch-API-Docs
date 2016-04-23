SkyWatch API Documentation
==========================
The SkyWatch API is a RESTful interface to the astronomy data collected for display in the `SkyWatch App <https://app.skywatch.co/>`_.
Main sources of the data include the Gamma Ray Coordinates Network and The Astronomer's Telegram.

How the data is organized
-------------------------
When a Gamma Ray Burst, Supernova or other transient occurrence is detected in the night sky, SkyWatch is notified through what we  call 
an **Event**. Because there are many space and ground based telescopes (called **Observatories**) on the network, we typically get 
several event notifications for the same event. Event notifications for same event are grouped together in what we call a **Portfolio**. 
This distinction is important when searching for information for a specific event. 

Simply finding the portfolio containing the event may be enough for your purposes. Information like location (Right Ascension and Declination
in Decimal Degrees), location error, redshift, the detection time of the latest event, the observatories that have observed the event and
the category of the event (Supernova, GRB, etc) is stored at the portfolio level. The portfolio is identified by a 12 byte alphanumeric 
string called the *portfolio_id*. Once the portfolio_id is determined, you can use that to find a specific event in that portfolio. Events
in a portfolio can be queried by the time detected or the category. Each event is uniquely identified by a 16 byte alphanumeric string
called the *event_id*.

The data returned
-----------------
All data is returned in JSON format, as a list of dictionaries. If your query returns no events or portfolios, an empty list is returned.
Each dictionary contains all of the non-null or non-zero data points that we have for that event or portfolio. A description of the data
points returned appears in :doc:`appendixa`. If the request ends in error, HTML containing the HTTP status code is returned.

Authentication
--------------
You must be a registered user on the SkyWatch App to use the API. You must specify your email address and password in the auth header of
the request. The API is served using SSL only, so the credentials are never passed in the clear.

API entry points
----------------

**https://api-public.skywatch.co/v0.1/portfolios/id/<portfolio_id>/**

Retrieve all of the non-null and non-zero data points for the specified <portfolio_id>. The potential data points returned are 
listed in :doc:`appendixa`.

**https://api-public.skywatch.co/v0.1/portfolios/date/<from_date>/<to_date>/**

Retrieve all of the portfolios where the latest event occurred from 00:00:00.000 UTC of the <from_date> to 23:59:59.999 UTC of the 
<to_date>. Dates must be specified in the YYYY-MM-DD format.

**https://api-public.skywatch.co/v0.1/portfolios/category/<category_name>/**

Retrieve all of the portfolios containing events of <category_name>. Category names are listed in :doc:`appendixb`.

**https://api-public.skywatch.co/v0.1/portfolios/observatory/<observatory_name>/**

Retrieve all of the portfolios containing events of observed at <observatory_name>. Observatory names are one of MAXI, Fermi, INTEGRAL,
SWIFT, MOA, KONUS, ATels, GCN Circulars.

**https://api-public.skywatch.co/v0.1/portfolios/location/<ra>/<dec>/<error>/**

Retrieve all of the portfolios containing events observed near <ra>, <dec> within error circle <error>. If <error> is not specified, 
it defaults to 0.1 degrees. <ra>, <dec> and <error> can only be specified in decimal degrees. Sexagesimal values are not accepted.

**https://api-public.skywatch.co/v0.1/portfolios/nearby/<portfolio_id>/**

Retrieve some of the data points from the SIMBAD database for the 10 closest known objects to the events in the portfolio specified by
<portfolio_id>. The potential data points returned are listed in :doc:`appendixc`.

**https://api-public.skywatch.co/v0.1/portfolios/telescopes/<portfolio_id>/**

Retrieve the data points for the three best telescopes to observe the events in the portfolio specified by <portfolio_id>. The potential 
data points returned are listed in :doc:`appendixc`.

**https://api-public.skywatch.co/v0.1/events/id/<event_id>/**

Retrieve all of the non-null and non-zero data points for the event specified by <event_id>. The potential data points returned are 
listed in :doc:`appendixb`.

**https://api-public.skywatch.co/v0.1/events/portfolio/<portfolio_id>/**

Retrieve all of the events in the portfolio specified by <portfolio_id>.

**https://api-public.skywatch.co/v0.1/events/portfolio/<portfolio_id>/category/<category_name>/**

Retrieve all of the events in the portfolio specified by <portfolio_id> containing events of <category_name>. Category names are listed
in :doc:`appendixb`.

**https://api-public.skywatch.co/v0.1/events/portfolio/<portfolio_id>/date/<from_date>/<to_date>/**

Retrieve all of the events in the portfolio specified by <portfolio_id> containing events where the latest event occurred from 
00:00:00.000 UTC of the <from_date> to 23:59:59.999 UTC of the <to_date>. Dates must be specified in the YYYY-MM-DD format.

Example
=======

Return the portfolio information for portfolio WS2WRDINQ7JK

.. code-block:: python

	import requests
	import pprint
	
	def main():
	    url = 'https://api-public.skywatch.co/v0.1/'
	    query = 'portfolios/id/WS2WRDINQ7JK/'
	    headers = { 'Accept': 'application/json' }
	    r = requests.get(url+query, headers=headers, auth=('roland@skywatch.co', 'XXXXXXXX'))
	    if r.status_code < 300:
	        pprint.pprint(r.json())
	
	if __name__ == '__main__':
	    main()
	    
Appendicies
===========
.. toctree::
   appendixa
   appendixb
   appendixc