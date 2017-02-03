---
layout: post
title: Lessons learned from migrating software
published: true
---

The [CRG cluster](http://www.linux.crg.es/index.php/Main_Page) I use at work recently migrated from version 6.3 of [Scientific Linux](https://www.scientificlinux.org/) to version 7.2. Painful as it was re-installing most of the programs I used daily, the good side is that I learned some things in the process.

# (Seemengly) undocumented changes in `dataset`

I use Python's package [`dateset`](https://dataset.readthedocs.io/en/latest/) to manipulate a SQL database. Yet the package works well, is fairly well documented and easy to use, I noticed the `find()` function stopped behaving as it used to be in my code: 
```
# 'sample_id' passes as a script parameter
sample_id = sys.argv[3]
# load 'input_metadata' table from the pre-connected 'db' database
input_metadata = db.load_table('input_metadata')
# find all rows in which the SAMPLE_ID field equals the content of the 'sample_id' variable
# 'return_count=True' used to count the number of rows matching the query and such number saved in 'row'
# then 'row' (integer) was used for conditional printing...
row = input_metadata.find(SAMPLE_ID=sample_id, return_count=True)
if row == 0:
	print "no_sample"
elif row > 1:
	print "multiple_samples"
```
With the new version of `dataset` the `find` funtion was not returning the number of matches, which affected the downstream code (not shown here). The hack that fixed this is this:
```
sample_id = sys.argv[3]
input_metadata = db.load_table('input_metadata')
# I avoid using the `find` function and just iterate over the list of SAMPLE_IDs to cound how many of them
# match that specified in `sample_id`
n_matches= len([row['SAMPLE_ID'] for row in input_metadata if row['SAMPLE_ID'] == sample_id])
if n_matches == 0:
	print "no_sample"
elif n_matches > 1:
	print "multiple_samples"
```


# Walking `$PATH`

The [`.bashrc` file](http://unix.stackexchange.com/questions/129143/what-is-the-purpose-of-bashrc-and-how-does-it-work) is used to *"put commands here to set up the shell for use in your particular environment, or to customize things to your preferences"*. For instance, it is very convenient to initialize/define paths whose content should be executable from anywhere. [`$PATH`](https://www.tutorialspoint.com/unix/unix-environment.htm) is a Unix environment variable containing colon-separated paths in which binaries are looked for (e.g. when you execute `samtools` from anywhere). Binaries are looked for sequentally, that is, when you type `samtools` Unix will look for the corresponding binary in the `$PATH` variable from left to right and use the first found. For instance, in my `.bashrc`:
```
# utils in my HOME
export PATH=/users/GR/mb/jquilez/utils:$PATH
# executables
export PATH=/software/mb/bin:$PATH
# anaconda
export PATH=/software/mb/el7.2/anaconda2/bin:$PATH
```
New paths are added to the left of `$PATH` so these will gain priority over the pre-existing paths in `$PATH`. Also, every path added to `$PATH` has higher priority so, as it is now, binaries installed by anaconda2 will be those used. 


# Launching Java applications

http://www.usadellab.org/cms/?page=trimmomatic
