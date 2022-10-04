# ДЗ1 Газизова Анита, 3 группа
< Работа с файлами(удаление, скачивание) была произведена в MobaXterm
1. Создаем ссылки на файлы 
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```
2. Выбираем случайные чтения, пять миллионов для типа pair-end и полтора миллиона для типа mate-pairs с помощью seed=813
```
seqtk sample -s813 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s813 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s813 /oilMP_S4_L001_R1_001.fastq 1500000 > mp1.fastq
seqtk sample -s813 /oilMP_S4_L001_R2_001.fastq 1500000 > m21.fastq
```

3. Используем fastQC для получения анализа чтений
```
mkdir fastqc
ls sub* mp* | xargs -tI{} fastqc -o fastqc {}
```

4. Используем multiQC для создания единого отчета по анализу
```
mkdir multiqc
multiqc -o multiqc fastqc
```
Как результат получен файл .html с полным отчетом(скриншоты приложены)

5. Используем platanus, чтобы обрезать чтения

Для типа pair-end:
```
platanus_trim sub*
```
Для типа mate-pairs:
```
platanus_internal_trim mp*
```

6. Используем fastQC и multiQC для анализа подрезанных чтений по аналогии с пунктами 3, 4
```
mkdir fastqc_trimmed
ls sub* mp*| xargs -tI{} fastqc -o fastqc_trimmed {}
```
```
mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
Как результат получен файл .html с полным отчетом(картинки приложены)

7. С помощью программы “platanus assemble” собираем контиги из подрезанных чтений (согласно документации к platanus тип mate-pairs может приводить к неправильным сборкам)
```
platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
8. С помощью программы “ platanus scaffold” собираем скаффолды из контигов, а также из подрезанных чтений
```
platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> scaffold.log
```
9. С помощью программы “ platanus gap_close” уменьшаем кол-во гэпов с помощью подрезанных чтений
```
platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> gapclose.log
```

