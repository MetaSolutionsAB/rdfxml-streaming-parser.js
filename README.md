# RDF/XML Streaming Parser

[![Build Status](https://travis-ci.org/rubensworks/rdfxml-streaming-parser.js.svg?branch=master)](https://travis-ci.org/rubensworks/rdfxml-streaming-parser.js)
[![Coverage Status](https://coveralls.io/repos/github/rubensworks/rdfxml-streaming-parser.js/badge.svg?branch=master)](https://coveralls.io/github/rubensworks/rdfxml-streaming-parser.js?branch=master)
[![npm version](https://badge.fury.io/js/rdfxml-streaming-parser.svg)](https://www.npmjs.com/package/rdfxml-streaming-parser) [![Greenkeeper badge](https://badges.greenkeeper.io/rubensworks/rdfxml-streaming-parser.js.svg)](https://greenkeeper.io/)

A [fast](https://gist.github.com/rubensworks/a351f394ca6b70d6ad4ec1adc691a453), _streaming_ [RDF/XML](https://www.w3.org/TR/rdf-syntax-grammar/) parser
that outputs [RDFJS](http://rdf.js.org/)-compliant quads.

## Installation

```bash
$ yarn install rdfxml-streaming-parser
```

This package also works out-of-the-box in browsers via tools such as [webpack](https://webpack.js.org/) and [browserify](http://browserify.org/).

## Require

```javascript
import {RdfXmlParser} from "rdfxml-streaming-parser";
```

_or_

```javascript
const RdfXmlParser = require("rdfxml-streaming-parser").RdfXmlParser;
```

## Usage

`RdfXmlParser` is a Node [Transform stream](https://nodejs.org/api/stream.html#stream_class_stream_transform)
that takes in chunks of RDF/XML data,
and outputs [RDFJS](http://rdf.js.org/)-compliant quads.

It can be used to [`pipe`](https://nodejs.org/api/stream.html#stream_readable_pipe_destination_options) streams to,
or you can write strings into the parser directly.

### Print all parsed triples from a file to the console

```javascript
const myParser = new RdfXmlParser();

fs.createReadStream('myfile.rdf')
  .pipe(myParser)
  .on('data', console.log)
  .on('error', console.error)
  .on('end', () => console.log('All triples were parsed!'));
```

### Manually write strings to the parser

```javascript
const myParser = new RdfXmlParser();

myParser
  .on('data', console.log)
  .on('error', console.error)
  .on('end', () => console.log('All triples were parsed!'));

myParser.write('<?xml version="1.0"?>');
myParser.write(`<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
         xmlns:ex="http://example.org/stuff/1.0/"
         xml:base="http://example.org/triples/">`);
myParser.write(`<rdf:Description rdf:about="http://www.w3.org/TR/rdf-syntax-grammar">`);
myParser.write(`<rdf:Description rdf:about="http://www.w3.org/TR/rdf-syntax-grammar">`);
myParser.write(`<ex:prop />`);
myParser.write(`</rdf:Description>`);
myParser.write(`</rdf:RDF>`);
myParser.end();
```

## Configuration

Optionally, the following parameters can be set in the `RdfXmlParser` constructor:

* `dataFactory`: A custom [RDFJS DataFactory](http://rdf.js.org/#datafactory-interface) to construct terms and triples. _(Default: `require('@rdfjs/data-model')`)_
* `baseIRI`: An initital default base IRI. _(Default: `''`)_
* `defaultGraph`: The default graph for constructing [quads](http://rdf.js.org/#dom-datafactory-quad). _(Default: `defaultGraph()`)_
* `strict`: If the internal SAX parser should parse XML in strict mode, and error if it is invalid. _(Default: `false`)_


```javascript
new RdfXmlParser({
  dataFactory: require('@rdfjs/data-model'),
  baseIRI: 'http://example.org/',
  defaultGraph: namedNode('http://example.org/graph'),
  strict: true,
});
```

## License
This software is written by [Ruben Taelman](http://rubensworks.net/).

This code is released under the [MIT license](http://opensource.org/licenses/MIT).
