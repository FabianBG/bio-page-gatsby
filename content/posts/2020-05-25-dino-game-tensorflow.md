---
title: Tech your computer to play chrome dino game
date: "2020-05-25T22:40:32.169Z"
template: "post"
draft: false
slug: "tensorflow-dino-game"
category: "AI"
tags:
  - AI
  - Tensorflow
  - Dino Game
description: "A simple neural net to play chrome offline dino game."
socialImage: "https://github.com/FabianBG/dinoplayer-neuralnet/blob/master/docs/dino1.png?raw=true"

---

# Intro
As an AI fanatic I decide to do a simple neural net to learn more about TensorFlow so I do a neural net which plays the offline chrome dino game, It a very simple approach than it is not the best and perfect but as a starter, point to learn AI, it is a funny way to do it.

# The project
For the common AI learning agents actually exist a lot of playgrounds (like GYM [https://gym.openai.com/]) which simplify the way you interact with the game, the playgrounds can be paused and played frame by frame which is very helpful, it has a lot of useful data of each frame of the game such as rewards and game status. But For this I want to make it from a very scratch. So I choose to do it with the famous dino game.

The project is coded in Python3, to get the actual state of the game it takes screenshots of the chrome window and has a controller for the keyboard to trigger the keypress. It is not a deep reinforcement learning approach, it is more like a supervised approach because first of all I played the game to generate the data then this data is fed to the neural net and finally is used to play.

For the inpatients, the source code is on GitHub [https://github.com/FabianBG/dinoplayer-neuralnet]

# Usage

First of all download the code and open google chrome on [crome://dino].

Then in the folder of the code execute the next command:

`python3 main.py preview` 

It shows the preview of the area in which the code takes the screenshoots so move the navigator with the game to fit the preview.

![preview](https://github.com/FabianBG/dinoplayer-neuralnet/blob/master/docs/dino1.png?raw=true)


When the screenshot area is set, use this command to generate the train data:

`python3 main.py gen-data`

To start recording data *press the key i*, and to stop *key e*, finally to save the data on the preview window press the *key q*.

It will generate a *data-train.pkl* file on the source. This contains the train data for your model.

Next step is to train the model, so just exec:
`python3 main.py train`

![train](https://github.com/FabianBG/dinoplayer-neuralnet/blob/master/docs/dino2.png?raw=true)

When all the train is done it saves a *model.tf* file which has the trained model.

Finally, to see how it does, execute
`python main.py predict`

And to start predicting press *press the key i* start the game and let the computer tries to do its best.

![sample](https://github.com/FabianBG/dinoplayer-neuralnet/blob/master/docs/dino3.gif?raw=true)

The net can be improved just modify the file *model.py* it has the net definition.