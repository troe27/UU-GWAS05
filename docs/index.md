# Linkage analysis and GWAS - Lab 5

In the following practice, we will use CRI-MAP to do the basic linkage analysis and create a simple linkage map.  
Linkage mapping is an older technique that has been mostly superceded by GWA-type Studies, but can be extremely powerful for mendelian diseases, in populations that is optimised for it or where other constraints limit more established methods.


## Server
Due to possible maintenance issues, we will not be using Rackham today. Instead, we will use a cloud computing platform.
We have spun up an Ubuntu instance that you can log-in  using a Web-interface.
go to [https://IP_ADRESS](https://IP_ADRESS) and log in there.

Here are your User-names and passwords:

| Name  | Password
|---|---|
|  Asmaa |  Tomaru |
| Julia  | BlackMinorca  |
|  Noah |  WhiteLeghorn |
| Emma  |  BarredPlymouthRock |
| Veera  |  SkanskBlommehoena |
| Alice  | Sebright  |
| Stella  |  RhodeIslandRed |
| Nina  | LightBrahma  |
|  Rebecka |  Araucana |
| Diana  |  Orpington |
|  Kajanthan | NorskeJaerhoens  |
|  Basel | Barnevelder  |
|  Philippine |  Kraienkopp |


## Compiling from Source

The Software we are using today is very old (from the 80s!), so we are going to be old-fashioned as well. We are going to compile it from source!

#### An excerpt from the Manual:
>This version of CRI-MAP is distributed as **source code** in **the language C**,
on a 360K DOS formatted diskette. To use it, you will need to obtain
access to a C compiler for your computer, and **compile the program**
yourself (see the section GETTING STARTED, below).  The present version
of the program adheres fairly rigidly to the conventions of "standard C"
as described in Kernighan and Ritchie (1978) (the main exception
being that some variable and function names have more than 8 significant
characters), and should be compatible with most C compilers.  The program
was developed and tested using vers. 2.0 and 2.3 of the VAX C compiler,
on a Microvax II with 5 Mb memory under the MicroVMS operating system.
It has been successfully ported to a number of other computers,
including other VAXes, SUN and Apollo workstations, and the Mac II.

#### obtain and extract the software
The Software is located at
```/path/to/software.tar.gz``` and is in the form of a [gzip](https://en.wikipedia.org/wiki/Gzip)-compressed [tarball](https://en.wikipedia.org/wiki/Tar_(computing)). copy it to your home-directory and extract it:

```bash
tar -xvf software.tar.gz
```

#### Compile
You can have a look at the files within the resulting folder: they contain pieces of the software and instructions on how to assemble it. you do not need to know what they mean or how they work, since we will use [make](https://en.wikipedia.org/wiki/Make_(software)) an install automation software, to instruct the compilers:
```bash
# change into the created folder:
cd /path/to/folder
# install
make
```
You will now see a large running wall of text full of warnings: this is the compiler reporting what it is doing. unless it says ```ERROR```, you can safely ignore most of it. If everything went well, you should now be able to find the compiled software in the folder.

Compilers are software that translate written code (the "source code", or "source") into machine readable software. In this case, the software is written in [```C```](https://en.wikipedia.org/wiki/C_(programming_language)) , Which is one of the most widely used languages that is traditionally compiled instead of interpreted ( like bash, R or Python).

![explanatory figure from https://hpc-wiki.info/mediawiki/hpc_images/8/8a/Compiler_Shematic.png](figures/Compiler_Shematic.png)

#### Test the software
 test the software by invoking its help-message:

 ```bash
/path/to/software -h
 ```


you should also use the resulting help-message to familiarise yourself with the usage and option list of crimap!

#### Input files
compiling the software should have also resulted in some example files being created: in the same folder, there should be a file named ```chr7a.gen```. Copy it to your working directory.

Cri-map is from a time before sequencing, and designed to work with very sparse and varied genetic data: allozymes, restriction assay data, etc.
this means that the genetic data is not as standardised as you are used to. Also, these standards did not exist at the time the software was created. Therefore, Cri-map has its own input format. Crimap requires multiple inputs. The required files are a ```.gen``` , a ```.loc```, a ```.dat``` and a ```.par``` file,  the most important of which is the ```.gen``` file, which contains all the genotyping and pedigree information, and should be formatted like this:

```
{# of families}  
{# of loci}   
{locus name1} {locus name2} . . .  ## (each name consisting of at most 15 characters)  

## For each family:  

{family ID} (consisting of an arbitrary character string)  
{# of members}  

## For each member in the family:  

{ID #} {mother's ID #} {father's ID #} {sex: 0 if female, 1 if male,
                                             3 if unknown}
{locus1 allele1}{locus1 allele2}{locus2 allele1}{locus2 allele2} . . .
```


#### have a look at the data from chr7a.gen:
```bash
31 17    #31 families, 17 loci

# names of the loci
'LOCX  
'D7COL-HAP-A  
'D7MET-HAP-A  
'D7S8-M-A   
'D7TCRB-BG-A  
'L281-HAP-A  
'L0281-T-A  
'L0544-M-A  
'L0751-M-A  
'L0887-M-A  
'L0917-HAP-A  
'L0966-R-A  
'L1020-T-A  
'L1020-HAP-A  
'L1033-M-A  
'R0012-M-A  
'R0040-M2-A  

12                      # family 12
13                      # 13 members
1 0 0 0                 # ID, Mother's ID, Fathers ID, SEX
# Alleles for the 17 loci:
4 6 2 2 20 20 0 0 2 2 21 41 0 0 1 2 0 0 3 3 0 0 2 5 30 10 30 10 2 2 0 0 0 0
```

### prepare the additional files for building a genetic map

the ```.par```, the ```.loc``` and the ```.dat``` file can be created automatically, by using the ```prepare``` option in crimap.
the ```.loc``` and ```.dat``` decribe the same information as the ```.gen``` file, while the ```.par``` file is a parameter file that contains the options and instructions for cri-map. This is another way of providing instructions to  software. Can you think why this might be advantageous?

```bash
/path/to/crimap 7a prepare
```

- create the additional files using the prepare command. this starts an interactive process:  
  - _Do you wish to change any of these values?_ -> **NO**  
  - _Do you wish to enter any new haplotyped systems?_ -> **NO**  
  - _Do you wish to hold any additional recombination fractions fixed?_ -> **NO**
  - Choose option **1** - 'build'.
  - _Do you wish to compute LOD tables for ALL pairs of loci?_ -> **YES**
  - _Do you wish to build the map incorporating ALL loci in decreasing order of their informativeness?_ -> **YES**  
  -  _OK to set up new parameter file?_ -> **YES**


### create a linkage map
The ```build``` option evaluates the likelihood of possible maps (also called "locus orders") using the most informative data first. Loci are added in their order of informativeness, i.e., in their ranking of number of informative meioses. Further, the addition of loci is a two step process. In the first step, the more efficient data from phase known meioses are used to evaluate possible maps; in the second, phase unknown information is used.

You can now build the linkage map and save the content in a file:
```bash
/path/to/crimap 7a build > filename.txt
```
**NB**:
- Subsequent runs might slightly alter the results, as Cri-map uses the information from previous runs ( as stored in the ```.ord``` file as priors)
- if your initial genetic map does not have more than two markers, consider changing the two markers used to initialise the map (the "ordered_markers" ) to two others by editing the ```.par```  file.

- Have a look at the output.
- Do you remember the distance-measure of linkage maps, [cM](https://en.wikipedia.org/wiki/Centimorgan)?
- Why does it not place all markers on the linkage map?


### Evaluate the distance between 2 specific markers
- using the ```fixed``` command, evaluate the distance between the markers L281-HAP-A and L0281-T-A, as well as between  L281-HAP-A and L0966-R-A.
- you will need to use the ```prepare``` option again to rewrite the parameter-file. chose the ```fixed``` option and input the markers you want to compare.
- look at the centiMorgan distance between these two pairs: what does the distance mean, biologically?


### Optional: improving the linkage map

Even if we have created a functional linkage map, it is possible to expand and improve on it. part of this is in picking the right two starting markers. these are often markers with a reasonable recombination fraction, e.g. that are quite distant to each other, but still that we are also quite confident in ( high LODscores). you can get the recombination fractions and LOD scores for each combination using the ```twopoint``` option.
Choosing loci that are far apart for the inital map has the advantage that the build run may "reach" more distant loci on either side of the map. However, there may be more errors in the relative positions of loci mapped both between the two inital loci and on either side. For this reason, such build runs are best followed by ```flips``` runs, which can be used to evaluate the relative improvement of "flipping" loci around.
if you get an improvement on the map from your successive runs, you can use them as ordered_markers with successive individual markers to add them to the linkage map with subsequent ```build```-runs.
