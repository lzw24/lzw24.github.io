# Blast_homework

#### 赖中文 2024011275

### 1. 使用网页版的 blastp进行序列比对

![](https://yingzexu-lala.github.io/bioinformatic/2.3 Blast/blast 1.1.png)
![](https://yingzexu-lala.github.io/bioinformatic/2.3 Blast/blast 1.2.png)
![](https://yingzexu-lala.github.io/bioinformatic/2.3 Blast/blast 1.3.png)

1. E-value：匹配次数的期望，表示在此次数据库搜索中，**预期得到 E-value 个得分为大于等于 S 的不同比对**。
2. P-value：在本次数据库搜索中，由随机机会产生得分≥当前得分的比对结果的概率。即**随机获得一个至少等于 S 的得分的概率**。

### 2. 使用bash脚本编程，打乱序列并两两进行blast比对

```bash
#!/bin/bash

#输入初始序列
s="MSTRSVSSSSYRRMFGGPGTASRPSSSRSYVTTSTRTYSLGSALRPSTSRSLYASSPGGVYATRSSAVRL"

#设置索引起止位置 si 和结束位置 ei
si=0
L=${#s}
ei=$(($L-1))

#创建组用于储存打乱生成的序列
shuffled_seqs=()

#生成10个随机打乱的序列
for ((n=1;n<=10;n++));do
        shuffled=""
        for i in `seq $si $ei | shuf `;do
                shuffled=$shuffled${s:$i:1}
        done
        shuffled_seqs+=("$shuffled")
        echo "Shuffled Sequence $n $shuffled"
done

#将输出目录设定为 output 文件夹
workdir="output"
cd "$workdir"
output="blast_result.txt"

#将打乱后的序列写入 fasta 文件
for i in "${!shuffled_seqs[@]}";do
        num=$((i+1))
        echo ">seq$num" > "seq$num.fasta"
        echo "${shuffled_seqs[$i]}" >> "seq$num.fasta"
done

#将所有打乱的序列两两进行 blastp 比对
for ((i=1;i<=10;i++));do
        for ((j=i+1;j<=10;j++));do
                echo "comparing seq$i vs seq$j:" >> "$output"
                blastp -query "seq$i.fasta" -subject "seq$j.fasta" -outfmt 6 >> "$output"
                echo "" >> "$output"
        done
done

exit 0
```

运行bash脚本，浏览 blast_result.txt 结果如下

```shell
root@6d74ba9dcc11:/home/test/share# ./shuffle_blast.sh
Shuffled Sequence 1 MRATSSRLRAGYSSLGASGRSTSRSVTSRSPGMRSTALTTYTSVLGYSPFSGSPYAVSSSRRTPRVSSSY
Shuffled Sequence 2 RTRGFSTLLSSSASSVPSRPYSPRGLSARRSRGPVSSSRTGSAGRTASYSYSTTLTRVSYGYTMSSASVM
Shuffled Sequence 3 RPYPSRVSSSSVSSRGVGTLRSTYFSMYSSTSTRSSLTMARRASRGGSPSAVGYTPSRSASLSLTTARYG
Shuffled Sequence 4 SSTGARTPMRRSSSLPRLVRSATGSGSTSGYMSYVYGAGPRTRRVSSSSSTATALTSSRSSVSRLYFSYP
Shuffled Sequence 5 YSMRPSSSSRATRTSSGSSSLSYSRGPGPARRRTGYSSRTVAVSRPLALLTYMSTASGYTGSVVFSSTSR
Shuffled Sequence 6 RTSYSLRSYMSPLYLASTSRSRSVFSVSGTRASPRSTYSGVPTSGMSGTPAYSRTSRASGSVALSTGRRS
Shuffled Sequence 7 APPVSTRRYVSSSYPSSRRFSASYRRASSLTSRTTPSMGLAGSLRSSGTRTTGSRYGTVSYSMSSGVLAS
Shuffled Sequence 8 MPSGRMRTSLSSGAVPRASSATGYSSPPFTSSASYGRRTVRSLRSTSYLRTTRSVYARLSVGSTYSSSSG
Shuffled Sequence 9 FTASSYTRTSSLSYRPRYRGATSSPSGTTTSRGVSSGPYARSYRRSSRASMRSSSGVALPSLSGLVVMST
Shuffled Sequence 10 VGYVSTGMALATPRRGPARYSSSSSSSSTRTLGYSRSVTVLMYSRSATGLSSRPRRYGSSTSRAPTSSSF

root@6d74ba9dcc11:/home/test/share# cat output/blast_result.txt
comparing seq1 vs seq2:

comparing seq1 vs seq3:

comparing seq1 vs seq4:
seq1    seq4    53.333  15      7       0       1       15      42      56      0.41    13.1

comparing seq1 vs seq5:
seq1    seq5    50.000  30      12      2       3       32      39      65      0.69    12.3

comparing seq1 vs seq6:
seq1    seq6    50.000  24      8       2       31      50      15      38      2.7     10.8
seq1    seq6    41.667  12      7       0       36      47      4       15      7.7      9.6

comparing seq1 vs seq7:
seq1    seq7    44.000  25      14      0       3       27      10      34      0.47    12.7
seq1    seq7    57.143  7       3       0       1       7       37      43      1.2     11.9

comparing seq1 vs seq8:

comparing seq1 vs seq9:
seq1    seq9    77.778  9       1       1       18      25      49      57      0.65    12.3

comparing seq1 vs seq10:

comparing seq2 vs seq3:

comparing seq2 vs seq4:

comparing seq2 vs seq5:

comparing seq2 vs seq6:
seq2    seq6    58.333  12      5       0       35      46      20      31      0.41    13.1

comparing seq2 vs seq7:
seq2    seq7    55.556  9       4       0       36      44      31      39      0.73    12.3
seq2    seq7    57.143  7       3       0       13      19      43      49      2.2     11.2

comparing seq2 vs seq8:
seq2    seq8    55.556  9       4       0       13      21      56      64      0.93    11.9

comparing seq2 vs seq9:
seq2    seq9    39.474  38      15      3       12      47      24      55      0.62    12.7

comparing seq2 vs seq10:

comparing seq3 vs seq4:

comparing seq3 vs seq5:
seq3    seq5    57.143  7       3       0       48      54      13      19      2.3     10.8
seq3    seq5    50.000  10      5       0       5       14      23      32      4.3     10.4

comparing seq3 vs seq6:
seq3    seq6    44.444  18      10      0       49      66      16      33      0.22    13.9

comparing seq3 vs seq7:
seq3    seq7    50.000  8       4       0       44      51      41      48      5.2     10.0

comparing seq3 vs seq8:

comparing seq3 vs seq9:
seq3    seq9    60.000  5       2       0       11      15      16      20      7.1      9.6

comparing seq3 vs seq10:
seq3    seq10   40.000  10      6       0       52      61      37      46      3.4     10.4

comparing seq4 vs seq5:

comparing seq4 vs seq6:
seq4    seq6    54.545  11      5       0       33      43      51      61      0.68    12.3
seq4    seq6    71.429  7       2       0       37      43      14      20      3.7     10.4

comparing seq4 vs seq7:

comparing seq4 vs seq8:
seq4    seq8    83.333  6       1       0       30      35      27      32      5.0     10.0

comparing seq4 vs seq9:

comparing seq4 vs seq10:
seq4    seq10   47.059  17      9       0       30      46      1       17      2.7     10.8

comparing seq5 vs seq6:
seq5    seq6    56.250  16      7       0       47      62      20      35      0.033   15.8
seq5    seq6    52.632  19      9       0       36      54      50      68      0.087   14.6

comparing seq5 vs seq7:

comparing seq5 vs seq8:
seq5    seq8    83.333  6       1       0       46      51      54      59      0.25    13.5
seq5    seq8    57.143  14      6       0       31      44      56      69      0.67    12.3

comparing seq5 vs seq9:

comparing seq5 vs seq10:

comparing seq6 vs seq7:

comparing seq6 vs seq8:

comparing seq6 vs seq9:
seq6    seq9    43.333  30      16      1       28      57      23      51      0.21    13.9

comparing seq6 vs seq10:
seq6    seq10   66.667  6       2       0       36      41      4       9       0.62    12.7

comparing seq7 vs seq8:
seq7    seq8    40.000  10      6       0       36      45      59      68      3.0     10.8

comparing seq7 vs seq9:
seq7    seq9    55.556  9       4       0       26      34      38      46      1.7     11.2

comparing seq7 vs seq10:

comparing seq8 vs seq9:

comparing seq8 vs seq10:

comparing seq9 vs seq10:
seq9    seq10   60.000  5       2       0       29      33      36      40      3.6     10.4

comparing seq1 vs seq2:

comparing seq1 vs seq3:
seq1    seq3    50.000  6       3       0       33      38      20      25      5.9     10.0

comparing seq1 vs seq4:

comparing seq1 vs seq5:
seq1    seq5    66.667  9       3       0       6       14      30      38      0.20    13.9

comparing seq1 vs seq6:
seq1    seq6    39.130  23      13      1       32      54      29      50      1.3     11.5

comparing seq1 vs seq7:

comparing seq1 vs seq8:
seq1    seq8    63.636  11      4       0       20      30      44      54      2.5     10.8

comparing seq1 vs seq9:

comparing seq1 vs seq10:

comparing seq2 vs seq3:

comparing seq2 vs seq4:
seq2    seq4    41.667  12      7       0       40      51      25      36      7.4      9.6
seq2    seq4    100.000 2       0       0       60      61      36      37      9.9      9.2

comparing seq2 vs seq5:

comparing seq2 vs seq6:

comparing seq2 vs seq7:
seq2    seq7    57.143  14      6       0       40      53      52      65      0.98    11.9
seq2    seq7    45.455  11      6       0       46      56      21      31      1.9     11.2
seq2    seq7    47.059  17      6       1       53      66      49      65      9.0      9.2

comparing seq2 vs seq8:
seq2    seq8    38.462  13      8       0       10      22      18      30      4.2     10.4

comparing seq2 vs seq9:
seq2    seq9    50.000  8       4       0       21      28      14      21      9.0      9.2

comparing seq2 vs seq10:
seq2    seq10   39.535  43      18      2       1       38      30      69      0.18    13.9
seq2    seq10   34.483  29      19      0       32      60      15      43      0.83    12.3

comparing seq3 vs seq4:
seq3    seq4    62.500  8       3       0       47      54      24      31      2.5     10.8
seq3    seq4    71.429  7       2       0       4       10      8       14      4.1     10.4

comparing seq3 vs seq5:
seq3    seq5    62.500  8       3       0       14      21      24      31      4.4     10.4

comparing seq3 vs seq6:

comparing seq3 vs seq7:
seq3    seq7    46.154  13      7       0       58      70      45      57      0.82    12.3
seq3    seq7    77.778  9       2       0       7       15      10      18      2.7     10.8

comparing seq3 vs seq8:

comparing seq3 vs seq9:

comparing seq3 vs seq10:
seq3    seq10   46.875  32      15      1       4       33      17      48      1.6     11.5

comparing seq4 vs seq5:
seq4    seq5    50.000  16      8       0       37      52      26      41      0.10    14.6
seq4    seq5    100.000 3       0       0       31      33      52      54      3.5     10.4

comparing seq4 vs seq6:

comparing seq4 vs seq7:
seq4    seq7    45.833  24      13      0       34      57      9       32      0.021   16.5
seq4    seq7    40.000  15      9       0       25      39      52      66      1.8     11.2
seq4    seq7    75.000  8       2       0       42      49      6       13      1.9     11.2

comparing seq4 vs seq8:

comparing seq4 vs seq9:
seq4    seq9    60.000  5       2       0       26      30      33      37      9.2      9.2

comparing seq4 vs seq10:

comparing seq5 vs seq6:
seq5    seq6    50.000  14      7       0       38      51      53      66      3.8     10.4
seq5    seq6    100.000 3       0       0       56      58      58      60      9.5      9.2

comparing seq5 vs seq7:
seq5    seq7    50.000  10      5       0       31      40      25      34      2.6     10.8
seq5    seq7    36.000  25      14      1       46      70      3       25      6.8      9.6

comparing seq5 vs seq8:
seq5    seq8    50.000  12      6       0       2       13      42      53      2.0     11.2
seq5    seq8    62.500  8       3       0       18      25      30      37      2.5     10.8

comparing seq5 vs seq9:
seq5    seq9    83.333  6       1       0       55      60      2       7       0.98    11.9
seq5    seq9    60.000  5       2       0       23      27      34      38      9.4      9.2

comparing seq5 vs seq10:
seq5    seq10   38.095  21      13      0       38      58      29      49      0.012   16.9
seq5    seq10   50.000  14      1       1       12      25      29      36      6.6      9.6

comparing seq6 vs seq7:
seq6    seq7    45.000  20      11      0       51      70      27      46      1.3     11.5
seq6    seq7    60.000  5       2       0       65      69      51      55      9.0      9.2

comparing seq6 vs seq8:
seq6    seq8    66.667  9       3       0       54      62      7       15      0.38    13.1
seq6    seq8    40.909  22      10      1       5       26      39      57      1.2     11.5
seq6    seq8    50.000  12      6       0       31      42      17      28      2.5     10.8

comparing seq6 vs seq9:
seq6    seq9    44.444  9       5       0       40      48      56      64      3.6     10.4

comparing seq6 vs seq10:
seq6    seq10   66.667  6       2       0       49      54      31      36      4.4     10.4
seq6    seq10   40.909  22      10      1       26      44      46      67      6.9      9.6

comparing seq7 vs seq8:
seq7    seq8    83.333  6       1       0       21      26      32      37      2.2     11.2

comparing seq7 vs seq9:
seq7    seq9    53.846  13      6       0       21      33      3       15      0.18    13.9

comparing seq7 vs seq10:

comparing seq8 vs seq9:
seq8    seq9    83.333  6       1       0       30      35      9       14      1.7     11.2
seq8    seq9    50.000  8       4       0       45      52      3       10      2.2     11.2

comparing seq8 vs seq10:

comparing seq9 vs seq10:

```

出现空白表明，这两个打乱生成的序列相似度过低，无法被blast识别，因此无生成内容。

此处为 outfmt 命令自动生成的表格，各列含义分别为：序列1，序列2，一致性（百分比），比对长度，错配个数，间隙个数，序列1配对起始位置，序列1配对终止位置，序列2配对起始位置，序列2配对终止位置，E-value，bit score。

### 3. 解释 blast 中的提速方法和原理

1. 将查询序列切分成短词，将数据库中所有序列的固定长度切分短词及其位置存入索引。进行 blast 时，先找到含有相同词的序列位置，再在该词附近进行比对。
2. 对每个相同词沿两侧双向延伸，直到累积得分开始下降超过阈值。这个阶段不引入空位，只计算不匹配的得分。可以快速过滤低相似度的随机匹配。

### 4. 分析对称和不对称PAM250的不同原因和不同应用

1. 形式不同的原因：不对称矩阵直接来源于突变数据，保留进化方向信息，且为了满足马尔科夫链的行归一化，会导致非对称的情况；对称矩阵只关注替换的 odds ratio，且对称的结构便于计算两条序列独立于方向的得分。
2. 不对称PAM250一般用于分子进化分析、系统发育重建、突变模型，有一定的方向性；对称PAM250一般用于蛋白质序列比对、相似性搜索，无方向性。
