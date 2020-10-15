---
title: Échantillonner des données dans des emplacements Stockage Azure différents – Team Data Science Process
description: Échantillonnez des données dans des conteneurs d’objets blob Azure, SQL Server et des tables Hive pour une taille réduite, mais plus représentative et facile à gérer.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718601"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Échantillonner des données dans des conteneurs d'objets blob Azure, SQL Server et des tables Hive

Les articles suivants expliquent comment échantillonner les données stockées dans l’un des trois emplacements Azure :

* Les [**données de conteneurs d’objets blob Azure**](sample-data-blob.md) sont échantillonnées par le biais de leur téléchargement par programmation, puis de leur échantillonnage à l’aide d’un exemple de code Python.
* Les [**données SQL Server**](sample-data-sql-server.md) sont échantillonnées à l’aide de SQL et du langage de programmation Python. 
* Les [**données de tables Hive**](sample-data-hive.md) sont échantillonnées à l’aide de requêtes Hive.

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Pourquoi échantillonner des données ?**

Si vous prévoyez d’analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Réduire la taille peut faciliter la compréhension et l’exploration des données, ainsi que l’ingénierie de fonctionnalités. Ce rôle d’échantillonnage dans le processus Cortana Analytics consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles Machine Learning.

