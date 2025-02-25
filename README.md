[![Build Status](https://travis-ci.org/tseemann/VelvetOptimiser.svg?branch=master)](https://travis-ci.org/tseemann/VelvetOptimiser) [![License: GPL v2](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)

# VelvetOptimiser: automate your Velvet assemblies

<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Introduction](#introduction)
- [Installation](#installation)
	- [Dependencies](#dependencies)
	- [Brew](#brew)
	- [Conda](#conda)
	- [Manual](#manual)
- [Version](#version)
- [Command Line](#command-line)
- [Examples](#examples)
- [Detailed Options](#detailed-options)
	- [``` -h or --help```](#-h-or---help)
	- [```-V or --version```](#-v-or---version)
	- [```-v or --verbose```](#-v-or---verbose)
	- [```-s or --hashs```](#-s-or---hashs)
	- [```-e or --hashe```](#-e-or---hashe)
	- [`-x or --step`](#-x-or---step)
	- [`-f or --velvethfiles`](#-f-or---velvethfiles)
	- [`-a or --amosfile`](#-a-or---amosfile)
	- [`-o or --velvetgoptions`](#-o-or---velvetgoptions)
	- [`-t or --threads`](#-t-or---threads)
	- [`-g or --genomesize`](#-g-or---genomesize)
	- [`-k or --optFuncKmer`](#-k-or---optfunckmer)
	- [`-c or --optFuncCov`](#-c-or---optfunccov)
	- [`-p or --prefix`](#-p-or---prefix)
	- [`-d or --dir_final`](#-d-or---dirfinal)
	- [`-z or --upperCovCutoff`](#-z-or---uppercovcutoff)
- [Bugs](#bugs)
- [To do](#to-do)
- [Contact](#contact)
- [License](#license)
- [References](#references)

<!-- /TOC -->

## Introduction

The VelvetOptimiser is designed to run as a wrapper script for the Velvet
assembler ([Zerbino and Birney 2008](#zerbino2008)) and to assist with optimising the
assembly.  It searches a supplied hash value range for the optimum,
estimates the expected coverage and then searches for the optimum coverage
cutoff.  It uses Velvet's internal mechanism for estimating insert lengths
for paired end libraries.  It can optimise the assemblies by either the
default optimisation condition or by a user supplied one.  It outputs the
results to a subdirectory and records all its operations in a logfile.

Expected coverage is estimated using the length weighted mode of the contig
coverage in all active short columns of the `stats.txt` file.

## Installation

### Dependencies

*   Velvet => 1.1
*   Perl => 5.8.8
*   BioPerl >= 1.4
*   GNU utilities: grep sed free cut

### Brew

```
brew install homebrew/science/velvetoptimiser
```

### Conda

```
conda install -c bioconda perl-velvetoptimiser
```

### Manual

1.  Download the tarball
2.  Copy it to the folder where you want to unpack it (it comes in its own folder)
3.  Add it to the path

```
# Step 1
wget -O velvetoptimiser_2.2.6.tar.gz https://github.com/tseemann/VelvetOptimiser/archive/refs/tags/2.2.6.tar.gz

# Step 2
mkdir $HOME/src && mv velvetoptimiser_2.2.6.tar.gz $HOME/src && cd $HOME/src && tar zxvf velvetoptimiser_2.2.6.tar.gz

# Step 3
echo "export PATH=$PATH:$HOME/src/velvetoptimiser_2.2.6"
```

### Testing the installation

You can test it with:

```
VelvetOptimiser.pl --help
```

or for a more comprehensive test:

```
cd $HOME/src/VelvetOptimiser/test
./run_test.sh
```

<!-- ADD SOME INFORMATION HOW HOW TO INSTALL -->

## Version

Version 2.2.6

## Command Line

```
VelvetOptimiser.pl \[options\] -f 'velveth input line'
```

```
  Options:

  --help          	This help.
  --V|version!		Print version to stdout and exit.
  --v|verbose+    	Verbose logging, includes all velvet output in the logfile. (default '0').
  --s|hashs=i     	The starting (lower) hash value (default '19').
  --e|hashe=i     	The end (higher) hash value (default '31').
  --x|step=i      	The step in hash search..  min 2, no odd numbers (default '2').
  --f|velvethfiles=s The file section of the velveth command line. (default '0').
  --a|amosfile!   	Turn on velvet's read tracking and amos file output. (default '0').
  --o|velvetgoptions=s Extra velvetg options to pass through.  eg. -long_mult_cutoff -max_coverage etc (default '').
  --t|threads=i   	The maximum number of simulataneous velvet instances to run. (default '4').
  --g|genomesize=f 	The approximate size of the genome to be assembled in megabases.
					Only used in memory use estimation. If not specified, memory use estimation
					will not occur. If memory use is estimated, the results are shown and then program exits. (default '0').
  --k|optFuncKmer=s The optimisation function used for k-mer choice. (default 'n50').
  --c|optFuncCov=s 	The optimisation function used for cov_cutoff optimisation. (default 'Lbp').
  --p|prefix=s    	The prefix for the output filenames, the default is the date and time in the format DD-MM-YYYY-HH-MM_. (default 'auto').
  --d|dir_final=s The name of the directory to put the final output into. (default '.')
  --z|upperCovCutoff=f The maximum coverage cutoff to consider as a multiplier of the expected coverage. (default '0.8').

```

**Advanced!**: VelvetOptimiser accepts custom optimisation functions. See [here](#-c-or---optfunccov).


## Examples

Find the best assembly for a lane of Illumina single-end reads, trying k-values between 53 and 75:

```
% VelvetOptimiser.pl -s 53 -e 75 -f '-short -fastq s_1_sequence.txt'
```

Print an estimate of how much RAM is needed by the above command, if we use eight threads at once,
and we estimate our assembled genome to be 4.5 megabases long:

```
% VelvetOptimiser.pl -s 53 -e 75 -f '-short -fastq s_1_sequence.txt' -g 4.5 -t 8
```

Find the best assembly for Illumina paired end reads just for k=31, using four threads (eg. quad core CPU),
but optimizing for N50 for k-mer length rather than sum of large contig sizes:

```
% VelvetOptimiser.pl -s 31 -e 31 -f '-shortPaired -fasta interleaved.fasta' -t 4 --optFuncKmer 'n50'
```

## Detailed Options

### ``` -h or --help```

Prints the commandline help to STDOUT.

### ```-V or --version```

Prints the program name and version to STDOUT.

**Note**  that other information is still
printed to STDERR. You can ignore this by redirecting the output like this:

  &nbsp;&nbsp;&nbsp;&nbsp;```% VelvetOptimiser.pl --version 2> /dev/null```

### ```-v or --verbose```

Adds the full `velveth` and `velvet`g output to the `logfile`. (Handy for looking at the insert lengths and sds that Velvet has chosen for each library.)

### ```-s or --hashs```

Parameter type required: odd integer > 0 & <= the `MAXKMERLENGTH` velvet was compiled with.

*   Default: 19

This is the lower end of the hash value range that the optimiser will search for the optimum.

If the supplied value is even, it will be lowered by 1.

If the supplied value is higher than `MAXKMERLENGTH`, it will be dropped to `MAXKMERLENGTH`.

### ```-e or --hashe```

Parameter type required: odd integer >= `hashs` & <= the `MAXKMERLENGTH` velvet was compiled with.

*   Default: `MAXKMERLENGTH`

This is the upper end of the hash value range that the optimiser will search for the optimum.

If the supplied value is even, it will be lowered by 1.

If the supplied value is higher than `MAXKMERLENGTH`, it will be dropped to `MAXKMERLENGTH`.

If the supplied value is lower than `hashs` then it will be set to equal `hashs`.

### `-x or --step`

Parameter type required: even integer < difference between `hashs` and `hashe`.

*   Default: 2

This parameter details the number of hash values to skip each increment from `hashs` to `hashe` when searching for the optimum.

If the supplied value is odd, it will be lowered by 1.

If the supplied value is less than 2, it will be set to 2.

If the supplied value is greater than the `hashs` to `hashe` range, it will be set to the range.

### `-f or --velvethfiles`

Parameter type required: string with '' or ""

*   No default.

This is a required parameter.  If this option is not specified, then the optimisers usage will be displayed.

You need to supply everything you would normally supply `velveth` at this point except for the hash size and the directory name in the following format.  

{\[-file_format\]\[-read_type\] filename} repeated for as many read files as you have.


File format options:
-   fasta
-   fastq
-   fasta.gz
-   fastq.gz
-   bam
-   sam
-   eland
-   gerald

Read type options:
-   short
-   shortPaired
-   short2
-   shortPaired2
-   long
-   longPaired

Examples:

*   `-f 'reads.fna'`
    -   `reads.fna` is short not paired and fasta. (these are the defaults: `-short` and `-fasta`)

*   `-f '-shortPaired -fastq paired_reads.fastq -long long_reads.fna'`
    -   Two read files supplied, first one is a paired end fastq file and the second is a long single ended read file.

*   `-f '-shortPaired paired_reads_1.fna -shortPaired2 paired_reads_2.fna'`
    -   Two read files supplied, both are short paired fastas but come from two different libraries, therefore needing two different CATEGORIES.
	
*	`-f '-shortPaired -fastq.gz -separate reads_R1.fastq.gz reads_R2.fastq.gz'`
	-	Separate files for forward and reverse reads of a paired end library are specified with the `-separate` tag. 

There is a fairly extensive checker built into the optimiser to check if the format of the string is correct.  However, it won't check the read files for their format (`fasta`, `fastq`, `eland` etc.)

### `-a or --amosfile`

Turns on Velvet\'s read tracking and `amos` file output.

This option is the same as specifying `-amos_file yes -read_trkg yes` in velvetg.  However, it will only be added to the final assembly and not to the intermediate ones.

### `-o or --velvetgoptions`

Parameter type required: `string`.

*   No default

String should contain extra options to be passed to `velvetg` as required such as `-long_mult_cutoff 1` or `-max_coverage 50` etc.  Warning, there is no sanity check, so be careful.  The optimiser will crash if you give velvetg something it doesn't handle.

### `-t or --threads`

Parameter type required: `integer`

*   Default: Number of CPUs in the current computer.

Specifies the maximum number of threads (simultaneous Velvet instances) to run.

### `-g or --genomesize`

Parameter type required: `float`.

*   No default.

This option will run the Optimiser's memory estimator.  It will estimate the memory required to run Velvet with the current -f parameter and number of threads.  Once the estimator has finished its calculations, it will display the required memory, make a recommendation and then exit the script.  This is useful for determining if you will have sufficient free RAM to run the assembly before you start.

You need to supply the approximate size of the genome you are assembling in mega bases.  For example, for a Salmonella genome I would use: -g 5

### `-k or --optFuncKmer`

Parameter type required: `string`.

*   Default: `n50`

This option will change the function that the Optimiser uses to find the best hash value from the given range.  The default is to use the `n50`.  I have found this function to work for me better than the previous single optimisation function, but you may wish to change it.  A list of possible variables to use in your optimisation function and some examples are shown below.

### `-c or --optFuncCov`

Parameter type required: `string`.

*   Default: `Lbp`

This option will change the function that the Optimiser uses to find the best hash value from the given range.  The default is to use the number of basepairs in contigs greater than 1 kilobase in length.  I have found this function to work for me but you may wish to change it.  A list of possible variables to use in your optimisation function and some examples are shown below.

Velvet optimiser assembly optimisation functions can be built from the following variables:

*   `LNbp` = The total number of Ns in large contigs
*   `Lbp` = The total number of base pairs in large contigs
*   `Lcon` = The number of large contigs
*   `max` = The length of the longest contig
*   `n50` = The `n50`
*   `ncon` = The total number of contigs
*   `tbp` = The total number of basepairs in contigs

Examples are:

*   `Lbp` = Just the total basepairs in contigs longer than 1kb
*   `n50*Lcon` = The n50 times the number of long contigs.
*   `n50*Lcon/tbp+log(Lbp)` = The n50 times the number of long contigs divided by the total bases in all contigs plus the log of the number of bases in long contigs.

**Be warned!** The optimiser doesn't care what you supply in this string and will attempt to run anyway.  If you give it a nonsensical optimisation function be prepared to receive a nonsensical assembly!

### `-p or --prefix`

Parameter type required: `string`

Default: The current date and time in the format `DD-MM-YYYY-HH-MM-SS_`

Names the `logfile` and the output directory with whatever prefix is supplied followed by `_logfile.txt` for the `logfile` and `_data_k` where `k` is the optimum hash value for the output data directory.

### `-d or --dir_final`

Parameter type required: `string`

Default: `.` (the current directory)

At the completion of the optimiser, any non default string will cause the final velvet output and the Velvet Optimiser `logfile` to be moved to the directory specified. If the directory already exists, an error is generated and the optimiser stops.

### `-z or --upperCovCutoff`

Parameter type required: `float`

Default: 0.8

Uses this fraction of the expected coverage to set the upper limit of the coverage cutoff range to search for the optimum in.

## Bugs

*   Still none that I am aware of.

## To do

*   Make the auto_XXX folders be in `--dir_final` when set, not in the current directory.
*   Use velvetk.pl script to choose suitable -s and -e parameters.

## Contact
Simon Gladman <mailto:simon.gladman@unimelb.edu.au>

Torsten Seemann <mailto:torsten.seemann@unimelb.edu.au>


## License

Copyright 2008- Simon Gladman & Torsten Seemann

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston,
MA 02110-1301, USA.

## References

[](#zerbino208)Zerbino and Birney (2008). Velvet: algorithms for de novo short read assembly using de Bruijn graphs. **Genome Research**. 18(5):821-9. doi: doi:[10.1101/gr.074492.107](http://www.genome.org/cgi/doi/10.1101/gr.074492.107)
