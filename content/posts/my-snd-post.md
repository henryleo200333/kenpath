+++
date = '2024-01-04T22:02:05+08:00'
draft = false
title = 'k8s淺談'
+++

這篇想記錄一些k8s的功能，  
和術語的簡易說明，  
我認為有chatGPT的幫助，  
現在學東西最重要的是學會問問題，  
只要知道k8s如何運作，  
實務上該如何設定都是能問到的，  
所以不會過多著墨。

## k8s是什麼
全名Kubernetes，希臘語中「掌舵者」的意思，  
象徵管理船上的貨櫃們。  
貨櫃指k8s中最小的管理單位(或說部屬單位)Pod。

## 部署描述檔 Dockerfile
檔名固定，作為生成image的文件，  
定義k8s部署內容，  
比如各image要產生幾個pod副本、定義path與服務的關係。

1. Pod
一個pod內可以部署多個container，  
一個container可以想成是一個應用、  
基於image產生的容器。

1. Service
當pod有多個時，  
service會做負載平衡，  
將請求分散給pod。  
可以通過Node Port供外部直接送請求過來，  
較常見是配合Ingress使用。

1. Ingress
定義URL解析規則，  
將請求導向不同的service。  
可以通過Ingress Controller實現。  

## Node 節點
一台實體或虛擬的運算資源(電腦)，就可以作為一個Node，  
依照工作分成Master Node與Worker Node 兩種，  
Master Node根據部屬描述檔內容，  
在Worker Node上產生Pod，  
並且管理Pod的生命週期。

## HPA 水平擴展
支援動態增減Pod數量，  
可以根據CPU使用率、記憶體使用率、自訂指標等，  
自動調整Pod數量。

## Rolling Update 不停機更新
可以滾動更新Pod，  
或是退版，  
不會影響到服務的可用性。