# hse22_hw1
ДЗ1 Газизова Анита, 3 группа
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
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}
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

7. С помощью программы “platanus assemble” собираем контиги из подрезанных чтений
