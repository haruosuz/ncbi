Haruo Suzuki  
Last Update: 2018-12-28

----------

# ASSEMBLY_REPORTS Project
Project started 2018-12-22.  

Using the assembly summary report files to find the sequence and annotation of my genome of interest.

## updates

- 2018-12-28
  - Created shell scripts `scripts/run_inspecting_gff_all.sh`
- 2018-12-25
  - renamed shell scripts `scripts/*.sh`
- 2018-12-22
  - Created the project directory using `mkdir -p ./assembly_reports/{data,scripts,analysis}`
  - Created shell scripts `scripts/*.sh`
  - Downloadeded metadata from <ftp://ftp.ncbi.nlm.nih.gov/genomes/ASSEMBLY_REPORTS/>
  - Downloaded genome data from <ftp://ftp.ncbi.nlm.nih.gov/genomes/all/>

```
mkdir -p ./{data/$(date +%F),analysis/$(date +%F)}
mkdir -p ./{data/2018-10-28,analysis/2018-10-28}
mkdir -p ./{data/2018-12-22,analysis/2018-12-22}
```

## project directories

    assembly_reports/
     README.md: project documentation
     scripts/: contains shell scripts
     data/: contains metadata and genome data
     analysis/: contains results of data analyses

## data

Data downloaded on 2018-10-28, 2018-12-22 from <ftp://ftp.ncbi.nlm.nih.gov/genomes/ASSEMBLY_REPORTS/> into `data/`:

```
ANI_report_bacteria.txt
README_assembly_summary.txt
README_change_notice.txt
assembly_summary_genbank.txt
assembly_summary_genbank_historical.txt
assembly_summary_refseq.txt
assembly_summary_refseq_historical.txt
prokaryote_type_strain_report.txt
species_genome_size.txt.gz

# assembly_accession organism_name
GCF_000005845.2 Escherichia coli str. K-12 substr. MG1655
GCF_000008865.2 Escherichia coli O157:H7 str. Sakai

# MD5 Checksums
MD5 (GCF_000005845.2_ASM584v2_genomic.gff.gz) = 83b7ffb7880b432ca0ef8f591e4d0a3f
MD5 (GCF_000008865.2_ASM886v2_genomic.gff.gz) = 75d2b78bb53ed8c8bd33978bb5c0e640
```

## scripts

The shell script `scripts/run.sh` automatically carries out the entire steps: creating directories (`data/` and `analysis/`), downloading data files, 
running the shell script for downloading data `scripts/run_get_assembly_summary.sh`, and for inspecting data `scripts/run_inspecting_assembly_summary.sh` (generating the output files `analysis/output.txt`).

Let's run the driver script in the project's main directory `assembly_reports/` with:

    bash scripts/run.sh > log.$(date +%F).txt 2>&1 &

inspecting metadata `assembly_summary_{genbank,refseq}.txt` with:
```
assembly_summary="assembly_summary_genbank.txt"
assembly_summary="assembly_summary_refseq.txt"
YYYYMMDD="2018-12-22"
#YYYYMMDD="2018-10-28"
YYYYMMDD=""
metadata="$PWD/data/${YYYYMMDD}/${assembly_summary}"; output="$PWD/analysis/${YYYYMMDD}/output_${assembly_summary}"
bash scripts/run_inspecting_assembly_summary.sh "$metadata" 2> stderr.txt > "$output"
```

downloading genome data (GFF files)
```
organism_name="Escherichia coli"
organism_name="Escherichia coli|Borreliella burgdorferi|Sinorhizobium meliloti"
organism_name="Escherichia coli str. K-12 substr. MG1655|Escherichia coli O157:H7 str. Sakai"
bash scripts/run_get_gff.sh "$metadata" "$organism_name" 2> stderr.txt > log.get_gff.txt
```

inspecting genome data (GFF file) with:
```
GFF="data/GCF_000008865.2_ASM886v2_genomic.gff" # Escherichia coli O157:H7 str. Sakai
bash scripts/run_inspecting_gff.sh "$GFF" 2> stderr.txt > analysis/output_gff.txt
```

bash scripts/run_inspecting_gff_all.sh "$GFF" > "analysis/output_gff_all.txt"


## analysis

### assembly_summary_refseq.txt

```
[] refseq_category
141914 na
 173 reference genome
6620 representative genome

[] assembly_level
2063 Chromosome
19651 Complete Genome
70439 Contig
56554 Scaffold

[] top 10 most abundant organism_name in metadata
10514 Escherichia coli
7968 Streptococcus pneumoniae
5668 Staphylococcus aureus
5433 Klebsiella pneumoniae
3997 Mycobacterium tuberculosis
3278 Pseudomonas aeruginosa
2757 Listeria monocytogenes
2620 Acinetobacter baumannii
2132 Salmonella enterica subsp. enterica serovar Typhi
1568 Neisseria meningitidis
```

