---
title: Connecter une fabrique de données à Azure Purview
description: En savoir plus sur la connexion d’une fabrique de données à Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/10/2021
ms.openlocfilehash: 6e4b04d2675d9eac89573741030f8fe0322ab60a
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597653"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connecter Data Factory à Azure Purview (préversion)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) est un service unifié de gouvernance des données qui vous aide à gérer et à régir vos données locales, multiclouds et SaaS (software-as-a-service). Vous pouvez connecter votre Data Factory à Azure Purview. Cette connexion vous permet d’utiliser Azure Purview pour capturer les données de traçabilité, ainsi que découvrir et explorer les ressources Azure Purview.

## <a name="connect-data-factory-to-azure-purview"></a>Connecter Data Factory à Azure Purview

Vous pouvez connecter Data Factory à Azure Purview de deux façons :

- [Connecter un compte Azure Purview à Data Factory](#connect-to-azure-purview-account-in-data-factory)
- [Inscrire Data Factory dans Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Se connecter au compte Azure Purview dans Data Factory

Pour établir la connexion, vous devez détenir le rôle de **Propriétaire** ou de **Contributeur** dans votre Data Factory.

1. Dans l’IU de création ADF, accédez à **Gestion** -> **Azure Purview**, et sélectionner **Connexion à un compte Purview**. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Capture d’écran de l’inscription d’un compte Purview.":::

2. Choisissez **A partir de l’abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès.

3. Une fois connecté, vous pouvez voir le nom du compte Purview sous l’onglet **Compte Purview**.

Lors de la connexion de Data Factory à Purview, l’IU ADF tente également d’accorder le rôle de **Conservateur de données Purview** à l’identité gérée de Data factory sur votre compte Purview. L’identité gérée est utilisée pour authentifier les opérations push de lignage de Data Factory vers Purview. Si vous disposez d’un rôle de **Propriétaire** ou d’**Administrateur d’accès utilisateur** sur le compte Purview, cette opération sera effectuée automatiquement. Si vous voyez un avertissement similaire à ce qui suit, cela signifie que le rôle nécessaire n’est pas accordé :

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Capture d’écran de l’avertissement lors de l’inscription d’un compte Purview.":::

Pour régler ce problème, accédez au portail Azure -> votre compte Purview -> Contrôle d’accès (IAM), vérifiez si le rôle de **Conservateur de données Purview** est accordé dans l’identité managée de Data Factory. Si ce n’est pas le cas, ajoutez manuellement l’attribution de rôle.

### <a name="register-data-factory-in-azure-purview"></a>Inscrire Data Factory dans Azure Purview

Pour savoir comment inscrire Data Factory dans Azure Purview, consultez [Guide pratique pour connecter Azure Data Factory et Azure Purview](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Rapporter les données de traçabilité à Azure Purview

Une fois que vous avez connecté Data Factory à un compte Purview, lorsque vous exécutez l’activité Copier, Flux de données ou Exécuter le package SSIS, vous pouvez obtenir la traçabilité entre les jeux de données créée par le traitement des données et disposer d’un aperçu de haut niveau de l’ensemble du processus de la charge de travail parmi les sources de données et la destination. Pour plus d’informations sur les fonctionnalités prises en charge, consultez [Activités Azure Data Factory prises en charge](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Pour une procédure pas à pas de bout en bout, reportez-vous au [Didacticiel : transmettre les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Découvrir et explorer des données avec Purview

Une fois que vous avez connecté Data Factory à un compte Purview, vous pouvez utiliser la barre de recherche située en haut au centre de l’IU de création Azure Data Factory pour rechercher des données et effectuer des actions. En savoir plus sur [Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md)

[Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

[Guide de l’utilisateur sur la traçabilité du Data Catalog dans Azure Purview](../purview/catalog-lineage-user-guide.md)