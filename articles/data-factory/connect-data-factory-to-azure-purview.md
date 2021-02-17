---
title: Connecter une fabrique de données à Azure Purview
description: En savoir plus sur la connexion d’une fabrique de données à Azure Purview
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: c42eb8cac283ce096c42c9e5d4b7eac9a3e12625
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364373"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connecter Data Factory à Azure Purview (préversion)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article explique comment connecter Data Factory à Azure Purview et comment signaler la traçabilité des données des activités Azure Data Factory Copier des données, Flux de données et Exécuter le package SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Connecter Data Factory à Azure Purview
Azure Purview est un nouveau service cloud qui permet aux utilisateurs de données de gérer de manière centralisée la gouvernance des données de leur patrimoine de données dans l’ensemble des environnements cloud et locaux. Vous pouvez connecter votre fabrique de données à Azure Purview, et cette connexion vous permet d’utiliser Azure Purview pour capturer des données de traçabilité des activités Copier, Flux de données et Exécuter le package SSIS. Vous pouvez connecter Data Factory à Azure Purview de deux façons :
### <a name="register-azure-purview-account-to-data-factory"></a>Inscrire un compte Azure Purview sur Data Factory
1. Dans le portail ADF, accédez à **Gérer** -> **Azure Purview**. Sélectionnez **Se connecter à un compte Purview**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Capture d’écran de l’inscription d’un compte Purview.":::
2. Vous pouvez choisir **Depuis un abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès. 
3. Une fois connecté, vous devez être en mesure de voir le nom du compte Purview sous l’onglet **Compte Purview**. 
4. Vous pouvez utiliser la barre de recherche en haut au centre du portail Azure Data Factory pour rechercher des données. 

Si vous voyez un avertissement dans le portail Azure Data Factory après avoir inscrit le compte Azure Purview dans Data Factory, suivez les étapes ci-dessous pour corriger le problème :

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Capture d’écran de l’avertissement lors de l’inscription d’un compte Purview.":::

1. Accédez à Portail Azure et recherchez votre fabrique de données. Choisissez la section « Balises » et vérifiez s’il existe une balise nommée **catalogUri**. Si ce n’est pas le cas, déconnectez et reconnectez le compte Azure Purview dans le portail ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Capture d’écran des balises lors de l’inscription d’un compte Purview.":::

2. Vérifiez si l’autorisation est accordée pour l’inscription d’un compte Azure Purview dans Data Factory. Voir [Guide pratique pour connecter Azure Data Factory et Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection).

### <a name="register-data-factory-in-azure-purview"></a>Inscrire Data Factory dans Azure Purview
Pour savoir comment inscrire Data Factory dans Azure Purview, consultez [Guide pratique pour connecter Azure Data Factory et Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Rapporter les données de traçabilité à Azure Purview
Lorsque les clients exécutent une activité Copy, Dataflow ou Exécuter le Package SSIS dans Azure Data Factory, ils peuvent obtenir la relation de dépendance et disposer d’une vue d’ensemble générale de l’intégralité du processus du workflow entre les sources de données et la destination.
Pour savoir comment collecter la traçabilité des données à partir d’Azure Data Factory, consultez [Traçabilité des données de Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Étapes suivantes
[Guide de l’utilisateur sur la traçabilité Catalog](../purview/catalog-lineage-user-guide.md)

[Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](turorial-push-lineage-to-purview.md)