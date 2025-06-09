**PickUpSplitsAction**

export the tablet split points of an accumulo table into a single text file

**ExportLinksDriver:**

mapreduce job that scans the backlinks Accumulo table over a —qurey row-range and put each matching row to hdfs as plain text.

we will need it if we analyse outside accumulo?

**overview/**

****Identify all domains in your WSOverviewthat receive a large number of links, and then turn that list into accumulo scan-ranges for downstream jobs
**mapper:** read from the WSOverveiw accumulo table (key domain pattern: value: link count for same pattern) (dommainPattern, count)

**combiner:** sum all counts for the same doaminPattern

**reducer:** get the doaminPattern and all its paartial counts, sums them to get the total link0count per domainPattern. Filters any domainPattern whose total count below —threshold. Then the output will be only the domain (pattern and totalCount) to a text file.

driver: configures the AccumuloInputFormat to read only the link-count columns (opl:ccXY)

After we’ve got a dirof files with lines like, werun TransformDomainListToRangesInJobConfAction, which reads every part-* file in our output dir. keeps those whose exceeds the barrier value. Normalizer the patterns (get the [example.com](http://example.com) of every domain, then reverse it for accumulo com.example). Then genereate accumulo range object per domain.  

also write hadoop confi xml file with those ranges, that can go to the job’s input settings, that can pass directly inot any mapreduce job 

**in shortt**

finds which domains exceed certain threshold using link-count.

converts that list into set of row-ranges 

so that our job focus only on those domains without rescanning your entire table.

**KeyWritable:**

row and cf as two text writable

compareTo: order by row then by cf

**ValidateProcessedFiles**

uses FileUtils.isGzipFile(), if missing .gz renames by adding .gz

Warning: I migratted all below as by going through their code looks essential for our process but need your confirmation

**CrawlIndexScanForOverview/**

**Mapper:**

get accumulo key (reverseddomain and .. )  and the corresponding accumulo value (longwritable(1)) (crawlindex table)

**emit()** when the row changes we call it to put the doamin and the code into the key, when a row is missing the httpcode gets a -1 as a marker

**Combiner**

local aggregation 

**Reducer**

increment crawlIndexSizeByHttpCode (hadoop counter?) (could be used for monitoring) 

**CrawlIndexFilterByRobotstxt/**

**CrawlIndexInsertCrawledData/**

**CrawlIndexUpdateRobotstxt/**
