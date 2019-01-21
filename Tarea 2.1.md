<h4 id="running-process_radtags"><strong>4.1.3 Running process_radtags</strong></h4>
<p>Here is how single-end data received from an Illumina sequencer might look:</p>
<pre><code>% ls ./raw 
lane3_NoIndex_L003_R1_001.fastq.gz lane3_NoIndex_L003_R1_006.fastq.gz lane3_NoIndex_L003_R1_011.fastq.gz
lane3_NoIndex_L003_R1_002.fastq.gz lane3_NoIndex_L003_R1_007.fastq.gz lane3_NoIndex_L003_R1_012.fastq.gz
lane3_NoIndex_L003_R1_003.fastq.gz lane3_NoIndex_L003_R1_008.fastq.gz lane3_NoIndex_L003_R1_013.fastq.gz
lane3_NoIndex_L003_R1_004.fastq.gz lane3_NoIndex_L003_R1_009.fastq.gz
lane3_NoIndex_L003_R1_005.fastq.gz lane3_NoIndex_L003_R1_010.fastq.gz
</code></pre>
<p>Then you can run process_radtags** in the following way:</p>
<pre><code>% process_radtags -p ./raw/ -o ./samples/ -b ./barcodes/barcodes_lane3 \ 
                  -e sbfI -r -c -q
</code></pre>
<p>I specify the directory containing the input files, <code>./raw</code>, the directory I want process_radtags to enter the output files, <code>./samples</code>, and a file containing my barcodes, <code>./barcodes/barcodes_lane3</code>, along with the restrction enzyme I used and instructions to clean the data and correct barcodes and restriction enzyme cutsites (<code>-r</code>, <code>-c</code>, <code>-q</code>).</p>
<p>Here is a more complex example, using paired-end double-digested data (two restriction enzymes) with combinatorial barcodes, and gzipped input files. Here is what the raw Illumina files may look like:</p>
<pre><code>% ls ./raw
GfddRAD1_005_ATCACG_L007_R1_001.fastq.gz GfddRAD1_005_ATCACG_L007_R2_001.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_002.fastq.gz GfddRAD1_005_ATCACG_L007_R2_002.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_003.fastq.gz GfddRAD1_005_ATCACG_L007_R2_003.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_004.fastq.gz GfddRAD1_005_ATCACG_L007_R2_004.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_005.fastq.gz GfddRAD1_005_ATCACG_L007_R2_005.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_006.fastq.gz GfddRAD1_005_ATCACG_L007_R2_006.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_007.fastq.gz GfddRAD1_005_ATCACG_L007_R2_007.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_008.fastq.gz GfddRAD1_005_ATCACG_L007_R2_008.fastq.gz
GfddRAD1_005_ATCACG_L007_R1_009.fastq.gz GfddRAD1_005_ATCACG_L007_R2_009.fastq.gz
</code></pre>
<p>Now we specify both restriction enzymes using the <code>--renz_1</code> and <code>--renz_2</code> flags along with the type combinatorial barcoding used. Here is the command:</p>
<pre><code>% process_radtags -P -p ./raw -b ./barcodes/barcodes -o ./samples/ \ 
                  -c -q -r --inline_index --renz_1 nlaIII --renz_2 mluCI
</code></pre>
<h4 id="the-output-of-process_radtags">The output of process_radtags</h4>
<p>The output of the process_radtags differs depending if you are processing single-end or paired-end data. In the case of <em>single-end reads</em>, the program will output one file per barcode into the output directory you specify. If the data do not have barcodes, then the file will retain its original name.</p>
<p>If you are processing <em>paired-end reads</em>, then you will get four files per barcode, two for the single-end read and two for the paired-end read. For example, given barcode ACTCG, you would see the following four files:</p>
<pre><code>sample_ACTCG.1.fq
sample_ACTCG.rem.1.fq
sample_ACTCG.2.fq
sample_ACTCG.rem.2.fq
</code></pre>
<p>The process_radtags program wants to keep the reads in <em>phase</em>, so that the first read in the <code>sample_XXX.1.fq</code> file is the mate of the first read in the <code>sample_XXX.2.fq file</code>. Likewise for the second pair of reads being the second record in each of the two files and so on. When one read in a pair is discarded due to low quality or a missing restriction enzyme cut site, the remaining read can’t simply be output to the <code>sample_XXX.1.fq</code> or <code>sample_XXX.2.fq</code> files as it would cause the remaining reads to fall out of phase. Instead, this read is considered a <em>remainder</em> read and is output into the <code>sample_XXX.rem.1.fq</code> file if the paired-end was discarded, or the <code>sample_XXX.rem.2.fq</code> file if the single-end was discarded.</p>
<h4 id="modifying-how-process_radtags-executes">Modifying how process_radtags executes</h4>
<p>The process_radtags program can be modified in several ways. If your data do not have barcodes, omit the barcodes file and the program will not try to demultiplex the data. You can also disable the checking of the restriction enzyme cut site, or modify what types of quality are checked for. So, the program can be modified to only demultiplex and not clean, clean but not demultiplex, or some combination.</p>
<p>There is additional information available in <a href="http://catchenlab.life.illinois.edu/stacks/comp/process_radtags.php">process_radtags manual page</a>.</p>

