Download Link: https://assignmentchef.com/product/solved-com4513-6513-lab-3-named-entity-recognition-with-the-structured-perceptron
<br>
The goal of this lab session is to learn a named entity recogniser (NER) using the structured perceptron. For each word in a sequence, the named entity recogniser should predict one of the following labels:

<ul>

 <li>O: not part of a named entity</li>

 <li>PER: part of a person’s name</li>

 <li>LOC: part of a location’s name</li>

 <li>ORG: part of an organisation’s name</li>

 <li>MISC: part of a name of a different type (miscellaneous, e.g. not person, location or organisation)</li>

</ul>

<strong>Load the Data</strong>

The training and the test data are available in this folder (use your university google ID to access it): <a href="https://drive.google.com/drive/folders/1JOvM7GHF15tW5ufwWlBjA7NAhOJfOkKF?usp=sharing">data</a><a href="https://drive.google.com/drive/folders/1JOvM7GHF15tW5ufwWlBjA7NAhOJfOkKF?usp=sharing">.</a> It consists of sentences up to 5 words long each from the data used in this <a href="https://www.clips.uantwerpen.be/conll2003/ner/">shared task on NER</a><a href="https://www.clips.uantwerpen.be/conll2003/ner/">.</a>

After downloading the data, you can obtain the word and tag sequences for each sentence using the following function:

<table width="690">

 <tbody>

  <tr>

   <td width="690"><strong>def </strong>load_dataset_sents(file_path, as_zip<strong><sub>=</sub></strong>True, to_idx<strong><sub>=</sub></strong>False, token_vocab<strong><sub>=</sub></strong>None, target_vocab<strong><sub>=</sub></strong>None):targets <strong><sub>= </sub></strong>[] inputs <strong><sub>= </sub></strong>[] zip_inps <strong><sub>= </sub></strong>[] <strong>with </strong>open(file_path) as f:<strong>for </strong>line <strong>in </strong>f:sent, tags <strong><sub>= </sub></strong>line.split(‘t’)words <strong>= </strong>[token_vocab[w.strip()] <strong>if </strong>to_idx <strong>else </strong>w.strip() <strong>for </strong>w <strong>in </strong>sent.split()] ner_tags <strong>= </strong>[target_vocab[w.strip()] <strong>if </strong>to_idx <strong>else </strong>w.strip() <strong>for </strong>w <strong>in </strong>tags.split()] inputs.append(words) targets.append(ner_tags)zip_inps.append(list(zip(words, ner_tags))) <strong>return </strong>zip_inps <strong>if </strong>as_zip <strong>else </strong>(inputs, targets)</td>

  </tr>

 </tbody>

</table>

<h1>e.g., train_data = load_dataset_sents(‘train-sents.txt’)</h1>

<strong>Feature Extraction</strong>

After having loaded the data, you need to write functions that extract and transform a sentence into differnt feature types (this is the Φ(<em>x,y</em>) in the Perceptron algorithm below) in the form of Python dictionaries (see lecture 4, part I slides) for a given sentence:

<ol>

 <li><strong>Current word-current label, </strong>Φ<sub>1</sub>(<em>x,y</em>) <strong>(1 mark):</strong>

  <ul>

   <li>(1) Write a funtion that takes as input a corpus <em>c </em>= [<em>s</em><sub>1</sub><em>,s</em><sub>2</sub><em>,…,s<sub>n</sub></em>] where <em>s<sub>i </sub></em>= [(<em>x</em><sub>1</sub><em>,y</em><sub>1</sub>)<em>,…,</em>(<em>x</em><sub>2</sub><em>,y</em><sub>2</sub>)] is a sentence consisting of pairs of words <em>x </em>and tags <em>y</em>, and returns a Python dictionary containing current word-current label counts for each pair of words and tags in the corpus, e.g. cw_cl_counts = {‘x1_y1’:103, ‘x2_y2’:12, …}. <strong>Tip: </strong>Keep only the features (dict. keys) that have a frequency greater or equal to 3 (you can delete keys in a Python dictionary) if your feature space is too big to keep in memory.</li>

   <li>(2) Write a function phi_1(x, y, cw_cl_counts) that takes as input a sentence and returns a dictionary with counts of the cw_cl_counts keys in the given sentence (<em>x,y</em>).</li>

  </ul></li>

 <li><strong>Previous label-current label </strong>Φ<sub>2</sub>(<em>x,y</em>) <strong>(1 mark): </strong>You need to write two functions following a similar two-step approach as you did for Φ<sub>1</sub>(<em>x,y</em>) above. After you have the feature extraction functionality in place, you will need to combine Φ<sub>2 </sub>with Φ<sub>1 </sub>to see whether the new feature type improves performance. This can be done by merging the two Python dictionaries you obtain for each feature type.</li>

 <li><strong>Optional Bonus (2 marks) </strong>Add at least two more feature types (Φ<sub>3</sub>(<em>x,y</em>) and Φ<sub>4</sub>(<em>x,y</em>)) of your choice following the same approach as for Φ<sub>1 </sub>and combine them with Φ<sub>1 </sub>and Φ<sub>2 </sub>to test if you obtain improved performance. Ideas: sub word features, previous/next words, label trigrams, etc..</li>

