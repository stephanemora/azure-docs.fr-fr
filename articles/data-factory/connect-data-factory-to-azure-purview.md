---
title: Connecter une fabrique de données à Azure Purview
description: En savoir plus sur la connexion d’une fabrique de données à Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/24/2021
ms.openlocfilehash: e38c990622806e5e769626acb84377fc468a25a2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966503"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connecter Data Factory à Azure Purview (préversion)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) est un service unifié de gouvernance des données qui vous aide à gérer et à régir vos données locales, multiclouds et SaaS (software-as-a-service). Vous pouvez connecter votre Data Factory à Azure Purview. Cette connexion vous permet d’utiliser Azure Purview pour capturer les données de traçabilité, ainsi que découvrir et explorer les ressources Azure Purview.

## <a name="connect-data-factory-to-azure-purview"></a>Connecter Data Factory à Azure Purview

Vous pouvez connecter Data Factory à Azure Purview de deux façons :

- [Connecter un compte Azure Purview à Data Factory](#connect-to-azure-purview-account-in-data-factory)
- [Inscrire Data Factory dans Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Se connecter au compte Azure Purview dans Data Factory

Vous devez détenir le rôle de **Propriétaire** ou de **Contributeur** dans votre fabrique de données pour vous connecter à un compte Azure Purview.

Pour établir la connexion sur l’interface utilisateur de création de Data Factory :

1. Dans l’IU de création ADF, accédez à **Gestion** -> **Azure Purview**, et sélectionner **Connexion à un compte Purview**. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Capture d’écran de l’inscription d’un compte Purview.":::

2. Choisissez **A partir de l’abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès.

3. Une fois connecté, vous pouvez voir le nom du compte Purview sous l’onglet **Compte Purview**.

Les informations de connexion de Purview sont stockées dans la ressource de la fabrique de données, comme ci-dessous. Pour établir la connexion de manière programmatique, vous pouvez mettre à jour la fabrique de données et ajouter les paramètres `purviewConfiguration`.

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

### <a name="register-data-factory-in-azure-purview"></a>Inscrire Data Factory dans Azure Purview

Pour savoir comment inscrire Data Factory dans Azure Purview, consultez [Guide pratique pour connecter Azure Data Factory et Azure Purview](../purview/how-to-link-azure-data-factory.md).

## <a name="set-up-authentication"></a>Configurer l’authentification

L’identité managée de la fabrique de données est utilisée pour authentifier les opérations push de traçabilité de la fabrique de données vers Purview. 

- Pour un compte Purview créé **le 18 août 2021 ou après**, accordez le rôle **Curateur de données** à l’identité managée de la fabrique de données sur votre **collection racine** Purview. En savoir plus sur le [Contrôle d’accès dans Azure Purview](../purview/catalog-permissions.md) et comment [Ajouter des rôles et restreindre l’accès par le biais de regroupements](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

    Lors de la connexion de la fabrique de données à Purview sur l’interface utilisateur de création, ADF tente d’ajouter automatiquement une telle attribution de rôle. Si vous avez un rôle **Administrateurs de collection** sur la collection racine Purview, cette opération s’effectue avec succès.

- Pour un compte Purview créé **avant le 18 août 2021**, accordez à l’identité managée de la fabrique de données le rôle Azure [**Curateur de données Purview**](../role-based-access-control/built-in-roles.md#purview-data-curator) intégré à votre compte Purview. En savoir plus sur le [Contrôle d’accès dans Azure Purview - Autorisations héritées](../purview/catalog-permissions.md#legacy-permission-guide).

    Lors de la connexion de la fabrique de données à Purview sur l’interface utilisateur de création, ADF tente d’ajouter automatiquement une telle attribution de rôle. Si vous avez le rôle intégré Azure **Propriétaire** ou **Administrateur de l’accès utilisateur** sur le compte Purview, cette opération est effectuée avec succès.

L’avertissement ci-dessous peut apparaître si vous disposez du privilège de lecture des informations d’attribution de rôle Purview et que le rôle requis n’est pas accordé. Pour vous assurer que la connexion est correctement configurée pour l’envoi (push) de traçabilité de pipeline, accédez à votre compte Purview et vérifiez si le rôle **Curateur de données Purview** est accordé à l’identité managée de la fabrique de données. Si ce n’est pas le cas, ajoutez manuellement l’attribution de rôle.

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Capture d’écran de l’avertissement lors de l’inscription d’un compte Purview.":::

## <a name="report-lineage-data-to-azure-purview"></a>Rapporter les données de traçabilité à Azure Purview

Une fois que vous avez connecté la fabrique de données à un compte Purview, lorsque vous exécutez des pipelines, Data Factory envoie les informations de traçabilité au compte Purview. Pour plus d’informations sur les fonctionnalités prises en charge, consultez [Activités Azure Data Factory prises en charge](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Pour une procédure pas à pas de bout en bout, reportez-vous au [Didacticiel : transmettre les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Découvrir et explorer des données avec Purview

Une fois que vous avez connecté la fabrique de données à un compte Purview, vous pouvez utiliser la barre de recherche située en haut au centre de l’IU de création Data Factory pour rechercher des données et effectuer des actions. En savoir plus sur [Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md)

[Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

[Guide de l’utilisateur sur la traçabilité du Data Catalog dans Azure Purview](../purview/catalog-lineage-user-guide.md)