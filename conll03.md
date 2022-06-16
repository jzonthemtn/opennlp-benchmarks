# OpenNLP Benchmarks for CoNLL03

## Steps to Reproduce

Download and extract Apache OpenNLP:

```
wget https://dlcdn.apache.org/opennlp/opennlp-2.0.0/apache-opennlp-2.0.0-bin.tar.gz
gunzip -c apache-opennlp-2.0.0-bin.tar.gz | tar xvf -
```

Download the CoNLL03 data set and unzip it:

```
wget https://data.deepai.org/conll2003.zip
unzip conll2003.zip
```

### Prepare the Data

Convert the ConLL03 train and test data to OpenNLP's format:

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderConverter conll03 -lang eng \
	-types per -data train.txt > corpus_train.txt
```

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderConverter conll03 -lang eng \
	-types per -data test.txt > corpus_test.txt
```

You will see output like `Execution time: 0.136 seconds` for both commands.

### Train and Evaluate the Model

Now train the model with default parameters and default features.

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderTrainer -model en_ner_person.bin \
	-lang eng -data corpus_train.txt -nameTypes person
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

Execution time: 9.972 seconds
```

Now evaluate the model:

```
apache-opennlp-2.0.0/bin/opennlp TokenNameFinderEvaluator -model en_ner_person.bin \
	-data corpus_test.txt -nameTypes person
```

You will see output like:

```
Loading Token Name Finder model ... done (0.067s)


Average: 4680.2 sent/s
Total: 3454 sent
Runtime: 0.738s

Evaluated 3453 samples with 1617 entities; found: 1472 entities; correct: 1370.
       TOTAL: precision:   93.07%;  recall:   84.72%; F1:   88.70%.
      person: precision:   93.07%;  recall:   84.72%; F1:   88.70%. [target: 1617; tp: 1370; fp: 102]

Execution time: 0.888 seconds
```

With detailed output from the report file:

```
=== Evaluation summary ===
  Number of sentences:   3453
    Min sentence size:      1
    Max sentence size:    124
Average sentence size:  13.45
           Tags count:      3
             Accuracy: 98.91%

<-end> Evaluation Corpus Statistics

=== Detailed Accuracy By Tag ===

-----------------------------------------------------------------------------
|          Tag | Errors |  Count |   % Err | Precision | Recall | F-Measure |
-----------------------------------------------------------------------------
| person-start |    243 |   1617 | 0.15    | 0.933     | 0.85   | 0.89      |
|  person-cont |    114 |   1156 | 0.099   | 0.935     | 0.901  | 0.918     |
|        other |    151 |  43662 | 0.003   | 0.992     | 0.997  | 0.994     |
-----------------------------------------------------------------------------

<-end> Tags with the highest number of errors

=== Confusion matrix ===

Tags with 100% accuracy:

      a       b       c | Accuracy | <-- classified as
 <43511>     58      93 |   -100%  |     a = other
    109   <1042>      5 |   -100%  |     b = person-cont
    229      14   <1374>|   -100%  |     c = person-start

<-end> Confusion matrix
```
