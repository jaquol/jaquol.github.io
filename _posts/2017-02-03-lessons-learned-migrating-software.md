---
layout: post
title: Lessons learned from migrating software
published: true
---

The [CRG cluster](http://www.linux.crg.es/index.php/Main_Page) I use at work recently migrated from version 6.3 of [Scientific Linux](https://www.scientificlinux.org/) to version 7.2. Painful as it was re-installing most of the programs I used daily, the good side is that I learned some things in the process.

# (
elif mode == "check_sample":
	sample_id = sys.argv[3]
	input_metadata = db.load_table('input_metadata')
	row = input_metadata.find(SAMPLE_ID=sample_id, return_count=True)
	if row == 0:
		print "no_sample"
	elif row > 1:
		print "multiple_samples"
