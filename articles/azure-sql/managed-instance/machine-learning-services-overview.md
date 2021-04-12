---
title: Machine Learning Services dans Azure SQL Managed Instance
description: Cet article fournit une vue d’ensemble ou Machine Learning Services dans Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599559"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services dans Azure SQL Managed Instance

Machine Learning Services est une fonctionnalité d’Azure SQL Managed Instance qui fournit un apprentissage automatique en base de données prenant en charge les scripts Python et R. La fonctionnalité inclut les packages Microsoft Python et R pour l’analyse prédictive haute performance et l’apprentissage automatique. Les données relationnelles peuvent être utilisées dans des scripts au moyen de procédures stockées, de scripts T-SQL contenant des instructions Python ou R ou de code R contenant T-SQL.

## <a name="what-is-machine-learning-services"></a>Qu’est-ce que Machine Learning Services ?

Machine Learning Services dans Azure SQL Managed Instance vous permet d’exécuter des scripts Python et R en base de données. Vous pouvez vous en servir pour préparer et nettoyer des données, effectuer l’ingénierie des fonctionnalités et entraîner, évaluer et déployer des modèles Machine Learning dans une base de données. La fonctionnalité exécute vos scripts là où résident les données, ce qui vous évite d’avoir à transférer les données vers un autre serveur à travers le réseau.

Utilisez Machine Learning Services avec le support R/Python dans Azure SQL Managed Instance pour :

- **Exécuter des scripts R et Python pour la préparation des données et le traitement des données à usage général** : vous pouvez maintenant intégrer vos scripts R/Python à Azure SQL Managed Instance où résident vos données, au lieu de déplacer les données vers un autre serveur pour exécuter des scripts R et Python. Vous pouvez éliminer le besoin de déplacement des données et les problèmes associés à la latence, à la sécurité et à la conformité.

- **Effectuer l'apprentissage des modèles de Machine Learning dans la base de données** : vous pouvez effectuer l'apprentissage des modèles à l’aide de n’importe quel algorithme Open source. Vous pouvez facilement mettre à l'échelle votre formation sur l’ensemble du jeu de données plutôt qu’en vous fiant à des échantillons de jeux de données tirés hors de la base de données.

- **Déployez vos modèles et scripts en production dans des procédures stockées** : les scripts et les modèles formés peuvent être mis en œuvre simplement en les incorporant dans des procédures stockées T-SQL. Les applications se connectant à Azure SQL Managed Instance peuvent tirer parti des prédictions et de l’intelligence de ces modèles en appelant simplement une procédure stockée. Vous pouvez également utiliser la fonction PREDICT T-SQL native pour mettre en œuvre des modèles en vue d’un scoring rapide dans des scénarios de scoring en temps réel hautement simultanés.

Les distributions de base de Python et de R sont incluses dans Machine Learning Services. Vous pouvez installer et utiliser des frameworks et des packages open source comme PyTorch, TensorFlow et scikit-learn, en plus des packages Microsoft [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) et [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) pour Python, et [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapR](/sql/machine-learning/r/ref-r-olapr) et [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) pour R.

## <a name="how-to-enable-machine-learning-services"></a>Comment activer Machine Learning Services

Vous pouvez activer Machine Learning Services dans Azure SQL Managed Instance en activant l’extensibilité à l’aide des commandes SQL suivantes (SQL Managed Instance redémarre et n’est pas disponible pendant quelques secondes) :

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Pour plus d’informations sur la façon dont cette commande affecte les ressources SQL Managed Instance, consultez [Gouvernance des ressources](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Activer Machine Learning Services dans un groupe de basculement

Dans un [groupe de basculement](failover-group-add-instance-tutorial.md), les bases de données système ne sont pas répliquées sur l’instance secondaire (pour plus d’informations, consultez [Limitations des groupes de basculement](../database/auto-failover-group-overview.md#limitations-of-failover-groups)).

Si la Managed Instance que vous utilisez fait partie d’un groupe de basculement, procédez comme suit :

- Exécutez les commandes `sp_configure` et `RECONFIGURE` sur chaque instance du groupe de basculement pour activer Machine Learning Services.

- Installez les bibliothèques R/Python sur une base de données utilisateur plutôt que sur la base de données MASTER.

## <a name="next-steps"></a>Étapes suivantes

- Prenez connaissance des [différences principales de Machine Learning SQL Services dans SQL Server](machine-learning-services-differences.md).
- Pour apprendre à utiliser Python dans Machine Learning Services, consultez [Exécuter des scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Pour apprendre à utiliser R dans Machine Learning Services, consultez [Exécuter des scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Pour plus d’informations sur le machine learning sur d’autres plateformes SQL, consultez la [documentation sur le machine learning SQL](/sql/machine-learning/index).
