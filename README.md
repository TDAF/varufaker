# varufaker
Small utility to generate log traces based on a template, supporting elastic Search indexing, files and stdout

## Usage
Install [nodejs](https://nodejs.org) 

```sh
npm install -g varufaker
```

```sh
varufaker --help

  Usage: varufaker [options] [command]


  Commands:

    elastic   Writes traces to Elastic Search
    stdout    Writes traces to stdout

  Options:

    -h, --help                 output usage information
    -V, --version              output the version number
    --every <every>            The delay between writes (milliseconds) [default: 1000]
                               Environment var: VA_EVERY
    --iterations <iterations>  The number of traces to write [default: 100]
                               Environment var: VA_ITERATIONS
    --template <template>      The template trace to write (see https://github.com/marak/Faker.js/) [default: ./lib/templates/tid.tpl]
                               Environment var: VA_TEMPLATE
    --esurl <esurl>            The Elastic Search endpoint (add your user pass if Shield is enabled) [default: http://localhost:9200]
                               Environment var: VA_ESURL
    --esindex <esindex>        The ElasticSearch index to write to (faker format supported) [default: varufaker-{{tid.yyyymmdd}}]
                               Environment var: VA_ESINDEX
    --verbose                  Write debug info to the stderr
                               Environment var: VA_VERBOSE

  Examples:

  Write to stdout
    $ varufaker stdout

  Write to a file
    $ varufaker stdout > ./out.txt

  Write to elasticsearch server directly[1]
    $ varufaker elastic
  [1] Only JSON templates are supported in this writer
```

## API 

```js
const varufaker = require('varufaker');

let writer = varufaker({ // ALL OPTIONAL 
  // same options than in command line
  every: 1000,
  iterations: 1000,
  template: '/path/to/template',
  verbose: true,  // Defaults to false
  // API only properties
  templateStr: 'my {{cool}} template', // This takes precedence over the template property above
  // Add your own stream to get the traces in your logic
  stream: {
    // Called with the trace (string) to write 
    write: function(str) { 
      // do whatever u want...
    }
  }
});

// ADD your own fakes to be used in the templates. E.g.
// { "myCustomField": "{{custom.myFake}}" }
varufaker.faker.custom = {
  myFake() {
    return 'This is Sparta';
  }
};

//write to stdout
writer.stdout()
  .then(success => process.stderr.write('All went fine? ' + success)); // dont write to stdout spurious traces ;)

// write to your custom stream
writer.stream().then(...);

```

## License

Copyright 2015, 2016 [Telefónica Investigación y Desarrollo, S.A.U](http://www.tid.es)

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
