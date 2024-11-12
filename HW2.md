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
conda install jcvi -c conda-forge -c bioconda
conda install bioconda::last
apt-get install -y texlive texlive-latex-extra
apt-get install -y cups

```
Создание промежуточных файлов:
```
python -m jcvi.compara.catalog ortholog chr4_ref chr4_samp --no_strip_names
python -m jcvi.compara.synteny screen --minspan=30 --simple chr4_ref.chr4_samp.anchors chr4_ref.chr4_samp.anchors.new
```
Дополнительно вручную надо создать файлы seqids и layout. 
seqids (наименования синвенсов):
LR881469.1
CP002687.1
layout:
# y, xstart, xend, rotation, color, label, va,  bed
.6,     .1,    .8,       0,      , ref, top, chr4_ref.bed
.4,     .1,    .8,       0,      , samp, top, chr4_samp.bed
# edges
e, 0, 1, chr4_ref.chr4_samp.anchors.simple
```
python -m jcvi.graphics.karyotype seqids layout
```
