---
created:
  - " 02-07-2024 21:42"
aliases: 
tags:
  - article/
---

# Some practical aspects of machine learning on graphs

From YSDA course 2024 by Oleg Platonov

> In this seminar, we will talk about a few useful topics about graph ML that are not particularly related to any lecture or to each other. This notebook provides a high level overview of the topics and some useful links for those who want to learn more about them. 
> In what follows, we will mostly focus on GNNs and the tasks of node classification and regression, but some of the ideas apply to broader settings, and there are a couple sections about deep learning on molecules closer to the end of the notebook.

  
  
## Different types of graphs and how GNNs can deal with them.

  

- undirected vs directed graphs

- homogeneous vs heterogeneous graphs

  

[A paper](https://arxiv.org/abs/2112.14936) about why many specialized GNNs for heterogeneous graphs proposed in the literature do not work well and simple modifications to standard GNNs are enough for these graphs.

  
  
  

## Learning on very large graphs.

  

Typically we train GNNs in a full-batch way, i.e. we train on all nodes in each optimization step. But what if the graph is so large it does not fit into RAM / VRAM?

  

**Approach 1. Minibatch training on sampled subgraphs.**

  

Do minibatch training - sample random small subgraphs for each optimization step. First, sample some seed nodes for which your GNN will make predictions. Then get their multi-hop neighbors, where the number of hops depends on the number of GNN layers (since each layer gets information from one hop further in the graph). However, the problem with getting all multi-hop neighbors is that the size of your subgraphs will increase exponentially with the number of GNN layers, and the $k$-hop neighborhood of some node might very well span the entire graph (this issue is known as the Neighborhood Explosion Phenomenon (NEP)). To avoid this problem, you might sample not all neighbors for each node, but some number of random neighbors. This significantly improves efficiency, but adds randomness to the GNN predictions (which is not necessarily harmful, but might be). Many different subgraph sampling strategies have been proposed, the main types of them are:

  

- node-based (the simplest strategy - neighbor sampling - was proposed in the [GraphSAGE paper](https://arxiv.org/abs/1706.02216))

  

- layer-based (e.g., [LADIES](https://arxiv.org/abs/1911.07323))

  

- subgraph-based (e.g., [cluster-GCN](https://arxiv.org/abs/1905.07953))

  

See [this paper](https://arxiv.org/abs/2210.13339) for details about this classification, links to examples of different types of strategies, and a cool new strategy - LABOR - that combines the advantages of neighbor-based and layer-based sampling.

  

To use this approach in DGL, see these tutorials: [1](https://docs.dgl.ai/stochastic_training/index.html), [2](https://docs.dgl.ai/guide/minibatch.html). Note that these examples use GraphBolt - a new efficient data loading framework from DGL. It is very fast, but has currently only [neighbor sampling](https://docs.dgl.ai/generated/dgl.graphbolt.NeighborSampler.html) and [LABOR sampling](https://docs.dgl.ai/generated/dgl.graphbolt.LayerNeighborSampler.html) implemented. To use an older approach to mini-batch training in DGL that additionally has some other sampling strategies implemented, including [full neighbor sampling](https://docs.dgl.ai/en/1.1.x/generated/dgl.dataloading.MultiLayerFullNeighborSampler.html#dgl.dataloading.MultiLayerFullNeighborSampler) and [cluster-GCN sampling](https://docs.dgl.ai/generated/dgl.dataloading.ClusterGCNSampler.html#dgl.dataloading.ClusterGCNSampler), see these tutorials: [1](https://docs.dgl.ai/en/1.1.x/tutorials/large/index.html), [2](https://docs.dgl.ai/en/1.1.x/guide/minibatch-node.html#guide-minibatch-node-classification-sampler).

  

**Approach 2. Distributed training.**

  

General approaches to distributed training (not specific to GNNs):

  

- data parallelism

- model parallelism

- vertical model parallelism (the most typical strategy is known as pipeline parallelism)

- horizontal model parallelism (aka tensor parallelism)

  

If you want to know more about them, read these blog posts: [1](https://huggingface.co/docs/transformers/perf_train_gpu_many), [2](https://sumanthrh.com/post/distributed-and-efficient-finetuning/), or enroll in the Efficient Deep Learning Systems course at YSDA.

  

But what about GNNs? Typically, GNNs are relatively small and graphs are relatively large, so during training most memory is used by activations (you have to store a vector of activations for each graph node in each GNN layer), rather than model parameters. Thus, Horizontal model parallelism (tensor parallelism) is not very useful - it only splits model parameters between GPUs, but not activations. Vertical model parallelism can be used, and you can either use a naive version of it (which is very inefficient, since only one GPU at a time is doing any work, while the rest are waiting for it) or combine minibatch training on subgraphs with pipeline parallelsim (for how to use pipeline parallelism in PyTroch, see [this tutorial](https://pytorch.org/docs/stable/pipeline.html)). But the most frequently used with GNNs option is data parallelism. There are a couple ways to do it:

  

- Data parallelism with minibatch training: split the graph between multiple GPUs and run minibatch training on small subgraphs on each GPU, i.e. choose a subgraph sampling strategy from the above and apply it to each partition of the main graph on each GPU separately, while synchronizing gradients when necessary. To use this approach in DGL, read these tutorials: [1](https://docs.dgl.ai/tutorials/multi/2_node_classification.html#sphx-glr-tutorials-multi-2-node-classification-py), [2](https://docs.dgl.ai/guide/distributed.html).

  

- Data parallelism with full-batch training: split the graph between miltiple GPUs and run full-batch training, i.e. train on the whole graph stored on multiple GPUs while exchanging messages between GPUs in every neighborhood aggregation step. This approach currently cannot be done in pure DGL, but you can do it using SAR - a library built on top of DGL specifically for this purpose. You can read about it here: [github](https://github.com/IntelLabs/SAR), [docs](https://sar.readthedocs.io/en/latest/), [blog post](https://medium.com/@gawrych.bartlomiej/distributed-full-graph-training-of-graph-neural-networks-with-sar-5fb47f432383), [closed DGL issue](https://github.com/dmlc/dgl/issues/5084).

  

**Approach 3. Do not use a GNN at all.**

  

We will discuss this one slightly later today (see *Making graph-agnostic models better at learning on graphs* section).

  
  
  

## Graphs with numerical node features.

  

Many real-world graphs contain numerical features of nodes (e.g., product prices in co-purchasing networks, road start / end coordinates in traffic networks, video lengths in video recommendation systems). Neural networks are generally not good at working with such features (compared to tree-based methods). How can we help them? The methods discussed below apply to all neural networks, not only GNNs.

  

The first and obvious step is to apply some kind of scaling / normalization to numerical features. You can read about different transformations for numerical features available in scikit-learn [here](https://scikit-learn.org/stable/auto_examples/preprocessing/plot_all_scaling.html#sphx-glr-auto-examples-preprocessing-plot-all-scaling-py). It is useful to experiment with different transformations. `StandardScaler` is the most often used one, but in our experience `QuantileTransformer(output_distribution='normal')` typically leads to slightly better results.

  

Can we do something even trickier to acheive even better results? Yes, we can. Check out [this paper](https://arxiv.org/abs/2203.05556) for a technique known as embeddings for numerical features. It often allows achieving slightly better results than just using standard feature transformations at the cost of having slightly more parameters / slower model. There are several variants of embeddings proposed in the paper, but PLR (Periodic-Linear-ReLU) embeddings generally perform the best. Note that it is better to apply a numerical feature embedder such as PLR after applying normal quantile transformation to features.

  
  
  

## What graph information can be used by different graph machine learning methods?

  

Useful information that a graph might contain for node classification tasks in addition to individual node features (which are used by graph-agnostic models) can be generally divided into the following ctaegories:

  

- neighborhood node features

  

- neighborhood node labels

  

- local graph structure

  

- global position in the graph

  

Which graph ML methods use which types of information? Think about GNNs, label propagation, unsupervised node embeddings. How can we provide addaitional types of information to methods that do not use these types in their standard implementation?

  
  
  

## Labels as input.

  

We can allow GNNs to directly access information about labels of (some) labeled nodes. Just add the one-hot encodings of the label ids (or learnable label embeddings) to node features (and for the node regression task, you can embed the node targets with PLR numerical feature embeddings before adding them to node features). But you need to separate the nodes for which the labels are available as features from the nodes on which you train the model (and for which the label features are masked). See [this paper](https://arxiv.org/abs/2009.03509) and [this paper (Label as Input section)](https://arxiv.org/abs/2103.13355). This technique is known as masked label prediction / labels as input. Note that if the node features already contain a lot of easily learnable information about node labels (which can be tested by training a graph-agnostic model), than this technique will not bring many benefits.

  
  
  

## Making graph-agnostic models better at learning on graphs.

  

Sometimes it is desirable to train a graph-agnostic model (e.g., logistic regression, MLP, ResNet, random forest, GBDT) on graph data because of its simplicity and scalability. But these models do not take into account any graph information for making their predictions (which is exactly why they are called graph-agnostic). How can we improve the performance of these models on graphs? There are two general approaches:

  

- graph-based postprocessing of model predictions

- graph-based preprocessing of node features (model inputs)

  

Note that both of these approaches can be used with GNNs as well, however, they tend to be far less effective in this case because GNNs already have access to some information about the graph (but sometimes they still allow achieving better performance, so why not try them).

  

**Approach 1. Graph-based postprocessing of model predictions.**

  

Take the model predictions and smooth them along graph edges, i.e. modify them to reduce the difference between predictions of neighboring nodes. This can be done with a variation of label propagation. In the standard label propagation, the inputs to the algorithm are a mix of ground truth labels (for labeled nodes) and zeros (for unlabeled nodes). To use it for model prediction postprocessing, simply replace the zeros for unlabeled nodes with model predictions (there are some more technical details, but this is the main idea). You can also compute model errors on labeled nodes and make a prediction correction step based on error smoothing with label propagation. To learn more about this approach, read the following papers: [MLP + label propagation for node classification](https://arxiv.org/abs/1810.05997) (this one focuses on end-to-end training, which is not very scalable, but there are also some experiments without end-to-end training, which show it works almost as well), [GNN + label propagation for node regression](https://arxiv.org/abs/2002.08274), [MLP + two types of label propagation for node classification](https://arxiv.org/abs/2010.13993) (this one is the easiest to read, so it is probably the best to start with it).

  

However, note that this approach makes the assumption that the graph is homophilous (assortative), i.e. edges typically connect nodes with similar labels (nodes of the same class for the classification task or nodes with close regression targets for the regression task), labels vary smoothly over the graph. This is not surprising, as label propagation, which is used in this method, makes the same assumption. If this assumption does not hold, i.e., the graph is heterophilous (disassortative), then this method will not work. If you want to measure the level of homophily (assortativity) of a graph, you can use [assortativity coefficient](https://networkx.org/nx-guides/content/algorithms/assortativity/correlation.html), which for the special case of node classification is also known as adjusted homophily (you can read about its properties in this case and other possible homophily measures in [this paper](https://arxiv.org/abs/2209.06177)). If the graph is not homophilous, it is likely better to use the next approach.

  
  

**Approach 2. Graph-based preprocessing of node features (model inputs).**

  

Simply add the graph information that you think is useful to node features.

  

If you think the neighbors features are useful, you can aggregate them and concatenate the aggregated vector to the node features vector. This method was proposed in [this paper](https://arxiv.org/abs/1902.07153), which suggests running several iterations of GCN graph convolution to aggregate the neighborhood features. This aggregation approach works particularly well when the node features are some embeddings (e.g., the embeddings of texts that are provided for each node), and often even a single iteration of graph convolution is enough. However, for other types of features, other approaches for aggregation might be better. For example, you can compute some statistics of all one-hop neighbors for each node (e.g., mean, median, max, min for numerical features, and proportions and counts for categorical features).

  

If you think the neighbors labels are useful, add their statistics over one-hop neighbors from the train set to node features (e.g., mean for the regression task and proportions and counts for the classification task). This can be viewed as a version of labels as input method, only this time you do not need to mask any train labels, because the model treats each node as an independent data sample. You can experiment with multi-hop neighbors labels too.

  

If you think the local graph structure is important, add some features describing it to node features, e.g., node degree, local clustering coefficient, graphlet counts (aka automorphism orbit counts). For efficiently computing graphlet counts, you can use [the ORCA algorithm](https://academic.oup.com/bioinformatics/article/30/4/559/205331), which is available in [this Python library](https://github.com/qema/orca-py).

  

If you think the global position in the graph is important, add some features describing it to node features, e.g., one-hot encoded cluster ids (or learnable cluster embeddings) obtained by some node clustering algorithm or self-supervised node embeddings such as DeepWalk.

  

If you think it is important to know which particular nodes a node is connected to, you can add this node's adjacency matrix row (or both the row and the column, if the graph is directed) to its node features (this new features will be very high-dimensional, but sparse, so your model might still be able to deal with them). This method was proposed in [this paper](https://arxiv.org/abs/2110.14446) and is inspired by an older method called [LINK](https://dl.acm.org/doi/10.1145/1526709.1526781), which is simply a logistic regression with adjacency matrix rows as features.

  

Models that do not work with graph structure, but take additional graph-based inputs, are sometimes called graph-augmented models. For example, there is [research on expressive power of graph-augmented MLPs](https://arxiv.org/abs/2010.15116).

  

Also note that adding additional graph-based features to nodes allows you to train GNNs on graphs that do not originally have node features.

  

This approach is an example of a more general feature augmentation approach for structured data. If you have some data samples which have some additional structure and you think this structure is important for the task at hand, you can compute some features describing this structure and add them to samples feature vectors to improve your model's performance. We will talk about one more example of this approach in the next section.

  
  
  

## Node feature augmentation for molecular graphs.

  

Let's take a break from node classification / regression and talk about graph classification / regression. For this task, Transformers that treat all graph nodes as a set and discard the graph structure are often used. One way to let such models still access some information about the graph topology is to add some features describing it to node feature vectors. This is the same approach that is used in the fields of NLP and CV, where Transformer models are much more prevalent - there positional encodings are often added to text tokens / image patches to describe in which position of the text / image a particular token / patch is located. For texts and images, designing such positional encodings is relatively easy, since texts are simply chain graphs and images are simply 2D grid graphs. But for the case of more general graphs, the task of designing helpful positional encodings is much more difficult. There are some attempts to do it using eigenvectors of the graph Laplacian, shortest path distances, random walks, etc. For a summary of some of these approaches, you can take a look at [this paper](https://arxiv.org/abs/2205.12454) (specifically, *Positional and structural encodings* part of Section 2 and Section 3.1 - *Modular positional and structural encodings*). \[However, the more interesting part of this paper is about combining Transformer and GNN layers in a single model to get the best of both worlds.\] But for molecules, which consist of atoms with categorical types, we can make a simpler and more useful feature augmentation. Just add binary features that indicate if an atom is connected to another atom of specific type (one binary feature for each atom type). We can go further and describe short chains of neighboring atoms starting at a specific atom with binary features. Cycles are important for determining the properties of molecules, so we can also add binary features indicating if an atom is a part of a cycle of a particular kind and in which position in this cycle it is located. We can make binary features indicating if an atom is a part of other important molecular substructures as well. This is similar to graphlet counts, except we now also take into account categorical node types. And categorical edge (bond) types can be considered as well. Such features often significantly improve the performance of molecular property prediction models, and can be useful not only for Transformers, but also for GNNs.

  
  
  

## Working with molecules as 3D structures.

  

Graphs are just a convenient way to represent molecules, but they are actually point clouds of atoms in 3D space. 3D structures can provide more information about molecules, and thus can potentially be better for machine learning, but working with them requires specialized deep learning architectures. The field that developes such architectures is known as Geometric Deep Learning (Graph Deep Learning is often considered a subfield of it). You can read about some methods proposed for working with 3D molecular structures in these survey papers: [1](https://arxiv.org/abs/2202.07230), [2](https://arxiv.org/abs/2312.07511).