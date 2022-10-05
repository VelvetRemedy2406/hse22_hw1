# hse21_hw1

Код  такой 

```
bash

pwd
```

Загружаем необходимые файлы
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```

Отбираем случайные чтения с random seed = 601
```
seqtk sample -s601 oil_R1.fastq 5000000 > R1_pe.fastq
seqtk sample -s601 oil_R2.fastq 5000000 > R2_pe.fastq
seqtk sample -s601 oilMP_S4_L001_R1_001.fastq 1500000 > R1_mp.fastq
seqtk sample -s601 oilMP_S4_L001_R2_001.fastq 1500000 > R2_mp.fastq
```

Удаляем ненужное
```
rm oil_R1.fastq oil_R2.fastq oilMP_S4_L001_R1_001.fastq oilMP_S4_L001_R2_001.fastq
```

Анализ
```
mkdir fastqc
ls *.fastq | xargs -P 4 -tI{} fastqc -o fastqc {}
mkdir multiqc
multiqc -o multiqc fastqc
```



До обработки:


<img width="709" alt="initial1" src="https://user-images.githubusercontent.com/71277325/139136416-24c8aad2-cddd-469f-9d52-2e3d34cc5c4a.PNG">
<img width="706" alt="initial2" src="https://user-images.githubusercontent.com/71277325/139136425-9ae62e23-b6f9-47f7-b28f-332e0bccb258.PNG">
<img width="689" alt="initial3" src="https://user-images.githubusercontent.com/71277325/139136436-3fd67c64-4c4f-4a8c-a4a5-5139db3b4503.PNG">



Подрезка
```
platanus_trim R1_pe.fastq R2_pe.fastq 
platanus_internal_trim R1_mp.fastq R2_mp.fastq 
```

Анализ после подрезки
```
mkdir trimmed
mv -v *trimmed trimme/
mkdir trimmed_fastqc
ls trimmed/* | xargs -P 4 -tI{} fastqc -o trimmed_fastqc {}
mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
```



После обработки:


<img width="720" alt="final1" src="https://user-images.githubusercontent.com/71277325/139136460-a904c839-6ce0-44c1-88d5-186d52dee35c.PNG">
<img width="697" alt="final2" src="https://user-images.githubusercontent.com/71277325/139136478-1d8d2dd4-f897-411d-a25f-62097cc1fd83.PNG">
<img width="699" alt="final3" src="https://user-images.githubusercontent.com/71277325/139136492-3aa3d026-7fc1-47b1-bb7c-d6105189b0b9.PNG">



Удаляем ненужное
```
rm R1_mp.fastq R1_pe.fastq R2_mp.fastq R2_pe.fastq
```

Контиги, скаффолды, гэпы
```
time platanus assemble -o Poil -t 2 -m 16 -f trimmed/R1_pe.fastq.trimmed trimmed/R2_pe.fastq.trimmed 2> assemble.log

time platanus scaffold -o Poil -c Poil_contig.fa -IP1 trimmed/R1_pe.fastq.trimmed  trimmed/R2_pe.fastq.trimmed -OP2 trimmed/R1_mp.fastq.int_trimmed trimmed/R2_mp.fastq.int_trimmed 2> scaffold.log

time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 trimmed/R1_pe.fastq.trimmed  trimmed/R2_pe.fastq.trimmed -OP2 trimmed/R1_mp.fastq.int_trimmed trimmed/R2_mp.fastq.int_trimmed 2> gapclose.log
```





<img width="251" alt="Снимок1" src="https://user-images.githubusercontent.com/71277325/139146888-7177fc0c-5ea8-43df-9fb4-3168a8355a4e.PNG">
<img width="267" alt="Снимок2" src="https://user-images.githubusercontent.com/71277325/139146909-2d61949c-1bdb-4df3-a01c-7aece4b4af0e.PNG">

