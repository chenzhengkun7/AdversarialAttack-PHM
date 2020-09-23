# Crafting Adversarial Examples for Deep Learning Based Prognostics

# Introduction
In manufacturing, unexpected failures are considered a primary operational risk, as they can hinder productivity and can incur huge losses. State-of-the-art Prognostics and Health Management (PHM) systems incorporate Deep Learning (DL) algorithms and Internet of Things (IoT) devices to ascertain the health status of equipment, and thus reduce the downtime, maintenance cost and increase the productivity. Unfortunately, IoT sensors and DL algorithms, both are vulnerable to cyber attacks, and hence pose a significant threat to PHM systems. In this paper, we adopt the adversarial example crafting techniques from the computer vision domain and apply them to the PHM domain. Specifically, we craft adversarial examples using the Fast Gradient Sign Method (FGSM) and Basic Iterative Method (BIM) and apply them on the Long Short-Term Memory (LSTM), Gated Recurrent Unit (GRU), and Convolutional Neural Network (CNN) based PHM models. We evaluate the impact of adversarial attacks using NASA's turbofan engine dataset. The obtained results show that all the evaluated PHM models are vulnerable to adversarial attacks and can cause a serious defect in the remaining useful life estimation. The obtained results also show that the crafted adversarial examples are highly transferable and may cause significant damages to PHM systems.

# Dataset
To evaluate the performance of the CNN, LSTM, and GRU DL algorithms, we use a well-known dataset, NASA's turbofan engine degradation simulation dataset C-MAPSS (Commercial Modular Aero-Propulsion System Simulation). This dataset includes 21 sensor data with different number of operating conditions and fault conditions. In this dataset, there are four sub-datasets (FD001-04). Every subset has training data and test data. The test data has run to failure data from several engines of the same type. Each row in test data is a time cycle which can be defined as an hour of operation. A time cycle has 26 columns where the 1st column represents engine ID, and the 2nd column represents the current operational cycle number. The columns from 3 to 5 represent the three operational settings and columns from 6-26 represent the 21 sensor values. The time series data terminates only when a fault is encountered.

# DL algorithms for RUL prediction
We use three DL models, specifically, LSTM, GRU, and CNN  for predicting the RUL of the aircraft engines as they are known for their applicability in the PdM domain. Note, at a time instant, if a piece of equipment has data available for the past N time cycles, then we use these data to predict the RUL at the N+1 time cycle. Fig. 1 shows the performance comparison of DL models with architectures LSTM(100,100,100,100) lh(80), GRU(100,100,100) lh(80), and CNN(64,64,64,64) lh(100). The notation LSTM(100,100,100,100) refers to a network that has 100 nodes in the hidden layers of the first, second, third, and fourth LSTM layers, and a sequence length of 80. In the end, there is a 1-dimensional output layer. From Fig. 1 it is evident that GRU(100, 100, 100) with a sequence length 80 provides the most accurate prediction among these three models with the least Root Mean Square Error (RMSE) of 7.62.

<img src="https://github.com/dependable-cps/AdversarialAttackPHM/blob/master/images/Comp_of_DL_Algo.PNG" height="400" width="1000">

To analyze the impact of untargeted attacks, we create a subset of test data from FD001 in which each engine has at least 150 time cycles of data. This gives us 37 engines in the FD001 dataset. This is done since engines more time cycles data helps the DL models to make more accurate RUL predictions. This gives us 37 engines in the FD001 dataset. The resultant dataset is re-evaluated using the LSTM, CNN, and GRU-based PHM models and the obtained RMSEs are 5.83, 7.92, and 5.77, respectively.

# Adversarial attacks
Fast Gradient Sign Method (FGSM)}: The FGSM was first proposed to generate adversarial images to fool the GoogLeNet model. The FGSM works by using the gradients of the neural network to create an adversarial example. This attack is also known as the one-shot method as the adversarial perturbation is generated by a single step computation.

Basic Iterative Method (BIM): The BIM is an extension of FGSM. In BIM, FGSM is applied multiple times with small step size, and clipping is performed after each step to ensure that they are in the range i.e. epsilon-neighbourhood of the original time series M. BIM is also known as Iterative-FGSM since FGSM is iterated with smaller step sizes. The adversarial examples generated through BIM are closer to the original input as perturbations are added iteratively and hence have a greater chance of fooling the network.

# Attack objective 
The objective of the attacker is to trigger either an early or delayed maintenance. An early, or in other words unnecessary maintenance can result in flight downtime and unnecessary maintenance, both of which lead to a loss of flight time, loss of human effort, loss of resources, and also incurs an extra maintenance cost. On the other hand, delayed maintenance may lead to an engine failure which might cause the loss of human lives in the worst case.

