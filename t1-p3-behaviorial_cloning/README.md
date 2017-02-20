This code is the implementation of the solution to Project 3: Behavioral Cloning of the
Udacity Self-Driving Car Nanodegree.

It uses Keras to train a convolutional neural network to predict steering angles given an
input image from a racing car simulator. This trained network is then used to drive the same
racing car autonomously around the track, just by feeding it images from the car cameras.

# Data collection

I followed the instructions from the project description to record images and steering angles
while driving around the track.

I also recorded many cases of recovery runs in order to improve the network prediction in
cases where the car starts to wander off the track and in the case of sharp turns.

I did many trial-and-error runs of data collection and network training, which showed me that
the normal driving data was less important than recovery data. Therefore, the final content of
my data is about 2 laps of normal driving (~2500 images) and twice as much recovery data
(~5000 images), for a total of ~7500 images.

I used the 10 Hz simulator and recorded at the lowest resolution (640x480) and fastest graphics
quality. I used a PS3 USB controller to have as smooth data as possible, but _a posteriori_, I
think it'd have been possible to get the same performance using just the keyboard. I used only
images from the center camera at an input resolution of 320x160 and 3 color channels. This
translates into ~100 MB of data on disk.

# Data preprocessing

I normalized the images to the [-0.5,0.5] range and resized them to a size of 32x16 (more on that below).

I did not perform any filtering on the steering angles data.

# Data augmentation

Since the recorded data is heavily biased towards the negative angles (since the car runs counter-clockwise
in a closed loop), I generated extra data by taking each image and flipping it around a vertical axis and
assigning an inverted steering angle to it. This effectively doubles the input data size and correctly
balances the distribution of steering angles. At this step, the full data sample becomes close to ~15000 images.

# Data split

I split the data into 70% training data, 15% validation and 15% test data.

# Network architecture

I used Keras to build a CNN with 2 convolutional layers with 32 filters of 3x3 kernel size and RELU activation.
I perform a 2x2 max pooling and use a 0.25 dropout after the second one.
After those, there are 4 fully connected layers of sizes 256, 128, 64 and 32 and RELU activation, with 0.5
dropout after the final layer.

The network takes 32x16x3 images as input and outputs a single scalar value for the steering angle.

I tried to keep the model simple while addinng non-linearity and dropout to account for the complexity of the data
and avoid overfitting it.

# Network training

I trained the network on a Macbook Pro Laptop CPU in 10 epochs with a batch size of 128 using an Adam
optimizer. I did not use a fit generator since the input data size is small enough to fit into the RAM:
15000 32x16x3 images is about 184 Mb in memory. The training took about 80 seconds to finish and the decrease in the
cost function showed a similar trend and values for both the training and validation sets. The test cost also
compared well to them, so this gave good confidence that the model is not overfitting the data.

# Model evaluation

I modified the drive.py script in order to apply the same preprocessing to the images coming from the
simulator. Observation of the behavior of the car around the track, which swerves at times,
but does not touch the yellow or red lines, and has good reactions at the sharpest turns, shows that the
model has trained well.

# Discussion and conclusion

I was quite surprised that model was able to perform so well in testing with such a small input size and a small
data size and a short training time. It seems that the quality of the data that's provided to the model has
such an incredible impact compared to feeding the model more and more similar data. Recovery data seems to be
so much more important than normal driving data. Recovery in straight lines is necessary, but a little of it is
enough. A lot of data was needed around the hardest turns.

I spent a significant time recording recovery data while observing how the model performed in the most difficult
spots, and record new data by starting at different configurations around them. Re-training the model from scratch
 and observing the new performance helped to give me good insights on how best to record the best data quality.
With just a few seconds of recovery data, I could usually notice a significant positive difference. It helped that
I could do a full retraining in less than 2 minutes.

Going at moderate speed also seemed to help. This could explain why I did not need the 50 Hz version of the simulator.

I tried this on the first track only. It'd be interesting to use the same method on the second track. I think it'll work,
but will probably need a lot more recovery data. However, it'd be interesting to mix data from both tracks.
The costs on training, validation and testing sets are comparable, so I'm confident that the model is not overfitting
the data from the first track.

I arrived at this by doing many test runs, with a lot of frustration at times. But in the end, it's a great (ongoing)
learning experience.
