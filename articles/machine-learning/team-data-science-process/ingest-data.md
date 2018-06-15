---
title: Charger des données dans des environnements de stockage Azure à des fins d’analyse | Microsoft Docs
description: Déplacer des données vers et depuis un stockage Azure Blob
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: d045bd37a4b3192672cc1bd37bc4bd14ea8d5402
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837182"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Charger des données dans des environnements de stockage à des fins d’analyse
Le processus Team Data Science nécessite que les données soient reçues ou chargées dans différents environnements de stockage pour y être traitées ou analysées de la façon la plus appropriée à chaque étape du processus. Les destinations de données couramment utilisées pour le traitement sont le stockage d’objets blob Azure, les bases de données SQL Azure, SQL Server sur machine virtuelle Azure, HDInsight (Hadoop) et Azure Machine Learning. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Ce **menu** pointe vers des rubriques qui décrivent comment recevoir les données dans les environnements cibles où les données sont stockées et traitées.

Les besoins techniques et ceux de l’entreprise, ainsi que l’emplacement initial, le format et la taille de vos données déterminent l’environnement cible dans lequel les données doivent être reçues pour atteindre les objectifs de votre analyse. Il n’est pas rare pour un scénario d’exiger le déplacement des données entre plusieurs environnements pour arriver à effectuer les différentes tâches nécessaires pour construire un modèle prédictif. Cette suite de tâches peut par exemple inclure l’exploration des données, leur prétraitement, leur nettoyage, l’échantillonnage et l’apprentissage du modèle.

