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

