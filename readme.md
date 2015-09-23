The denoising dirty documents repo
==================================

Current approach
----------------
The hardest image type is the serif font with the crumpled noise type, and on that we get around

*See Category.ipynb for this experiment*

1. Split the documents up by stain type, font and font size
2. Train a model on each type
3. Apply the model to the noisy documents

### Model details

#### Layers:

1. Flatten - to convert images of size (1, 15, 15) pixels to flat arrays of 255 pixels
2. Dense layer of size 255*2, sigmoid activation
3. Dropout 50% probability
4. Batch normalization, 255*2 size
5. Dense, 255*2, sigmoid activation
6. 50% dropout
7. Batch normalization, 255*2 size
8. Dense, 255*2, sigmoid activation
9. 50% dropout
10. Batch normalization, 255*2 size
11. Dense, 255*2 size, sigmoid activaton
12. Linear combiation, single output

#### Error function:

The error function is called _clipping mse_. It's like normal mean squared error, 
except for where the network places values outside of the range [0, 1]. When this
happens, we penalize it far less - 5% of the normal rate. 

Most of the image is 0 or 1 (black or white). As such, we want to let the neural
network place some values "outside" this range, and we clip it later. This means 
it can make things that are definitely black extra black, and things that are 
white extra white, and then save the in-between values for more difficult areas.

### Activation

I'd had some issues with relu activation. They train really fast, but they seem
to cap out faster than sigmoid. Sigmoid is slower to start, but seems to have a 
lower error bound. That said, perhaps I'm wrong.


Other approaches & experiments
------------------------------

- Convolutional networks didn't seem to lead anywhere.
- More features, for example, the median image, didn't seem to add much either. They substantially increase training costs 
- I have not fully explored direct autoencoding of patches. I regress from 15x15 pixels to a single pixel. We could instead go from 15x15 to 15x15 and 
  either run it over the image in strides of 15 pixels, or we could take strides of size one and then average out all the values we get for each pixel.
- I haven't yet done full hyperparameter optimization. We should definitely get on this, it could put us over the edge.
