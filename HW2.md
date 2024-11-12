1. Построение MuMMer plot
создание окружения и установка:
``` bash
conda create -n mummer
conda activate mummer
conda install bioconda::mummer
conda install conda-forge::gnuplot
conda install conda-forge::xclip
```
выравнивание 4й хромосомы на референс и построение графика ddplot

```bash 
nucmer  chr4_ref.fasta  chr4_sample.fasta 
mummerplot --png --filter out.delta
```
2. Построение с помощью MCScan 
```
conda install bioconda::agat
conda install jcvi -c conda-forge -c bioconda
conda install bioconda::last
apt-get install -y texlive texlive-latex-extra
apt-get install -y cups

```
Создание промежуточных файлов:
```
agat_convert_sp_gff2bed.pl --gff chr4_ref.gff3 -o chr4_ref.bed
agat_convert_sp_gff2bed.pl --gff chr4_samp.gff3 -o chr4_samp.bed

agat_sp_extract_sequences.pl -g chr4_ref.gff3 -f chr4_ref.fasta -t cds -o chr4_ref_cds.fasta
agat_sp_extract_sequences.pl -g chr4_ref.gff3 -f chr4_ref.fasta -t tRNA -o chr4_ref_trna.fasta

agat_sp_extract_sequences.pl -g chr4_samp.gff3 -f chr4_samp.fasta -t tRNA -o chr4_samp_trna.fasta
agat_sp_extract_sequences.pl -g chr4_samp.gff3 -f chr4_samp.fasta -t cds -o chr4_samp_cds.fasta

cat chr4_sample_cds.fasta chr4_sample_trna.fasta > chr4_sample.cds
cat chr4_ref_cds.fasta chr4_ref_trna.fasta > chr4_ref.cds

```
```
python -m jcvi.compara.catalog ortholog chr4_ref chr4_samp --no_strip_names
python -m jcvi.compara.synteny screen --minspan=30 --simple chr4_ref.chr4_samp.anchors chr4_ref.chr4_samp.anchors.new
```
Дополнительно вручную надо создать файлы seqids и layout. 
seqids (наименования синвенсов):
LR881469.1
CP002687.1
layout:
 y, xstart, xend, rotation, color, label, va,  bed
.6,     .1,    .8,       0,      , ref, top, chr4_ref.bed
.4,     .1,    .8,       0,      , samp, top, chr4_samp.bed
 edges
e, 0, 1, chr4_ref.chr4_samp.anchors.simple 
```
python -m jcvi.graphics.karyotype seqids layout
```

