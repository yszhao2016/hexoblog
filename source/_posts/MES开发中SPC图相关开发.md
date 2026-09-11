---
title: MES开发中SPC图相关开发
abbrlink: 14ad81d9
date: 2026-09-03 20:03:00
tags:
---
# 一、计量型图

    xbarR【均值-极差图】、xbarS【】 I-MR【单值-移动极差图】

相关名词解释

    子组容量： 用于xr xs 图中，就是一组数据有几个样本

    子组号：就是每个子组的序号【感觉就是为了判异，没这不好操作了】

    过程能力窗口（子组数量）：用于xr xs 图中 计算ucl lcl的 

    控制限：

## 1.1 均值-极差图（Xbar-R）

    子组样本量较小 n ≤ 10（最常用，如 n=5

### 1.1.1 均值图
        图上的点为每个子组的 平均值        

        计算每个子组的平均值
        A2:控制限系数
        CL = X̄   所有子组中样本 平均数
        UCL = X̄ + A2 *R̄ ( 样本平均值 + A2 * 极差平均值)
        LCL = X̄ - A2 *R̄ ( 样本平均值 - A2 * 极差平均值)

        其中A2是一组根据子组变化的常量【 A₂ = 3 / (d₂·√n)】
       
```java
   public static double getA2(int n) {
        double[][] a2 = {
                {2, 1.880}, {3, 1.023}, {4, 0.729},
                {5, 0.577}, {6, 0.483}, {7, 0.419},
                {8, 0.373}, {9, 0.337}, {10, 0.308}
        };
        for (double[] v : a2) {
            if ((int) v[0] == n) return v[1];
        }
        return 0.577;
    }
```

###  1.1.2 极差图

        图上的点为 每个子组的极差值

        计算出每个子组的极差，极差= 每个子组最大值- 每个子组最小值
        D₃、D₄ 表示 R 图的控制限系数(根据子组固定的)
        D₃  LCL系数，   1 - 3·d₃/d₂
        D₄  UCL系数    1 + 3·d₃/d₂

        CL = R̄  所有子组极差的平均值
        UCL = D4*R̄  (D4 * 所有子组极差的平均值)
        LCL = D3*R̄  (D3 * 所有子组极差的平均值)

```java
 public static double getD3(int n) {
        double[][] d3 = {{1,0},
                {2, 0}, {3, 0}, {4, 0}, {5, 0},
                {6, 0}, {7, 0.076}, {8, 0.136},
                {9, 0.184}, {10, 0.223}
        };
        for (double[] v : d3) {
            if ((int) v[0] == n) return v[1];
        }
        return 0;
}
    
public static double getD4(int n) {
        double[][] d4 = {{1,3.267},
                {2, 3.267}, {3, 2.575}, {4, 2.282},
                {5, 2.114}, {6, 2.004}, {7, 1.924},
                {8, 1.864}, {9, 1.816}, {10, 1.777}
        };
        for (double[] v : d4) {
            if ((int) v[0] == n) return v[1];
        }
        return 2.114;
}   
```

## 1.2 均值-标准差图(xbar-S)

    子组样本量较大 n > 10，用标准差估计离散度更准确

    标准差计算
        1.算子组平均值
        2.每个数据与平均值的差
        3.差值平方
        4.求平方和
        5.除以 n - 1
        6.开平方根
### 1.2.1 均值图    
    
        图上的点为 每个子组的 平均值

        cl = X̄   所有子组中样本平均值
        ucl = X̄ + A3 * σ̄  所有子组中样本平均值 + A3 * 标准差平均值
        lcl = X̄ - A3 * σ̄  所有子组中样本平均值 - A3 * 标准差平均值

### 1.2.2 标准差图
        图上的点为 每个子组的 标准差

        cl = σ̄   每个子组的标准差的平均值
        ucl = B4 * σ̄  
        lcl = B3 * σ̄

```java

/**
 * 标准差
 */
public static double stdDev(List<Double> values) {
    double mean = mean(values);
    double sumSq = 0;
    for (double v : values) {
        sumSq += Math.pow(v - mean, 2);
    }
    return Math.sqrt(sumSq / (values.size() - 1));
}


public static double getA3(int n) {
        double[][] a3 = {
                {2, 2.659}, {3, 1.954}, {4, 1.628},
                {5, 1.427}, {6, 1.287}, {7, 1.182},
                {8, 1.099}, {9, 1.032}, {10, 0.975}
        };
        for (double[] v : a3) {
            if ((int) v[0] == n) return v[1];
        }
        return 1.427;
}

/**
 * B3 系数（S 图 LCL）
 */
public static double getB3(int n) {
    double[][] b3 = {
            {2, 0}, {3, 0}, {4, 0},
            {5, 0}, {6, 0.030}, {7, 0.118},
            {8, 0.185}, {9, 0.239}, {10, 0.284}
    };
    for (double[] v : b3) {
        if ((int) v[0] == n) return v[1];
    }
    return 0;
}

/**
 * B4 系数（S 图 UCL）
 */
public static double getB4(int n) {
    double[][] b4 = {
            {2, 3.267}, {3, 2.568}, {4, 2.266},
            {5, 2.089}, {6, 1.970}, {7, 1.882},
            {8, 1.815}, {9, 1.761}, {10, 1.716}
    };
    for (double[] v : b4) {
        if ((int) v[0] == n) return v[1];
    }
    return 2.089;
}
```

## 1.3 单值-移动极差图(I-MR)   

