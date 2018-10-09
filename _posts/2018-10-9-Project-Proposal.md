---
layout: post
title: Project Proposal
---

# Problem Statement
Generative music is a very interesting field – who doesn't want to hear a new Mozart composition? We propose two methods of generating music through image generation, Markov Models and GANs, and will compare their results.

Think of a piece of music; it has notes that vary in pitch, and each note has a quantizable start and end time. Consider embedding a musical piece with three instruments into an image. The columns of the image can represent some temporal moment, while the rows of the image can represent a pitch. The RGB channels can each represent an individual instrument (you could splurge and do four instruments if you used the alpha channel too).

Now that we have some means of embedding music as images, we can treat the lines of notes in the images as textures.

Since we know something about texture generation, we can apply these methods to generating music. This is nice, since Markov Models are inherently repetitive, and could create some of the repetition that music entails. 

However, Markov Models are so 19xx – anyone that's anyone uses deep learning. Here, we will use both Markov Models and GANs to generate music embedded into images, and then compare the results.

# Approach
* Download lots of classical MIDI files
    * Transpose them into the same key, for the purposes of the Markov Models
    * Pieces should only have a single melody and an underlying chord progression
* Create a program to convert these MIDI files to images
* Train several Markov Models of different resolutions from the input MIDI images
* Train a GAN from the input MIDI images
* Convert the outputs of the Markov Models and the GAN from image back to MIDI
* Listen to the MIDI files for a qualitative analysis
* Look at the tonality of pieces and the beat distribution to analyze musicality of generated pieces

# Experiments and Results 
The experimentation involves generating multiple Markov Models of different resolutions, as well as architecting and training a GAN. We will also experiment with the length of the output of the generated music to see if longer musical phrases fall apart quicker (to play with the curse of dimensionality).

We’ll use a dataset of classical music written by Antonio Vivaldi in 4/4, since his music has a distinctive style and composition that will allow us to draw parallels between the generated and original music (more on our evaluation criteria later).

The code that will be written is
* A converter from MIDI to our image encoding
* A converter from our image encoding back to MIDI
* A way to train Markov Models with these image representations of MIDI
* A way to generate images from these Markov Models
* A GAN to be trained with these image representations of MIDI
* Analysis software to calculate a generated song's
    * Tonality
    * Contour
    * Contour similarity across measures
    * Empty space

Some code we’re not going to write from scratch:
* TensorFlow or Pytorch based implementation of GANs

Experiments will involve
* Generating music through the various Markov Models
    * Varying the size of the Markov Models
    * Varying the length of the images generated
* Generating music through the GAN
    * Varying the size of the images generated/discriminated by the GAN

Our evaluation criteria involves measuring stylistic similarity of the generated music to that of the original classical music dataset. We’ll write code that compares music on the metrics of tonality, contour, and consistency of time signature. We will also qualitatively analyze the outputs.

Datasets:
* To get the midi files we will use these websites: https://www.classicalarchives.com/midi.html and http://www.kunstderfuge.com 

Inspiration:
* [This youtuber](https://www.youtube.com/watch?v=nA3YOFUCn4U) trained different deep learning networks to create jazz music:




