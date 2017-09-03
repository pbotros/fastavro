fastavro
========
[![Build Status](https://travis-ci.org/tebeka/fastavro.svg?branch=master)](https://travis-ci.org/tebeka/fastavro)

**If you're interested in maintaining this package - please drop me a line**

The current Python `avro` package is packed with features but dog slow.

On a test case of about 10K records, it takes about 14sec to iterate over all of
them. In comparison the JAVA `avro` SDK does it in about 1.9sec.

`fastavro` is less feature complete than `avro`, however it's much faster. It
iterates over the same 10K records in 2.9sec, and if you use it with PyPy it'll
do it in 1.5sec (to be fair, the JAVA benchmark is doing some extra JSON
encoding/decoding).

If the optional C extension (generated by [Cython][cython]) is available, then
`fastavro` will be even faster. For the same 10K records it'll run in about
1.7sec.

`fastavro` supports the following Python versions:

* Python 2.6
* Python 2.7
* Python 3.4
* Python 3.5
* Python 3.6
* PyPy
* PyPy3

[Cython]: http://cython.org/

Usage
=====

Reading
-------


```python
import fastavro as avro

with open('weather.avro', 'rb') as fo:
    reader = avro.reader(fo)
    schema = reader.schema

    for record in reader:
        process_record(record)
```

You may also explicitly specify reader schema to perform schema validation:

```python
import fastavro as avro

schema = {
    'doc': 'A weather reading.',
    'name': 'Weather',
    'namespace': 'test',
    'type': 'record',
    'fields': [
        {'name': 'station', 'type': 'string'},
        {'name': 'time', 'type': 'long'},
        {'name': 'temp', 'type': 'int'},
    ],
}


with open('weather.avro', 'rb') as fo:
    reader = avro.reader(fo, reader_schema=schema)

    # will raise a fastavro.reader.SchemaResolutionError in case of
    # incompatible schema
    for record in reader:
        process_record(record)
```

Writing
-------

```python
from fastavro import writer

schema = {
    'doc': 'A weather reading.',
    'name': 'Weather',
    'namespace': 'test',
    'type': 'record',
    'fields': [
        {'name': 'station', 'type': 'string'},
        {'name': 'time', 'type': 'long'},
        {'name': 'temp', 'type': 'int'},
    ],
}

# 'records' can be any iterable (including a generator)
records = [
    {u'station': u'011990-99999', u'temp': 0, u'time': 1433269388},
    {u'station': u'011990-99999', u'temp': 22, u'time': 1433270389},
    {u'station': u'011990-99999', u'temp': -11, u'time': 1433273379},
    {u'station': u'012650-99999', u'temp': 111, u'time': 1433275478},
]

with open('weather.avro', 'wb') as out:
    writer(out, schema, records)
```

You can also use the `fastavro` script from the command line to dump `avro`
files.

    fastavro weather.avro

By default fastavro prints one JSON object per line, you can use the `--pretty`
flag to change this.

You can also dump the avro schema

    fastavro --schema weather.avro


Here's the full command line help

    usage: fastavro [-h] [--schema] [--codecs] [--version] [-p] [file [file ...]]

    iter over avro file, emit records as JSON

    positional arguments:
      file          file(s) to parse

    optional arguments:
      -h, --help    show this help message and exit
      --schema      dump schema instead of records
      --codecs      print supported codecs
      --version     show program's version number and exit
      -p, --pretty  pretty print json

Installing
==========
`fastavro` is available both on [PyPi](http://pypi.python.org/pypi) 

    pip install fastavro

and on [conda-forge](https://conda-forge.github.io) `conda` channel.

    conda install -c conda-forge fastavro

Hacking
=======

As recommended by Cython, the C files output is distributed. This has the
advantage that the end user does not need to have Cython installed. However it
means that every time you change `fastavro/pyfastavro.py` you need to run
`make`.

For `make` to succeed you need both python and Python 3 installed, Cython on both
of them. For `./test-install.sh` you'll need [virtualenv][venv].

[venv]: http://pypi.python.org/pypi/virtualenv


Changes
=======

See the [ChangeLog]

[ChangeLog]: https://github.com/tebeka/fastavro/blob/master/ChangeLog

Contact
=======

[Project Home](https://github.com/tebeka/fastavro)
