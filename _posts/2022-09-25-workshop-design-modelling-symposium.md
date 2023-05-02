---
title:  "[Workshop] Learning a machine to automatically furnish a room @Berlin Arts"
layout: post
categories: workshop
---

The [Design Modelling Symposium 2022](https://design-modelling-symposium.de/), "Towards Radical Regeneration", was held at the University of Arts in Berlin and consisted of two consecutive parts: in the weekend it hosted several workshops, while throughout the weekdays there was a conference. We only attended one of the workshops - spanning the whole weekend. 


## About the workshop and Jeroen Lith

The workshop we attended -  "Machine Learning for Floor Plans" - was led by an enthousiastic, young, and hands-on guy, **Jeroen Lith, founder of *PlanFinder*** (a Revit, Rhino, and Grasshoper plugin). He is an educated (MSc, Delft) and trained (several companies, among which are OMA) Architect and holds a degree in Computer Science as well (MSc, Bristol). Over the last 2 to 3 years, he developed the idea and created PlanFinder basically himself - here and there he had some help of freelancers. Throughout the workshop he shared his motivation about developing it, showed the plug-in's main features, and for most of the time curated a session in which he guided us through the whole development procedure of one of the features - that of automatic furnishing. Although the workshop itself was interesting and we had many great discussions with the other participants, the organization wasn't flawless: no real welcome, not enough lunch, not professional. Nonetheless, I would recommend going if the content seems relevant/nice because of 1) the diversity in people that attend (young to old, scientists to practical architects, and many nationalities), 2) that it spans two full days (no rush), and 3) it seems that they attract nice people that give the workshops.

## About PlanFinder

### Motivation

The creation of floor plans is a core problem in Architectural design, hence the process of developing and analysing them should be investigated and finetuned at least regularly. As an Architect, Jeroen's obervations about the Architectural practice were clear: "Architects have too little time", "there is too little budget", "there is often a limited amount of people per project", and "a lot of time is wasted by the time-consuming execution of a pile of *too* simple tasks" (all highly correlated though). Furthermore, he found that archival data (that of existing, older, and finished projects) is "often left unused", eventhough that such precedents can be really valuable when designing a new project. His main motivation for the development of PlanFinder was a combination of the following: **"accelerating Architectural design"** (to overcome the first set of issues) and **use of older projects** (to overcome the second issue). He found that for creating floor plans there exists exactly these problems in practice, and _even though designing floor plans is often about following similar rules (sort of), holistically it is a task for which it is shown to be very difficult to find an all-working algorithm beforehand_ (based on a set of rules from beginning - say a floor plan's mask - to the end - a detailed program, including walls, doors, furniture, etc.). One could say that the **rules are fuzzy**, and there does not exist, at least insofar I know, a hand-crafted algorithm that properly works in every case (what are the best ones actually?). If rules are fuzzy, a data-driven approach is useful, in which a model learns the correlation itself (loosely speaking it learns the rules, although it is very hard to distill these rules from the model post-hoc, if possible at all) based on a large corpus of precedents (the unused examples mentioned before!). The core of Jeroen's work is to use and train such models on floor plan data he collected himself (across many companies and countries!), and then use it to inform the features he build in the plug-in.


### PlanFinder plug-in
_PlanFinder_ is a (so far: Revit, Rhino, and Grasshopper) plug-in for accelerating Architectural design. The main features are: 

- **FIT**: fitting a plan for a given mask (floor plan envelope)
- **FURNISH**: automatic furnishing of a given input room
- **GENERATE**: generate a (new, fully partitioned, and furnished) floor plan for a given mask (will not be explained in this blog)

