---
title:  "[MSc. thesis] Potential Enrichments in Malaria Diagnostics: Hyperspectral Imaging and Group-Equivariant Neural Networks"
layout: post
categories: thesis
---

![OverviewOpticalScheme](https://user-images.githubusercontent.com/40263235/184492398-14a88d2e-2ab8-43dd-baa9-13722f0639ab.png)

Under supervision of [Temitope Agbana](https://www.temitopeagbana.com/) and [Gleb Vdovin](https://www.tudelft.nl/staff/g.v.vdovine/?cHash=e07bdf8dbe4cd03a0cedd7c4761a2c6b), I succesfully defended my **MSc. thesis** (_Systems and Control_, Delft University of Technology) near the end of 2020, the year the corona virus kinda shocked our planet.


## Master thesis

The **abstract** of my work:

Proper diagnostics are essential in the combat against severe diseases which mainly have big impacts in remote areas in poor countries. A focus direction within the NC4I group at DCSC, Delft University of Technology, is the development of new imaging modalities and the design and implementation of smarter algorithms for improved detection of parasitic diseases. The first part of my research exploits hyperspectral imagery (HI) as new potential imaging modality of thin blood smears that could highly improve on preparation time, labor intensiveness and use of materials. HI retrieves both spatial and spectral information of the observed objects simultaneously, thus providing the ability to discriminate near similar constituents within the blood smear. In doing so, it enables the possibility of label-free detection. In this thesis, the development and building of such a system is addressed and carried out. In the context of malaria, it is shown that HI is promising and lays a profound foundation for further exploration. The design and evaluation of improved generalizing neural networks characterize the essence of the second and larger part of the research. Several group-equivariant networks are evaluated and compared with conventional convolutional networks which shows that efficient and redefined integration of weights can help build smarter and more robust classifiers for the detection of parasites. In group-equivariant networks, re-interpreting the way feature maps are connected to one another manifests in the development of convolutional stages that equivary under an increased amount of transformations besides merely translations. It is shown that enlarging the heuristic of that transformation group (the extra amount of transformations the operations are equivariant under) significantly contributes to better performance without necessarily increasing the size or changing the architecture of the networks. Compared to the aforementioned baseline (conventional convolutional stages), the best network (being equivariant under 16 equidistant rotations and mirror reflections) improves approximately 2-fold on all relevant performance metrics, among which are accuracy, sensitivity, specificity, precision, and the F1-score which are common measures in the classification of malaria. The networks were tested on the Rajaraman database. Furthermore, the pre-trained models are used as classifiers for a different database extracted from the microscope build by AiDx medical. At least for this specific database, it is shown that the more realistic transformations the pre-trained networks equivary under, the more robust they are.

**Full report**? [Click here](https://repository.tudelft.nl/islandora/object/uuid%3A6f2b9550-9e34-4e3f-87bc-2e3a9dc8973f).

