---
title: Échantillonner des données dans des conteneurs d’objets blob Azure, SQL Server et des tables Hive | Microsoft Docs
description: Comment explorer les données stockées dans différents environnements Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b7b233532a526879e886950c3a0d8cfe169251a7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441500"
---
# <a name="heading"></a>Échantillonner des données dans des conteneurs d'objets blob Azure, SQL Server et des tables Hive

Les articles suivants expliquent comment échantillonner les données stockées dans l’un des trois emplacements Azure :

* Les [**données de conteneurs d’objets blob Azure**](sample-data-blob.md) sont échantillonnées par le biais de leur téléchargement par programmation, puis de leur échantillonnage à l’aide d’un exemple de code Python.
* Les [**données SQL Server**](sample-data-sql-server.md) sont échantillonnées à l’aide de SQL et du langage de programmation Python. 
* Les [**données de tables Hive**](sample-data-hive.md) sont échantillonnées à l’aide de requêtes Hive.

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Pourquoi échantillonner des données ?**

Si vous prévoyez d’analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Cette opération facilite la compréhension et l’exploration des données, ainsi que la conception de fonctionnalités. Son rôle dans le processus Cortana Analytics consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.

