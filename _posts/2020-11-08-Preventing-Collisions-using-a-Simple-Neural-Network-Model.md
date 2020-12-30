---
layout: post
title:  "Predicting Collisions using a Simple Neural Network Model"
tags: artificial-intelligence python machine-learning deep-learning
---

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/neural-network.png?raw=true" height="200">

# Project overview

Collision prevention is one of the fundamental aspects in self-driving cars and autonomous robot systems. This project presents a simple and effective model to train a robot to avoid collisions using neural networks. Neural networks, also called artifical neural networks mimics the complex decision making processes in human brain: input data "flow" through the neurons where linear/non-linear activation functions are applied; the outputs of each layer of neurons then flow into the next layer until the final output layer integrates the results of network and outputs the "decision". Deep neural networks are powerful and versatile, and have shown great successes in computer vision, speech recognition, and natural lanuage tasks

# The Simulated Environment 

A robot wanders ranmly in a 2D space with walls and obstacles (grey boxes). The robot has 5 distance sensors and the training data contains sensor readings and a binary label (0/1) to indicate whether or not the collision occured. 

## Before Training: The Robot Moves and Collides Randomly

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/before-training1.gif?raw=true">

*The robot turns red when collision occurs*

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/before-training2.gif?raw=true">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/before-training3.gif?raw=true">

# Neural Network Model

## Model Infrastructure

The neural netwrok model consits of just 3 layers: input layer, 1 hidden layer of 4 neurons with sigmoid activation functions, and 1 output layer. 

```python
import torch.nn as nn
class Action_Conditioned_FF(nn.Module):
    def __init__(self):
        super(Action_Conditioned_FF,self).__init__()
        input_size = 6
        hidden_size = 4
        output_size = 1
        self.input_to_hidden = nn.Linear(input_size, hidden_size)
        self.nonlinear_activation = nn.Sigmoid()
        self.hidden_to_output = nn.Linear(hidden_size,output_size)

    def forward(self, input):
        hidden = self.input_to_hidden(input)
        hidden = self.nonlinear_activation(hidden)
        output = self.hidden_to_output(hidden)
        return output
```

## Model Training with Adam Optimizer and Mini-bathces

### ADAM optimizer

ADAM (Adaptive moment estimation) is a stochastic gradient descent method that leverages the power of adaptive learning rate methods for each parameter . It is computationally efficient and requires little memory. 

```python
 optimizer = torch.optim.Adam(model.parameters(),lr = learning_rate)
```

### Mini-Batch

The train data set contains merely 100 rows so I used mini-batch method to shuffle and possibly reusing the data points in a stoichastic way. Mini-batch gradient descent is useful for large data volumes too to provide computational efficiencies. It "injects" some noise to each gradient update which is helpful to "bouce" out from a local minima. 

```python
batch_size = 20 
 for epoch_i in range(no_epochs):
        model.train()            
        # data_loaders.train_loader returns training data (0.8/0.2 split)
        for idx, sample in enumerate(data_loaders.train_loader): 
            train_input,train_target_output = sample['input'],sample['label']
            
            # convert data types 
            train_target_output = train_target_output.unsqueeze(-1)
            train_target_output = train_target_output.float()
            train_input = train_input.float()
            
            # forward process
            train_network_output = model.forward(train_input)
            
            # compute loss (loss function defined in a separate func, MSELoss)	
            loss = loss_function(train_network_output,train_target_output)
            optimizer.zero_grad()                     
            loss.backward()
            optimizer.step()   
```

# Results & Conclusion

## After Training: Robot Proactively Avoids Collisions and Reaches the Goal

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/after-training1.gif?raw=true">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/after-training2.gif?raw=true">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/predict-collision-nn-demo/after-training3.gif?raw=true">



Out of 1000 predictions, the false positive rate is 0.5% and false negative rate is 0.2% in other words, the prediction accuracy is 99%+. 



## Acknowledgement

The code templates and training data are provided in CSE571 - Artificial Intelligence course at Arizona State University. 