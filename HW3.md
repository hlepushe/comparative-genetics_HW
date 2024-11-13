# 1.  создание окружения и установка необходимых тулов

```
conda create -n jupiter_plot -y
conda activate jupiter_plot
conda install bioconda::jupiterplot
conda install bioconda::circos -y
conda install bioconda::circos-tools
conda install bioconda::minimap2
conda install bioconda::samtools
conda install bioconda::bedtools
conda install -c conda-forge perl-gd
```
# 2. построение Jupiner_plot
```
jupiter name=jup_plot_2 ref=halleri_genomic_no_scaff.fna fa=sample.1_ASM2449851v1_genomic.fna t=2
```
# 3.  построение графика GC-состава circos
   индексирование и составление файла с информацией по скаффолдам
```
samtools faidx halleri_genomic_no_scaff.fna
samtools faidx sample.1_ASM2449851v1_genomic.fna
cut -f1,2 sample.1_ASM2449851v1_genomic.fna.fai > scaffolds_info_sample.txt
```
создание файла кариотипа 
```
nano kariotipe.sh
```
```
chr - CP086749.1 CP086749.1 0 29886721 chr1
chr - CP086750.1 CP086750.1 0 20220154 chr2
chr - CP086751.1 CP086751.1 0 22777719 chr3
chr - CP086752.1 CP086752.1 0 19437119 chr4
chr - CP086753.1 CP086753.1 0 27765196 chr5
```
```
chmod +x karyotype.sh
./karyotype.sh
```
```
bedtools makewindows -g scaffolds_info_sample.txt -w 20000 > interval_arab.bed
bedtools nuc -fi sample.1_ASM2449851v1_genomic.fna -bed interval_arab.bed > gc_content.txt
awk '{print $1, $2, $3, $5}' gc_content.txt > gc_for_circos.txt
```
```
nano circos_plot.conf 
```
```
debuggroup = output, summary
debuglevel = 2

# Определение кариотипа генома
karyotype = karyotype.txt

# Настройка изображения
<image>
    # Параметры изображения
    radius           = 1500p             # Размер радиуса в пикселях
    background       = white             # Цвет фона
    file             = /home/card/sravn_gen/arab/gc_content_circos.svg  # Имя выходного файла
    png              = no               # Формат вывода PNG
    svg              = yes                # Отключение формата SVG
</image>

<plots>
    # График GC-содержания в виде линии
    <plot>
        type         = line              # Тип графика: линия
        file         = ./gc_for_circos.txt # Файл с данными для графика
        r1           = 0.8r              # Внутренний радиус графика
        r0           = 0.6r              # Внешний радиус графика
        color        = blue              # Цвет линии графика
        thickness    = 2p                # Толщина линии графика

        <axes>
            show      = yes
            thickness = 1p
            color     = grey
        </axes>

        <backgrounds>
            <background>
                color = vvlgrey
                y1    = 1.0
                y0    = 0
            </background>
        </backgrounds>
    </plot>
</plots>

# Настройка идеограммы (визуальное представление хромосом)
<ideogram>
    <spacing>
        default = 0.01r                  # Промежуток между хромосомами (радиус)
    </spacing>
    thickness       = 50p                # Толщина идеограммы в пикселях
    stroke_thickness = 2p                # Толщина границы
    stroke_color    = black              # Цвет границы
    radius          = 0.85r              # Радиус, на котором размещены идеограммы

    show_label      = yes                # Показать метки хромосом
    label_font      = default            # Шрифт для меток
    label_radius    = 1.1r               # Радиус, на котором будут метки
    label_size      = 24p                # Размер шрифта меток
    label_parallel  = yes                # Параллельная ориентация меток
</ideogram>

# Подключение дополнительных настроек (стили и общие параметры)
<<include etc/colors_fonts_patterns.conf>>
<<include etc/housekeeping.conf>>
```
```
circos -conf circos_plot.conf
```
# 4. построение графика Кимуры 
для получения нужных файов быо использовано окружение, где есть repeatmodeler, repeatmasker 

```
BuildDatabase -name db_chr4 chr4_sample.fasta
RepeatModeler -database db_chr4 -threads 4 -LTRStruct
RepeatMasker -lib /home/card/sravn_gen/arab/chr_4_repeat/consensi.fa.classified -pa 4 -dir RM_output_a /home/card/sravn_gen/arab/chr4_sample.fasta -a
```
далее данные обрабатывались скриптом limura_plot, который лежит в этой дериктории. 
