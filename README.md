# Generate LCN

A Node.js based tool to generate a lexical coocurrence network (LCN) for brazilian portuguese UGC (User-Generated Content).

# How does it works?

The first step is the pre-processing of the input text, which is normalized to lowercase, trimmed and all the punctuation/accents are removed. After that, the text is tokenized and the stopwords are also removed. The last step of prepairing consists in the replacement of some slangs and internet slangs by theirs respective synonyms. This replacement uses an improved version of @avanco [UGCNormal](https://github.com/avanco/UGCNormal) dictionary.

After that, a simple lexical coocurrence algorithm is used to break the text in a list of n-tuples, which length is based in the `coocurrenceWindow` parameter. Then, the following graph structure is generated based in this n-tuple list:

```json
{
  "vertex": {
    "[token]": {
      "[token]": "boolean"
    }
  },
  "nodes": {
    "[token]": "frequency"
  },
  "edges": {
    "[token]": {
      "[token]": "frequency"
    }
  }
}
```

The `vertex` is an object which represents the coocurrences of the tokens. The `nodes` object saves the frequencies of all the tokens. By last, the `edges` represents the relations between nodes, which is weightened by the coocurrence frequency.

By last, the `generateLCN` function outputs a string that is in the dot file format, a Graphviz compatible graph representation format.

# How to use it

This project have no NPM dependencies, but you must have Node.js version 12+ to use it. The following code snippet illustrates its use:

```javascript
//example.js

const fs = require("fs");
const generateLCN = require("./generate-lcn");

// Chico Buarque's song "Construção".
const construcaoTxt = `
Amou daquela vez como se fosse a última
Beijou sua mulher como se fosse a última
E cada filho seu como se fosse o único
E atravessou a rua com seu passo tímido
`;

const outputDotFile = generateLCN({
  /* Defines the coocurrence window (optional value, default is 4). */
  coocurrenceWindow: 2,
  /* Defines the number of edges a vertex can have, ordered by the
    coocurrence frequency (optional value, default is 1). */
  maxEdges: 2,
  /* The threshold frequency for a token to be included
    in the graph (optional value, default is 1). */
  minFrequency: 1,
  /* Limits the number of nodes by this number, ordered by
    the token frequency (optional value, default is Infinity). */
  nodeLimit: 100,
  /* The input text for the algorithm. It should have at least
    50 words for the algorithm to behave accordingly (required value). */
  inputText: construcaoTxt,
});

fs.writeFileSync("graph.dot", outputDotFile);
```

This code snippets takes the excerpt from the Dom Casmurro book and writes the LCN to the `graph.dot` file. If rendered in Graphviz, it should generate the following graphical network (text size is accordingly to token frequency):

# Future work

Currently, this algorithm does not determines the rendering position of the network. Using a force-directed algorithm could improve the readability of the graph. Also, there are room for many performance optimizations.
