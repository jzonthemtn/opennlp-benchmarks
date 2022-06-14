# OpenNLP Benchmarks for CoNLL03

## Steps to Reproduce

Download and extract Apache OpenNLP:

```
wget https://dlcdn.apache.org/opennlp/opennlp-2.0.0/apache-opennlp-2.0.0-bin.tar.gz
gunzip -c apache-opennlp-2.0.0-bin.tar.gz | tar xvf -
```

Download the CoNLL03 data set and unzip it:

```
https://data.deepai.org/conll2003.zip
unzip conll2003.zip
```

### Prepare the Data

Convert the ConLL03 train and test data to OpenNLP's format:

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderConverter conll03 -lang eng -types per -data train.txt > corpus_train.txt
```

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderConverter conll03 -lang eng -types per -data test.txt > corpus_test.txt
```

You will see output like `Execution time: 1.750 seconds` for both commands.

### Train and Evaluate the Model

Now train the model.

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderTrainer -model en_ner_person.bin -lang eng -data corpus_train.txt
```

You will see output like:

```
Indexing events with TwoPass using cutoff of 0

	Computing event counts...  done. 203621 events
	Indexing...  done.
Collecting events... Done indexing in 26.72 s.
Incorporating indexed data for training...  
done.
	Number of Event Tokens: 203621
	    Number of Outcomes: 3
	  Number of Predicates: 442041
Computing model parameters...
Performing 300 iterations.
  1:  . (201717/203621) 0.9906492945226671
  2:  . (202770/203621) 0.9958206668270955
  3:  . (203129/203621) 0.9975837462737144
  4:  . (203261/203621) 0.9982320094685715
  5:  . (203381/203621) 0.9988213396457143
  6:  . (203429/203621) 0.9990570717165714
  7:  . (203454/203621) 0.9991798488368095
  8:  . (203494/203621) 0.9993762922291906
  9:  . (203509/203621) 0.9994499585013333
 10:  . (203533/203621) 0.999567824536762
 20:  . (203592/203621) 0.9998575785405238
 30:  . (203613/203621) 0.9999607113215239
Stopping: change in training set accuracy less than 1.0E-5
Stats: (203621/203621) 1.0
...done.

Training data summary:
#Sentences: 14041
#Tokens: 203621
#person entities: 6600

Writing name finder model ... Compressed 442041 parameters to 29538
4 outcome patterns
done (1.465s)

Wrote name finder model to
path: /tmp/en_ner_person.bin

Execution time: 45.903 seconds
```

Now evaluate the model:

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderEvaluator -model en_ner_person.bin -data corpus_test.txt
```

You will see output like:

```
Loading Token Name Finder model ... done (0.501s)
current: 243.0 sent/s avg: 243.0 sent/s total: 299 sent
current: 859.9 sent/s avg: 524.5 sent/s total: 1050 sent
current: 1185.4 sent/s avg: 744.3 sent/s total: 2235 sent


Average: 983.5 sent/s
Total: 3454 sent
Runtime: 3.512s

Evaluated 3453 samples with 1617 entities; found: 1472 entities; correct: 1370.
       TOTAL: precision:   93.07%;  recall:   84.72%; F1:   88.70%.
      person: precision:   93.07%;  recall:   84.72%; F1:   88.70%. [target: 1617; tp: 1370; fp: 102]

Execution time: 4.495 seconds
```
