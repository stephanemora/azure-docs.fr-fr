---
title: Découvrir et explorer des données dans ADF avec Purview
description: Découvrez comment découvrir et explorer des données dans Azure Data Factory à l’aide de Purview
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386269"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Découvrir et explorer des données dans ADF avec Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans le cadre de cet article, vous allez inscrire un Azure Purview auprès d’une fabrique de données. Cette connexion vous permet de découvrir des ressources Azure Purview et d’interagir avec celles-ci via des fonctionnalités d’ADF. 

Vous pouvez effectuer les tâches suivantes dans ADF : 
- Utiliser la zone de recherche en haut pour rechercher des ressources Purview en fonction de mots clés 
- Comprendre les données en fonction des métadonnées, de la traçabilité, des annotations 
- Connecter ces données à votre fabrique de données avec des services ou des jeux de données liés 

## <a name="prerequisites"></a>Prérequis 
- [Compte Azure Purview](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Connecter un compte Azure Purview à Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Utilisation d’Azure Purview dans Data Factory 

Pour utiliser Azure Purview dans Data Factory, vous devez avoir accès à ce compte Purview. Data Factory passe par votre autorisation Purview. Par exemple, si vous avez un rôle d’autorisation conservateur, vous pourrez modifier les métadonnées analysées par Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Découverte de données : rechercher dans les jeux de données 

Pour découvrir les données inscrites et analysées par Azure Purview, vous pouvez utiliser la barre de recherche en haut au centre du portail Data Factory. Veillez à sélectionner Azure Purview pour rechercher toutes les données de votre organisation. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Capture d’écran d’opérations sur des jeux de données.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Actions que vous pouvez effectuer sur des jeux de données avec des ressources Data Factory 
Vous pouvez créer directement un service lié, un jeu de données ou un flux de données sur les données dans lesquelles vous recherchez via Azure Purview.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Capture d’écran montrant comment vous pouvez créer directement un service lié, un jeu de données ou un dataflow sur les données dans lesquelles vous recherchez via Azure Purview.":::

##  <a name="nextsteps"></a>Étapes suivantes 

- [Inscrire et analyser des ressources Azure Data Factory dans Azure Purview](../purview/register-scan-azure-synapse-analytics.md)
- [Comment rechercher des données dans Azure Purview Data Catalog](../purview/how-to-search-catalog.md)