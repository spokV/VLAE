## Variational Ladder AutoEncoder with Maximum Mean Discrepancy
#### VLAE as a graphical model
![](https://i.ibb.co/yp7gwwT/vlae.png)

### Description

Paper link: https://arxiv.org/abs/1702.08396 <br>
My implementation use Maximum Mean Discrepancy with RBF kernel instead of KL divergence between _P(z)_ $ and _P(x | z)_
<br>
In the Jupyter Notebbok you can find an application of VLAE into MNIST dataset and a wide description of the model

### How to customize the NN
##### Model creation
Actually _VLAE_ isn't a python class but a _Model_ instance and generated by `make_vlae` function where the only input is the size of the latent space

    latent_size = 2
    vae = make_vlae(latent_size)
<br>

##### How to customize the network
If you want to change the layer configuration you have change interally the code of the VLAE. For instance, we have `make_encoder` that create a sub-model that contains only the encoder part, while the other side is created by `make_decoder`. <br>Note that `make_decoder` receive 3 input arguments: respectively the size of _z&#8321;_, _z&#8322;_ and _z&#8323;_ and receive in input _h&#8321;_, _h&#8322;_ and _h&#8323;_ got in output from the encoder part

    def make_vlae(latent_size):
        with tf.name_scope('encoder'):
            encoder = make_encoder(latent_size)
        with tf.name_scope('decoder'):
            decoder = make_decoder(latent_size, latent_size, latent_size)
        inputs = Input((28,28,1))
        h_1, h_2, h_3 = encoder(inputs)
        z_1 = NormalVariational(latent_size, add_kl=False, coef_kl=0.0, add_mmd=True, lambda_mmd=1., name='z_1_latent')(h_1)
        z_2 = NormalVariational(latent_size, add_kl=False, coef_kl=0.0, add_mmd=True, lambda_mmd=1., name='z_2_latent')(h_2)
        z_3 = NormalVariational(latent_size, add_kl=False, coef_kl=0.0, add_mmd=True, lambda_mmd=10., name='z_3_latent')(h_3)
        
        decoded = decoder([z_1, z_2, z_3])
        vlae = Model(inputs, decoded, name='vlae')
        return vlae

Now it's your turn to check from the file `make_encoder` and `make_decoder`! One of my first objective while wrote code is clarity: when possible, i've declared variables such that names are like in the author paper