Even though these tasks have been explored in academia for a while now (talking about doing it in a data-driven way), it is one of the first properly working programs in well known 3d modelling software. The plug-in is unique because of its **easiness in use**, **consistent output representations**, **diversity of options** (such as the "culture option" in which you can specify from which country to draw inspiration: the database in his words is "growing every month"), and **clever under-the-hood programming** that make the features possible in the first place. That having said, the models that are used for training are rather simple, but apparently good enough for the tasks (although upto validation, because we haven't yet extensively used the tool). Furthermore, his way of thinking about a problem and solving it is inspirational: to what exactly boils the problem down? what do we need in order to solve it, i.e., on what factors is it based, and can we get them out easily? what function do we want to approximate, and is this a function that we can hand-craft easily? 

### FIT
The fit function is simple, yet, in my opinion quite ingenious because of its apparent usefulness (and for the sake of argument: its simplicity). In his words, the FIT functions can be best described as "Tinder for floor plans". The idea is simple: given a mask of a particular floor plan one aims to partition, retrieve the programs that are "closest" to it. This closeness is based on some very simple _heuristics_: the matching is based mainly on the **similarity between mask shapes** (how close is the input mask shape w.r.t. the mask shapes in the database - based on the Intersection-over-Union, an 1-to-1 computable metric between shape outlines), the wall segment(s) that represent the **facade**, and location of the **entrance**. The only thing you have to specify in the software is the apartments envelope, for example a rectangle of size 8 x 6 m, including the facade (as a line) and entrace (as a point). You have access to the 5 closest matches per input: a simple GUI is generated when the FIT function is activated; easy navigating through the options (see the figure below).

![image](https://user-images.githubusercontent.com/40263235/193461235-ef800dbf-febe-4395-b29a-db395f69cd00.png)

### FURNISH
This feature can automatically furnish a not-yet-detailed program. It does so in three ordered steps:

1) Classify the function of each room
2) Place the doors
3) Place the furniture

**Classification of the rooms** is learned by using a 2D convolutional neural network. The training data is as follows: for every individual room in a floor plan, a similarly shaped data instance (raster image with same width and height) is created by highlighting the pixels (white or some other color) that together form the interior of the room. He called them "patches". For every floor plan, several data instances are therefore created, including a class label that reflects the room type. Although a bit weird (normally one would use multi-class segmentation), models can be trained in this way.

**Placement of the doors** is done in a somewhat overkill manner by using a U-Net that learns to highlight, in this case through _binary segmentation_, the positions: high enough pixel values indicate positions where doors are should be placed.

**Placemet of the furniture** is not based on pixels, but directly **works on the geometry of the rooms**, in this particular case the room was modeled as a **polygon**. What is an interesting question in itself is **how to represent the room** in the first place. Clearly, there are many ways: for example one could represent a room (or floor plan for that matter) as an _image_ (raster format), as a _geometry_ (vector format), a _graph_ (complex format), or a combination. Depending on the task you want to solve, you should chose the representation carefully. For example, when placing a bed or couch in a room, there are nearly any examples in which they are _not_ placed along the wall. **If you reduce the couch/bed to be a point (which is thus possible), the problem becomes much simpler: namely as a _point_ that is to be placed somewhere _along one of the walls_ that enclose the room**. Note: later you have to convert it back to a real couch/bed in the software, but that is simple. In this case, it makes sense to represent the room as a vector that specifies the geometry of the room as a **closed polygon**:

![fplan2room2polygon](https://user-images.githubusercontent.com/40263235/193460746-4bf01df7-4f88-4a3f-94a7-5487775ae542.png)

The polygon consists of points that are either wall joints or corners, or points that indicate the position of a door. There are 4 sub-features per point in the polygon: {X (position), Y (position), is-door (1 if a door, 0 if not), part-facade (1 if point is part of the facade, 0 if not)}. Now, we **randomly place candidate points** (along the polygon), say every 40cms. For every candidate point, **a fixed-sized set of neighbouring points (those found closest on the polygon - both clock- and counterclockwise) are added to the left and right, forming a fixed-sized 1D vector**:

![polygon2featurevectors](https://user-images.githubusercontent.com/40263235/193460753-3888b881-681b-49f3-bc82-7f7d80d840b4.png)

In order to train a supervised model we need labels. Since we know from the rooms we train it on where the couches are placed, we define the label to be TRUE when the candidate point is as close as 20 cm (or less) away from the _real_ position, the rest is FALSE. In the end, we are able to formulate the problem as a binary one, with an input feature size that is very small, yet, informative enough. Clearly, the **classes are highly imbalanced** (much more negatives than positives). A relatively small (3 layered) 1D convolutional network was able to perform well enough (although the class imbalance seemed to result in somewhat collapsed solutions).

In the workshop we focused on 1) the familiarization of different ways to represent Architecture, 2) creating the dataset in Grasshopper/Rhino, 3) training from scratch ML model (in Google Colaboratory), 4) evaluate the results in Grasshopper/Rhino. 

