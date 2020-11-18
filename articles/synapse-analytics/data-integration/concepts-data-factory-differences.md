---
title: Différences par rapport à Azure Data Factory
description: Découvrez comment les fonctionnalités d’intégration de données d’Azure Synapse Analytics diffèrent de celles d’Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357647"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Intégration de données dans Azure Synapse Analytics par rapport à Azure Data Factory

Dans Azure Synapse Analytics, les fonctionnalités d’intégration de données telles que des pipelines et flux de données Synapse sont basées sur celles d’Azure Data Factory. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Data Factory](../../data-factory/introduction.md). Presque toutes les fonctionnalités sont identiques ou similaires, et la documentation est partagée entre les deux services. Cet article met en évidence et identifie les différences actuelles entre Azure Data Factory et Azure synapse.

Pour savoir si une fonctionnalité ou un article Azure Data Factory s’applique à Azure Synapse, vérifiez le moniker en haut de l’article.

![S’applique au moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "S’applique au moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Fonctionnalités de Azure Data Factory non planifiées pour Azure Synapse

Les fonctionnalités suivantes sont disponibles dans Azure Data Factory, mais ne sont pas planifiées pour Azure synapse.

* **Lift-and-shift de packages SSIS :** Dans Azure Data Factory, vous avez la possibilité d’élever et déplacer des packages SSIS à l’aide du runtime d’intégration SSIS. Le runtime d’intégration SSIS et l’activité Exécuter le Package SSIS ne sont pas disponibles dans les espaces de travail synapse. 
* **Durée de vie :** La durée de vie est un paramètre du runtime d’intégration Azure qui permet au cluster Spark dans le flux de données de mappage de rester *chaud* pendant un certain temps après l’achèvement d’un flux de données. Cette fonctionnalité n’est pas disponible dans les espaces de travail Synapse.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Fonctionnalités Azure Synapse non prises en charge dans Azure Data Factory

Les fonctionnalités suivantes sont disponibles dans Azure Synapse, mais ne sont pas planifiées pour Azure Data Factory.

* **Surveillance des travaux Spark pour le mappage des flux de données :** Dans Synapse, le moteur Spark est contenu dans l’abonnement de l’utilisateur afin que les utilisateurs puissent consulter des journaux Spark détaillés. Dans Azure Data Factory, l’exécution du travail se produit sur un cluster Spark géré par Azure Data Factory, et ces informations ne sont pas disponibles. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Fonctionnalités d’Azure Data Factory qui se comportent différemment dans Synapse

Les fonctionnalités suivantes se comportent différemment ou n’existent pas actuellement dans Azure synapse. 

* **Flux de wrangling data :** L’activité de Flux de wrangling data est disponible uniquement dans Azure Data Factory pour l’instant.
* **La galerie de modèles de solution :** Dans Azure Data Factory, les utilisateurs peuvent trouver des modèles de pipeline dans la galerie de modèles de solution. Dans les espaces de travail Synapse, le centre de connaissances contient un ensemble différent de modèles, ainsi que des jeux de données et des scripts SQL supplémentaires. 
* **Intégration Git et solution CI/CD native :** Actuellement, un espace de travail Synapse ne peut pas se connecter à un référentiel Git ni suivre le même processus d’intégration et de livraison en continu qu’Azure Data Factory.
* **Intégration avec Azure Monitor :** Les espaces de travail Synapse ne s’intègrent pas à Azure Monitor à la manière d’Azure Data Factory.
* **Configuration du runtime d’intégration hybride :** Au sein d’un espace de travail Synapse, un utilisateur ne peut pas avoir à la fois un runtime d’intégration de VNet managé et un runtime d’intégration Azure. Cette capacité est prise en charge dans Azure Data Factory.
* **Partage du runtime d’intégration :** Les runtimes d’intégration auto-hébergés ne peuvent pas être partagés entre des espaces de travail Synapse. Cette capacité est prise en charge dans Azure Data Factory.
* **Les runtimes d’intégration inter-régions pour les flux de données :** Les flux de données ne peuvent pas s’exécuter sur des runtimes d’intégration dans différentes régions d’un espace de travail Synapse. Cette capacité est prise en charge dans Azure Data Factory.

## <a name="next-steps"></a>Étapes suivantes

Commencez à utiliser l’intégration de données dans votre espace de travail Synapse en découvrant comment [ingérer des données dans un compte Azure Data Lake Storage Gen2](data-integration-data-lake.md).
