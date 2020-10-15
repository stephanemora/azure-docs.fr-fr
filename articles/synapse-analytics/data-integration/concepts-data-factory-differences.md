---
title: Différences par rapport à Azure Data Factory
description: Découvrez comment les fonctionnalités d’intégration de données d’Azure Synapse Analytics diffèrent de celles d’Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91338632"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Intégration de données dans Azure Synapse Analytics par rapport à Azure Data Factory

Dans Azure Synapse Analytics, les fonctionnalités d’intégration de données telles que des pipelines et flux de données Synapse sont basées sur celles d’Azure Data Factory. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Data Factory](../../data-factory/introduction.md). Presque toutes les fonctionnalités sont identiques ou similaires, et la documentation est partagée entre les deux services. Cet article met en évidence et identifie les différences actuelles entre Azure Data Factory et Azure synapse.

Pour savoir si une fonctionnalité ou un article Azure Data Factory s’applique à Azure Synapse, vérifiez le moniker en haut de l’article.

![S’applique au moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "S’applique au moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Fonctionnalités de Azure Data Factory non planifiées pour Azure Synapse

Les fonctionnalités suivantes sont disponibles dans Azure Data Factory, mais ne sont pas planifiées pour Azure synapse.

* Possibilité d’une migration lift-and-shift de packages SSIS.
* Snowflake en tant que récepteur dans l’activité de copie et le flux de données de mappage.
* Paramètre de durée de vie du flux de données de mappage du runtime d’intégration Azure.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Fonctionnalités Azure Synapse non prises en charge dans Azure Data Factory

Les fonctionnalités suivantes sont disponibles dans Azure Synapse, mais ne sont pas planifiées pour Azure Data Factory.

* La surveillance des travaux Spark des flux de données de mappage est disponible uniquement dans Synapse. Dans Synapse, le moteur Spark est contenu dans l’abonnement de l’utilisateur afin que les utilisateurs puissent consulter des journaux Spark détaillés. Dans Azure Data Factory, l’exécution du travail se produit sur un cluster Spark géré par Azure Data Factory. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Fonctionnalités d’Azure Data Factory qui se comportent différemment dans Synapse

Les fonctionnalités suivantes se comportent différemment ou n’existent pas actuellement dans Azure synapse. 

* Flux de wrangling data
* Galerie de modèles de solution
* Intégration Git et solution CI/CD native
* Intégration avec Azure Monitor
* Attribution d’un nouveau nom à des ressources après publication
* Configuration de runtime d’intégration hybride au sein d’un espace de travail Synapse. Un utilisateur ne peut pas avoir à la fois un runtime d’intégration de VNet managé et un runtime d’intégration Azure.
* Partage de runtime d’intégration entre des espaces de travail Synapse

## <a name="next-steps"></a>Étapes suivantes

Commencez à utiliser l’intégration de données dans votre espace de travail Synapse en découvrant comment [ingérer des données dans un compte Azure Data Lake Storage Gen2](data-integration-data-lake.md).
