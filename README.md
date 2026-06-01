# port-analysis

Benchmarking data for [PORT](https://github.com/immem-hackathon-2025/PORT).

## Genome assembly

Autocycler:

```bash
while read line; do 
    fq=path/${line}.fastq.gz 
    mkdir -p ${line} 
    cd ${line} 
    nice autocycler.sh "$fq" 10 2 
    cd ..
done < ids.txt
```

Canu:

```bash
for f in path/*.fastq.gz; do 
    sample=$(basename "$f" .fastq.gz) 
    canu -d "$sample" -p "$sample" genomeSize=5m -trimmed -nanopore "$f" maxThreads=16; 
done
```

Dragonflye - without polypolish:
```bash
for f in path/*.fastq.gz; do 
    sample=$(basename "$f" .fastq.gz) 
    nice dragonflye --gsize 5M --cpus 12 --outdir "$sample" --reads "$f"
done
```

Hybracter - run on apptainer:

```bash
for reads in *.fastq.gz; do \
    sample=$(basename "$reads" .fastq.gz); \
    apptainer exec "$containerImage" hybracter long-single \
        -l "$reads" \
        -o "$sample" \
        -s "$sample"
        -t 16 \
        --auto ; \
done
```

Plassembler:

```bash
for line in $(cat ids.txt); do \
  plassembler long \
  -d path/to/plassembler/database \
  -l path/${line}.fastq.gz -o ${line} \
  -c 1000000 -t 16 -p ${line}; \
done
```