![image](https://user-images.githubusercontent.com/40263235/193461455-a07c7452-5328-4040-bf72-5c774d8c6b2e.png)

## Main take-aways, questions, thoughts, and discussion points

> Entrance and facade

What seemed to be **very influential factors** (at least that is how Jeroen thought about it and build it in) are that of the **facade (the placement of it) and the location of the entrance**. Interestingly, the location and size of the facade are often not a given entity in some well known datasets. 

> Representing Architectural data

Especially the question about how to represent a floor plan (in a mathematical and programmable way) is super interesting, and he showed us new ways of how to look at it - and also why it is such an important thing to do, because it closely relates to the problem you are tryig to solve. Thnink about a simple task like room counting: do we need an image? No, if a list of rooms was provided, we can compute it directly by computing the lenght of the list (or vector). It created the endevaour to formalize more explicitly how the different ways certain Architectural renderings can be represented. Maybe an idea is to construct a hierarchical categorical system that describes the ways that do so: how the different representations relate to each other, how easy they are handled by computers, whether they are easily accessible (from either an image, CAD, or BIM format), and whether the different representations can be easily converted between one and the other. For example, can the geometry of floorplan be reliably extracted from an image of a sketched (potentially old) floorplan? Which representation is most useful/representative for which task? Which compression (or abstraction) techniques throw away which information (and is this information of any interest?)?

> On the relevance of automatic program finding

There was a somewhat long discussion about whether the main task PlanFinder aimt to solve - that of automatically generating a program for a given input room bounday - is the _most_ relevant task. Two participants advocated that a more relevant task is that of _unit division given the boundary and locations of facade and stairs of a building_, i.e., determining the outlines of the individual apartments. First of all, I think the fact that a particular problem is seemingly more interesting, doesn't render the other irrelevant. Although I am not an Architect, I think (1) **both problems are of the same nature** (that of divisioning a particular boundary into smaller shapes: yes, of course, depending on other "fuzzy rules", but these could be learned from appropriate data) and (2) **they might influence (inform) each other a lot** (although this is an open question, at least for me). Clearly, whether the two problems can be separated (or should be done in parallel) is an interesting topic - and has the potential to be explored with the [Neufert! dataset](https://zenodo.org/record/7070952#.YzLgltdByUm) (as the database provides detailed information at the scale of the building).

> The difference between the practice and the academia, and the positive effect of just talking with other

The difference between what practicing Architects think is possible / done is not in line with what is done in research, especially not with that which is done in Computer science (Computer vision) research. There seems to be two _too_ well separated islands that have a very bad internet or phone connection (and whether activily being hold back or not remains unknown for now). For example, the research on automatic generation of floorplans is _much_ further than what PlanFinder has implemented. Clearly, this is not necessarily problem, but it shows once more that the realizations of already implemented ideas in research (but not in an inaccessible way for designers, e.g. only through often not well organized, incomplete, and not understandable Github repositories) are always lacking behind. 

Furthermore, I can't ignore to again mention the importance of _just talking_ with each other (among Architects and engineers), whether it for example is to update each other about certain practices being used (from Architect to engineer) or that it is not easy to directly use BIM data in machine leanring models (from engineer to Architect). 

> Remaining quotes (there could be some repetition here and there)

- "We are living in a time that we can't not use AI. It is up to us (he mentioned: young scientists and practitioners) to leverage the progress"
- "We are creative professionals and have to be brave enough to use and try out the different options, without constantly referring back to old practices"
- "There is some things that you can find precise logic for, but for a lot of things you cannot. In such cases the rules are fuzzy, although somehow we unconsciously know how to progress. Statistical or data-drive approaches can also work in such a world, in which many implicit and fuzzy rules exist."
- "The people working in the Architectural discipline should be more focussing on the holistic viewpoints instead of doing a pile of uninteresting work" 

Special thanks to [Tarique Ali](https://www.linkedin.com/in/alitarique/) who recommended the workshop to me in the first place and went with me there. His notes helped me a lot in writing this.

## References

- [Design Modelling Symposium 2022](https://design-modelling-symposium.de/)
- [PlanFinder](https://www.planfinder.xyz/)
- [Jeroen van Lith](https://www.linkedin.com/search/results/all/?heroEntityKey=urn%3Ali%3Afsd_profile%3AACoAAAOfQnwBbH2k4k3bieZArNe1RmfTYBdo4Uc&keywords=jeroen%20van%20lith&origin=RICH_QUERY_TYPEAHEAD_HISTORY&position=0&searchId=b1622786-9c93-428f-bb7b-5c6ee55a0aaa&sid=CLC)
