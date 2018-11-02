---
layout: post
title: Midterm Project Update
---
By Ehsan Asdar (easdar3), Ashwini Iyer (aiyer65), Matthew Kaufer (mkaufer3), Nidhi Palwayi (spalwayi3), and Kexin Zhang (kzhang323)

#Abstract
Our project focuses on music generation through image processing techniques. We developed a method for encoding songs in images and trained variable order Markov models on these image representations. Our experiments produced the best results when the model was trained on a few songs with a distinct style.

<img src="/static/images/img1_output.png"/>
<p class="caption">Sample of song embedded in image</p>

#Introduction
Novel music generation is a large area of active research. For this project, we wanted to experiment with generative computer vision approaches we have been learning in this course (along with more advanced deep learning based CV algorithms) to see if they can also serve as effective methods of generating music. To apply these CV approaches, we created an experimentation pipeline that converts input MusicXML music formats into images, generates songs based off of these inputs, encoded as images, and then converts these output images back into audio for playback. The generated music is evaluated by its ability to sustain musical patterns throughout the song.

##Applications
Generative music has several creative applications. Producers and musicians can employ generative techniques to synthesize new music samples and develop new songs based on similar work. For example, given a trained model, one can generate different segments of music given previous note histories. 

##Existing Approaches
Some existing works that we researched include Wavenet, projects from Google’s Magenta, and Cycle GAN audio. While our idea is not unique, our project is still meaningful, since we plan to explore image representations of songs and experiment with a variety of techniques for music generation.

#Approach:

To generate music, we developed the following procedures:

1. A program to download MusicXML files in bulk 
2. A program to monophonically encode songs as images
3. A pipeline to train variable order Markov models on given image representations of songs
4. A pipeline to generate song images of variable length from a given variable order Markov model, mimicking texture synthesis
5. A process to convert the generated images to audio

## Collecting Data
For our input data, we wrote a program to search and download MusicXML songs in bulk from the MuseScore API. We downloaded a total of 800 songs that were composed for a solo pianist. Our song corpus included traditional classical music, movie scores from John Williams, and songs from Undertale, a video game with a distinctive soundtrack.

## Image Representation
First, we developed an image representation for songs. The idea is that songs can be encoded as images where the columns represent time and the rows represent notes.

We considered two formats for the input audio: midi and MusicXML files. Midi files encode data with millisecond differences between notes, while MusicXML preserves the note's musical value itself &mdash; for instance, Midi might say a note lasted 100 milliseconds, while MusicXML might say that a note was a quarter note. Both represent pitch as an integer. 

Although we initially planned to use midi files, we found that midi files did not allow us to distinguish between different note durations (quarter notes, half notes, etc.) with ease. That means that, with midi files, we would need to manually quantize note durations, leading to a large number of previous states to consider, a large state space for the Markov chain, and potential for quantization errors. However, MusicXML files have information on the notes themselves, which makes the image representation more compact and accurate. The rows of the image were used to encode the note, with a range of two octaves, while the width of the image represents the temporal sequence of notes in the song’s melody. Each column is a one hot vector, and we used the RGB channels of the “on” pixels to represent the type and the duration of the note. In particular, blue pixels represent notes while red pixels represent rests, and the intensity of the blue or red channel is scaled to represent the duration of the note.

One other thing to note is that all input songs were normalized to the key of C &mdash; if songs of different keys were input into the Markov model, transitions between notes could be out of key, and sound dissonant.

## Training and Running Markov Models

With our image representations of songs, we can treat sequences of notes in the image as textures and use these as inputs for a Markov chain, a common texture synthesis technique. We figured this would be a good approach for generative music, since songs often have patterns and repetition that can be represented well in a Markov model. We opted to use a variable order model instead of a classical n-order Markov model. This allowed us to consider more than one previous state, which is good because patterns in music usually rely on some musical context. In addition, the variable order model affords more flexibility, since the number of past states considered can vary up to a maximal order. This is useful as the number of past notes to consider varies throughout a piece of music and is not fixed in quantity. 

We used the Python `vomm` library for training variable order Markov models and generating new song images based on the inputs. The inputs are columns of the song images, and the model predicts next values, which are also columns of an image. These predicted values form the image representation of the output song. This output image can then be decoded to construct a Midi file. 

#Experiments and Results

We tested our image generation model on several training sets. On smaller training sets of songs with distinct, repetitive phrases, the variable order Markov model performed well. This is because the generated piece tended to audibly mimic motifs from the few input pieces. For example, we trained the model with a variable order of five on a small set of Star Wars soundtrack songs &mdash; in this case, the Markov model generated music that incorporated common melodic lines from the inputs. With a small set of songs from Undertale, the model produced similar results.

<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/playlists/635424402&color=%236a9fb5&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/playlists/635427759&color=%236a9fb5&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>
<br/>
However, when we used larger training datasets, the results were poor. For example, when we trained the models on sets of 20 and 100 classical songs, the generated music sounded disjoint and mangled.
<br/>
<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/playlists/635438529&color=%236a9fb5&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

This makes sense, since a smaller set of distinct songs has more patterns and a more recognizable motif that the Markov chain is able to represent well. With larger training sets, there are less small common patterns that the listener can distinguish between, and since the Markov model can only go so far back in time, the model is unable to capture the overall essence of *all* the pieces.

#Conclusion and Future Work
Our next steps include experimenting with other approaches, such as GANs or recurrent neural networks, and comparing the results to the Markov model results. In addition, we plan to add improvements to our current approach. Notably, our method for encoding music into images can only represent pieces monophonically, which means that harmony notes and underlying chords are discarded. We plan to develop and implement an alternative encoding scheme that supports polyphonic pieces, which may yield more interesting results.

Overall, our work so far has shown that we can effectively represent songs as images, and that Markov models can produce fairly good results on small, distinctive sets of songs.

#References
carykh. “3 Neural Nets Battle to Produce the Best Jazz Music.” YouTube, YouTube, 7 June 2017, www.youtube.com/watch?v=uiJAy1jDIQ0&t=8s.
