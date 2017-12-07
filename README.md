# Simulate-rnaseq-reads
Simulating RNA-Seq reads from transcriptome to test software (e.g. for Drop-Seq singleCell software) 

# Purpose

In order to test DropSeq processing software in can be benefical to have synthetic data.
This data can be used to test DropSeq software and be used to perform saturation studies etc.

# Function

This script simulates RNA-Seq data, that is, sampling random sequences of a given length (--seqlength) from 
a set of sequences in fasta format (e.g. transcriptomes of various species).
Those sequences are written out as read 1 (*_R1.fastq).
The header of each sampled sequence is retained, so it is possible to track it's origin (i.e. species and gene name) to validate mapping etc.

## RNA-Seq data
Read 1 can be used to simulate single end RNA-Seq data. Paired end is not implemented at the moment (file an issue if desired).

## DropSeq data
The simulated sequences aim to reflect the DropSeq protocoll as described my Macosko et al. 2015.
For each species (provided as seperate fasta files) a given number of cells (--nrcells) is generated. 
Note, STAMP could be used interchangibly with cell. 
For each cell a random barcode (12mer) is generated written out to read 2 position 1-12 (*_R1.fastq). --nrseqs sequences are generated for each cell sharing the same barcode.
A random sequence (8mer) (simulating UMI) is also generated for each sampled sequence and written to read 2 (position 13-20).
This is independent from cell and species origin.

Since data is written as fastq (i.e. fasta + qualities), qualities values are set to a default value (9) for each nucleic acid. 



# Requirements

* python 3.5+ including the following libraries:
  * randomdict
  * Bio
  * Bio.SeqRecord
  * random
* Transcriptome(s) (as fasta fasta)
   For example Mouse, Human and Fly transcriptomes can be retrieved as below:


      wget ftp://ftp.ensembl.org/pub/release-89/fasta/mus_musculus/cdna/Mus_musculus.GRCm38.cdna.all.fa.gz
      wget ftp://ftp.ensembl.org/pub/release-89/fasta/homo_sapiens/cdna/Homo_sapiens.GRCh38.cdna.all.fa.gz
      wget ftp://ftp.ensembl.org/pub/release-89/fasta/drosophila_melanogaster/cdna/Drosophila_melanogaster.BDGP6.cdna.all.fa.gz


# Usage

    usage: simDropSeqSeqs.py [-h] [--T T [T ...]] [--Outputname OUTPUTNAME]
                             [--nrcells [NRCELLS]] [--nrseqs [NRSEQS]]
                             [--seqlength [SEQLENGTH]]

    optional arguments:
    -h, --help            show this help message and exit
  
    --T T [T ...]         Transcriptome(s) [fasta]. If multiple files, seperated
                          by space.
  
    --Outputname OUTPUTNAME
                          Base name of output files [fastq]. Two files
                          *_R1.fastq and _R2.fastq will be generated.
    --nrcells [NRCELLS]   Number of simulated cells
    --nrseqs [NRSEQS]     Number of simulated sequences per cell
    --seqlength [SEQLENGTH]
                        Length of generated sequences (in base pairs)
# Example

This example takes 3 transcriptomes as input to simulated a mixed Species experiment of human, mouse and fly cells.
Equal number of cells for each species are generated by default (--nrcells).

    python3 $RUNPATH/simDropSeqSeqs.py --T Drosophila_melanogaster.BDGP6.cdna.all.fa \
                                           Mus_musculus.GRCm38.cdna.all.fa \
                                           Homo_sapiens.GRCh38.cdna.all.fa \
                                       --seqlength 50 \
                                       --nrcells 4 \
                                       --nrseqs 2 \
                                       --Outputname mixedSpecieslibs

# Output

    head mixedSpecieslibs_R*

    ==> mixedSpecieslibs_R1.fastq <==
    @FBtr0301524:CTCTGCCGTGCC:CTCACGGC:1/1 FBtr0301524 cdna chromosome:BDGP6:3R:9238083:9240267:-1 gene:FBgn0000412 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:D1 description:D1 chromosomal protein [Source:FlyBase;Acc:FBgn0000412]
    CTCTGCCGTGCCCTCACGGC
    +
    99999999999999999999
    @FBtr0070801:CTCTGCCGTGCC:AAAACTGG:2/1 FBtr0070801 cdna chromosome:BDGP6:X:5672734:5673543:1 gene:FBgn0029785 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:RpL35 description:Ribosomal protein L35 [Source:FlyBase;Acc:FBgn0029785]
    CTCTGCCGTGCCAAAACTGG
    +
    99999999999999999999
    @FBtr0302281:GTTAGTTGATTG:TTCGAGGA:3/1 FBtr0302281 cdna chromosome:BDGP6:3L:19750943:19793569:-1 gene:FBgn0266136 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:Gyc76C description:Guanylyl cyclase at 76C [Source:FlyBase;Acc:FBgn0266136]
    GTTAGTTGATTGTTCGAGGA
    
    ==> mixedSpecieslibs_R2.fastq <==
    @FBtr0301524:CTCTGCCGTGCC:CTCACGGC:1/2 FBtr0301524 cdna chromosome:BDGP6:3R:9238083:9240267:-1 gene:FBgn0000412 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:D1 description:D1 chromosomal protein [Source:FlyBase;Acc:FBgn0000412]
    CCTCTCTTGCCGCTGGCAAGGTGTCCAAAGAGGAAACAACAAAGCCCCGT
    +
    99999999999999999999999999999999999999999999999999
    @FBtr0070801:CTCTGCCGTGCC:AAAACTGG:2/2 FBtr0070801 cdna chromosome:BDGP6:X:5672734:5673543:1 gene:FBgn0029785 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:RpL35 description:Ribosomal protein L35 [Source:FlyBase;Acc:FBgn0029785]
    AAGAAAAACTAAATAAAATCTTTTTTTTAAAGTGAAAAAAAAAAAAAAAA
    +
    99999999999999999999999999999999999999999999999999
    @FBtr0302281:GTTAGTTGATTG:TTCGAGGA:3/2 FBtr0302281 cdna chromosome:BDGP6:3L:19750943:19793569:-1 gene:FBgn0266136 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:Gyc76C description:Guanylyl cyclase at 76C [Source:FlyBase;Acc:FBgn0266136]
    GTGGCCTGGTCAATATGAAGGGCAAGGGCGATGTGGTTACTTGGTGGCTA

    STDOUT:

    50|4|2
    created dict for Drosophila_melanogaster.BDGP6.cdna.all.fa
    CTCTGCCGTGCC
    GTTAGTTGATTG
    TACTATCGCAAG
    TGTTCGCCCCAT
    created dict for Mus_musculus.GRCm38.cdna.all.fa
    TAGTCACCAATG
    ATTTTGCATAGG
    AGGTGCAGATAC
    CCGTTACCCTAA
    created dict for Homo_sapiens.GRCh38.cdna.all.fa
    TAATTCGTATAA
    CAACCCGTTTGA
    CCTGGTGTTTGC
    ACTGCGGATCAA

