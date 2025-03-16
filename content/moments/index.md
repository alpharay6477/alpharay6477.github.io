---
title: 瞬间
summary: 
tags:
date: 2025-01-07T22:56:14Z
categories:
showDate : false
showDateOnlyInArticle : false
showDateUpdated : false
showHeadingAnchors : false
showPagination : false
showReadingTime : false
showTableOfContents : false
showTaxonomies : false 
showWordCount : false
showSummary : false
sharingLinks : false
showEdit: false
showViews: false
showLikes: false
showAuthor: false
layoutBackgroundHeaderSpace: false
authors:
  - 葛世杰
---

***

### —— 25.03.13

今天重新配了下 Slurm，结果 munge 一直和另外两个节点认证不上，确认了munge.key、权限、hosts、时间同步、id munge（虽然不影响），搞了一下午才发现更改 munge.key 之后要在每个节点上重新 restart munge.server ，被自己气笑了。。
