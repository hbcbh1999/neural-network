# neural-network

[![Build Status](https://travis-ci.org/A1essandro/neural-network.svg?branch=master)](https://travis-ci.org/A1essandro/neural-network)
[![Coverage Status](https://coveralls.io/repos/github/A1essandro/neural-network/badge.svg?branch=master)](https://coveralls.io/github/A1essandro/neural-network?branch=master)
[![Code Climate](https://codeclimate.com/github/A1essandro/neural-network/badges/gpa.svg)](https://codeclimate.com/github/A1essandro/neural-network)
[![Latest Stable Version](https://poser.pugx.org/a1essandro/neural-network/v/stable)](https://packagist.org/packages/a1essandro/neural-network) 
[![Latest Unstable Version](https://poser.pugx.org/a1essandro/neural-network/v/unstable)](https://packagist.org/packages/a1essandro/neural-network)
[![Total Downloads](https://poser.pugx.org/a1essandro/neural-network/downloads)](https://packagist.org/packages/a1essandro/neural-network)
[![License](https://poser.pugx.org/a1essandro/neural-network/license)](https://github.com/A1essandro/neural-network/blob/master/LICENSE)

###### Language choice:
[![English](https://img.shields.io/:readme-EN-336699.svg)](https://github.com/A1essandro/neural-network/blob/master/README.md)
[![Russian](https://img.shields.io/:readme-RU-cc3300.svg)](https://github.com/A1essandro/neural-network/blob/master/README.ru.md)

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage examples](#usage)
    - [XOR example](#xor-example)
    - [Manually configuration](#manually-configuration-of-network)
- [Specification](#specification)
    - [Network](#network)
    - [Layers](#layers)
    - [Nodes](#nodes)
    - [Synapses](#synapses)
- [Contribute](#contribute)
- [License](#license)

## Requirements
This package is only supported on PHP 5.5 and above.

## Installation
#### Method #1(recommended): Composer package
See more [getcomposer.org](http://getcomposer.org).

Execute command 
```
composer require a1essandro/neural-network ^0.1.0
```
Or add line to `composer.json`

```
"require": {
    ...
    "require a1essandro/neural-network": "^0.1.0"
},
```

#### Method #2: Clone repository
Execute command 
```
git clone https://github.com/A1essandro/neural-network
```

## Usage

### Common

#### XOR example:

```php
use Neural\BackpropagationTeacher;
use Neural\MultilayerPerceptron;

require_once '../vendor/autoload.php';

//Creation neural network, with 2 input-neurons, one hidden layer with 2 neurons and one output neuron:
$p = new MultilayerPerceptron([2, 2, 1]); //You may add more hidden layers or neurons to layers: [2, 3, 2, 1]
$p->generateSynapses(); //automatically add synapses

$t = new BackpropagationTeacher($p); //Teacher with backpropagation algorithm

//Teach until it learns
$learningResult = $t->teachKit(
    [[1, 0], [0, 1], [1, 1], [0, 0]], //kit for learning
    [[1], [1], [0], [0]], //appropriate expectations 
    0.3, //error
    10000 //max iterations
);

if ($learningResult != -1) {
    echo '1,0: ' . round($p->input([1, 0])->output()[0]) . PHP_EOL;
    echo '0,1: ' . round($p->input([0, 1])->output()[0]) . PHP_EOL;
    echo '0,0: ' . round($p->input([0, 0])->output()[0]) . PHP_EOL;
    echo '1,1: ' . round($p->input([1, 1])->output()[0]) . PHP_EOL;
}

/* Result:
1,0: 1
0,1: 1
0,0: 0
1,1: 0
*/
```

#### Manually configuration of network

```php
$p = new MultilayerPerceptron([2, 2, 1]);

//Equivalent to:

$p = new MultilayerPerceptron();
$p->addLayer(new Layer())->toLastLayer()
    ->addNode(new Input())
    ->addNode(new Input())
    ->addNode(new Bias());
$p->addLayer(new Layer())->toLastLayer()
    ->addNode(new Neuron())
    ->addNode(new Neuron())
    ->addNode(new Bias());
$p->addLayer(new Layer())->toLastLayer()
    ->addNode(new Neuron());

//Do not forget to add synapses:

$p->generateSynapses();

//Or you may direct the process:

$neuronFilter = function($node) {
    return $node instanceof Neuron;
};

$secondLayerNeuron = iterator_to_array($p->getLayers()[1]->getNodes($neuronFilter))[0];
$input = iterator_to_array($p->getLayers()[0]->getNodes())[0];
$secondLayerNeuron->addSynapse(new Synapse($input));

//and so on...
```

## Specification

#### Network

Interface implementation of `INetwork` is a container comprising nodes (`INode`) interconnected by synapses (`Synapse`).

#### Layers

Interface implementations of `ILayer` are formal groups of `INode` in a `LayeredNetwork`.

#### Nodes

`INode` - neurons, input-neurons etc.

#### Synapses

`Synapse` - is a connection between two nodes (`INode`). Synapse gets output (call `output()`) of neuron-transmitter and convert the value via its weight. Result value gets neuron-reciever (it call `output()` of `ISynapse`).

## Contribute

Contributions to the package are always welcome!

## License

The code base is licensed under the MIT license.
