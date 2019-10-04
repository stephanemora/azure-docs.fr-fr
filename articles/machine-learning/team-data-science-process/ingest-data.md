---
title: Charger des données dans des environnements de Stockage Azure - Team Data Science Process
description: Déplacer des données vers et depuis un stockage Azure Blob
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303585"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Charger des données dans des environnements de stockage à des fins d’analyse

Le processus Team Data Science nécessite que les données soient reçues ou chargées dans différents environnements de stockage pour y être traitées ou analysées de la façon la plus appropriée à chaque étape du processus. Les destinations de données couramment utilisées pour le traitement sont le stockage d’objets blob Azure, les bases de données SQL Azure, SQL Server sur machine virtuelle Azure, HDInsight (Hadoop) et Azure Machine Learning. 

Les articles suivantes expliquent comment recevoir les données dans les environnements cibles où les données sont stockées et traitées.

* Vers/À partir d’[Azure Blob Storage](move-azure-blob.md)
* Vers [SQL Server dans les machines virtuelles Azure](move-sql-server-virtual-machine.md)
* Vers la [base de données Azure SQL](move-sql-azure.md)
* Vers les [tables Hive](move-hive-tables.md)
* Vers les [tables partitionnées SQL](parallel-load-sql-partitioned-tables.md)
* À partir du [Serveur SQL Server local](move-sql-azure-adf.md)

Les besoins techniques et ceux de l’entreprise, ainsi que l’emplacement initial, le format et la taille de vos données déterminent l’environnement cible dans lequel les données doivent être reçues pour atteindre les objectifs de votre analyse. Il n’est pas rare pour un scénario d’exiger le déplacement des données entre plusieurs environnements pour arriver à effectuer les différentes tâches nécessaires pour construire un modèle prédictif. Cette suite de tâches peut par exemple inclure l’exploration des données, leur prétraitement, leur nettoyage, l’échantillonnage et l’apprentissage du modèle.
