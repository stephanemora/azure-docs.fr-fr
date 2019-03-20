---
title: Comprendre les tarifs Azure Data Factory à travers des exemples | Microsoft Docs
description: Cet article explique et décrit le modèle tarifaire Azure Data Factory avec des exemples détaillés.
documentationcenter: ''
author: shlo
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: 80b1f90ee0d9f5003c39eb6a853a07d2d64ca482
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085335"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Comprendre les tarifs Data Factory à travers des exemples

Cet article explique et décrit le modèle tarifaire Azure Data Factory avec des exemples détaillés.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copier des données d’AWS S3 vers le Stockage Blob Azure toutes les heures

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3.

2. Un jeu de données de sortie pour les données sur le Stockage Azure.

3. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

   ![Scénario 1](media/pricing-concepts/scenario1.png)

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 2 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 2 exécutions d’activité (1 pour l’exécution du déclencheur, 1 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 2 enregistrements d’exécution de monitoring retentés (1 pour l’exécution du pipeline, 1 pour l’exécution d’activité) |

**Prix total du scénario : 0,16811 $**

- Opérations Data Factory = **0,0001 $**
  - Lecture/écriture = 10 \* 0,00001 = 0,0001 $ [1 lecture/écriture = 0,50 / 50 000 = 0,00001]
  - Monitoring = 2 \* 0,000005 = 0,00001 $ [1 monitoring = 0,25 $/ 50 000 = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,168 $**
  - Exécutions d’activité = 0,001 \* 2 = 0,002 $ [1 exécution = 1 $ / 1 000 = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copier des données et les transformer toutes les heures avec Azure Databricks

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure et les transformer avec Azure Databricks selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3 et un jeu de données de sortie pour les données sur le Stockage Azure.
2. Une activité Azure Databricks pour la transformation de données.
3. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

![Scénario 2](media/pricing-concepts/scenario2.png)

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 3 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 3 exécutions d’activité (1 pour l’exécution du déclencheur, 2 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 3 enregistrements d’exécution de monitoring retentés (1 pour l’exécution du pipeline, 2 pour l’exécution d’activité) |
| Exécution de l’activité Databricks – hypothèse : temps d’exécution = 10 minutes | 10 minutes d’exécution de l’activité de pipeline externe |

**Prix total du scénario : 0,16916 $**

- Opérations Data Factory = **0,00012 $**
  - Lecture/écriture = 11 \* 0,00001 = 0,00011 $ [1 lecture/écriture = 0,50 / 50 000 = 0,00001]
  - Monitoring = 3 \* 0,000005 = 0,00001 $ [1 monitoring = 0,25 $/ 50 000 = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,16904 $**
  - Exécutions d’activité = 0,001 \* 3 = 0,003 $ [1 exécution = 1 $ / 1 000 = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)
  - Activité du pipeline externe = 0,000041 $ (au prorata de 10 minutes de durée d’exécution, à 0,00025 $/h par Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copier des données et les transformer toutes les heures avec des paramètres dynamiques

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure et les transformer avec Azure Databricks (avec des paramètres dynamiques dans le script) selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3 et un jeu de données de sortie pour les données sur le Stockage Azure.
2. Une activité de recherche pour transmettre dynamiquement des paramètres au script de transformation.
3. Une activité Azure Databricks pour la transformation de données.
4. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

![Scénario 3](media/pricing-concepts/scenario3.png)

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 3 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 4 exécutions d’activité (1 pour l’exécution du déclencheur, 3 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 4 enregistrements d’exécution de monitoring retentés (1 pour l’exécution du pipeline, 3 pour l’exécution d’activité) |
| Exécution de l’activité de recherche – hypothèse : temps d’exécution = 1 minute | 1 minute d’exécution de l’activité de pipeline |
| Exécution de l’activité Databricks – hypothèse : temps d’exécution = 10 minutes | 10 minutes d’exécution de l’activité de pipeline externe |

**Prix total du scénario : 0,17020 $**

- Opérations Data Factory = **0,00013 $**
  - Lecture/écriture = 11 \* 0,00001 = 0,00011 $ [1 lecture/écriture = 0,50 / 50 000 = 0,00001]
  - Monitoring = 4 \* 0,000005 = 0,00002 $ [1 monitoring = 0,25 $/ 50 000 = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,17007 $**
  - Exécutions d’activité = 0,001 \* 4 = 0,004 $ [1 exécution = 1 $ / 1 000 = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)
  - Activité du pipeline = 0,00003 (au prorata d’1 minute de durée d’exécution, à 0,002 $/h par Azure Integration Runtime)
  - Activité du pipeline externe = 0,000041 $ (au prorata de 10 minutes de durée d’exécution, à 0,00025 $/h par Azure Integration Runtime)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez les tarifs associés à Azure Data Factory, vous pouvez vous lancer !

- [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Présentation du service Azure Data Factory](introduction.md)

- [Création visuelle dans Azure Data Factory](author-visually.md)