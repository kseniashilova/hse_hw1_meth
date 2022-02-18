# hse_hw1_meth
В работе будут проанализированы следующие 3 BS-Seq образца, полученные на разных стадиях развития эмбриона мыши:
* SRR5836473 - 8 Cell
* SRR3824222 - Epiblast
* SRR5836475 - ICM  
Цель работы - изучение глобального изменения уровня CpG метилирования ДНК при раннем эмбриональном развитии мыши. А именно, наблюдение волн метилирования в процессе развития эмбриона. Выравнивание чтений производится только на 11 хромосому.  
# 1. Анализ отчета fastqc  
Из предыдущей домашней работы приведу [отчет по multiqc](https://github.com/kseniashilova/hse_hw1_meth/blob/main/multiqc_RNA.html) для RNA-seq чтений для мыши. 
Отчет для исследуемого образца (8 Cell) приведен на скриншотах ниже и [тут](https://github.com/kseniashilova/hse_hw1_meth/blob/main/SRR5836473_1_fastqc.html).  
Из общей статистики можно сделать вывод о том, что для каждого образца для RNA-seq анализа %GC больше, чем для нашего исследуемого образца. Около 50% против 36%.  
Для RNA-seq:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/general_stat_RNA.PNG)  
Для 8 cell:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/general_stat_8cell.PNG)  
Из диаграммы Per Sequence GC Content можно считать явное отклонение распределения от теоретического, о чем и свидетельствует fastqc. Что касается RNA-seq, для образцов критических отклонений не найдено, более того, 3 из 6 прошли как "зеленые" по этому параметру.  
Для RNA-seq:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/GC_RNA.PNG)  
Для 8 cell:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/GC_8cell.PNG)    
И, что касается графиков Per base sequence content (распределение нуклеотидов), можно сказать, что в RNA-seq отчете видны равные доли для цитозина, тимина, гуанина и аденина. Для 8 cell последовательности можно сказать, что явно видно превосходство тимина и малая доля цитозина для каждой из позиций. График для аденина находится выше, чем для гуанина, но они, тем не менее, довольно близки по процентам.  
Для RNA-seq:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/content_RNA.PNG)  
Для 8 cell:  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/content_8cells.PNG)  
  
**Вывод** (Бонус) :  
# 2. 
## a) Число ридов, закартированных на участки 11347700-11367700; 40185800-40195800  
Чтобы построить таблицу для числа ридов, закартированных на определенные участки, воспользуемся набором комад: 
* ! samtools sort
* ! samtools index (для ускорения работы)
* ! samtools view  
  
Таблица (число ридов):  
| образец / участки | 1347700-11367700 | 40185800-40195800 |
|-------------------|------------------|-------------------|
| 8 cell            | 1090             | 464               |
| Epiblast          | 2328             | 1062              |
| ICM               | 1456             | 630               |


## b) Дедупликация для всех образцов одновременно (Бонус 1)
Чтобы провести дедупликацию одновременно нескольких файлов, нужно запустить следующий скрипт:  
```
! ls /content/drive/MyDrive/bioinfHW1/cell8/SRR5836473_1_bismark_bt2_pe.bam /content/drive/MyDrive/bioinfHW1/epiblast/SRR3824222_1_bismark_bt2_pe.bam /content/drive/MyDrive/bioinfHW1/ICM/SRR5836475_1_bismark_bt2_pe.bam  | xargs -P 3 -tI{} deduplicate_bismark  --bam  --paired  -o {}.deduplicated {}
```
Здесь я явно указала названия нужных мне файлов, но, если бы они лежали в удобном месте и рядом, можно было бы значительно укоротить строку с исползованием регулярных выражений и зменить три названия на "\*_pe.bam" или "\*.bam" в зависимости от того, какие файлы еще есть рядом. В общем, мне не хотелось копировать файлы в более удобные места по причине экономии времени, поэтому указала три имени файлов явно.  

Таблица (дупликации):  
| образец / участки |% удаленных дупликаций |
|-------------------|-----------------------|
| 8 cell            | 18.31%                |
| Epiblast          | 2.92%                 |
| ICM               | 9.08%                 |
  
**Вывод** (Бонус) :  
  
## c) Коллинг метилирования цитозинов
Выполним команду для каждого образца, но, нужно предварительно скачать папку ./mouse_chr  с диска:  
```
! bismark_methylation_extractor --gzip --bedGraph --cytosine_report --genome_folder ./mouse_chr output_file_name.deduplicated.bam
```
## d) Отчет и описание M-bias plot  
<img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR5836473.PNG" width="500"> <img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR5836473_2.PNG" width="500">  
  

<img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR3824222.PNG" width="500"> <img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR3824222_2.PNG" width="500">
  

<img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR5836475.PNG" width="500"> <img src="https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/mbias_SRR5836475_2.PNG" width="500">


## e) Гистограмма распределения метилирования цитозинов по хромосоме
```
# гистограмма
data = pd.read_csv('{id}_1_bismark_bt2_pe.bam.deduplicated.deduplicated.bedGraph', 
                   delimiter='\t', names=['chr11', 'a', 'b', '%'], skiprows=1)
plt.hist(data['%'], density=True, bins=100)
plt.title('ICM SRR5836475')
plt.xlabel('% methylation')
plt.ylabel('density')
plt.show()
```
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/hist_8cell.png)  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/hist_epiblast.png)  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/hist_icm.png)  
## f) Уровень покрытия b метилирования для каждого образца
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/image_cov.png)  
![](https://github.com/kseniashilova/hse_hw1_meth/blob/main/pic/image_meth.png)  
