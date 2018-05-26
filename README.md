# PHP NLP-Client

This is a simple PHP library for performing Natural Language tasks using the Web64 NLP-Server https://github.com/web64/nlpserver

NLP Tasks Available through Web64's NLP Server:
* Language detection
* Entity Extraction (NER) - Multilingual
* Sentiment Analysis - Multilingual
* Embeddings / Neighbouring words  - Multilingual
* Article Extraction from HTML or URL
* Summarization

NLP Tasks Available through Stanford's CoreNLP Server:
* Entity Extraction (NER)

NLP Tasks Available through Microsoft Labs API:
* Related concepts

## Installation
```bash
composer require web64/php-nlp-client
```
### Core NLP (Java)

Download CoreNLP server here: https://stanfordnlp.github.io/CoreNLP/index.html#download

```bash
# Run the server using all jars in the current directory (e.g., the CoreNLP home directory)
java -mx4g -cp "*" edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000
```
More info about running the CoreNLP Server: https://stanfordnlp.github.io/CoreNLP/corenlp-server.html
## Usage

### Language detection:
```php
$nlp = new \Web64\Nlp\NlpClient('http://localhost:6400/');
$detected_lang = $nlp->language( "The quick brown fox jumps over the lazy dog" );
// 'en'
```

### Article Extraction

```php
// From URL
$nlp = new \Web64\Nlp\NlpClient('http://localhost:6400/');
$newspaper = $nlp->newspaperUrl('https://github.com/web64/nlpserver');

// or from HTML
$html = file_get_contents( 'https://github.com/web64/nlpserver' );
$newspaper = $nlp->newspaperHtml( $html );

Array
(
    [article_html] => <div><h1><a id="user-content-nlp-server" class="anchor" href="#nlp-server"></a>NLP Server</h1> .... </div>
    [authors] => Array()
    [canonical_url] => https://github.com/web64/nlpserver
    [meta_data] => Array()
    [meta_description] => GitHub is where people build software. More than 27 million people use GitHub to discover, fork, and contribute to over 80 million projects.
    [meta_lang] => en
    [source_url] => 
    [text] => NLP Server. Python Flask web service for easy access to multilingual NLP tasks such as language detection, article extraction...
    [title] => web64/nlpserver: NLP Web Service
    [top_image] => https://avatars2.githubusercontent.com/u/76733?s=400&v=4
)
```

### Entity Extraction & Sentiment Analysis
```php
$nlp = new \Web64\Nlp\NlpClient('http://localhost:6400/');
$polyglot = $nlp->polyglot( $text, 'en' );

$entities = $polyglot->getEntities();
$sentiment = $polyglot->getSentiment();
```

### Neighbouring words (Embeddings)
```php
$nlp = new \Web64\Nlp\NlpClient('http://localhost:6400/');
$neighbours = $nlp->neighbours('obama', 'en');
/*
Array
(
    [0] => Bush
    [1] => Reagan
    [2] => Clinton
    [3] => Ahmadinejad
    [4] => Nixon
    [5] => Karzai
    [6] => McCain
    [7] => Biden
    [8] => Huckabee
    [9] => Lula
)
*/
```

### Spacy Entities
```php
$text = "Harvesters is a 1905 oil painting on canvas by the Danish artist Anna Ancher, a member of the artists' community known as the Skagen Painters.";

$nlp = new \Web64\Nlp\NlpClient('http://localhost:6400/');
$entities = $nlp->spacy_entities( $text );
/*
Array
(
    [DATE] => Array
        (
            [0] => 1905
        )

    [NORP] => Array
        (
            [0] => Danish
        )

    [ORG] => Array
        (
            [0] => the Skagen Painters
        )

    [PERSON] => Array
        (
            [0] => Anna Ancher
        )
)
*/
```

English is used by default. To use another language ensure Spacy language model is downloaded and add the language as the second parameter
```php
$entities = $nlp->spacy_entities( $spanish_text, 'es' );
```


### Summarizer
Extract short summary from a long text
```php
$summary = $nlp->summarize( $long_text );
```


### Readability
Article Extraction using python port of Readability.js

```php
$nlp = new \Web64\Nlp\NlpClient( 'http://localhost:6400/' );

// From URL:
$article = $nlp->readabilityUrl('https://github.com/web64/nlpserver');

// From HTML:
$html = file_get_contents( 'https://github.com/web64/nlpserver' );
$article = $nlp->readabilityHtml( $html );

/*
Array
(
    [article_html] => <div><h1>NLP Server</h1><p>Python 3 Flask web service for easy access to multilingual NLP tasks ...
    [short_title] => web64/nlpserver: NLP Web Service
    [text] => NLP Server Python 3 Flask web service for easy access to multilingual NLP tasks such as language detection  ...
    [title] => GitHub - web64/nlpserver: NLP Web Service
)
*/
```


### Polyglot Entities & Sentiment Analysis
This uses the Polyglot multilingual NLP library to return entities and a sentiment score for given text.
Ensure the models for the required languages are downloaded for Polyglot.

```php
$polyglot = $nlp->polyglot( $text );

// Specify language
$polyglot = $nlp->polyglot( $text, 'no' );

$polyglot->getSentiment(); // -1

$polyglot->getEntityTypes(); 
/*
Array
(
    [Locations] => Array
    (
        [0] => United Kingdom
    )
    [Organizations] =>
    [Persons] => Array
    (
        [0] => Ben
        [1] => Sir Benjamin Hall
        [2] => Benjamin Caunt
    )
)
*/

$polyglot->getLocations();  // Array of Locations
$polyglot->getOrganizations(); // Array of organisations
$polyglot->getPersons(); // Array of people



$polyglot->getEntities();
/*                                              
Returns combined array of all entities
Array                                          
(                                              
    [0] => Ben                                 
    [1] => United Kingdom                      
    [2] => Sir Benjamin Hall                   
    [3] => Benjamin Caunt                      
)
*/
```


