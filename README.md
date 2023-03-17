# EasyFuse 

[![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/tron-bioinformatics/EasyFuse)](https://github.com/TRON-Bioinformatics/EasyFuse/releases)
[![Docker Image Version (latest semver)](https://img.shields.io/docker/v/tronbioinformatics/easyfuse?label=docker)](https://hub.docker.com/r/tronbioinformatics/easyfuse)
[![License](https://img.shields.io/badge/license-GPLv3-green)](https://opensource.org/licenses/GPL-3.0)

EasyFuse is a pipeline to detect fusion transcripts from RNA-seq data with high accuracy.
EasyFuse uses five fusion gene detection tools, [STAR-Fusion](https://github.com/STAR-Fusion/STAR-Fusion/wiki), [InFusion](https://bitbucket.org/kokonech/infusion/src/master/), [MapSplice2](http://www.netlab.uky.edu/p/bioinfo/MapSplice2), [Fusioncatcher](https://github.com/ndaniel/fusioncatcher), and [SoapFuse](https://sourceforge.net/p/soapfuse/wiki/Home/) along with a powerful read filtering strategy, stringent re-quantification of supporting reads and machine learning for highly accurate predictions.

<p align="center"><img src="img/easyfuse_workflow.png"></p>

 - Publication: [Weber D, Ibn-Salem J, Sorn P, et al. Nat Biotechnol. 2022](https://doi.org/10.1038/s41587-022-01247-9)

We recommend using EasyFuse with Docker or Singularity.

## Usage


### Download reference data

Before running EasyFuse the following reference annotation data needs to be downloaded (~92 GB).

```
# Download reference archive
wget ftp://easyfuse.tron-mainz.de/easyfuse_ref_v2.tar.gz

# Extract reference archive
tar xvfz easyfuse_ref_v2.tar.gz
```

### Run EasyFuse with Docker

The Docker image can be downloaded from [dockerhub](https://hub.docker.com/r/tronbioinformatics/easyfuse) using the following command:

```
docker pull tronbioinformatics/easyfuse:latest
```

EasyFuse will require three folders:

* The input data folder containing FASTQ files, in this example `/path/to/data`.
* The reference data folder, in this example `/path/to/easyfuse_ref`
* The output folder, in this example `/path/to/output`

EasyFuse can be started by mapping the input data, references, and output folders.

```
docker run \
  --name easyfuse_container \
  -v /path/to/easyfuse_ref:/ref \
  -v /path/to/data:/data \
  -v /path/to/output:/output \
  --rm \
  -it easyfuse:latest \
  python /code/easyfuse/processing.py -i /data -o /output

```

### Run EasyFuse with Singularity

Alternatively, EasyFuse can be executed with [Singularity](https://sylabs.io/docs/) as follows:

```
singularity exec 
  --containall \
  --bind /path/to/easyfuse_ref:/ref \
  --bind /path/to/data:/data \
  --bind /path/to/output:/output \  
  docker://tronbioinformatics/easyfuse:latest \
  python /code/easyfuse/processing.py -i /data/ -o /output

```

The output can be found in `/path/to/output/FusionSummary`.


### Output format

EasyFuse creates three output files per sample in the `FusionSummary` folder: 

 - `<Sample_Name>_fusRank_1.csv`
 - `<Sample_Name>_fusRank_1.pred.csv` 
 - `<Sample_Name>_fusRank_1.pred.all.csv`
 
Within the files, each line describes a candidate fusion transcript. The prefix `<Sample_Name>` is inferred from the input fastq file names. The file `_fusRank_1.csv` contains only annotated features, while the files `.pred.csv` and `.pred.all.csv` contain additionally the prediction probability assigned by the EasyFuse model as well as the assigned prediction class (*positive* or *negative*). The file `.pred.all.csv` contains information on all considered fusion transcripts, while the file `.pred.csv` contains only those with a *positive* assigned prediction class. 

#### Example Output

The following table shows an example of the `.pred.all.csv` file.:

| BPID | context_sequence_id | FTID | Fusion_Gene | Breakpoint1 | Breakpoint2 | context_sequence_100_id | type | exon_nr | exon_starts | exon_ends | exon_boundary1 | exon_boundary2 | exon_boundary | bp1_frame | bp2_frame | frame | context_sequence | context_sequence_bp | neo_peptide_sequence | neo_peptide_sequence_bp | fusioncatcher_detected | fusioncatcher_junc | fusioncatcher_span | starfusion_detected | starfusion_junc | starfusion_span | infusion_detected | infusion_junc | infusion_span | mapsplice_detected | mapsplice_junc | mapsplice_span | soapfuse_detected | soapfuse_junc | soapfuse_span | tool_count | tool_frac | ft_bp_best | ft_a_best | ft_b_best | ft_junc_best | ft_span_best | ft_anch_best | wt1_bp_best | wt1_a_best | wt1_b_best | wt1_junc_best | wt1_span_best | wt1_anch_best | wt2_bp_best | wt2_a_best | wt2_b_best | wt2_junc_best | wt2_span_best | wt2_anch_best | ft_bp_cnt_best | ft_a_cnt_best | ft_b_cnt_best | ft_junc_cnt_best | ft_span_cnt_best | ft_anch_cnt_best | wt1_bp_cnt_best | wt1_a_cnt_best | wt1_b_cnt_best | wt1_junc_cnt_best | wt1_span_cnt_best | wt1_anch_cnt_best | wt2_bp_cnt_best | wt2_a_cnt_best | wt2_b_cnt_best | wt2_junc_cnt_best | wt2_span_cnt_best | wt2_anch_cnt_best | prediction_prob | prediction_class
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 8:42968214:+_10:43116584:+ | 07b05d58a3f6d152 | HOOK3_8:42968214:+_ENST00000307602_RET_10:43116584:+_ENST00000355710 | HOOK3_RET | 8:42968214:+ | 10:43116584:+ | b18ce98522749d2e | trans | 20 | 42968013*43116584 | 42968214*43116731 | right_boundary | left_boundary | both | 0 | 0 | in_frame | AAGAAGGCATTTGCAGCTCCAGACTCAATTAGAACAGCTCCAAGAAGAAACATTCAGACTAGAAGCAGCCAAAGATGATTATCGAATACGTTGTGAAGAGTTAGAAAAGGAGATCTCTGAACTTCGGCAACAGAATGATGAACTGACCACTTTGGCAGATGAAGCTCAGTCTCTGAAAGATGAGATCGACGTGCTGAGACATTCTTCTGATAAAGTATCTAAACTAGAAGGTCAAGTAGAATCTTATAAAAAGAAGCTAGAAGACCTTGGTGATTTAAGGCGGCAGGTTAAACTCTTAGAAGAGAAGAATACCATGTATATGCAGAATACTGTCAGTCTAGAGGAAGAGTTAAGAAAGGCCAACGCAGCGCGAAGTCAACTTGAAACCTACAAGAGACAGGAGGATCCAAAGTGGGAATTCCCTCGGAAGAACTTGGTTCTTGGAAAAACTCTAGGAGAAGGCGAATTTGGAAAAGTGGTCAAGGCAACGGCCTTCCATCTGAAAGGCAGAGCAGGGTACACCACGGTGGCCGTGAAGATGCTGAAAGAGAACGCCTCCCCGAGTGAGCTGCGAGACCTGCTGTCAGAGTTCAACGTCCTGAAGCAGGTCAACCACCCACATGTCATCAAATTGTATGGGGCCTGCAGCCAGGATGGCCCGCTCCTCCTCATCGTGGAGTACGCCAAATACGGCTCCCTGCGGGGCTTCCTCCGCGAGAGCCGCAAAGTGGGGCCTGGCTACCTGGGCAGTGGAGGCAGCCGCAACTCCAGCTCCCTGGACCACCCGGATGAGCGGGCCC | 400 | "NAARSQLETYKRQEDPKWEFPRKNLV" | 13 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 4 | 0.8 | 400 | 1327.72589688 | 1096.43026325 | 155.679753403 | 328.039480385 | 25 | 400 | 869.582622581 | 2.22399647719 | 0 | 1.11199823859 | 0 | 400 | 0 | 648.294973101 | 0 | 0 | 0 | 400 | 1194 | 986 | 140 | 295 | 25 | 400 | 782 | 2 | 0 | 1 | 0 | 400 | 0 | 583 | 0 | 0 | 0 | 0.762 | positive |
| 8:42968214:+_10:43116584:+ | 07b05d58a3f6d152 | HOOK3_8:42968214:+_ENST00000307602_RET_10:43116584:+_ENST00000340058 | HOOK3_RET | 8:42968214:+ | 10:43116584:+ | b18ce98522749d2e | trans | 19 | 42968013*43116584 | 42968214*43116731 | right_boundary | left_boundary | both | 0 | 0 | in_frame | AAGAAGGCATTTGCAGCTCCAGACTCAATTAGAACAGCTCCAAGAAGAAACATTCAGACTAGAAGCAGCCAAAGATGATTATCGAATACGTTGTGAAGAGTTAGAAAAGGAGATCTCTGAACTTCGGCAACAGAATGATGAACTGACCACTTTGGCAGATGAAGCTCAGTCTCTGAAAGATGAGATCGACGTGCTGAGACATTCTTCTGATAAAGTATCTAAACTAGAAGGTCAAGTAGAATCTTATAAAAAGAAGCTAGAAGACCTTGGTGATTTAAGGCGGCAGGTTAAACTCTTAGAAGAGAAGAATACCATGTATATGCAGAATACTGTCAGTCTAGAGGAAGAGTTAAGAAAGGCCAACGCAGCGCGAAGTCAACTTGAAACCTACAAGAGACAGGAGGATCCAAAGTGGGAATTCCCTCGGAAGAACTTGGTTCTTGGAAAAACTCTAGGAGAAGGCGAATTTGGAAAAGTGGTCAAGGCAACGGCCTTCCATCTGAAAGGCAGAGCAGGGTACACCACGGTGGCCGTGAAGATGCTGAAAGAGAACGCCTCCCCGAGTGAGCTGCGAGACCTGCTGTCAGAGTTCAACGTCCTGAAGCAGGTCAACCACCCACATGTCATCAAATTGTATGGGGCCTGCAGCCAGGATGGCCCGCTCCTCCTCATCGTGGAGTACGCCAAATACGGCTCCCTGCGGGGCTTCCTCCGCGAGAGCCGCAAAGTGGGGCCTGGCTACCTGGGCAGTGGAGGCAGCCGCAACTCCAGCTCCCTGGACCACCCGGATGAGCGGGCCC | 400 | "NAARSQLETYKRQEDPKWEFPRKNLV" | 13 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 4 | 0.8 | 400 | 1327.72589688 | 1096.43026325 | 155.679753403 | 328.039480385 | 25 | 400 | 869.582622581 | 2.22399647719 | 0 | 1.11199823859 | 0 | 400 | 0 | 648.294973101 | 0 | 0 | 0 | 400 | 1194 | 986 | 140 | 295 | 25 | 400 | 782 | 2 | 0 | 1 | 0 | 400 | 0 | 583 | 0 | 0 | 0 | 0.762 | positive |
| 8:42968214:+_10:43116584:+ | 07b05d58a3f6d152 | HOOK3_8:42968214:+_ENST00000527306_RET_10:43116584:+_ENST00000355710 | HOOK3_RET | 8:42968214:+ | 10:43116584:+ | b18ce98522749d2e | trans | 9 | 42968013*43116584 | 42968214*43116731 | right_boundary | left_boundary | both | -1 | 0 | no_frame | AAGAAGGCATTTGCAGCTCCAGACTCAATTAGAACAGCTCCAAGAAGAAACATTCAGACTAGAAGCAGCCAAAGATGATTATCGAATACGTTGTGAAGAGTTAGAAAAGGAGATCTCTGAACTTCGGCAACAGAATGATGAACTGACCACTTTGGCAGATGAAGCTCAGTCTCTGAAAGATGAGATCGACGTGCTGAGACATTCTTCTGATAAAGTATCTAAACTAGAAGGTCAAGTAGAATCTTATAAAAAGAAGCTAGAAGACCTTGGTGATTTAAGGCGGCAGGTTAAACTCTTAGAAGAGAAGAATACCATGTATATGCAGAATACTGTCAGTCTAGAGGAAGAGTTAAGAAAGGCCAACGCAGCGCGAAGTCAACTTGAAACCTACAAGAGACAGGAGGATCCAAAGTGGGAATTCCCTCGGAAGAACTTGGTTCTTGGAAAAACTCTAGGAGAAGGCGAATTTGGAAAAGTGGTCAAGGCAACGGCCTTCCATCTGAAAGGCAGAGCAGGGTACACCACGGTGGCCGTGAAGATGCTGAAAGAGAACGCCTCCCCGAGTGAGCTGCGAGACCTGCTGTCAGAGTTCAACGTCCTGAAGCAGGTCAACCACCCACATGTCATCAAATTGTATGGGGCCTGCAGCCAGGATGGCCCGCTCCTCCTCATCGTGGAGTACGCCAAATACGGCTCCCTGCGGGGCTTCCTCCGCGAGAGCCGCAAAGTGGGGCCTGGCTACCTGGGCAGTGGAGGCAGCCGCAACTCCAGCTCCCTGGACCACCCGGATGAGCGGGCCC | 400 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 4 | 0.8 | 400 | 1327.72589688 | 1096.43026325 | 155.679753403 | 328.039480385 | 25 | 400 | 869.582622581 | 2.22399647719 | 0 | 1.11199823859 | 0 | 400 | 0 | 648.294973101 | 0 | 0 | 0 | 400 | 1194 | 986 | 140 | 295 | 25 | 400 | 782 | 2 | 0 | 1 | 0 | 400 | 0 | 583 | 0 | 0 | 0 | 0.762 | positive |
| 8:42968214:+_10:43116584:+ | 07b05d58a3f6d152 | HOOK3_8:42968214:+_ENST00000527306_RET_10:43116584:+_ENST00000340058 | HOOK3_RET | 8:42968214:+ | 10:43116584:+ | b18ce98522749d2e | trans | 8 | 42968013*43116584 | 42968214*43116731 | right_boundary | left_boundary | both | -1 | 0 | no_frame | AAGAAGGCATTTGCAGCTCCAGACTCAATTAGAACAGCTCCAAGAAGAAACATTCAGACTAGAAGCAGCCAAAGATGATTATCGAATACGTTGTGAAGAGTTAGAAAAGGAGATCTCTGAACTTCGGCAACAGAATGATGAACTGACCACTTTGGCAGATGAAGCTCAGTCTCTGAAAGATGAGATCGACGTGCTGAGACATTCTTCTGATAAAGTATCTAAACTAGAAGGTCAAGTAGAATCTTATAAAAAGAAGCTAGAAGACCTTGGTGATTTAAGGCGGCAGGTTAAACTCTTAGAAGAGAAGAATACCATGTATATGCAGAATACTGTCAGTCTAGAGGAAGAGTTAAGAAAGGCCAACGCAGCGCGAAGTCAACTTGAAACCTACAAGAGACAGGAGGATCCAAAGTGGGAATTCCCTCGGAAGAACTTGGTTCTTGGAAAAACTCTAGGAGAAGGCGAATTTGGAAAAGTGGTCAAGGCAACGGCCTTCCATCTGAAAGGCAGAGCAGGGTACACCACGGTGGCCGTGAAGATGCTGAAAGAGAACGCCTCCCCGAGTGAGCTGCGAGACCTGCTGTCAGAGTTCAACGTCCTGAAGCAGGTCAACCACCCACATGTCATCAAATTGTATGGGGCCTGCAGCCAGGATGGCCCGCTCCTCCTCATCGTGGAGTACGCCAAATACGGCTCCCTGCGGGGCTTCCTCCGCGAGAGCCGCAAAGTGGGGCCTGGCTACCTGGGCAGTGGAGGCAGCCGCAACTCCAGCTCCCTGGACCACCCGGATGAGCGGGCCC | 400 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 4 | 0.8 | 400 | 1327.72589688 | 1096.43026325 | 155.679753403 | 328.039480385 | 25 | 400 | 869.582622581 | 2.22399647719 | 0 | 1.11199823859 | 0 | 400 | 0 | 648.294973101 | 0 | 0 | 0 | 400 | 1194 | 986 | 140 | 295 | 25 | 400 | 782 | 2 | 0 | 1 | 0 | 400 | 0 | 583 | 0 | 0 | 0 | 0.762 | positive |
  

#### Column description

Overview of all features/columns annotated by EasyFuse:

- **BPID:** The BPID (breakpoint ID) is an identifier composed of `chr1:position1:strand1_chr2:position2:strand2` and is used as the main identifier of fusion breakpoints throughout the EasyFuse publication. In the BPID, `chr` and `position` are 1-based genomic coordinates (GRCh38 reference) of the two breakpoint positions.
- **context_sequence_id:** The context sequence id is a unique identifier (hash value) calculated from `context_sequence`, the fusion transcript sequence context (400 upstream and 400 bp downstream from the breakpoint position).  
- **FTID:** The FTID is a unique identifier composed of `GeneName1_chr1:position1:strand1_transcript1_GeneName2_chr2:position2:strand2_transcript2`. All transcript combinations are considered.
- **Fusion_Gene:** Fusion Gene is a combination of the gene symbols of the involved genes in the form: `GeneName1_GeneName2`.  
- **Breakpoint1:** Breakpoint1 is a combination of the first breakpoint position in the form: `chr1:position1:strand1`.  
- **Breakpoint2:** Breakpoint2 is a combination of the second breakpoint position in the form: `chr2:position2:strand2`.
- **context_sequence_100_id:** The context sequence 100 id is a unique identifier (hash value) calculated from 200 bp context sequence (100 upstream and 100 bp downstream from the breakpoint).   
**type:** EasyFuse identifies six different types of fusion genes. The type describes the configuration of the involved genes to each other with respect to location on chromosomes and transcriptional strands:  
  - `cis_near`:  Genes on the same chromosome, same strand, order of genes matches reading direction, genomic distance < 1Mb (read-through likely)
  - `cis_far`: Genes on the same chromosome, same strand, order of genes matches reading direction, genomic distance  >= 1Mb 
  - `cis_trans`:  Genes on the same chromosome, same strand, but the order of genes does not match the reading direction
  - `cis_inv`:  Genes on the same chromosome but on different strands
  - `trans`:  Genes on different chromosomes, same strand
  - `trans_inv`:  Genes on different chromosomes, different strands

- **exon_nr:** Number of exons involved in the fusion transcript  
- **exon_starts:** Genomic starting positions of involved exons
- **exon_ends:** Genomic end positions of involved exons  
- **exon_boundary1:** Exon boundary of the breakpoint in Gene1 
    - `left_boundary` is 5' in strand orientation
    - `right_boundary` is 3' in strand orientation
    - `within` means breakpoint is inside exon)  
- **exon_boundary2:** Exon boundary of the breakpoint in Gene2 (`left_boundary` is 5' in strand orientation, `right_boundary` is 3' in strand orientation, `within` means breakpoint is inside exon)  
- **exon_boundary:** describes which of the partner genes (gene 1 + gene 2) has their breakpoint on an exon boundary:
  -  `both`:  `left_boundary`  + `right_boundary` 
  - `5prime`: `left_boundary` + `within` 
  -  `3prime`: `within` + `right_boundary`
  - `no_match`: `within` + `within`  

- **bp1_frame:** Reading frame of translated peptide at breakpoint for fusion transcript1 (-1 is non-coding region/no frame; 0,1,2 is coding region with indicated offset for reading frame)  
- **bp2_frame:** Reading frame of translated peptide at breakpoint for fusion transcript2 (-1 is none-coding region/no frame; 0,1,2 is coding region with indicated offset for reading frame)  
- **frame:** Type of frame for translation of fusion gene: 
  - `in_frame`: translation of wild type peptide sequences without frameshift after breakpoint (both coding frames are equal, `bp1_frame` == `bp2_frame` != `-1`)
  - `neo_frame`:  translation of none-coding region after breakpoint leads to novel peptide sequence (`bp1_frame` is 0, 1, or 2 and `bp2_frame` is -1) 
  - `no_frame`: no translation (`bp1_frame` is -1)
  - `out_frame`: out of frame translation after breakpoints leads to novel peptide sequence (`bp1_frame` != `bp2_frame` != -1)
- **context_sequence:** The fusion transcript sequence downstream and upstream from the breakpoint (default 800 bp, shorter if transcript start or end occurs within the region)  
- **context_sequence_bp:** Position of breakpoint in context sequence  
- **neo_peptide_sequence:** Translated peptide sequence of context sequence starting at 13 aa before breakpoint until 13 aa after breakpoint (for in-frame transcripts) or until next stop codon (for out frame and neo frame). This is to consider only the region around the breakpoint that may contain neo-epitopes.
- **neo_peptide_sequence_bp:** Breakpoint on translated peptide sequence.  
- ***toolname*_detected:** 1 if breakpoint was detected by respective tool, 0 if not (*toolname* is one of `fusioncatcher`, `starfusion`, `infusion`, `mapsplice` or `soapfuse`)
- ***toolname*_junc:** Junction read count (reads covering breakpoint) reported by *toolname*  
- ***toolname*_span:** Spanning read count (read pairs with each partner on one side of breakpoint) reported by *toolname*  
- **tool_count:** Number of tools detecting fusion gene breakpoint  
- **tool_frac:** Fraction of tools out of 5  
- ***category*_bp_best:** Location of breakpoint on context sequence (400 for an 800 bp context sequence). Whereby *category* describes (here and in the following columns) the reference sequence to which the reads were mapped and quantified: 
  - `ft`: context_sequence of fusion transcript 
  - `wt1`: corresponding sequence of fusion partner 1 (wild type 1)
  - `wt2`: corresponding sequence of fusion partner 2 (wild type 2)
- ***category*_a_best:**  Fraction of read counts from 1 million reads that map to either context sequence (ft) or wildtype sequence (wt1 or wt2) 400 left of breakpoint  
- ***category*_b_best:**  Fraction of read counts from 1 million reads that map to either context sequence (ft) or wildtype sequence (wt1 or wt2) 400 left of breakpoint  
- ***category*_junc_best:** Fraction of read counts from 1 million reads that map to sequence and overlap breakpoint by at least 10 bp  
- ***category*_span_best:** Fraction of read pairs from 1 million sequenced read pairs, that map to both sides of breakpoint position  
- ***category*_anch_best:** Maximal read anchor size across all junction reads, where the anchor size for a given read is defined as the minimum distance between read start and breakpoint or read end and the breakpoint.  
- ***category*_bp_cnt_best:** Location of breakpoint on context sequence (400 for an 800 bp context sequence)  
- ***category*_a_cnt_best:** Number of reads, that map to either context sequence (ft) or wildtype sequence (wt1 or wt2) left of breakpoint  
- ***category*_b_cnt_best:** Number of reads, that map to either context sequence (ft) or wildtype sequence (wt1 or wt2) 400 right of breakpoint  
- ***category*_junc_cnt_best:** Number of reads that map to sequence and overlap breakpoint by at least 10 bp  
- ***category*_span_cnt_best:** Number of read pairs, that map to both sides of breakpoint position  
- ***category*_anch_cnt_best:** Maximal read anchor size across all junction reads, where the anchor size for a given read is defined as the minimum distance between read start and breakpoint or read end and the breakpoint.  

- **prediction_prob:** The predicted probability according to the machine learning model that the fusion candidate is a true positive. 
- **prediction_class:** The predicted class (`negative` or `positive`) according to the machine learning model. This classification relies on a user-defined threshold (default 0.5) applied to the `precition_prob` column. 


## Citation

If you use EasyFuse, please cite:  [Weber D, Ibn-Salem J, Sorn P, et al. Nat Biotechnol. 2022](https://doi.org/10.1038/s41587-022-01247-9)
## NextFlow

### Background
NextFlow is a workflow engine that allows to define and run workflows with multiple steps using a Domain Specific Language (DSL). It provides an expressive syntax that allows to parallelize tasks, scale out by parallelization across multiple machines seamlessly, define computational requirements with a high granularity, audit workflow performance, virtualise hardware and software requirements and improve reproducibility across different users and environments. Nextflow integrates with several queue batch processors and in particular with slurm. 
Installation and setup
NextFlow runs as a process for each workflow being executed, there is no need to setup any service.
Download the binary as follows:

```
curl -s https://get.nextflow.io | bash
```

You will need java >= 8. The default java in tronland is OpenJDK Java 8, but OpenJDK sometimes cause difficult to spot issues, I would recommend using the Oracle Java 11 that can be loaded as follows:
```
module load java/11.0.1
```
There is a common configuration file where you can define among other things the queue batch processor to use or the default amount of memory for each task.The configuration file has to be located at $HOME/.nextflow/config
To run nextflow in Tronland it is enough with the following configuration:
```
process.executor = 'slurm'

#this is optional

process.memory = '4G'
```
You can also use different profiles to switch between settings more easily. Below the standard profile is used by default, but if indicated with "-profile test" it will use the test profile and not run in slurm.
```
profiles {
    standard {
        process{
            executor = 'slurm'
            memory = '4G'
            clusterOptions = '-x tronc6,tronc7'
            errorStrategy = "finish"
        }
    }
    test {
        process.executor = 'local'
    }
}
```
It may be important not to use all nodes in the cluster to avoid saturating the cluster with something like "clusterOptions = '-x tronc6,tronc7'".

Also, when running multiple lengthy jobs in parallel and the first fails you may want that the rest to finish in order to cache their results; this can be achieved using "errorStrategy=finish".

It may be handy to setup nextflow so intermediate files are always deleted after execution. The ability to cache certain steps in a pipeline will be lost of course and you have to ensure that you use the directive "publishDir" with mode "copy" or "move" so your final outputs are not removed. Add this to your config file if you understand the implications.
```
cleanup = true
```
For further details on configuration see https://www.nextflow.io/docs/latest/config.html#configuration-file

## Sample workflow
The following is a sample process that receives a BAM file and a name prefix as input and outputs the deduplicated BAM with its index and a file of duplicate reads.
```
process picardDeduplication {
    module 'java/11.0.1'
    memory '16g'
    cpus 1
    publishDir "${output_folder}", mode: "copy"
    tag "${name}"
 
    input:
      file bam
      val name
 
    output:
      file "${name}.dedup.bam" into dedup_bam
      file "${name}.dedup.bai" into dedup_bai
      file "${name}.dedup.bam.bai" into dedup_bam_bai
      file "${name}.duplicates.txt" into duplicates
 
    """
    java -Djava.io.tmpdir=${scratch_folder} -Xmx${task.memory} -jar ${picard_jar} MarkDuplicates I=${bam} M=${name}.duplicates.txt O=${name}.dedup.bam VALIDATION_STRINGENCY=LENIENT ASSUME_SORTED=true CREATE_INDEX=true
    cp '${name}.dedup.bai' '${name}.dedup.bam.bai'
    """
}
```
NOTE: the publishDir defines where your outputs will be linked from, copied or moved  as defined. For large jobs it is important to define with care to avoid the later need to pick results from the miriad of working subfolders.

NOTE 2: the tag is relevant to define to identify individual jobs. For instance, if one given sample takes triple the time that all other samples for the same job you may want to identify which sample caused that. You can set as many tags as you wish.



This process has to be used in a workflow, as follows:
```
#!/usr/bin/env nextflow
 
/**
This workflow is a simple one step workflow that runs Picard's deduplication
*/
 
picard_jar = "/code/picard/2.18.17/picard.jar"
scratch_folder = "/scratch/info/projects/my_dummy_project"
 
params.name = false
params.bam = false
 
process picardDeduplication {
 
    [...]
 
}
```
We can include multiple processes in a workflow and if they do not depend on each, as in the example below, they will be parallelized:

```
#!/usr/bin/env nextflow
 
/**
This workflow is a simple one step workflow that runs Picard's deduplication
*/
 
picard_jar = "/code/picard/2.18.17/picard.jar"
scratch_folder = "/scratch/info/projects/my_dummy_project"
 
params.name = false
params.bam = false
 
process picardDeduplication {
 
    [...]
 
}
 
process snvVariantCalling {
    input:
        file "${name}.dedup.bam" into dedup_bam
        file "${name}.dedup.bai" into dedup_bai
 
    [...]
}
 
process indelVariantCalling {
    input:
        file "${name}.dedup.bam" into dedup_bam
        file "${name}.dedup.bai" into dedup_bai
 
    [...]
}
```

## Run a workflow
To run a workflow we just need to call the binary nextflow with the workflow file and the required parameters, such as:

```
nextflow my_picard_dedup_workflow.nf --bam my_input.bam --name my_deduplicated
```
This will trigger the orchestrator nextflow process which will send jobs to slurm for execution. The nextflow process needs to be running for the whole duration of the workflow execution. For this reason it is recommended to run the nextflow process inside a screen, a tmux or run it as job in the queue. The computational requirements for the nextflow process are minimal, run it as follows:

```
sbatch -N 1 --mem=1G -n 1 --job-name NF run_sample.sh
```

where in run_sample.sh you have:
```
#!/bin/bash
 
module load java/11.0.1
nextflow my_picard_dedup_workflow.nf --bam my_input.bam --name my_deduplicated
```
NOTE: the folder where you run nextflow will be where intermediate files and logs will be stored and depending on your workflow read and write operations will happen in this folder. It is recommended to use folders under /flash to profit from SSD read/write speed. This can be combined with a "publishDir" value in /scratch/info so you make sure that your results are stored in a more persistent file system.

WARNING: any module loaded in the session where nextflow is triggered from will be inherited by all processes potentially causing conflicts, ensure to run "module purge" before executing any nextflow workflow

Rexecuting workflows
Did your workflow failed in the third step after 10 hours of execution? No worries, just rerun it using the flag "-resume". Make sure that you don't mess with the intermediate files stored by nextflow, any modification will avoid you from rexecuting.

See the details here https://www.nextflow.io/blog/2019/demystifying-nextflow-resume.html

