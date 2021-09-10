---
title: 'Démarrage rapide : Créer un compte Azure Purview dans le portail Azure'
description: Ce guide de démarrage rapide explique comment créer un compte Azure Purview et configurer des autorisations pour commencer à l’utiliser.
author: nayenama
ms.author: nayenama
ms.date: 08/18/2021
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 4f0ef5010a0862b1fa5514d83f6570eefa2c4e10
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102530"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Démarrage rapide : Créer un compte Azure Purview dans le portail Azure.

Azure Purview est un outil de gouvernance unifiée des données qui vous aide à gérer votre paysage de données. Ce guide de démarrage rapide décrit les étapes de création d’un compte Azure Purview dans le portail Azure et la prise en main du processus de classification, de sécurisation et de découverte de vos données dans Purview.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être Contributeur ou Propriétaire, ou être administrateur de l’abonnement Azure.

* Votre propre [locataire Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Pas de [Stratégies Azure](../governance/policy/overview.md) empêchant la création de **comptes de stockage** ou d’**espaces de noms Event Hub**. Purview déploie un compte Stockage géré et un Event Hub lors de sa création. Si une politique de blocage existe et doit rester en place, veuillez suivre notre [Guide de marquage des exceptions Purview](create-purview-portal-faq.md) pour préparer votre environnement.

## <a name="create-an-azure-purview-account"></a>Créer un compte Azure Purview

1. Accédez à la page **Comptes Purview** dans le [portail Azure](https://portal.azure.com).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Capture d’écran montrant la page des comptes Purview dans le portail Azure":::

1. Sélectionnez **Créer** afin de créer un compte Azure Purview.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Capture d’écran avec le bouton de création en évidence pour Azure Purview dans le portail Azure.":::
  
      Vous pouvez aussi aller sur la Place de marché, rechercher **Azure Purview**, et sélectionner **Créer**.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Capture d’écran montrant Azure Purview sur la Place de marché Azure, avec le bouton de création en évidence.":::

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
