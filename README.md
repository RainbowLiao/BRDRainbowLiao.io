# This is RainbowLiao's data world.

## I'm a college student of department of Biological Resoure in NCYU. 

## My research is about the community of frog's parasites, so here shows a piece of my result....


## 黑眶蟾蜍體內寄生蟲資料

```{r, echo=FALSE, warning=FALSE, error=FALSE}
library(data.table)
library(ape)
library(vegan)
library(dplyr)
library(tidyr)
library(ggplot2)

# data loading
frog_data <- fread('BRDRainbowLiao.io/黑眶整理表.csv', header = T)
vectors <- fread('BRDRainbowLiao.io/sex&loc.csv', header = T)
frog_data_no0 <- filter(frog_data, infection == 'yes')
tbl_frog0 <- tbl_df(frog_data_no0)
head(tbl_frog0)

parasite <- select(tbl_frog0, 3:4, 10:14)
head(parasite)

# sqrt轉換 trans <- sqrt
sqare_root_para <- sqrt(parasite[3:7])
```
![table](https://github.com/RainbowLiao/BRDRainbowLiao.io/raw/master/table.PNG)

## PCoA 

###(主座標分析，分析寄生蟲組成在不同地點或性別間的距離或差異)

```{r, echo=FALSE, warning=FALSE, error=FALSE}

# distance 以 hellinger 轉換
frog_sq <- decostand(sqare_root_para, "hellinger")


# 計算相對距離
frog_sq_dist <- dist(frog_sq)

```

## Plots

```{r pressure, echo=FALSE, warning=FALSE, error=FALSE}
res <- cmdscale(frog_sq_dist, 4, eig= T)
(en2 <- envfit(res, frog_sq, perm = 999))
limits = apply(res$points, 2, range)
xlim = c(limits[1,1], limits[2,1]) + c(-0.2,0)
ylim = c(limits[1,2], limits[2,2])
x = res$points[,1]
y = res$points[,2]
plot(x, y, xlim=xlim, ylim=ylim, asp=1, xlab="PCo1",
     ylab="PCo2", main = 'Asian toad parasites')

# 以不同顏色辨識不同樣區並以性別符號區分性別
points(res$points[1:18,], col = 'blue', pch = 19) 
points(res$points[19:40, ] , col = 'red', pch = 19) 
plot(en2, col = "gray")
plot(en2, p.max = 0.05, col = "red", lwd = 30)
names = as.character(tbl_frog0$Sex)
text(x, y, labels= names, pos=2, cex=1, offset=1)
```
![description](https://github.com/RainbowLiao/BRDRainbowLiao.io/raw/master/PCoA.PNG)

## 以permanova檢測差異
```{r, echo=FALSE, warning=FALSE, error=FALSE}
# permanova
adonis(frog_data_no0[, 10:14]~frog_data_no0$`Capture loc.`*frog_data_no0$Sex)

```
https://github.com/RainbowLiao/BRDRainbowLiao.io/raw/master/permanova.PNG

#### 結果顯示在不同地點間有差異而性別無

## 寄生蟲是否有co-occurence關係
```{r}
# cooccurce
library(cooccur)
frog_co <- t(decostand(frog_sq, "pa"))
coocur_1 <- cooccur(mat = frog_co, type = "spp_site",
                    thresh = TRUE, spp_names = TRUE)
summary(coocur_1)

```
https://github.com/RainbowLiao/BRDRainbowLiao.io/raw/master/cooccur.PNG

#### 結果顯示並無任何共同出現關係
