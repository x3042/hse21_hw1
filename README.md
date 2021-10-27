# hse21_hw1
### <p align=center> Задание 1 </p>
1. Создадим символьные ссылки на файлы, чтобы не копировать их:<br>
  ```bash
  ls /usr/share/data-minor-bioinf/assembly/* | xargs -tI{} ln -s {}
  ```
2. Зададим SEED, для того чтобы удобнее команду применять было:<br>
  ```bash
  SEED=1123
  ```
3. С помощью команды seqtk выбираем случайно 5 миллионов чтений типа paired-end и 1.5 миллиона чтений типа mate-pairs
  ```bash
  seqtk sample -s$SEED oil_R1.fastq 5000000 > sub1.fastq
  seqtk sample -s$SEED oil_R1.fastq 5000000 > sub2.fastq
  seqtk sample -s$SEED oilMP_S4_L001_R1_001.fastq 1500000 > mate_pair_1.fastq
  seqtk sample -s$SEED oilMP_S4_L001_R2_001.fastq 1500000 > mate_pair_2.fastq
  ```
4. Оцениваем качество исходных чтений с помощью fastQC:<br>
  Создадим нужные директории:<br>
  ```bash
  mkdir fastqc
  mkdir multiqc
  ```
  Теперь сделаем, что нужно:<br>
  ```bash
  ls sub* mate_pair_* | xargs -tI{} fastqc -o fastqc {}
  ```
5. Собираем отчёт с помощью multiQC:<br>
  ```bash
  multiqc -o multiqc fastqc
  ```
6. Подрезаем чтения по качеству:<br>
  ```bash
  platanus_trim sub*
  platanus_internal_trim mate_pair_*
  ```
7. Удалям исходные .fastq файлы
  ```bash
  rm sub*.fastq mate_pair_*.fastq
  ```
8. Оцениваем качество подрезанных данных с помощью fastQC:<br>
  Создадим нужные директории:
  ```bash
  mkdir fastqc_trimmed
  mkdir multiqc_trimmed
  ```
  Сделаем, что нужно:<br>
  ```bash
  ls sub* mate_pair_*| xargs -tI{} fastqc -o fastqc_trimmed {}
  ```
9. Собираем отчёт с помощью multiQC:<br>
  ```bash
  multiqc -o multiqc_trimmed fastqc_trimmed
  ```
10. Собираем контиги с помощью “platanus assemble”:<br>
  ```bash
  time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
  ```
11. Собираем скаффолды:<br>
  ```bash
  time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mate_pair_1.fastq.int_trimmed mate_pair_2.fastq.int_trimmed 2> scaffold.log
  ```
12. Уменьшаем кол-во гэпов с помощью программы “platanus gap_close”:
  ```bash
  time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mate_pair_1.fastq.int_trimmed mate_pair_2.fastq.int_trimmed 2> gapclose.log
  ```
