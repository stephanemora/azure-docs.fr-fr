---
title: 'Tutoriel : Utilisation d’Azure App Configuration pour gérer les indicateurs de fonctionnalités| Microsoft Docs'
description: Dans ce tutoriel, vous allez découvrir comment gérer les indicateurs de fonctionnalités séparément à partir de votre application à l’aide d’Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412288"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Didacticiel : Gérer les indicateurs de fonctionnalités dans Azure App Configuration

Vous pouvez stocker tous les indicateurs de fonctionnalités dans Azure App Configuration et les gérer à partir d’un emplacement unique. Il propose une interface utilisateur de portail, appelé **Gestionnaire de fonctionnalités**, qui est conçu spécifiquement pour les indicateurs de fonctionnalités. De plus, App Configuration prend en charge le schéma de données d’indicateurs de fonctionnalités .NET Core en mode natif.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Définir et gérer des indicateurs de fonctionnalités dans App Configuration.
> * Accéder à des indicateurs de fonctionnalités à partir de votre application.

## <a name="create-feature-flags"></a>Créer des indicateurs de fonctionnalités

Le **Gestionnaire de fonctionnalités** dans le portail Azure pour App Configuration fournit une interface utilisateur pour la création et la gestion des indicateurs de fonctionnalités que vous utilisez dans votre application. Effectuez les étapes suivantes pour ajouter un nouvel indicateur de fonctionnalité.

1. Sélectionnez **Gestionnaire de fonctionnalités** > **+ Créer** pour ajouter un indicateur de fonctionnalité.

    ![Liste d’indicateurs de fonctionnalités](./media/azure-app-configuration-feature-flags.png)

2. Entrez un nom de clé unique pour l’indicateur de fonctionnalité. Vous avez besoin de ce nom pour référencer l’indicateur dans votre code.

3. Affectez éventuellement à l’indicateur de fonctionnalité une description plus conviviale.

4. Définissez l’état initial de l’indicateur de fonctionnalité. Le plus souvent, il s’agit simplement de *On* ou *Off*.

    ![Créer un indicateur de fonctionnalité](./media/azure-app-configuration-feature-flag-create.png)

5. Quand l’état est *On*, spécifiez éventuellement une condition supplémentaire afin de le qualifier avec **Ajouter un filtre**. Entrez une clé de filtre intégrée ou personnalisée, et associez des paramètre(s). Parmi les filtres intégrés figurent :

    | Clé | Paramètres JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100 percent} |
    | Microsoft.TimeWindow | {"Start": UTC time, "End": UTC time} |

    ![Filtre d’indicateur de fonctionnalité](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Mettre à jour l’état des indicateurs de fonctionnalités

Effectuez les étapes suivantes pour changer la valeur d’état d’un indicateur de fonctionnalité.

1. Sélectionnez **Gestionnaire de fonctionnalités**.

2. Cliquez sur **...** > **Modifier** à droite d’un indicateur de fonctionnalité que vous souhaitez modifier.

3. Définissez le nouvel état de l’indicateur de fonctionnalité.

## <a name="access-feature-flags"></a>Accéder à des indicateurs de fonctionnalités

Les indicateurs de fonctionnalités créés par le **Gestionnaire de fonctionnalités** sont stockés et récupérés en tant que valeurs de clés régulières. Ils sont conservés sous un préfixe d’espace de noms spécial *.appconfig.featureflag*. Vous pouvez afficher les valeurs de clés sous-jacentes à l’aide de l’**Explorateur de configuration**. Votre application peut les récupérer à l’aide de fournisseurs de configuration App Configuration, de kits SDK, d’extensions de ligne de commande et d’API REST.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment gérer les indicateurs de fonctionnalités et leurs états à l’aide d’App Configuration. Pour plus d’informations sur la prise en charge de la gestion des fonctionnalités dans App Configuration et ASP.NET Core, consultez les ressources suivantes.

* [Utiliser des indicateurs de fonctionnalités dans une application ASP.NET Core](./use-feature-flags-dotnet-core.md)
