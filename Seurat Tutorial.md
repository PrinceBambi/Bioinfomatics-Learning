# 载入包
library(stringr)

library(tidyverse)

library(Seurat)

library(ggplot2)

# 解压文件
untar("GSE231920_RAW.tar", exdir = "input")

dir("input")

file.remove("GSE231920_RAW.tar")

# 修改文件命名

files <- list.files("input", full.names = TRUE)

for (file in files) {
  newname <- str_remove(file, "GSM7306055_sample2_")
  file.rename(file, newname)
}

dir("input")

# 读入数据
rm(list = ls())

sample2 <- Read10X("input")

dim(sample2)

# 创建对象
sample2_obj <- CreateSeuratObject(counts = sample2,
                                  min.cells = 3,
                                  min.features = 200)
                                  
dim(sample2_obj)

# 计算线粒体RNA比例
sample2_obj[["Percent.mt"]] <- 
  PercentageFeatureSet(sample2_obj, pattern = "^MT-")

head(sample2_obj@meta.data)

VlnPlot(sample2_obj, 
        features = c("nFeature_RNA",
                     "nCount_RNA", 
                     "Percent.mt"), 
        ncol = 3,pt.size = 0.5)

# 数据质控过滤
sample2_obj <- subset(sample2_obj, 
                     nFeature_RNA < 7500 &
                       nCount_RNA < 50000 & 
                       Percent.mt < 30)
                       
dim(sample2_obj)

# 降维处理并保存对象
file  <- "Sample2.Rdata"

if(!file.exists(file)) {
  seu.obj = sample2_obj %>% 
    NormalizeData() %>% 
    FindVariableFeatures() %>%
    ScaleData(features = rownames(.)) %>%  
    RunPCA(features = VariableFeatures(.))  %>%
    FindNeighbors(dims = 1:15) %>% 
    FindClusters(resolution = 0.5) %>% 
    RunUMAP(dims = 1:15) %>% 
    RunTSNE(dims = 1:15)
  save(seu.obj,file = file) 
  ##注意此段函数运行结果被保存至名为“seu.obj”的数据文件中，而非自己命名的文件
}

# 载入对象
load(file)

# 查看降维拐点
pca <- ElbowPlot(seu.obj) 

pca

ggsave("PCA.pdf", plot = pca, width = 6, height = 5)

# 调整降维分析
file.remove("Sample2.Rdata")

if(!file.exists(file)) {
  seu.obj = sample2_obj %>% 
    NormalizeData() %>% 
    FindVariableFeatures() %>%
    ScaleData(features = rownames(.)) %>%  
    RunPCA(features = VariableFeatures(.))  %>%
    FindNeighbors(dims = 1:10) %>% 
    FindClusters(resolution = 0.5) %>% 
    RunUMAP(dims = 1:10) %>% 
    RunTSNE(dims = 1:10)
  save(seu.obj,file = file) 
}

# 查看降维结果并保存
umap <- DimPlot(seu.obj, reduction = "umap",label = T)+
  NoLegend()
  
umap

ggsave("umap.pdf", plot = umap, width = 6, height = 5)

tsne <- DimPlot(seu.obj, reduction = "tsne",label = T)+
  NoLegend()
  
tsne

ggsave("tsne.pdf", plot = tsne, width = 6, height = 5)
