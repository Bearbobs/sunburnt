Sunburnt
========

Sunburnt is a Python-based interface for working with the [Apache Solr](http://lucene.apache.org/solr/) search engine.

It was written by Toby White <toby@timetric.com> for use in the [Timetric
platform](http://timetric.com).

Please send queries/comments/suggestions to the [mailing list](http://groups.google.com/group/python-sunburnt).

Bugs can be filed on the [issue tracker](https://github.com/tow/sunburnt/issues).

It's tested with Solr 1.4.1 and 3.1; previous versions were known to work
with 1.3 and 1.4 as well.

Full documentation can be found at http://opensource.timetric.com/sunburnt/index.html.

Dependencies
============

## Requirements: ##

  * [httplib2](http://code.google.com/p/httplib2/)
  * [lxml](http://lxml.de)

## Strongly recommended ##

  * [mx.DateTime](http://www.egenix.com/products/python/mxBase/mxDateTime/)

    Sunburnt will happily deal with dates stored either as Python datetime
    objects, or as mx.DateTime objects. The latter are preferable,
    having better semantics and a wider representation range. They will
    be used if present, otherwise sunburnt will fall back to Python
    datetime objects.

  * [pytz](http://pytz.sourceforge.net)

    If you're using native Python datetime objects with Solr (rather than
    mx.DateTime objects) you should also have pytz installed to guarantee
    correct timezone handling.

## Optional (only to run the tests) ##

  * `nose <http://somethingaboutorange.com/mrl/projects/nose/>`_

Additions
=========
Forked by Károly "Charles" Nagy to add some essential feature for other projects.

## Range facets: ##

Every range field can be set separately in order to fulfill the requirements. 
The facet_ranger.update() method takes a dictionary with the field names and 
the essential parameters (start, end and gap)

Example:
	
	from sunburnt import SolrInterface
	si = SolrInterface('http://some.url:8983/solr/')
	si = si.query('Query')
	si = si.facet_ranger.update({
		'price':
			{'range.start': 1, 'range.end': 99999, 'range.gap': 5000}
	})
		
[More info](<http://charlesnagy.info/it/python/solr-python-interface-sunburnt-fork "Range facet support for sunburnt")
	
## Spatial index: ##

Spatial filtering is now available through sunburnt with filter_spatial function. 

Example:
	

	from sunburnt import SolrInterface
	SOLR_URL = 'http://some.url:8983/solr/'
	_s = SolrInterface(SOLR_URL)
	_s = _s.query('*')
	_s = _s.filter_spatial(latlon=(47.4775899,18.8108992), distance=10)
	
Order by distance:

	_s = _s.filter_spatial(latlon=(47.4775899,18.8108992), distance=10).sort_by('score')
	
To have the distance in fields you need to add .field_limit(score=True)

	_s = _s.filter_spatial(latlon=(47.4775899,18.8108992), distance=10).field_limit(score=True).sort_by('score')
	
Note: In Solr 3.x in order to be able to retrieve the distance as score the following workaround is necessary in search.py:

	if self.qgeodist:
	opts.update({
		'q': '{!func}geodist()',
	})

For more info: http://wiki.apache.org/solr/SpatialSearch#Returning_the_distance

@TODO As a side effect parameters passed in query() will be lost, filter() works properly.

@TODO make geodist function flaggable when calling the filter_spatial

## Stats Component ##

StatsComponent is now available with stats function.

More info: http://wiki.apache.org/solr/StatsComponent

Example:
    

    from sunburnt import SolrInterface

    si = SolrInterface(SOLR_URL)
    si = si.query('*')
    si = si.stats(fields=['price', ])
    _r = si.execute()
    print _r.stats_counts.stats_fields

## Dataimport handler: ##

I had to trigger the dataimporter delta-import command from the code so I added support for this function. 

Example:

	from sunburnt import SolrInterface
	SOLR_URL = 'http://some.url:8983/solr/'
	_s = SolrInterface(SOLR_URL)
	_s.dataimport('delta-import')
	_response = _s.dataimport('status')
	print _response.import_status
	
You can find me on [Twitter](https://twitter.com/charlesnagy "Charlesnagy Twitter"), [My Blog](http://charlesnagy.info/ "Charlesnagy.info") or [LinkedIn](http://www.linkedin.com/in/nkaroly "Károly Nagy - MySQL DBA")

