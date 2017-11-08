# jgi-single-cell
A lab book for analysis of jgi single cell genomics
### Download the files. I had to do this manually :( .. This must be fixed.  either Haijk is going to fix it or I'm going to make my jgi-query script work.  I moved the files from ~/Downloads to /Users/joevineis/Documents/BOWEN/jgi_single_cells/downloads.  Then I unziped all files and decompressed the tar files


    for i in `cat samples.txt `; do unzip $i'_FD_download.zip'; done

    mkdir zip-directories-from-jgi
    mv *.zip zip-directories-from-jgi/
    ls *Raw/IMG\ Data/ | grep .gz > tar-files.txt
    for i in `cat tar-files.txt`; do tar -zxvf *Raw/IMG\ Data/$i; done



### Collect all of the fna files an generate contigs.dbs

     mkdir SAG-genomes-fa-files
     mv 27*/*.fna SAG-genomes-fa-files/
     
     ls *.fna | sed 's/\.fna//g' > samples.txt

     for i in `cat samples.txt `; do anvi-script-reformat-fasta $i'.fna' -l 0 --simplify-names -o $i'-simpleids.fna'; done
     for i in `cat samples.txt `; do anvi-gen-contigs-database -f $i'-simpleids.fna' -n $i'-single-cell' -L 5000 -o $i'-contigs.db'; done
     

### Add some important hmm gene calls to the db and ncbi cogs.  

    for i in *contigs.db; do anvi-run-hmms -c $i; done
    for i in `cat samples.txt`; do anvi-run-ncbi-cogs -c $i'-contigs.db' -T 10 --sensitive; done

### For phylogenetic analysis.

#### 1. explore the completeness etc for each SAG


     working from here: /Users/joevineis/Documents/BOWEN/jgi_single_cells/downloads/SAG-genomes-fa-files
     anvi-display-contigs-stats *.db

     anvi-get-sequences-for-hmm-hits --external-genomes external-genomes.txt \
                                -o concatenated-proteins.fa \
                                --hmm-source Campbell_et_al \
                                --gene-names Ribosom_S12_S23, Ribosomal_L1, Ribosomal_L10, Ribosomal_L11, Ribosomal_L11_N, Ribosomal_L12, Ribosomal_L13, Ribosomal_L14, Ribosomal_L16, Ribosomal_L17, Ribosomal_L18e, Ribosomal_L18p, Ribosomal_L19, Ribosomal_L2, Ribosomal_L20, Ribosomal_L21p, Ribosomal_L22, Ribosomal_L23, Ribosomal_L27, Ribosomal_L28, Ribosomal_L29, Ribosomal_L2_C, Ribosomal_L3, Ribosomal_L32p, Ribosomal_L35p, Ribosomal_L4, Ribosomal_L5, Ribosomal_L5_C, Ribosomal_L6, Ribosomal_L9_C, Ribosomal_L9_N, Ribosomal_S10, Ribosomal_S11, Ribosomal_S13, Ribosomal_S15, Ribosomal_S16, Ribosomal_S17, Ribosomal_S18, Ribosomal_S19, Ribosomal_S2, Ribosomal_S20p, Ribosomal_S3_C, Ribosomal_S4, Ribosomal_S5, Ribosomal_S5_C, Ribosomal_S6, Ribosomal_S7, Ribosomal_S8, Ribosomal_S9
                                --return-best-hit \
                                --get-aa-sequences \
                                --concatenate

