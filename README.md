# Linked Data Content Negotiation for Rack Applications

This is [Rack][] middleware that provides [Linked Data][] content
negotiation for Rack applications. You can use `Rack::RDF` with any
Ruby web framework based on Rack, including with Ruby on Rails 3.0 and with
Sinatra.

This version is based on [rack-linkeddata][] without the hard dependency on the [linkeddata][] gem, to allow applications to better manage their dependencies

* <http://github.com/datagraph/rack-rdf>

[![Gem Version](https://badge.fury.io/rb/rack-rdf.png)](http://badge.fury.io/rb/rack-rdf)
[![Build Status](https://travis-ci.org/ruby-rdf/rack-rdf.png?branch=master)](http://travis-ci.org/ruby-rdf/rack-rdf)

## Features

* Implements [HTTP content negotiation][conneg] for RDF content types.
* Supports all [RDF.rb][] compatible serialization formats.
* Compatible with any Rack application and any Rack-based framework.

## Examples

### Adding RDF content negotiation to a Rails 3.x application

    # config/application.rb
    require 'rack/rdf'
    
    class Application < Rails::Application
      config.middleware.use Rack::RDF::ContentNegotiation
    end

### Adding RDF content negotiation to a Sinatra application

    #!/usr/bin/env ruby -rubygems
    require 'sinatra'
    require 'rack/linkeddata'
    
    use Rack::LinkedData::ContentNegotiation
    
    get '/hello' do
      RDF::Graph.new do |graph|
        graph << [RDF::Node.new, RDF::DC.title, "Hello, world!"]
      end
    end

### Adding RDF content negotiation to a Rackup application

    #!/usr/bin/env rackup
    require 'rack/rdf'
    
    rdf = RDF::Graph.new do |graph|
      graph << [RDF::Node.new, RDF::DC.title, "Hello, world!"]
    end
    
    use Rack::RDF::ContentNegotiation
    run lambda { |env| [200, {}, rdf] }

### Defining a default RDF content type

    use Rack::RDF::ContentNegotiation, default: "text/turtle"

Options are also passed to the writer, which can allow options to be shared among the application
and different components.

    shared_options = {:default => "text/turtle", :standard_prefixes => true, }
    use Rack::RDF::ContentNegotiation, shared_options
    run MyApplication, shared_options

### Testing RDF content negotiation using `rackup` and `curl`

    $ rackup doc/examples/hello.ru
    
    $ curl -iH "Accept: text/plain" http://localhost:9292/hello
    $ curl -iH "Accept: text/turtle" http://localhost:9292/hello
    $ curl -iH "Accept: */*" http://localhost:9292/hello

## Description

`Rack::RDF` implements content negotiation for any [Rack][] response
object that implements the `RDF::Enumerable` mixin. You would typically
return an instance of `RDF::Graph` or `RDF::Repository` from your Rack
application, and let the `Rack::RDF::ContentNegotiation` middleware
take care of serializing your response into whatever RDF format the HTTP
client requested and understands.

The middleware queries [RDF.rb][] for the MIME content types of known RDF
serialization formats, so it will work with whatever serialization extensions
that are currently available for RDF.rb. (At present, this includes support
for N-Triples, N-Quads, Turtle, RDF/XML, RDF/JSON, JSON-LD, RDFa, TriG and TriX.)

##Documentation

<http://http://rubydoc.info/github/ruby-rdf/rack-rdf/>

* {Rack::RDF}
  * {Rack::RDF::ContentNegotiation}

## Dependencies

* [Rack](http://rubygems.org/gems/rack) (~> 2.0)
* [RDF](http://rubygems.org/gems/rdf) (~> 3.1)

## Installation

The recommended installation method is via [RubyGems](http://rubygems.org/).
To install the latest official release of the gem, do:

    % [sudo] gem install rack-rdf

## Download

To get a local working copy of the development repository, do:

    % git clone git://github.com/ruby-rdf/rack-rdf.git

Alternatively, download the latest development version as a tarball as
follows:

    % wget http://github.com/ruby-rdf/rack-rdf/tarball/master

## Contributing
This repository uses [Git Flow](https://github.com/nvie/gitflow) to mange development and release activity. All submissions _must_ be on a feature branch based on the _develop_ branch to ease staging and integration.

* Do your best to adhere to the existing coding conventions and idioms.
* Don't use hard tabs, and don't leave trailing whitespace on any line.
* Do document every method you add using [YARD][] annotations. Read the
  [tutorial][YARD-GS] or just look at the existing code for examples.
* Don't touch the `.gemspec`, `VERSION` or `AUTHORS` files. If you need to
  change them, do so on your private branch only.
* Do feel free to add yourself to the `CREDITS` file and the corresponding
  list in the the `README`. Alphabetical order applies.
* Do note that in order for us to merge any non-trivial changes (as a rule
  of thumb, additions larger than about 15 lines of code), we need an
  explicit [public domain dedication][PDD] on record from you.

## References

* <http://www.w3.org/DesignIssues/LinkedData.html>
* <http://linkeddata.org/docs/how-to-publish>
* <http://linkeddata.org/conneg-303-redirect-code-samples>
* <http://www.w3.org/TR/cooluris/>
* <http://www.w3.org/TR/swbp-vocab-pub/>
* <http://patterns.dataincubator.org/book/publishing-patterns.html>

## Authors

* [Arto Bendiken](http://github.com/bendiken) - <http://ar.to/>
* [Gregg Kellogg](http://github.com/gkellogg) - <http://greggkellogg.net/>

## License

This is free and unencumbered public domain software. For more information,
see <http://unlicense.org/> or the accompanying {file:UNLICENSE} file.

[Rack]:           http://rack.github.com/
[RDF.rb]:         http://ruby-rdf.github.com/rdf/
[Linked Data]:    http://linkeddata.org/
[conneg]:         http://en.wikipedia.org/wiki/Content_negotiation
[YARD]:           http://yardoc.org/
[YARD-GS]:        http://rubydoc.info/docs/yard/file/docs/GettingStarted.md
[PDD]:            http://unlicense.org/#unlicensing-contributions
