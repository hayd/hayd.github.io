---
layout: post
title:  "Reading json directly into pandas"
date:   2013-06-12 22:05:17
categories: python pandas json
---

New to pandas 0.12 release, is a [`read_json`](http://pandas.pydata.org/pandas-docs/dev/generated/pandas.io.json.read_json.html) function (which uses the speedy [ujson](https://pypi.python.org/pypi/ujson) under the hood).

It's as easy as whacking in the path/url/string of a [valid](http://jsonlint.com/) json:

{% highlight python %}
In [1]: df = pd.read_json('https://api.github.com/repos/pydata/pandas/issues?per_page=5')
{% endhighlight %}


Let's inspect a few columns to see how we've done:

{% highlight python %}
In [2]: df[['created_at', 'title', 'body', 'comments']]
Out[2]:
           created_at                                   title                                     body  comments
0 2013-06-12 02:54:37          DOC add to_datetime to api.rst  Either I'm being thick or `to_dateti...         4
1 2013-06-12 01:16:19             ci/after_script.sh missing?  https://travis-ci.org/gliptak/pandas...         0
2 2013-06-11 23:07:52        ENH Prefer requests over urllib2  At the moment we use urllib2 for htt...         7
3 2013-06-11 21:12:45           Nothing in docs about io.data  There's nothing on the docs about th...         0
4 2013-06-11 19:50:17  DOC: Clarify quote behavior parameters  I've been bit many times recently by...         1
{% endhighlight %}

The `parse_dates` argument has a good crack at parsing any columns which look like they're dates, and it's worked in this example (converting created_at to Timestamps). It looks carefully at the datatype and at column names (you can pass also pass a column name explicitly to ensure it gets converted) to choose which to parse.

After you've done some analysis in your favourite data analysis library, the corresponding `to_json` allows you can export results to valid json.

{% highlight python %}
In [4]: res = df[['created_at', 'title', 'body', 'comments']].head()

In [5]: res.to_json()
Out[5]: '{"created_at":{"0":1370695148000000000,"1":1370665875000000000,"2":1370656273000000000,"3":1370649233000000000,"4":1370646347000000000},"title":{"0":"CLN: refactored url accessing and filepath conversion from urls to io.common","1":"minor doc issue: prevent ipython history cache warning in examples","2":"Alter imports in pandas\\/__init__.py to be explicit","3":"Allow aggregate funcs to return arrays in groupby","4":"ENH: add ujson support in pandas.io.json"},"body":{"0":"","1":"http:\\/\\/pandas.pydata.org\\/pandas-docs\\/dev\\/timeseries.html#period","2":"Also adds an `__all__` to all the api files as well as a test case that\\nchecks that everything in the api files ends up in the toplevel pandas\\nnamespace. I personally find it hard to trace back the top level pandas\\nfunctions because of the `from xyz import *` calls. I changed everything\\nto be explicit. I think it\'s better, but it\'s your call.\\n\\nThe only large change with this is that the `pandas` module\\/package no\\nlonger exports `numpy\\/np` at the top level.","3":"fixes #3788\\n\\nPlease check out whether you like the error message for `Performance Warning`.\\n\\nAlso, I\'m not sure whether this means that groupby fails under certain conditions and not others (like when trying Cython, etc.).","4":"This is @wesm PR #3583 with this:\\r\\n\\r\\nIt builds now, and passes travis on py2 and py3, had 2 issues:\\r\\n\\r\\n- clean was erasing the *.c files from ujson\\r\\n- the module import didn\'t work because it was using the original init function\\r\\n\\r\\nConverted to new io API: ``to_json`` \\/ ``read_json``\\r\\n\\r\\nDocs added"},"comments":{"0":0,"1":1,"2":20,"3":18,"4":6}}'
{% endhighlight %}
	
Here, `orient` decides how we should layout the data:

{% highlight python %}
orient : {'split', 'records', 'index', 'columns', 'values'},
    default is 'index' for Series, 'columns' for DataFrame

    The format of the JSON string
    split : dict like
        {index -> [index], columns -> [columns], data -> [values]}
    records : list like [{column -> value}, ... , {column -> value}]
    index : dict like {index -> {column -> value}}
    columns : dict like {column -> {index -> value}}
    values : just the values array
{% endhighlight %}
	    
For example (note times have been exported as epoch, but we could have used iso via):

{% highlight python %}
In [6]: res.to_json(orient='records')
Out[6]: '[{"created_at":1370695148000000000,"title":"CLN: refactored url accessing and filepath conversion from urls to io.common","body":"","comments":0},{"created_at":1370665875000000000,"title":"minor doc issue: prevent ipython history cache warning in examples","body":"http:\\/\\/pandas.pydata.org\\/pandas-docs\\/dev\\/timeseries.html#period","comments":1},{"created_at":1370656273000000000,"title":"Alter imports in pandas\\/__init__.py to be explicit","body":"Also adds an `__all__` to all the api files as well as a test case that\\nchecks that everything in the api files ends up in the toplevel pandas\\nnamespace. I personally find it hard to trace back the top level pandas\\nfunctions because of the `from xyz import *` calls. I changed everything\\nto be explicit. I think it\'s better, but it\'s your call.\\n\\nThe only large change with this is that the `pandas` module\\/package no\\nlonger exports `numpy\\/np` at the top level.","comments":20},{"created_at":1370649233000000000,"title":"Allow aggregate funcs to return arrays in groupby","body":"fixes #3788\\n\\nPlease check out whether you like the error message for `Performance Warning`.\\n\\nAlso, I\'m not sure whether this means that groupby fails under certain conditions and not others (like when trying Cython, etc.).","comments":18},{"created_at":1370646347000000000,"title":"ENH: add ujson support in pandas.io.json","body":"This is @wesm PR #3583 with this:\\r\\n\\r\\nIt builds now, and passes travis on py2 and py3, had 2 issues:\\r\\n\\r\\n- clean was erasing the *.c files from ujson\\r\\n- the module import didn\'t work because it was using the original init function\\r\\n\\r\\nConverted to new io API: ``to_json`` \\/ ``read_json``\\r\\n\\r\\nDocs added","comments":6}]'
{% endhighlight %}
    
*Note, our times have been converted to unix timestamps (which also means we'd need to use the same `pd.to_datetime` trick 
when read_json it back in). Also NaNs, NaTs and Nones will be converted to JSON's null.*

And save it to a file:

{% highlight python %}
In [7]: res.to_json(file_name)
{% endhighlight %}

Useful.

*Warning: `read_json` requires [*valid* JSON](http://jsonlint.com/), so doing something like will cause some Exception:*

{% highlight python %}
In [8]: pd.read_json("{'0':{'0':1,'1':3},'1':{'0':2,'1':4}}")
# ValueError, since this isn't valid JSON

In [9]: pd.read_json('{"0":{"0":1,"1":3},"1":{"0":2,"1":4}}')
Out[9]: 
   0  1
0  1  2
1  3  4
{% endhighlight %}
	
Just as an further example, here I can get all the issues from github (there's a limit of 100 per request), this is how easy it is to extract data in pandas:

{% highlight python %}
In [10]: page = 1
         df = pd.read_json('https://api.github.com/repos/pydata/pandas/issues?page=%s&per_page=100' % page)
         while df:
             dfs[page] = df
             page += 1
             df = pd.read_json('https://api.github.com/repos/pydata/pandas/issues?page=%s&per_page=100' % page)

In [11]: dfs.keys()  # 7 requests come back with issues
Out[11]: [1, 2, 3, 4, 5, 6, 7]

In [12]: df = pd.concat(dfs, ignore_index=True).set_index('number)

In [13]: df
Out[13]:
<class 'pandas.core.frame.DataFrame'>
Int64Index: 613 entries, 3813 to 39
Data columns (total 18 columns):
assignee        27  non-null values
body            613  non-null values
closed_at       0  non-null values
comments        613  non-null values
comments_url    613  non-null values
created_at      613  non-null values
events_url      613  non-null values
html_url        613  non-null values
id              613  non-null values
labels          613  non-null values
labels_url      613  non-null values
milestone       586  non-null values
pull_request    613  non-null values
state           613  non-null values
title           613  non-null values
updated_at      613  non-null values
url             613  non-null values
user            613  non-null values
dtypes: datetime64[ns](1), int64(2), object(15)

In [14]: df.comments.describe()
Out[14]:
count    613.000000
mean       3.590538
std        9.641128
min        0.000000
25%        0.000000
50%        1.000000
75%        4.000000
max      185.000000
dtype: float64
{% endhighlight %}
	
It deals with fairly moderately sized files fairly efficiently, here's [a 200Mb file](https://github.com/hayd/sf-city-lots-json) (this is on my 2009 macbook air, I'd expect times to be may be faster on better hardware e.g. an SSD):

{% highlight python %}
In [15]: %time pd.read_json('citylots.json')
CPU times: user 4.78 s, sys: 684 ms, total: 5.46 s
Wall time: 5.89 s
{% endhighlight %}
	
Thanks to [wesm](https://github.com/wesm), [jreback](https://github.com/jreback) and [Komnomnomnom](https://github.com/Komnomnomnom) for putting it together.
