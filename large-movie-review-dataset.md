# Apache OpenNLP Benchmarks for Large Movie Review Dataset Document Classification

The Large Movie Review Dataset is available at http://ai.stanford.edu/~amaas/data/sentiment/.

```
@InProceedings{maas-EtAl:2011:ACL-HLT2011,
  author    = {Maas, Andrew L.  and  Daly, Raymond E.  and  Pham, Peter T.  and  Huang, Dan  and  Ng, Andrew Y.  and  Potts, Christopher},
  title     = {Learning Word Vectors for Sentiment Analysis},
  booktitle = {Proceedings of the 49th Annual Meeting of the Association for Computational Linguistics: Human Language Technologies},
  month     = {June},
  year      = {2011},
  address   = {Portland, Oregon, USA},
  publisher = {Association for Computational Linguistics},
  pages     = {142--150},
  url       = {http://www.aclweb.org/anthology/P11-1015}
}
```

## Get and Prepare the Dataset

Get the dataset and extract it:

```
wget http://ai.stanford.edu/~amaas/data/sentiment/aclImdb_v1.tar.gz
gunzip -c aclImdb_v1.tar.gz | tar xvf -
```

Combine the positive and negative reviews into single separate files (each containing one review per line):

```
awk '{print "negative\t" $0}' ./aclImdb/train/neg/*.txt > negative.txt
awk '{print "negative\t" $0}' ./aclImdb/train/pos/*.txt > positive.txt
cat negative.txt positive.txt > training.txt
```

The `training.txt` file should have 25,000 lines (reviews).

## Train a Document Classification Model

Download and extract Apache OpenNLP:

```
wget https://dlcdn.apache.org/opennlp/opennlp-2.0.0/apache-opennlp-2.0.0-bin.tar.gz
gunzip -c apache-opennlp-2.0.0-bin.tar.gz | tar xvf -
```

Train the classification model using the default parameters:

```
apache-opennlp-2.0.0/bin/opennlp DoccatTrainer -lang eng -data training.txt -model reviews.bin
```

The output will be similar to (trimmed for brevity):

```
Indexing events with TwoPass using cutoff of 5

	Computing event counts...  done. 25000 events
	Indexing...  done.
Sorting and merging events... done. Reduced 25000 events to 24904.
Done indexing in 3.73 s.
Incorporating indexed data for training...  
done.
	Number of Event Tokens: 24904
	    Number of Outcomes: 2
	  Number of Predicates: 49338
...done.
Computing model parameters ...
Performing 100 iterations.
  1:  ... loglikelihood=-17328.679513999836	0.5
  2:  ... loglikelihood=-17211.385062834383	0.895
  3:  ... loglikelihood=-17096.643006176993	0.89608
  4:  ... loglikelihood=-16984.327243758315	0.89756
...
 96:  ... loglikelihood=-11482.127956384167	0.9362
 97:  ... loglikelihood=-11447.924532664598	0.93644
 98:  ... loglikelihood=-11414.001924383781	0.93664
 99:  ... loglikelihood=-11380.356063246394	0.937
100:  ... loglikelihood=-11346.982967722744	0.93744
Writing document categorizer model ... done (0.299s)

Wrote document categorizer model to
path: /tmp/reviews.bin

Execution time: 15.709 seconds
```

Evaluate the model:

```
apache-opennlp-2.0.0/bin/opennlp DoccatEvaluator -model reviews.bin -data test.txt
```

The output will be similar to:

```
Loading Document Categorizer model ... done (0.088s)
current: 18383.8 doc/s avg: 18383.8 doc/s total: 18860 doc


Average: 20178.4 doc/s 
Total: 25001 doc
Runtime: 1.239s

Accuracy: 0.93772
Number of documents: 25000
Execution time: 1.379 seconds
```