---
title: "CIS 4930/6930 Data Processing"
author: "Dax Gerts"
highlighter: highlight.js
output: html_document
knit: slidify::knit2slides
mode: selfcontained
hitheme: tomorrow
subtitle: Google Ngrams Analysis
framework: io2012
widgets: []
---

## Google Ngrams Dataset (1)

The data used was divided into **1-gram** and **2-gram** datasets, both of which was provided by Google Books in July 2009. The data was in the form of ngrams, which are linguistic tools which allow for large-scale frequency analysis.

Variables:

* Word1
* Word2 (2-gram data only)
* Year
* Count

--- .class #id 

## Google Ngrams Dataset (2)

<img src="C:/Users/Dax/Dropbox/DataProcessing/ngramViewer.png" style="width:800px;height800px">

More information can be found [here](http://storage.googleapis.com/books/ngrams/books/datasetsv2.html)

--- .class #id

## Benchmarking (1)

The following are a series of benchmarking tests which were run to establish base processing times for the **1-gram** and **2-gram** datasets.

**1-Gram Tuple Count: (Read)**


```r
# Title: TupleCount1
# Author: Dax Gerts
# Date: 13 November 2015
# Runtime: 8.218 seconds
# Results: 1,430,731,493 tuples

require(gtBase)

data = Read(ngrams1)
result = Count(data)
View(result)
```

--- .class #id

## Benchmarking (2)

**1-Gram Word Count: (Read + Aggregate Sum)**


```r
# Title: WordCount1
# Author: Dax Gerts
# Date: 13 November 2015
# Runtime: 8.947 seconds
# Results: 1,320,305,357,364 words

require(gtBase)

data = Read(ngrams1)
result = Sum(Count,data = data)
View(result)
```

--- .class #id

## Benchmarking (3)

**2-Gram Tuple Count: (Read)**


```r
# Title: TupleCount2
# Author: Dax Gerts
# Date: 13 November 2015
# Runtime: 16.481 seconds
# Results: 37,582,158,107 tuples

require(gtBase)

data = Read(ngrams2)
result = Count(data)
View(result)
```

--- .class #id

## Benchmarking (4)

**2-Gram Word Count: (Read + Aggregate Sum)**


```r
# Title: WordCount2
# Author: Dax Gerts
# Date: 13 November 2015
# Runtime: 47.213 seconds
# Results: 4,068,566,751,420 two-word phrases

require(gtBase)

data = Read(ngrams2)
result = Sum(Count,data = data)
View(result
```

--- .class #id

## Data Cleaning (1)

Running any query on the Ngram data shows a large amount of noise, which is an artifact of the OCR technology used by Google as well as the result of the Part-Of-Speech (POS) tagging used for linguistic purposes.

<!-- html table generated in R 3.2.0 by xtable 1.8-0 package -->
<!-- Tue Dec 08 10:31:20 2015 -->
<table border=1>
<tr> <th>  </th> <th> word </th> <th> sum_count </th>  </tr>
  <tr> <td align="right"> 1 </td> <td> Nikonowicz_NOUN </td> <td> 18 </td> </tr>
  <tr> <td align="right"> 2 </td> <td> Ponung </td> <td> 45 </td> </tr>
  <tr> <td align="right"> 3 </td> <td> tll5 </td> <td> 28 </td> </tr>
  <tr> <td align="right"> 4 </td> <td> living.65_NOUN </td> <td> 46 </td> </tr>
  <tr> <td align="right"> 5 </td> <td> 552-3 </td> <td> 168 </td> </tr>
  <tr> <td align="right"> 6 </td> <td> over.12_PRT </td> <td> 45 </td> </tr>
  <tr> <td align="right"> 7 </td> <td> Tuttle. </td> <td> 44 </td> </tr>
  <tr> <td align="right"> 8 </td> <td> donors.8_NOUN </td> <td> 46 </td> </tr>
  <tr> <td align="right"> 9 </td> <td> Beug </td> <td> 116 </td> </tr>
  <tr> <td align="right"> 10 </td> <td> 39,916 </td> <td> 83 </td> </tr>
   </table>

--- .class #id

## Data Cleaning (2)

To counter the problems created by this noise, the data was filtered using Grokit's regular expression parser, **Match**, based on the Onigurama BSD library. The basis for filtering was to remove any non-word items, such as:

* Numeric characters
* Punctuation
* POS-tagging
* Items which occured fewer than five times over a given interval

--- .class #id

## Data Cleaning (3)

This method was tested in several ways (will elaborate more in later slides) resulting in the following code and output.


```r
data = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && Count >= 5]
```
<!-- html table generated in R 3.2.0 by xtable 1.8-0 package -->
<!-- Tue Dec 08 10:31:24 2015 -->
<table border=1>
<tr> <th>  </th> <th> rank </th> <th> avg_rwf </th> <th> Word1 </th> <th> year_count </th>  </tr>
  <tr> <td align="right"> 1 </td> <td align="right">   1 </td> <td align="right"> 0.11 </td> <td> the </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 2 </td> <td align="right">   2 </td> <td align="right"> 0.06 </td> <td> and </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 3 </td> <td align="right">   3 </td> <td align="right"> 0.03 </td> <td> a </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 4 </td> <td align="right">   4 </td> <td align="right"> 0.02 </td> <td> that </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 5 </td> <td align="right">   5 </td> <td align="right"> 0.01 </td> <td> as </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 6 </td> <td align="right">   6 </td> <td align="right"> 0.01 </td> <td> his </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 7 </td> <td align="right">   7 </td> <td align="right"> 0.01 </td> <td> was </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 8 </td> <td align="right">   8 </td> <td align="right"> 0.01 </td> <td> with </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 9 </td> <td align="right">   9 </td> <td align="right"> 0.01 </td> <td> which </td> <td align="right"> 425 </td> </tr>
  <tr> <td align="right"> 10 </td> <td align="right">  10 </td> <td align="right"> 0.01 </td> <td> he </td> <td align="right"> 425 </td> </tr>
   </table>

--- .class #id

## Language Diversity (1)

A query to show the increasing number of words in the English language as a function of time


```r
## Show changing volume of words over time with filtering

require(gtBase)
require(gtStats)

ng1 = Read(ngrams1)[!Match[pattern = "_"](word) && Match[pattern = "[alpha]"](word) && nwords >= 10 && year > 0]

ng2 = GroupBy(
  ng1,
  group = year,
  sum_count_5_min = Count()
)

View(ng2)
```

--- .class #id

## Language Diversity (2)

<img src="C:/Users/Dax/Dropbox/DataProcessing/langdiv1.jpg" style="width:1000px;height1000px">

--- .class #id

## Language Diversity (3)

According to a variety of sources, the English language sits at approximately a million unique words, so why is the number so high here?

* Proper names are retained
* Alternate spellings, slang
* Not all words are officially recognized as part of the language.

[How big is the English language?](http://www.languagemonitor.com/number-of-words/number-of-words-in-the-english-language-1008879/)

--- .class #id

## Language Diversity (4)

Language diversity query adapted to 2grams.


```r
## Show changing volume of words over time with filtering

require(gtBase)
require(gtStats)

ng1 = Read(ngrams2)[Year > 0]

ng2 = ng1[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && !Match[pattern = "_"](Word2) && Match[pattern = "[alpha]"](Word2) && Count >= 5]

ng3 = GroupBy(
  ng2,
  group = Year,
  sum_count_5_min = Count()
)

View(ng3)
```

--- .class #id

## Language Diversity (5)

<img src="C:/Users/Dax/Dropbox/DataProcessing/langdiv2.jpg" style="width:1000px;height1000px">

--- .class #id

## Core English (1)


```r
require(gtBase)
require(gtStats)

options(show.piggy = TRUE)
a = Ask(
  theta = integer(desc="Inverse of minimum relative frequency"),
  minyears = integer(desc="Minimum number of years")  
)

minFreq = 1.0/a$theta
minYears = a$minyears

ng = Read(ngrams1)[!Match[pattern = "_"](word) && Match[pattern = "[alpha]"](word) && nwords >= 5]
```

--- .class #id

## Core English (2)


```r
gl = GroupBy(
  ng,
  group = year,
  sum_count = Sum(nwords)
)

js1 = Join(Read(ngrams1)[!Match[pattern = "_"](word) && Match[pattern = "[alpha]"](word) && nwords >= 5], year, gl, year)[nwords/sum_count >= .(minFreq)]

gl2 = GroupBy(
  js1,
  group= c(word),
  avg_rwf = Mean(nwords/sum_count),
  year_count = Count()
)

r1 = gl2[year_count >= .(minYears)]

View(r1)
```

--- .class #id

## Core English (3)

<img src="C:/Users/Dax/Dropbox/DataProcessing/coreEnglish1.png" style="width:800px;height800px">

--- .class #id

## Core English (4)

<img src="C:/Users/Dax/Dropbox/DataProcessing/thecontrast.jpg" style="width:1000px;height1000px">

--- .class #id

## Core English (5)

<img src="C:/Users/Dax/Dropbox/DataProcessing/coreEnglish2.png" style="width:1000px;height1000px">

--- .class #id

## What's New in the English Language (1)


```r
#Group by word, get total year count, word-usage count across years
ng2 = GroupBy(
  js1,
  group = word,
  year_count = Count(),
  freq_count = Sum(nwords),
  agg_year_count = Sum(sum_count),
  rel_agg_freq = Sum(nwords/sum_count)
)                                                               

#Display words that occured less than 109 years (i.e. "new", had zero-year) and eliminate low-frequency words.
ng3 = ng2[year_count < 109 && freq_count >= 0.00001]

#Sort 
ng4 = OrderBy(ng3,dsc(freq_count))


#View results
View(ng4)
```

--- .class #id

## What's New in the English Language (2)


```r
#Group by word, get total year count, word-usage count across years
ng2 = GroupBy(
  js1,
  group = word,
  year_count = Count(),
  freq_count = Sum(nwords),
  agg_year_count = Sum(sum_count),
  rel_agg_freq = Sum(nwords/sum_count)
)                                                               

#Display words that occured less than 109 years (i.e. "new", had zero-year) and eliminate low-frequency words.
ng3 = ng2[year_count < 109 && freq_count >= 0.00001]

#Sort 
ng4 = OrderBy(ng3,dsc(freq_count))

#View results
View(ng4)
```

--- .class #id

## What's New in the English Language (3)

<img src="C:/Users/Dax/Dropbox/DataProcessing/newEnglish1.png" style="width:900px;height900px">

--- .class #id

## What's New in the English Language (4)

<img src="C:/Users/Dax/Dropbox/DataProcessing/newEnglish2.png" style="width:900px;height900px">

--- .class #id

## Gender Pronouns (1)


```r
require(gtBase)
require(gtStats)

ng1 = Read(ngrams1)[!Match[pattern = "_"](word) && Match[pattern = "[alpha]"](word) && year >= 1900]

ng2 = ng1[word == "he"|| word == "him" || word == "himself" || word == "his" || word == "she" || word == "her" || word == "herself" || word == "hers"]

ng3 = GroupBy(
  ng2,
  group = word,
  use_count = Sum(nwords)
)

ng4 = OrderBy(ng3,dsc(use_count))

View(ng4)
```

--- .class #id

## Gender Pronouns (2)

The two smallest slices are "himself" (left) and "herself" (right). Note: "hers" doesn't even register in the pie chart.

<img src="C:/Users/Dax/Dropbox/DataProcessing/gender.jpg" style="width:900px;height900px">

--- .class #id

## Zipf (1)


```r
ng = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && Count >= 5]

gl = GroupBy(
    ng,
    group = Year,
    sum_count = Sum(Count)
  )

ng2 = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && Count >= 5]

js1 = Join(ng2, Year, gl, Year)[Count/sum_count >= .(minFreq)]

gl2 = GroupBy(
    js1,
    group= c(Word1),
    avg_rwf = Mean(Count/sum_count),
    year_count = Count()
  )

results = OrderBy(gl2, dsc(avg_rwf))[year_count >= .(minYears)]

View(results)
```

--- .class #id

## Zipf (2)

Unigram Correlation: 0.9943113

<img src="C:/Users/Dax/Dropbox/DataProcessing/zipf1.png" style="width:700px;height700px">

--- .class #id

## Zipf (3)

Bigram Correlation: 0.9247849

<img src="C:/Users/Dax/Dropbox/DataProcessing/zipf2.png" style="width:700px;height700px">

--- .class #id

## Collocations (1)


```r
# Query: Collocation2008
# Author: Dax Gerts
# Date: 4 November 2015
# Runtime Without Segmenter: approx. 12.83 minutes (with filter at > 0.000001, one in 100K) approx no convergence (with filter at > 0.0000001, one in 100K)
# Runtime With Segmenter:    approx. 12.85 minutes (with filter at > 0.000001, one in 100K) approx 14.03 minutes (with filter at > 0.0000001, one in 1M)

# Description: Attempts to identify English collocations in 2008 by comparing the relative frequency of natural occuring bigrams to independently calculated bigrams

library(gtBase)
library(methods)

# Bigram set

#Read and clean bigram data
bigramData = Read(ngrams2)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && !Match[pattern = "_"](Word2) && Match[pattern = "[alpha]"](Word2) && Year == 2008 && Count >= 5]
```

--- .class #id

## Collocations (2)


```r
#Group by year to get bigram phrase totals
bigramGroup <- Segmenter(GroupBy(
  bigramData,
  group = c(Year),
  bigram_sum_count = Sum(Count)
));

#Join year word totals with original bigram data
bigramData2 = Read(ngrams2)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1) && !Match[pattern = "_"](Word2) && Match[pattern = "[alpha]"](Word2) && Year == 2008 && Count >= 5]
bigramJoin = Join(bigramData2, Year, bigramGroup, Year)[Count/bigram_sum_count > 0.0000001]

#Group by word, get relative phrase frequency
bigramGroup2 = Segmenter(GroupBy(
  bigramJoin,
  group = c(Word1, Word2),
  bigram_rel_freq = Mean(Count/bigram_sum_count)
));
```

--- .class #id

## Collocations (3)


```r
# Word1 set

#Read and clean word1 data
word1data = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1)  && Year == 2008 && Count >= 5]

#Group by year to get word 1 totals
word1Group = Segmenter(GroupBy(
  word1data, group = Year, word1_sum_count = Sum(Count)
));

#Join year word totals with original word1 data
word1Data2 = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1)  && Year == 2008 && Count >= 5]
word1Join = Join(word1Data2, Year, word1Group, Year)

#Group by word, get relative word frequency
word1Group2 = Segmenter(GroupBy(
  word1Join,
  group = c(w1 = Word1),
  word1_rel_freq = Mean(Count/word1_sum_count)
));
```

--- .class #id

## Collocations (4)


```r
# Word2 set

#Read and clean word1 data
word2data = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1)  && Year == 2008 && Count >= 5]

#Group by year to get word 2 totals
word2Group = Segmenter(GroupBy(
  word2data, group = Year, word2_sum_count = Sum(Count)
));

#Join year word totals with original word1 data
word2Data2 = Read(ngrams1)[!Match[pattern = "_"](Word1) && Match[pattern = "[alpha]"](Word1)  && Year == 2008 && Count >= 5]
word2Join = Join(word2Data2, Year, word2Group, Year)

#Group by word, get relative word frequency
word2Group2 = Segmenter(GroupBy(
  word2Join,
  group = c(w2 = Word1),
  word2_rel_freq = Mean(Count/word2_sum_count)
));
```

--- .class #id

## Collocations (5)


```r
# Join tables

#Join onegram data to Word1 of twogram data
joinWord1 = Join(word1Group2, w1,bigramGroup2, Word1)

#Join onegram data to Word2 of twogram data
joinWord2 = Join(word2Group2, w2, joinWord1, Word2)

#Calcuate independent frequencies and collocation stats
collocations = Segmenter(GroupBy(
  joinWord2,
  group = c(Word1,Word2),
  bigram_rel_freq = Sum(bigram_rel_freq + 0.00000),
  independent_rel_freq = Mean(word1_rel_freq * word2_rel_freq),
  colloc_stat = Mean(bigram_rel_freq/(word1_rel_freq*word2_rel_freq))
));
results = OrderBy(collocations,asc(colloc_stat))
View(results)
```

--- .class #id

## Collocations (6)

<!-- html table generated in R 3.2.0 by xtable 1.8-0 package -->
<!-- Tue Dec 08 10:31:26 2015 -->
<table border=1>
<tr> <th>  </th> <th> colloc_stat </th> <th> Word1 </th> <th> Word2 </th> <th> bigram_rel_freq </th> <th> independent_rel_freq </th>  </tr>
  <tr> <td align="right"> 1 </td> <td align="right"> 3280379.52 </td> <td> mutatis </td> <td> mutandis </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 2 </td> <td align="right"> 2644263.55 </td> <td> Fertil </td> <td> Steril </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 3 </td> <td align="right"> 2020596.85 </td> <td> spina </td> <td> bifida </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 4 </td> <td align="right"> 1950183.33 </td> <td> Anesth </td> <td> Analg </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 5 </td> <td align="right"> 1709379.81 </td> <td> Nag </td> <td> Hammadi </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 6 </td> <td align="right"> 1563349.80 </td> <td> Mardi </td> <td> Gras </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 7 </td> <td align="right"> 1523877.68 </td> <td> Phnom </td> <td> Penh </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 8 </td> <td align="right"> 1503741.11 </td> <td> myasthenia </td> <td> gravis </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 9 </td> <td align="right"> 1444928.14 </td> <td> Martinus </td> <td> Nijhoff </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 10 </td> <td align="right"> 1411294.78 </td> <td> Helicobacter </td> <td> pylori </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
   </table>

--- .class #id


## Collocations (7)

<!-- html table generated in R 3.2.0 by xtable 1.8-0 package -->
<!-- Tue Dec 08 10:31:27 2015 -->
<table border=1>
<tr> <th>  </th> <th> colloc_stat </th> <th> Word1 </th> <th> Word2 </th> <th> bigram_rel_freq </th> <th> independent_rel_freq </th>  </tr>
  <tr> <td align="right"> 99591 </td> <td align="right"> 0.00 </td> <td> the </td> <td> that </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99590 </td> <td align="right"> 0.00 </td> <td> the </td> <td> was </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99589 </td> <td align="right"> 0.00 </td> <td> the </td> <td> the </td> <td align="right"> 0.00 </td> <td align="right"> 0.01 </td> </tr>
  <tr> <td align="right"> 99588 </td> <td align="right"> 0.00 </td> <td> a </td> <td> the </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99587 </td> <td align="right"> 0.00 </td> <td> the </td> <td> and </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99586 </td> <td align="right"> 0.00 </td> <td> his </td> <td> the </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99585 </td> <td align="right"> 0.00 </td> <td> the </td> <td> he </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99584 </td> <td align="right"> 0.00 </td> <td> a </td> <td> that </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99583 </td> <td align="right"> 0.00 </td> <td> and </td> <td> and </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
  <tr> <td align="right"> 99582 </td> <td align="right"> 0.00 </td> <td> the </td> <td> as </td> <td align="right"> 0.00 </td> <td align="right"> 0.00 </td> </tr>
   </table>

--- .class #id

## Future Endeavors

* String-processing tools
  * StringSplit
* 3-, 4-, 5- grams
* Speech Generation

