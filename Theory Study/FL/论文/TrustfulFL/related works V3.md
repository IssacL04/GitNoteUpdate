As mentioned, our model aims to ensure comprehensive security against any potential malicious server or clients. On this basis, to strengthen the model's adaptability to lightweight and distributed FL conditions, low communication cost, and robustness to dropouts are still under consideration. The constraints of diversified features and complicated application scenes will encourage us to learn from and compare with suitable solutions in existing works.

**Byzantine-robust**

Our model solves the issue of byzantine robustness by introducing a pure, honest client node to evaluate the tendency of whether a particular client is malicious. Cosine similarity is applied in the model to describe the tendency. The contribution parameter will be reset to zero, meaning the prohibition of a particular malicious client node. Another way to use the cosine tendency is to cut clients into clusters and use the overall cosine similarity to carry out model aggregation[1].

Besides cluster aggregation, there are also other ways to avoid byzantine attacks. Clients can send their dataset section before aggregation as an index so that the server can efficiently deduce whether the client has turned malicious by comparing the aggregation set with the initial section. Despite the low communication cost, the solution can't handle the circumstance of a malicious server[2]. Moreover, the DnC algorithm can also sufficiently defend against byzantine attacks. It utilizes spectral methods based on singular value decomposition (SVD) to detect and remove outliers. In addition, an adaptive attack against DnC has been constructed to demonstrate its robustness.[3]

**Aggregation Verification**

Verification models based on  Homomorphic Hash are fragile to private key leakage problems. High communication cost due to hash functions and digital signatures makes it difficult to apply on a large scale. Our model introduces a new verification function using cryptographic hash to increase efficiency and safety. 

Besides verification encryption, global watermarks and fingerprints can also verify the vector clusters efficiently. Experimental results show such a mechanism is effective in ownership verification, traceability, and maintains good fidelity and robustness against various watermark removal attacks.[4]

Differential privacy and Secure multi-party computation are also main research directions in FL security.However, they have not made significant breakthroughs in communication costs and time complexity, which is also a problem that needs to be addressed in the following research.[5] [6]

[1] F. Sattler, K. -R. Müller, T. Wiegand and W. Samek, "On the Byzantine Robustness of Clustered Federated Learning," ICASSP 2020 - 2020 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP), Barcelona, Spain, 2020, pp. 8861-8865, doi: 10.1109/ICASSP40776.2020.9054676.

[2]Prakash, Saurav, and Amir Salman Avestimehr. "Mitigating byzantine attacks in federated learning." *arXiv preprint arXiv:2010.07541* (2020).

[3]Shejwalkar, Virat, and Amir Houmansadr. "Manipulating the byzantine: Optimizing model poisoning attacks and defenses for federated learning." *NDSS*. 2021.

[4]Shao, Shuo, et al. "FedTracker: Furnishing Ownership Verification and Traceability for Federated Learning Model." *arXiv preprint arXiv:2211.07160* (2022).

[5]Xu, Guowen, et al. "Verifynet: Secure and verifiable federated learning." *IEEE Transactions on Information Forensics and Security* 15 (2019): 911-926.

[6]Bonawitz, Keith, et al. "Practical secure aggregation for privacy-preserving machine learning." *proceedings of the 2017 ACM SIGSAC Conference on Computer and Communications Security*. 2017.