### gff

```
# assembly_accession organism_name
GCF_000005845.2 Escherichia coli str. K-12 substr. MG1655
GCF_000006965.1 Sinorhizobium meliloti 1021
GCF_000008685.2 Borreliella burgdorferi B31
GCF_000008865.2 Escherichia coli O157:H7 str. Sakai
GCF_000026345.1 Escherichia coli IAI39
GCF_000183345.1 Escherichia coli O83:H1 str. NRG 857C
GCF_000299455.1 Escherichia coli O104:H4 str. 2011C-3493
```

- Genome size of "Escherichia coli O157:H7 str. Sakai" (5498578 bp) was larger than that of "Escherichia coli str. K-12 substr. MG1655" (4641652 bp).
- The number of proteins with unknown function (i.e. "hypothetical protein") was larger in "str. Sakai" (784) than in "str. K-12" (5).

#### GCF_000008865.2_ASM886v2_genomic.gff
GCF_000008865.2 Escherichia coli O157:H7 str. Sakai

```
[] genome size
NC_002695.2	5498578
NC_002128.1	92721
NC_002127.1	3306

[] top 10 most abundant product names in genome
 784 hypothetical protein
  84 inner membrane protein
  77 transcriptional regulator
  54 transposase
  52 phage minor tail protein
  31 transporter
  28 oxidoreductase
  28 lipoprotein
  27 transcriptional repressor
  26 membrane protein
```

#### GCF_000005845.2_ASM584v2_genomic.gff
GCF_000005845.2 Escherichia coli str. K-12 substr. MG1655

```
[] genome size
NC_000913.3	4641652

[] top 10 most abundant product names in genome
  32 putative inner membrane protein
   8 putative oxidoreductase
   8 IS5 transposase and trans-activator
   6 IS1 protein InsB
   6 IS1 protein InsA
   5 hypothetical protein
   4 putative structural protein%2C ethanolamine utilization microcompartment
   4 putative hydrolase
   4 inner membrane protein
   4 IS3 element protein InsF
```

## Reproducibility test
再現性テスト

```
diff analysis/2018-10-28/output_assembly_summary_refseq.txt analysis/2018-12-22/output_assembly_summary_refseq.txt
```

The results were reproduced by other researchers, including my classmate, advisor, collaborator, and colleague.

### Run environment
実行環境

#### hdmac00 - λ18
[SFC Classroom - λ18](http://classroom.sfc.keio.ac.jp/class/l-to/l-18.html)

	$ uname -a
	Darwin hdmac00.sfc.keio.ac.jp 15.6.0 Darwin Kernel Version 15.6.0: Mon Aug 29 20:21:34 PDT 2016; root:xnu-3248.60.11~1/RELEASE_X86_64 x86_64

	> sessionInfo()
	R version 3.3.1 (2016-06-21)
	Platform: x86_64-apple-darwin13.4.0 (64-bit)
	Running under: OS X 10.11.6 (El Capitan)

#### Haruo Suzuki's Mac OS X 10.9.5

	$uname -a
	Darwin localhost 13.4.0 Darwin Kernel Version 13.4.0: Mon Jan 11 18:17:34 PST 2016; root:xnu-2422.115.15~1/RELEASE_X86_64 x86_64

	> sessionInfo()
	R version 3.3.0 (2016-05-03)
	Platform: x86_64-apple-darwin13.4.0 (64-bit)
	Running under: OS X 10.9.5 (Mavericks)

## run environment

```
$uname -a
Darwin net74-dhcp153.sfc.keio.ac.jp 17.7.0 Darwin Kernel Version 17.7.0: Fri Nov  2 20:43:16 PDT 2018; root:xnu-4570.71.17~1/RELEASE_X86_64 x86_64
```

## references
- https://github.com/haruosuz/introBI/tree/master/2018
  - https://github.com/haruosuz/introBI/blob/master/2018/CaseStudy.md#ncbi-assembly_reports
  - https://github.com/haruosuz/introBI/blob/master/2018/CaseStudy.md#2018-11-27
- [GFF format](http://genome.ucsc.edu/FAQ/FAQformat.html#format3)
- [Bioinformatics Data Skills: Reproducible and Robust Research With Open Source Tools](https://github.com/haruosuz/books/blob/master/bds/README.md)
  - Chapter 3. Remedial Unix Shell
    - The 2>&1 operator is what redirects standard error to the standard output stream.
  - Chapter 7. Unix Data Tools
    - Text Processing with Awk
- https://www.ncbi.nlm.nih.gov/pubmed/20215432
Nucleic Acids Res. 2010 Jul;38(13):4207-17. doi: 10.1093/nar/gkq140. Epub 2010 Mar 9.
Transposases are the most abundant, most ubiquitous genes in nature.
Aziz RK1, Breitbart M, Edwards RA.