# Attack signatures: 
As mentioned earlier, the FD001 sub-dataset has 100 engines, each of which has 21 sensors. Note, 7 out of these 21 engines can be ignored since their measurements remain constant. For the rest of the 14 sensors, we used the normalization technique to convert the raw sensory data into a normalized scale. Fig. 2 shows a 3-D representation of the sensor data from engine ID 49 for 300 time cycles. We use the resultant normalized dataset to generate adversarial examples using FGSM and BIM. For illustration, Fig. 3 and Fig. 4 shows examples of a perturbed data from sensor 2 of engine ID 49 crafted using FGSM (with epsilon = 0.3) and BIM (with alpha = 0.003, \epsilon = 0.3 and I= 100), respectively. From Fig. 4 it can be observed that the BIM attack generates adversarial examples that are closer to the input. We choose epsilon = 0.3 for both FGSM and BIM attacks to make sure that the crafted adversarial examples are stealthy. Such stealthy attacks often fall within the boundary conditions of the sensor measurements, and hence they are indeed hard to detect using the common attack detection mechanisms.

<img src="https://github.com/dependable-cps/AdversarialAttackPHM/blob/master/images/3D.PNG" height="300" width="400">

<img src="https://github.com/dependable-cps/AdversarialAttackPHM/blob/master/images/Attack_Signatures.PNG" height="550" width="950">

# Impact of adversarial attacks on turbofan engine PdM
From Fig. 5, we observe that the FGSM attack (with epsilon=0.3) increases the RMSE of CNN, LSTM and GRU models by 231\%, 234\%, and 194\%, respectively, when compared to the DL models without attack. For the BIM attack (with alpha = 0.003, epsilon = 0.3 and I= 100), we also observe the similar trend, that is the RMSE for the CNN, LSTM and GRU model is increased by 394%, 451%, and 446%, respectively, when compared to the DL models without attack. In all cases, as shown in Fig. 5, the BIM attack results in a larger RMSE when compared to the FGSM attack.

It is evident from Fig. 5 that LSTM, GRU, and CNN are greatly affected by both FGSM and BIM attack. In the case of BIM attack, there is a considerable impact on all PdM models.

<img src="https://github.com/dependable-cps/AdversarialAttackPHM/blob/master/images/Impact_of_attack.PNG" height="400" width="1000">

# Piece-wise RUL prediction
To elucidate the impact of FGSM and BIM attacks on specific engine data, we first apply the piece-wise RUL prediction (using the same DL models) for a single-engine (in this case engine ID 17) and then apply the crafted adversarial examples. The piece-wise RUL prediction gives a better visual representation of degradation (health status) in an aircraft engine. 

From Fig. 6, We observe that the crafted adversarial examples have a strong impact from the beginning of the RUL prediction on the CNN model when compared to the LSTM and GRU models. The piece-wise RUL prediction after the attack on the CNN model follows the same trend of the piece-wise RUL without attack, however, the attacked RUL values remain quite far from the actual prediction. On the other hand, the impact of adversarial attacks on the GRU model is interesting since we observe that the RUL remains almost constant up to 104 time cycles and 129 time cycles for FGSM and BIM attacks, respectively, then starts decreasing. Such a phenomenon is deceiving in nature as it indicates that the engine is quite healthy and may influence a `no maintenance required' decision by the maintenance engineer.  Once again, it is evident that the BIM attack has a stronger impact on piece-wise RUL prediction when compared to the FGSM attack.

# Performance variation vs. the amount of perturbation
 In this part of the experiments, we explore the impact of the amount of perturbation $\epsilon$ on the GRU model performance in terms of RMSE. The obtained result is shown in Fig 7. We observe that for the larger values of epsilon, the BIM attack results in higher RMSE when compared to the FGSM. For instance, for epsilon=1.2, the FGSM attack results in an RMSE of 32.63, whereas the BIM attack results in an RMSE of 53.67. This shows that for the same value of epsilon, BIM can generate adversarial examples impacting the RMSE approximately twice when compared to the FGSM.

<img src="https://github.com/dependable-cps/AdversarialAttack-PHM/blob/master/images/Performance.PNG" height="240" width="300">

# Citation
If this is useful for your work, please cite our <a href="https://arxiv.org/abs/2009.10149">paper</a>:<br>

<div class="highlight highlight-text-bibtex"><pre>
@article{advPHM20,
title={Crafting Adversarial Examples for Deep Learning Based Prognostics (Extended Version)},
  author={Mode, Gautam Raj and Hoque, Khaza Anuarul},
  journal={arXiv preprint arXiv:2009.10149},
  year={2020}
}
</pre></div>
