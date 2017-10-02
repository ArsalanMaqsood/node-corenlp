# CoreNLP for NodeJS

This library helps making NodeJS applications using the state-of-the-art technology for Natural Language Processing: Stanford CoreNLP.

[![Build Status](https://travis-ci.org/gerardobort/node-corenlp.svg?branch=master)](https://travis-ci.org/gerardobort/node-corenlp)

[![NPM package](https://nodei.co/npm/corenlp.png)](https://www.npmjs.com/package/corenlp)

*This project is under active development, please stay tuned for updates.  More documentation and examples are comming.*

## Example

Assuming that StanfordCoreNLPServer is running on `http://localhost:9000`....

```javascript
import CoreNLP, { Properties, Pipeline } from 'corenlp';

const props = new Properties({
  annotators: 'tokenize,ssplit,pos,lemma,ner,parse',
});
const pipeline = new Pipeline(props, 'English'); // uses ConnectorServer by default

const sent = new CoreNLP.simple.Sentence('The little dog runs so fast.');
pipeline.annotate(sent)
  .then(sent => {
    console.log('parse', sent.parse());
    console.log(CoreNLP.util.Tree.fromSentence(sent).dump());
  })
  .catch(err => {
    console.log('err', err);
  });
```

## API

Read the full [API documentation](https://gerardobort.github.io/node-corenlp/docs).

## Setup

### 1. Install the package:

```bash
npm i --save corenlp
```

### 2. Download Stanford CoreNLP

### 2.1. Shortcut (recommended to give this library a first try)

Via `npm`, run this command from your own project after having installed this library:

```bash
npm explore corenlp -- npm run corenlp:download
```

Once downloaded you can easily start the server by running

```bash
npm explore corenlp -- npm run corenlp:server
```

Or you can manually download the project from the Stanford's CoreNLP download section at: https://stanfordnlp.github.io/CoreNLP/download.html
You may want to download, apart of the full package, other language models (see more on that page).

### 2.2. Via sources

For advanced projects, in where you may need to customize the library, we highly recommend to download the source code from the [original repository](https://github.com/stanfordnlp/CoreNLP), and compile it using `ant jar`.

*NOTE*: Some functionality included in this library, for `TokensRegex`, `Semgrex` and `Tregex`, requires the latest version from the repository, which is more recent than the last stable.

### 3. Configure Stanford CoreNLP

There are two method to connect your NodeJS application to Stanford CoreNLP:

1. HTTP is the preferred method since it requires CoreNLP to initialize just once to serve many requests, it also avoids extra I/O given that the CLI method need to write temporary files to run *recommended*.
2. Via Command Line Interface, this is by spawning processes from your app.


#### 3.1. Using StanfordCoreNLPServer

```bash
# Run the server using all jars in the current directory (e.g., the CoreNLP home directory)
java -mx4g -cp "*" edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000
```

CoreNLP connects by default via StanfordCoreNLPServer, using port 9000.  You can also opt to setup the connection differently:

```javascript
import CoreNLP, { Properties, Pipeline, ConnectorServer } from 'corenlp';

const connector = new ConnectorServer({ dsn: 'http://localhost:9000' });
const props = new Properties({
  annotators: 'tokenize,ssplit,pos,lemma,ner,parse',
});
const pipeline = new Pipeline(props, 'English', connector);
```

#### 3.2. Use CoreNLP via CLI

CoreNLP expects by default the StanfordCoreNLP package to be placed (unzipped) inside the path `${YOUR_NPM_PROJECT_ROOT}/corenlp/`.  You can also opt to setup the CLI interface differently:

```javascript
import CoreNLP, { Properties, Pipeline, ConnectorCli } from 'corenlp';

const connector = new ConnectorCli({
  classPath: 'corenlp/stanford-corenlp-full-2017-06-09/*', // specify the paths relative to your npm project root
  mainClass: 'edu.stanford.nlp.pipeline.StanfordCoreNLP', // optional
  props: 'StanfordCoreNLP-spanish.properties', // optional
});
const props = new Properties({
  annotators: 'tokenize,ssplit,pos,lemma,ner,parse',
});
const pipeline = new Pipeline(props, 'English', connector);
```

### 4. Use it

```javascript
// ... initialize pipeline first (see above)

const sent = new CoreNLP.simple.Sentence('Hello world');
pipeline.annotate(sent)
  .then(sent => {
    console.log(sent.words());
  })
  .catch(err => {
    console.log('err', err);
  });
```

## External Documentation

```bash
Properties
Pipeline
Service
ConnectorServer                   # https://stanfordnlp.github.io/CoreNLP/corenlp-server.html
ConnectorCli                      # https://stanfordnlp.github.io/CoreNLP/cmdline.html
CoreNLP
  simple                          # https://stanfordnlp.github.io/CoreNLP/simple.html
    Annotable
    Annotator
    Document
    Sentence
    Token
    annotator                     # https://stanfordnlp.github.io/CoreNLP/annotators.html
      TokenizerAnnotator          # https://stanfordnlp.github.io/CoreNLP/tokenize.html
      WordsToSentenceAnnotator    # https://stanfordnlp.github.io/CoreNLP/ssplit.html
      POSTaggerAnnotator          # https://stanfordnlp.github.io/CoreNLP/pos.html
      MorphaAnnotator             # https://stanfordnlp.github.io/CoreNLP/lemma.html
      NERClassifierCombiner       # https://stanfordnlp.github.io/CoreNLP/ner.html
      ParserAnnotator             # https://stanfordnlp.github.io/CoreNLP/parse.html
      DependencyParseAnnotator    # https://stanfordnlp.github.io/CoreNLP/depparse.html
      RelationExtractorAnnotator  # https://stanfordnlp.github.io/CoreNLP/relation.html
      DeterministicCorefAnnotator # https://stanfordnlp.github.io/CoreNLP/coref.html
  util
    Tree                          # http://www.cs.cornell.edu/courses/cs474/2004fa/lec1.pdf
```

## References

This library is *not* maintained by [StanfordNLP](https://github.com/stanfordnlp).  However, it's based on and depends on [StanfordNLP/CoreNLP](https://github.com/stanfordnlp/CoreNLP) to function.

### [Stanford CoreNLP Reference](https://github.com/stanfordnlp/CoreNLP)

Manning, Christopher D., Mihai Surdeanu, John Bauer, Jenny Finkel, Steven J. Bethard, and David McClosky. 2014. The Stanford CoreNLP Natural Language Processing Toolkit In Proceedings of the 52nd Annual Meeting of the Association for Computational Linguistics: System Demonstrations, pp. 55-60.
