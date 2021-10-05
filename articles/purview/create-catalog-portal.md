---
title: 'Démarrage rapide : Créer un compte Purview dans le portail Azure'
description: Ce guide de démarrage rapide explique comment créer un compte Azure Purview et configurer des autorisations pour commencer à l’utiliser.
author: nayenama
ms.author: nayenama
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-portal
ms.openlocfilehash: c1a8b47df487cb3159ee1ac7492cf5eb04c1dec6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217426"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Démarrage rapide : Créer un compte Azure Purview dans le portail Azure

Ce guide de démarrage rapide décrit les étapes de création d’un compte Azure Purview dans le portail Azure et la prise en main du processus de classification, de sécurisation et de découverte de vos données dans Purview.

Azure Purview est un service de gouvernance de données qui vous aide à gérer et régir votre paysage de données. En vous connectant aux données de vos sources locales, multiclouds et SaaS (software-as-a-service), Purview crée un mappage à jour de vos informations. Il identifie et classe les données sensibles, et fournit un lignage de bout en bout. Les consommateurs de données peuvent découvrir des données au sein de votre organisation, et les administrateurs de données sont en mesure d’auditer, de sécuriser et de garantir l’utilisation appropriée de vos données.

Pour plus d’informations sur Purview, [consultez notre page de présentation](overview.md). Pour plus d’informations sur le déploiement de Purview dans votre organisation, [consultez nos bonnes pratiques de déploiement](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="create-an-azure-purview-account"></a>Créer un compte Azure Purview

1. Accédez à la page **Comptes Purview** dans le [portail Azure](https://portal.azure.com).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Capture d’écran montrant la page des comptes Purview dans le portail Azure":::

1. Sélectionnez **Créer** afin de créer un compte Azure Purview.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Capture d’écran avec le bouton de création en évidence pour Azure Purview dans le portail Azure.":::
  
      Vous pouvez aussi aller sur la Place de marché, rechercher **Azure Purview**, et sélectionner **Créer**.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Capture d’écran montrant Azure Purview sur la Place de marché Azure, avec le bouton Créer encadré":::

1. Sur la nouvelle page Créer un compte Purview, sous l’onglet **Général**, sélectionnez l’abonnement Azure dans lequel vous souhaitez créer votre compte Purview.

1. Sélectionnez un **groupe de ressources** existant ou créez-en un pour contenir votre compte Purview.

    Pour plus d’informations sur les groupes de ressources, consultez notre article sur l’[utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

1. Entrez un **nom de compte Purview**. Les espaces et les symboles ne sont pas autorisés.
    Le nom du compte Purview doit être globalement unique. Si vous voyez l’erreur suivante, modifiez le nom du compte Purview et essayez de créer à nouveau.

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="Capture d’écran montrant l’écran Créer un compte Purview avec un nom de compte déjà utilisé, et le message d’erreur mis en évidence.":::

1. Choisissez un **emplacement**.
    La liste affiche uniquement les emplacements qui prennent en charge Purview. L’emplacement que vous choisissez sera la région dans laquelle votre compte Purview et les métadonnées seront stockés. Les sources peuvent être hébergées dans d’autres régions.

      > [!Note]
      > Azure Purview ne prend pas en charge le déplacement de comptes inter-régions. Veillez donc à déployer dans la bonne région. Pour plus d’informations à ce sujet, consultez [Prise en charge des opérations de déplacement pour les ressources](../azure-resource-manager/management/move-support-resources.md).

1. Sélectionnez **Vérifier + créer**, puis **Créer**. La création prend quelques minutes. L’instance de compte Azure Purview nouvellement créée apparaît dans la liste de la page de vos **Comptes Purview**.

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="Capture d’écran montrant l’écran Créer un compte Purview avec le bouton Vérifier + créer en évidence":::

## <a name="open-purview-studio"></a>Ouvrir Purview Studio

Une fois votre compte Azure Purview créé, vous allez utiliser Purview Studio pour y accéder et le gérer. Il existe deux façons d’ouvrir Purview Studio :

* Ouvrir votre compte Purview sur le [portail Azure](https://portal.azure.com). Sélectionnez la vignette « Ouvrir Purview Studio » sur la page Vue d’ensemble.
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Capture d’écran montrant la page de présentation du compte Purview, avec la vignette Purview Studio en évidence.":::

* Vous pouvez également accéder à [https://web.purview.azure.com](https://web.purview.azure.com), sélectionner votre compte Purview et vous connecter à votre espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Purview et à y accéder par le biais de Purview Studio.

Lisez les articles suivants pour apprendre à naviguer dans Purview Studio, à créer une collection et à accorder l’accès à Purview.

* [Utilisation de Purview Studio](use-purview-studio.md)
* [Création d'une collection](quickstart-create-collection.md)
* [Ajouter des utilisateurs à votre compte Azure Purview](catalog-permissions.md)