</ol>

<strong>Tip: </strong>For the feature extraction part, you can use the Counter and Python dictionaries to store the feature names as keys with their corresponding value (see the slides of lecture 4.1). <strong>Tip++: </strong>The feature representation should be obtained by looking only into the training data, otherwise your model is cheating!

<strong>Implement the Perceptron</strong>

Finally after you have your feature extraction mechanism in place, you need to implement a structured perceptron <strong>(2 marks)</strong>. This has to be done using two functions. One function is called train and takes as input the training data as in:

<strong>Input: </strong><em>D<sub>train </sub></em>= <em>{</em>(<strong>x</strong><sup>1</sup><em>,</em><strong>y</strong><sup>1</sup>)<em>…</em>(<strong>x</strong><em><sup>M</sup>,</em><strong>y</strong><em><sup>M</sup></em>)<em>} set </em><strong>w </strong>= 0

<strong>for </strong>(<strong>x</strong><em>,</em><strong>y</strong>) <em>∈ D<sub>train </sub></em><strong>do </strong><em>predict </em><strong>yˆ </strong>= argmax<strong>w </strong><em>· </em>Φ(<strong>x</strong><em>,</em><strong>y</strong>)

<strong>y</strong><em>∈Y<sup>N</sup></em>

<strong>if yˆ </strong><em>6</em>= <strong>y then</strong>

<em>update </em><strong>w </strong>= <strong>w </strong>+ Φ(<strong>x</strong><em>,</em><strong>y</strong>) <em>− </em>Φ(<strong>x</strong><em>,</em><strong>yˆ</strong>) <strong>return w</strong>

The second function should be called predict that takes as input the current weights <em>w</em>, and a feature representation of a sentence (Φ(<em>x,y</em>)) and returns the predicted tag sequence <em>y</em>ˆ using the argmax over all the possible tag sequences.

During training of the structured perceptron, you are advised to use multiple passes with randomised order and averaging.

<strong>Evaluate the Perceptron with Different Feature Types</strong>

Write a test funtion to evaluate the perceptron. The input should be the trained weights (<strong>w</strong>) of your perceptron and test data. As the dataset is imbalanced (most words are not part of a named entity), the evaluation metric to use is the microaveaged F1 score from scikit learn: <a href="http://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html">http://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html</a><a href="http://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html">. </a>You should call it in your code as:

<h1>f1_micro = f1_score(correct, predicted, average=’micro’, labels=[‘ORG’, ‘MISC’, ‘PER’, ‘LOC’])</h1>

where y_true and y_predicted are arrays containing the correct and the predicted labels. These arrays are flatten and contain all the tags for all sentences in the test data (true and predicted respectively).

<strong>Tip: </strong>You should re-use the <strong>predict </strong>function to get the prediction for a given test sentence by iterating over all the possible tag sequences for its words.

For each feature set you use (Φ<sub>1</sub>, Φ<sub>1</sub>+Φ<sub>2 </sub>and the bonus features if you opt in), you need to answer the following questions:

<ul>

 <li>What is the F1 score you obtain for each feature set?</li>

 <li>What are the most positively-weighted features for each feature set? Give the top 10 for each class (tag) and comment on whether they make sense. (If they don’t you might have a bug.)</li>

 <li>Are the differences among the feature sets in micro-F1 score expected? Did the features you propose improve the results? Why?</li>

</ul>

Correct answers to questions count <strong>2 marks for each feature set </strong>you have implemented (<strong>+1 </strong>for the bonus features).

<strong>Tip: </strong>You can use barplots (see Python’s matplotlib library) and tables to visualise/summarise the results in your report.