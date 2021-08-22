---
title: Déployer des modèles en production - Team Data Science Process
description: Guide pratique pour déployer des modèles en production, ce qui leur permet de jouer un rôle actif dans les décisions d’entreprise.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bef5e76372959fb79686fd414c09e7d30431b52
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379489"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Déployer des modèles en production pour jouer un rôle actif dans les décisions d’entreprise

Le déploiement en production permet à un modèle de jouer un rôle actif dans une entreprise. Les prédictions issues d’un modèle déployé peuvent être utilisées pour les décisions d’entreprise.

## <a name="production-platforms"></a>Plateformes de production

Il existe différentes approches et plateformes pour mettre les modèles en production. Voici quelques options :

- [Où déployer des modèles avec Azure Machine Learning](../how-to-deploy-and-where.md)
- [Déploiement d’un modèle dans SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Azure Synapse Analytics](/azure/synapse-analytics/spark/apache-spark-machine-learning-mllib-notebook)

>[!NOTE]
>Avant de procéder au déploiement, vous devez vérifier que le niveau de la latence du modèle est suffisamment faible pour permettre l’utilisation de ce dernier dans l’environnement de production.
>

>[!NOTE]
>Pour un déploiement à l’aide de Microsoft Azure Machine Learning Studio, consultez [Déploiement d’un service web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Test A/B

Lorsque plusieurs modèles sont en production, le [test A/B](https://en.wikipedia.org/wiki/A/B_testing) peut être utilisé pour comparer les performances des modèles. 
 
## <a name="next-steps"></a>Étapes suivantes

Des procédures pas à pas illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente.
