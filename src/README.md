# DINo: Time and space continuous PDEs with Implicit Neural Representations

## Basic overiew of the method

A PDE is a time-evolving function of space, which we denote as $v_t(x)$ at time-instant $t$ for some spacial input $x$. The goal is to predict $v_t(x)$ given $v_0, \forall t > 0$. Here's how this is done:

1. The input function of $x$ is encoded to a latent function of $x$ via an Encoder $E$ such that $E_\psi(v_t) = \alpha_t$. The parameters of the encoder are $\psi$.

2. After this, the method provides an implicit model of $\alpha_t$ as if it belonged to a trajectory. Hence, we use a network to evolve the latent representation $\alpha_t$ to $\alpha_{t+T}$ by assuming it belongs to an ODE: $\alpha_{t+T} = \int_{t}^T \alpha_t dt$.

3. Upon obtaining $\alpha_{t+T}$ from the latent ODE, we decode $\alpha+{t+T}$ to $v_{t+T}$ via the decoder $D(\alpha_{t+T}) = v_{t+T}$.

4. Note that the encoding and decoding happens via *auto-decoding*, where there is no real encoder. Instead you run an optimization over $\alpha$ s such that they best represent the decoder and the data.

5. During inference, you "encode" $\tilde{v}_0$ via the optimization of $\alpha$ s to $\tilde{\alpha}_{0}$, evolve it over time using $f(\cdot)$ to get $\tilde{\alpha}_t$ and then decode to get the next time-evolved spatial function $\tilde{v}_t$ in the PDE trajectory.

6. The actual novelty and power of the method comes from the decoder design, which is an INR whose weights are modulated in a way which simulates space-time separation in the decoder.

7. Why the above is necessary: directly modelling a separation of variables in the decoder architecture disentangles the expressions of space and time within the latent $\alpha$. In an entangled latent representation, it's possible that encoding a spatial observation from an OOD grid leads to a perturbed latent encoding of time because of domain-shift => poor generalisation.

8. Why long-horizon is forecasting better in this method: the latent $\alpha_t$ is modelled continuously hence the even if the actual spatial input is discretized, the continuous ODE integration captures the dynamics better. A fully discrete approach will have the set of points it can predict as much sparser in comparison. 