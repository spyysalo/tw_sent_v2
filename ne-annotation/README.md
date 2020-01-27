# Standoff NE annotation

This directory contains named entity mention annotation for the
twitter sentiment data in [brat](http://brat.nlplab.org/)-flavoured
standoff format.

## Split into documents

```
cut -f 2- ../data_ann.csv | gsplit \
    --lines 1 --numeric-suffixes=1 --suffix-length=4 --additional-suffix=.txt
for f in x*; do mv $f ts${f#x}; done
```

## Initial tagging with Turku NER tagger

```
for f in *.txt; do
    echo -e "text=" | cat - $f | curl --data @- $URL/tagdemo/tag?format=ann \
        | perl -pe 's/\n?$/\n/' > ${f%.txt}.ann
done
```

## Duplicate elimination

for i in `seq -w 1 1509`; do s=$(echo $i | perl -pe 's/^0+//'); for j in `seq -w $((s+1)) 1509`; do diff -q -s ts$i.txt ts$j.txt; done; done | egrep 'are identical' > duplicates.txt

perl -pe 's/Files.* and (\S+) are identical/$1/' duplicates.txt | sort | uniq | wc -l
#      47

perl -pe 's/Files.* and (\S+) are identical/$1/' duplicates.txt | sort | uniq | perl -pe 's/^(\S+)\.txt/$1.txt $1.ann/' | xargs git rm

