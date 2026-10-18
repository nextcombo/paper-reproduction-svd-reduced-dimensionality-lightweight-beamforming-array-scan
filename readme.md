# [Paper Reproduction] SVD-Reduced Dimensionality Lightweight Beamforming Array Scanning: From Matrix Decomposition to AI Quick Prediction [Attached Python + MATLAB Code]

[Thesis Reproduction] SVD-Reduced Dimensionality Lightweight Beamforming Array for Massive MIMO, Satellite-Based Beamforming, and Onboard Millimeter Wave Radar: From Matrix Decomposition to AI Fast Prediction
Introduction: With the widespread adoption of 5G Massive MIMO, satellite-based beamforming, and onboard millimeter wave radars, large-scale antenna arrays cannot escape from three major challenges: high computation, high storage, and high hardware costs. When implementing full-domain beamforming with a hundred-element phased array, the massive complex-valued weight matrix can significantly burden the phase shifters and signal processing chips.
Recently, Xia et al. published a low-rank array beamforming scheme in Communications Engineering that employs SVD singular value reduction + LIVG linearly independent virtual group + PSO particle swarm optimization + Transformer deep learning prediction to complete the entire link, significantly reducing the control dimensionality while achieving lightweight beamforming without significant loss in beam performance. This paper reproduces the entire process simulation of Fig.3 and Fig.4 using MATLAB+PyTorch, disassembling the entire algorithm logic, simulation results, and engineering implementation value, suitable for practitioners in antenna arrays, wireless communications, and radar signal processing.
I. Industry Pain Points: The Dilemma of Large-Scale Beamforming Arrays
The core principle of beamforming arrays is to adjust the amplitude and phase of each element to form a main beam at a specified direction through coherent addition of electromagnetic waves, achieving continuous scanning from 0° to 30°.
Assuming a 128-element one-dimensional uniform linear array with 128 scanning angles, each angle corresponds to a set of 128-dimensional complex-valued weight vectors, ultimately forming a 128×128 weight matrix W:
Storage layer: Extensive complex-valued weights occupy a large cache, limiting small terminal hardware resources on satellites and vehicles;
Computation layer: Real-time beam switching requires frequent updating of all elemental excitations, with computational complexity growing quadratically with the number of elements;
Hardware layer: Each element is equipped with its own T/R components and phase shifter, leading to simultaneous surges in total system power consumption, cost, and volume with an increase in element count.
However, the industry has long overlooked a critical feature: the weight vectors corresponding to continuous scanning angles are highly correlated. As beam angles smoothly change, the phase of the elements forms a continuous linear distribution, leading to significant redundancy in the weight matrix. The SVD (Singular Value Decomposition) singular value decomposition is precisely a mathematical tool designed to remove redundancy and extract core features, serving as the foundational layer of the entire lightweight beam control link.
The entire light-weight beam control link forms a closed loop: from the weight matrix → SVD low-rank compression → LIVG (Low-Rank Vector Generation) for vector selection → PSO (Particle Swarm Optimization) for offline optimization → Transformer for online rapid prediction, with the complete process as follows:
Scanning angle weight matrix W → SVD dimensionality reduction to obtain a low-rank approximation Wr → select a small number of linearly independent vectors to form LIVG → PSO optimizes the LIVG combination to reduce reconstruction error → Transformer learns the mapping relationship without needing repeated optimization and directly outputs LIVG2.1 SVD singular value reduction: Stripping redundancy from the weight matrix for complex weights matrices: U, V are unitary matrices, S is the singular value diagonal matrix, with singular values arranged in descending order, numerical size representing the beam energy carried by the corresponding spatial feature.
Only retain the first r largest singular values, truncate the reconstructed low-rank matrix, discarding noise and redundant components at the tail.
This time, we reproduce two typical configurations:
1. 
A 128-element array: Retain rank r=16, with a relative error only;
2. 
A 16-element small array: Retain rank r=4, controlling dimensionality directly reducing by 75%.
Experiments have proven that using only 1/8 or even 1/4 of the original dimensions can fully preserve the phase and amplitude characteristics required for beam scanning, with virtually no distortion in the far-field main lobe position and scanning envelope.
2.2 LIVG Linearly Independent Virtual Group: Achieving Ultra-Low Dimensional Control with Simplified SVD Compression
2.3 Particle Swarm Optimization (PSO): To optimize the LIVG combination, direct equidistant selection of LIVG may lead to issues such as significant reconstruction errors and excessive dynamic range for coefficient K. The experiment employs a particle swarm optimization algorithm to search globally for the optimal LIVG combination:
1  
Particles represent a set of candidate LIVG indices;
2  
The fitness function integrates two indicators: the reconstructed far-field and ideal beam error, and the amplitude / phase range of K coefficient.
3  
The particle positions are updated iteratively through individual optimal pbest and global optimal gbest, rapidly converging to the low error and easy hardware implementation of the optimal solution.
The convergence curve of the simulation shows that within 200 iterations, the objective function rapidly decreases and stabilizes. The optimized LIVG reconstruction beam sidelobes are lower, and the main lobe alignment accuracy is higher.
2.4 Transformer Deep Learning: Offline Training, Online Second-Level Prediction with PSO Iterative Optimization is time-consuming, requiring reiteration every time array parameters or scanning ranges are changed, making it unsuitable for real-time dynamic scenarios. The original text proposes a solution to address this issue using Transformer assistance:
1  
Dataset Construction: Massive Array Parameters + SVD Low-Rank Weight Matrix as Input, Optimized LIVG Indexes by PSO as Labels;
2.
3.
Inference Stage: Newly input array weights without iterative optimization, directly output the optimal LIVG combination, with inference time in milliseconds.
Forming an engineering implementation process of "PSO offline dataset construction, Transformer online rapid inference," significantly reducing the computational overhead of dynamic beam steering.
III. Reproducibility Experimental Configuration and Simulation Results Comparison 3.1 Simulation Basic Parameters Simulation Panel
Fig.3 (Large Array SVD Decomposition Verification)
Fig.4 (Small Array LIVG Optimization + AI Prediction)
Array Form 
1D Uniform Line Array
1D Uniform Line Array
Number of Array Elements N
128
16
Scanning Step M
128
128
Scanning Range
0°~30°
0°~30°
SVD Retained Rank r
16
4
LIVG Vector Numbers
16
4
Optimization Method
Interval selection of LIVG
PSO Global Optimization + Transformer Prediction
Simulation Tools
MATLAB (Phase Matrix, Far Field Plotting)
MATLAB Electromagnetic Simulation + PyTorch Deep Learning Training
1.
Phase Matrix Contrast (Original a/b) The original phase matrix and the low-rank reconstruction matrix with r=16 are side by side compared, the continuous stripe structure of the phase changes with scanning angle is completely retained, only the minor fluctuation redundancy is eliminated by SVD, and the core phase distribution is fully preserved.
2.
Far Field Scanning Polarization Pattern (Original d/e) The ideal complete weight generated far field, the main lobe scanning trajectory of the low-rank reconstruction far field with SVD is completely resonant, the beam envelope and sidelobe level difference is extremely small, there is no obvious distortion to the naked eye.
LIVG substrate and K coefficient (original figure c/f) 16 groups of evenly spaced LIVG far-field mode uniform coverage of 0°~30° scanning range; the amplitude of the coefficient K smoothly changes with scanning angle, proving that a small amount of substrate can be linearly fitted for all scanning states. Quantitative error: rank 16 reconstruction matrix relative error is only, numerical level verification of compression accuracy.
3.3 Fig.4 Reproduction: 16-element low-dimensional control + AI acceleration scheme Fig.4 For lightweight engineering scenarios, focusing on the extreme compression scenario of r=4, fully reproduces the optimization and entire deep learning process:
1  
The phase fringe height of the weight matrix reconstruction is highly matched with the original weight, SVD low-rank matrix and PSO optimized LIVG reconstruction matrix. Four sets of bases are sufficient to restore all scanning phase information of 16 elements.
2  
After the optimization of far-field beam reconstruction effect, LIVG can follow the ideal beam with no deviation in continuous scanning. The single-angle slice curve shows that the main lobe pointing error can be ignored and the sidelobe suppression effect meets the standard.
3  
PSO converges rapidly with its iterative curve, and the optimized LIVG vector uniformly covers the scanning range to avoid beam distortion caused by local optima.
4  
Transformer predicts the results by inputting array weights into the model for training. The LIVG combination reconstruction beams and PSO offline optimization results are highly approximated in inference output, allowing for rapid generation of usable bases without iteration, suitable for dynamic beam scheduling scenarios.
3.4 Reproducibility Consistency Summary This reproducibility fully covers the two core panels of the original text, with image panel layouts, data trend changes, and physical phenomena matching exactly with the original text. Only minor differences in pixel details exist, allowing for a complete reproduction of the core simulation conclusions of the paper, validating the effectiveness of the entire low-rank beamforming link.
4. Results in-depth analysis: What engineering challenges has this solution addressed? 4.1 SVD dimensionality reduction: Reduction of data redundancy from the source. Large array weights inherently possess low rank characteristics, without sacrificing scan range or beam accuracy. It can only be achieved through matrix decomposition to reduce storage and computational requirements, thereby reducing the hardware pressure on subsequent optimization and control, adaptable for star-borne, drones, and vehicles with limited computing power.
4.2 LIVG low-dimensional control: Significant reduction in hardware channels. Taking an 16-element array as an example, the control dimensionality is reduced from 16 to 4, resulting in a 75% reduction in the number of control channels for phase shifters and amplifiers, directly reducing the overall size, power consumption, and hardware cost of the system. It is an ideal complementary algorithm for liquid crystal phased arrays and lightweight satellite terminals.
4.3 PSO+Transformer combination: Balancing precision and real-time performance. PSO: Offline high-precision optimization suitable for parameter tuning before product launch;
Transformer: Online millisecond-level inference capable of handling dynamic scenarios such as rapid beam switching and adaptive adjustment of array parameters; combined, it balances optimization precision and real-time performance, suitable for dynamic beam scenarios in 6G HAPS airborne base stations, low-altitude security radars, etc.
5. Landing application scenario prospects:
1  
2  
Massive MIMO base station macro array beam coverage optimization for 5G/6G, SVD compression reduces the real-time precoding computation load on the baseband, reducing the chip computing power consumption of base stations.
3  
Autonomous driving radar for vehicles requires high-speed multidirectional beam scanning, and a lightweight control scheme shortens the beam switching latency, enhancing obstacle detection response speed.
4  
Lightweight active phased array radar for drones and portable low-altitude security radars are constrained by power supply and volume, relying on this algorithm to significantly reduce the number of hardware channels, achieving miniaturized deployment.
VI. Summary of this complete MATLAB and PyTorch-based reproduction of Xia's low-rank phased array beam scanning simulation experiment, verifying the feasibility of the entire link from SVD dimensionality reduction - LIVG basis - PSO optimization - Transformer prediction:
1  
The weight matrix of phased array continuous scanning has a natural low-rank structure, and SVD truncation reconstruction can reduce dimensions under very small error.
2  
LIVG Linearly independent basis can reconstruct complete full-domain scanning beams with much lower control dimensions than the number of elements.
3  
PSO can solve the optimal base combination offline, Transformer deep learning realizes fast prediction without iteration, and balances accuracy and real-time performance.
4  
The entire lightweight solution is compatible with various scenarios such as satellite communication, onboard radar, and large-scale MIMO, providing mature algorithm ideas for low power consumption and miniaturized phased array systems.
Future research directions can be further expanded: the low-rank characteristics of planar two-dimensional arrays, SVD improvement algorithms under array mutual coupling, and lightweight Transformer model compression. These efforts will push this set of solutions from simulation to actual hardware testing.
References Xia, S., Zhao, M., Ma, Q. et al. Dimensionality reduced antenna array for beamforming/steering. Communications Engineering 5, 38 (2026).DOI: https://doi.org/10.1038/s44172-026-00588-6
End Interaction: Have you encountered computational and storage bottlenecks while conducting large-scale phased array simulations? Have you tried SVD low-rank, deep learning beamforming related solutions? We welcome exchanges of array simulation pitfalls in the comment section! #PhasedArray#BeamForming#SVDDimensionalityReduction#MassiveMIMO#SatelliteCommunication#AntennaSimulation

## Images

![[Paper Reproduction] SVD-Reduced Dimensionality Lightweight Beamforming Array Scanning: From Matrix Decomposition to AI Fast Prediction [Attached Python + MATLAB Code]](cover.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