### 1.3.1 单值图

    图上点为单值的值
    
    MR̄ =  移动极差和/(n-1) 移动极差平均值
    CL  = X̄ 所有单值的总平均
    UCL = X̄ + 3 * (MR̄/D2)  所有单值的总平均 + 3 * 移动极差平均值/D2
    LCL = X̄ - 3 * (MR̄/D2)  所有单值的总平均 - 3 * 移动极差平均值/D2
    令E2 = 3/D2
    简写  UCL = X̄ + E2 * MR̄
         LCL X̄ - E2 * MR̄
```java
public static double getD2(int n) {
    double[][] d2 = {
            {2, 1.128}, {3, 1.693}, {4, 2.059},
            {5, 2.326}, {6, 2.534}, {7, 2.704}
    };
    for (double[] v : d2) {
        if ((int) v[0] == n) return v[1];
    }
    return 2.326;
}

/**
 * E2 系数（MR 图控制限）
 */
public static double getE2(int n) {
    double[][] e2 = {
            {1, 2.66},
            {2, 0.7979}, {3, 0.8862}, {4, 0.9213},
    };
    for (double[] v : e2) {
        if ((int) v[0] == n) return v[1];
    }
    return 2.66;
}
```

### 1.3.2移动极差图

    图上点为移动极差的值

    CL  = MR̄ 所有移动极差 平均值
    UCL = D4 * MR̄
    LCL = D3 * MR̄

```java
public static double getD3(int n) {
    double[][] d3 = {{1,0},
            {2, 0}, {3, 0}, {4, 0}, {5, 0},
            {6, 0}, {7, 0.076}, {8, 0.136},
            {9, 0.184}, {10, 0.223}
    };
    for (double[] v : d3) {
        if ((int) v[0] == n) return v[1];
    }
    return 0;
}

public static double getD4(int n) {
    double[][] d4 = {{1,3.267},
            {2, 3.267}, {3, 2.575}, {4, 2.282},
            {5, 2.114}, {6, 2.004}, {7, 1.924},
            {8, 1.864}, {9, 1.816}, {10, 1.777}
    };
    for (double[] v : d4) {
        if ((int) v[0] == n) return v[1];
    }
    return 2.114;
}
```

# 二、计数型图
    C图【缺陷数图】、U图【缺陷率图】 NP图【不合格品数图】、P图【不合格率图】
## 2.1 缺陷数图(C图 Count)

c图用于监控固定机会/固定面积/固定单位下的缺陷数。
C图上点 为缺陷数

    cl = C̄  缺陷数的 平均值
    ucl = C̄ + 3*√C̄   缺陷数的平均值 + 3 * 开根号缺陷数的平均值
    lcl = C̄ - 3*√C̄

    泊松分布：均值 = 方差 = C̄  ，所以标准差 = √C̄
    LCL 若 < 0，取 0。
```java
        for (SpcDataCount data : spcDataList) {
            int c = data.getUnqualifiedNum();
            totalDefect += c;
        }

        // 3. 平均缺陷数 c̄
        double cBar = (double) totalDefect / spcDataList.size();

        // 4. 控制限（C图：固定样本量）
        double sigma = 3 * Math.sqrt(cBar);

        double ucl = SpcMathUtil.round4(cBar + sigma);
        double lcl = SpcMathUtil.round4(Math.max(0, cBar - sigma));
        double cl = SpcMathUtil.round4(cBar);
```
## 2.2 单位缺陷数图(U图 Unit)

u图用于监控单位单位缺陷数，适用于检查单位数可变。【ucl、lcl 每个点多不一样的】
U图上的点 为缺陷率率（缺陷数/样本数）

    cl = ū  缺陷数的 平均值（总缺陷数 ÷ 总检查单位数）
    ucl = ū + 3√(ū/nᵢ)  缺陷数的平均值 + 3 * 开根号 （缺陷数的平均值 / 当前的样本量值）
    lcl = ū - 3√(ū/nᵢ)
```java

```
## 2.3 不良品/不合格数图 (NP图 Number of defectives)

np图用于监控固定样本量下的不合格品数，样本量必须固定。
NP图上点 为不合格数  

    cl = np̄  不合格品数的平均值
    ucl = np̄ + 3√(np̄(1-p̄))
    lcl = np̄ - 3√(np̄(1-p̄))

```java
    // 3. 平均不合格品数 np̄
    double npBar = (double) totalDefect / spcDataList.size();
    
    // 4. 平均不合格品率 p̄
    double pBar = npBar / n;
    
    // 5. 控制限（固定）
    double sigma = 3 * Math.sqrt(npBar * (1 - pBar));
    double ucl = SpcMathUtil.round4(npBar + sigma);
    double lcl = SpcMathUtil.round4(Math.max(0, npBar - sigma));
    double cl = SpcMathUtil.round4(npBar);
```
## 2.4 不良品/不合格率图 (P图 Proportion)

p图用于监控不合格品率/比例，适用于样本量可变或固定的计件型数据。
P图上点 为不合格率

    cl = p̄  不合格品率的平均值[总不合格数/总的样品数]
    ucl =  p̄ + 3√(p̄(1-p̄)/nᵢ)
    lcl =  p̄ - 3√(p̄(1-p̄)/nᵢ)

```java
    double pBar = (double) totalDefect / totalSample;
        
    double ucl = Double.NaN;
    double lcl = Double.NaN;
    
    for (SpcDataCount data : spcDataList) {
        int n = data.getSampleNum();
    
        double sigma = Math.sqrt(pBar * (1 - pBar) / n);
         ucl = pBar + 3 * sigma;
         lcl = Math.max(0, pBar - 3 * sigma);
    }
```
    
