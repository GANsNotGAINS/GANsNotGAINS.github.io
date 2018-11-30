# Abstract
Our goal in this update was to see if we could produce coherent music using GANs, as well as create an image encoding that captures polyphonic music. We learned that sparse data, such as the one hot vector encoding that we were using, is not good input data for GANs and produces very poor results. 

# Introduction
For the second part of this project we wanted to continue our exploration of finding an encoding of the image data. Previously, we used a monophonic one-hot encoding to convert the Music-XML files, but this time we also explored polyphonic encoding. Polyphonic encoding would allow the generated music to produce multiple sounds simultaneously – monophonic could represent a simple melody, while polyphonic could represent songs with chords or bass lines accompanying the melody. Additionally, we expanded upon our previous work to incorporate a new method of music generation. In the last update, we used Markov chains to produce new music, and came up with fairly good results, but this time we decided to explore GANs. 

Because GANs are based on top of convolutional neural networks, they are able to create relationships between pixels that Markov chains are unable to do – Markov chains can only look at previous states to determine the next note to pick, while GANs could discover a specific functional relationship between a set of notes.

# Approach
New Polyphonic Encoding:
Our previous encoding scheme represented a song as an image where columns represented discrete notes and rows represented the pitch of the note, with one row being dedicated to encoding rests. Each column was one-hot, representing one note at one pitch at one time interval, and the coloring of the single non-black pixel indicated the length of the note. This approach works well as a simple encoding scheme, but has one crucial downside - it can’t represent multiple notes being played at the same time – this occurs frequently in music, either through chords, or multiple instruments harmonizing.

To combat this problem for this update we developed an alternative “polyphonic” (multi-note) encoding scheme to represent a song visually. Here’s a sample song:

![](https://i.imgur.com/hqiZUlq.png)

In this encoding rows still represent pitch. The major change is the function of each column; in our previous encoding, each column contained a single note with the pixel color representing the length of that note. Instead, in this encoding, each column represents a single sixteenth note. A horizontal line of yellow pixels represents a note that is held for multiple timesteps - two pixels represent an eighth note and four a quarter note. The red pixel is used to mark the end of a note. Since each column is now of a uniform time, we're able to have multiple concurrent notes – notice how for a given column of time, multiple simultaneous pitches are playing. This also gives each song a more interesting and less constrained visual character, helping the generative CV approaches we use later.

# GANs
GANs are an unsupervised machine learning algorithm and consist of two neural networks. One of the neural networks, the generator, generates data, while the other neural network, the discriminator, decides whether data fed into the algorithm from the generated data is similar to the training data.

GANs have had many cool uses in the past few years from transferring style of painter onto an image to generating realistic headshots of people. In the same light of using previous images to create novel images, we hoped that GANs would help us create novel images based on the music encoded images that it trained on. 

We used Deep Convolutional Generative Adversarial Network, or DCGANs, to create our images. DCGANs are most famous for being able to generate photorealistic images of faces. DCGANs are also notable for using convolutional neural networks (CNNs), which have become a popular approach for computer vision and deep learning problems.

We hoped that by training a DCGAN on large datasets of input images, the generator would be able to approximate the true data distribution to produce new images that are similar to our inputs. Then, we can discretize and decode the result images to get a generated song. 

# Experiment and Results
We trained several DCGANs and experimented with varying the image encoding scheme and network sizes in the generator and discriminator. 

Our data consisted of 800 classical songs downloaded from MuseScore, which is a site where users may upload transcriptions of music. These songs were converted with both the monophonic and polyphonic encoding schemes. Then, the image representations were tiled to 24 x 24 images, as most open source implementations of GANs are built to support square images. 

![gif of gan training](https://raw.githubusercontent.com/GANsNotGAINS/music-dcgan/master/example/animated.gif)

For the monophonic encoding scheme, we generated approximately 10,000 training images. Training the DCGAN on these images converged fairly quickly after about 2000 iterations. The DCGAN was able to pick up that the primary colors in the input images were black, red, and blue, and the results for generated images consisted mainly of these colors. However, the training images were extremely sparse, since the columns were one hot vectors to represent the sequences of notes and vectors. The GAN failed to replicate this, and the outputs contain a lot of noise. In addition, tweaking the network sizes in the generator and discriminator did not result in significant improvements in output results. Thus, while the GAN was able to somewhat successfully mimic the color distribution of the input images, it failed to replicate the underlying image structure – namely, that only one note can be played at a time. 

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/537584484%3Fsecret_token%3Ds-FxfBM&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>

We also experimented with training the DCGAN using images created via the polyphonic encoding scheme. Here, the training image dataset consisted of 24,000 images. The loss function of the generator and discriminator took more iterations to converge in this case. This increase in convergence time likely occurred because the input images of the polyphonic scheme were more feature rich than those from the monophonic scheme. The increased amount of training images also likely contributed to this. The GAN also failed to pick up on some of the patterns in the input images. For example, in our training images, rows of yellow pixels are always followed by red pixels. However, this is not evident in the generated images. 


For both sets of training data, we ran into issues with mode collapse.

The first issue occurs when the GAN reached Nash Equilibrium, and as a result fails to converge. Nash Equilibrium occurs when the actions of discriminator or generator no longer matter because the outcome of who will win does not change. The mode collapse occurs when only a few modes, or in our case one mode, of the data are generated. This occurs when generator continuously fools the discriminator when focusing on one mode. Within the context of our data, mode collapse means that, despite an increase in training iterations, the exact same output was generated.

It is also likely that our results from GANs were poor because GANs tend to work well for natural images. Research projects with GANs often involve images taken with a camera, such as photos of people or landscapes. Our input images are not natural and our encoding and decoding methods for representing music impose many assumptions and rules on the images. The GAN was not able to pick up on these qualities. 

In addition, we trained the GANs on 24 x 24 images because it was most straightforward to run the convolutional neural networks in the generator and discriminator on square inputs. However, it is possible that better results could have been achieved by training on longer sequences, by manipulating our GAN to take in 24x48 or 24x96 images.

Overall, our GAN results were poor due to issues with mode collapse and the rigidness of our image representation encoding methods. 

<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/537582885%3Fsecret_token%3Ds-pd3iu&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>

# Conclusion and Future Work
Overall, we built two encoding schemes for MusicXML files, one supporting polyphonic and one monophonic. After building a corpus of thousands of input images for each encoding, we fed the inputs into GANs and variable order Markov models to generate outputs with what we hoped would have a similar style to the input. We had varying results which you can listen to through the SoundCloud links, but overall, the project provided us a great opportunity to learn about various image processing techniques, specifically in the field of image generation.

We had fun experimenting with different models to create music. To further explore this problem it would be interesting to see if other deep learning approaches like variable autoencoders or pixelCNN would have produced better results. It would also be interesting to explore other input forms and see if, for example, a Char-RNN that treats notes as characters performs better or worse – this wouldn't be as acceptable for this project as it would take in music embedded as text, and there would be no images. 


References: 

https://arxiv.org/abs/1511.06434 