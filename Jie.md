* SMASH stops in certain sampling event suddenly.
  Reason: The mc_particle_lists contain two particles located at the same point which isn't permit in SMASH. 
  Solution: Give a random shift in position to particles sampled from the same hysurface.

  - sampler_cpu(~around line 934 of sampler.cc)
    [...particle.pdgcode = list_hadrons_.at(nid).pdgcode;]
    double tiny_move=(Random::canonical()*2.0-1.0)*1e-6;
    FourVector shift(0.0, tiny_move, tiny_move, tiny_move);
    particle.position=position+shift;
    [momentum=momentum_in_lrf.lorentzBoost(-ele.elocity);...]

  - sampler_gpu(~around line 782 and 618 of kernel_sample_particle.cl)
    [...real4 momentum1 = (real4){mt*cosh(rapidity),...]
    real tiny_move=tyche_i_float(state)*1e-6;
    real4 position=(real4){txyz.s0,txyz.s1+tiny_move,txyz.s2+tiny_move,txyz.s3+tiny_move};
    [real8 ptc1 = (real8) {momentum1.s0,momentum1.s1,...}...]

* Cannot find GL/gl.h during compilation of any program supported by OPENCL or CUDA
  Solution: check the existence of /usr/include/GL and give correct cuda path.

* Certain version of SMASH only work under the support of certain version of Boost. Such as boost_1.69 for SMASH_1.8
### From SMASH Readme
|SMASH|1.8|2.0|2.1|2.2|3.0|3.1|3.2|
|-|---|---|---|---|---|---|---|
|gcc|>=4.8|>=4.8|>=4.8|>=4.8|>=8.0|>=8.0|>=8.0|
|cmake|>=2.8.11|>=2.8.11|>=3.1|>=3.9|>=3.16|>=3.16|>=3.16|
|GSL|>=2.0|>=2.0|>=2.0|>=2.0|>=2.0|>=2.0|>=2.0|
|Eigen3|-|-|-|-|>=3.0|>=3.0|>=3.0|
|boost|>=1.49|>=1.49|>=1.49|>=1.49|-|-|-|
|pythia|8.235|8.303|8.303|8.307|8.309|8.310|8.312|

### Successfull Combination
|SMASH|1.8|2.0|2.1|2.2|3.0|3.1|3.2|
|-|---|---|---|---|---|---|---|
|gcc|10.3.0|-|-|-|10.3.0|-|-|
|cmake|3.20.1|-|-|-|3.20.1|-|-|
|GSL|2.7|-|-|-|2.7|-|-|
|Eigen3|3.3.9|-|-|-|3.3.9|-|-|
|boost|1.69|-|-|-|1.69|-|-|
|pythia|8.303|8.303|8.303|8.307|8.309|8.310|8.312|

* A random rotation of initial fireball in transverse plane is necessary sometimes, especially when you are calculating sth w.r.t. reaction plane(e.g. directed flow)

* sampler/sampler_cpu/src/particletype.cc
    Don't forget return pmag in the last function.
