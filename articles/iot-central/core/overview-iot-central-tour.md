---
title: Visite guidée de l’interface utilisateur d’Azure IoT Central | Microsoft Docs
description: Familiarisez-vous avec les parties principales de l’interface utilisateur d’Azure IoT Central que vous utilisez pour créer, gérer et utiliser votre solution IoT.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: cffd8e0347f501e82ca6e62d5c120b232869cbc0
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653318"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Visite guidée de l’interface utilisateur d’Azure IoT Central

Cet article vous présente l’interface utilisateur d’Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour créer, gérer et utiliser une application IoT Central et ses appareils connectés.

## <a name="iot-central-homepage"></a>Page d’accueil d’IoT Central

La page [Page d’accueil d’IoT Central](https://apps.azureiotcentral.com/) vous permet d’en savoir plus sur les dernières nouvelles et fonctionnalités disponibles sur IoT Central, de créer des applications, et de visualiser et lancer vos applications existantes.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Page d’accueil d’IoT Central":::

### <a name="create-an-application"></a>Créer une application

Dans la section **Créer**, vous pouvez parcourir la liste des modèles IoT Central sectoriels pour démarrer rapidement, ou bien partir de zéro en utilisant un modèle d’application personnalisée.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Page de génération d’IoT Central":::

Pour plus d’informations, consultez le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Lancer votre application

Vous lancez votre application IoT Central en accédant à l’URL que vous avez choisie lors de la création de l’application. Vous pouvez également consulter la liste de toutes les applications auxquelles vous avez accès dans le [gestionnaire d’applications IoT Central](https://apps.azureiotcentral.com/myapps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Gestionnaire d’applications IoT Central":::

## <a name="navigate-your-application"></a>Parcourir votre application

Une fois que vous êtes dans votre application IoT, utilisez le volet gauche pour accéder aux différentes fonctionnalités. Vous pouvez développer ou réduire le volet de gauche en sélectionnant l’icône représentant trois lignes située en haut du volet :

> [!NOTE]
> Les éléments affichés dans le volet gauche varient en fonction de votre rôle d’utilisateur. Apprenez-en plus sur [la gestion des utilisateurs et des rôles](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="volet gauche":::

  :::column-end:::
  :::column span="2":::
     **Tableau de bord** affiche tous les tableaux de bord d’application et personnels. 
     
     **Appareils** vous permet de gérer tous vos appareils.

     **Groupes d’appareils** vous permet d’afficher et de créer des collections d’appareils spécifiées par une requête. Les groupes d’appareils sont utilisés dans l’application pour effectuer des opérations en bloc.

     **Règles** vous permet de créer et de modifier des règles pour superviser vos appareils. Les règles sont évaluées en fonction des données des appareils et déclenchent des actions personnalisables.

     **Analytique** expose des fonctionnalités puissantes pour analyser les tendances historiques et mettre en corrélation différentes télémétries provenant de vos appareils.

     **Travaux** vous permet de gérer vos appareils à grande échelle en effectuant des opérations en bloc.

     **Modèles d’appareil** vous permet de créer et gérer les caractéristiques des appareils qui se connectent à votre application.

     **Exportation de données** vous permet de configurer une exportation continue vers des services externes comme un stockage et des files d’attente.

     **Administration** vous permet de gérer les paramètres, la personnalisation, la facturation, les utilisateurs et les rôles de votre application.

     **Mes applications** vous permet de revenir au gestionnaire d’applications IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Recherche, aide, thème et support

Le menu supérieur s’affiche sur chaque page :

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="Barre d’outils d’IoT Central":::

* Pour rechercher des appareils, entrez une valeur de **recherche**.
* Pour modifier la langue de l’interface utilisateur ou le thème, choisissez l’icône des **paramètres**. En savoir plus sur la [gestion de vos préférences d’application](howto-manage-preferences.md)
* Pour obtenir de l’aide et un support, choisissez la liste déroulante **Aide** pour obtenir une liste des ressources. Vous pouvez [obtenir des informations sur votre application](howto-faq.yml#how-do-i-get-information-about-my-application-) à partir du lien **À propos de votre application**. Dans une application relevant du plan tarifaire gratuit, les ressources de support incluent un accès à la [conversation en direct](howto-show-hide-chat.md).
* Pour vous déconnecter de l’application, choisissez l’icône **Compte**.

Vous pouvez choisir entre un thème clair ou un thème foncé pour l’interface utilisateur :

> [!NOTE]
> La possibilité de choisir entre des thèmes clairs et foncés n’est pas disponible si votre administrateur a configuré un thème personnalisé pour l’application.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Capture d’écran d’IoT Central - Choisir un thème.":::

### <a name="dashboard"></a>tableau de bord

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Capture d’écran du tableau de bord IoT Central.":::

* **Tableau de bord** est la première page que vous voyez quand vous vous connectez à votre application IoT Central. Vous pouvez créer et personnaliser plusieurs tableaux de bord d’application. En savoir plus sur l’[ajout de vignettes à votre tableau de bord](howto-manage-dashboards.md)

* Vous pouvez aussi créer des tableaux de bord personnels pour superviser ce qui vous intéresse. Pour en savoir plus, consultez l’article [Créer des tableaux de bord personnels Azure IoT Central](howto-manage-dashboards.md).

### <a name="devices"></a>Appareils

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Capture d’écran de la page Appareils.":::

Cette page montre les appareils de votre application IoT Central, regroupés par _modèle d’appareil_.

* Un modèle d’appareil définit un type d’appareil pouvant se connecter à votre application.
* Un appareil représente un appareil réel ou simulé dans votre application.

### <a name="device-groups"></a>Groupes d’appareils

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Page Groupe d’appareils":::

Cette page vous permet de créer et de visualiser des groupes d’appareils dans votre application IoT Central. Vous pouvez utiliser des groupes d’appareils pour effectuer des opérations en bloc dans votre application ou pour analyser des données. Pour plus d’informations, consultez l’article [Utiliser des groupes d’appareils dans votre application Azure IoT Central](tutorial-use-device-groups.md).

### <a name="rules"></a>Règles
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Capture d’écran de la page Règles.":::

Cette page vous permet d’afficher et de créer des règles basées sur les données des appareils. Quand une règle est déclenchée, elle peut déclencher une ou plusieurs actions, comme l’envoi d’un e-mail ou l’appel d’un webhook. Pour en savoir plus, consultez le didacticiel [Configuration des règles](tutorial-create-telemetry-rules.md).

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Capture d’écran de la page Analytique.":::

Analytique expose des fonctionnalités puissantes pour analyser les tendances historiques et mettre en corrélation différentes télémétries provenant de vos appareils. Pour en savoir plus, consultez l’article [Créer des analytiques pour votre application Azure IoT Central](howto-create-analytics.md).

### <a name="jobs"></a>travaux

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Page Travaux":::

Cette page vous permet d’afficher et de créer des travaux qui peuvent être utilisés pour des opérations de gestion des appareils en bloc sur vos appareils. Vous pouvez mettre à jour les propriétés de l’appareil, ses paramètres et exécuter des commandes sur des groupes d’appareils. Pour plus d’informations, consultez l’article [Exécuter une tâche](howto-manage-devices-in-bulk.md).

### <a name="device-templates"></a>Modèles d’appareil

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Capture d’écran des modèles d’appareils.":::

La page Modèles d’appareils vous permet d’afficher et de créer des modèles d’appareil dans l’application. Pour plus d’informations, consultez le didacticiel [Définir un nouveau type d’appareil dans votre application Azure IoT Central](howto-set-up-template.md).

### <a name="data-export"></a>Exportation de données

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Exportation de données":::

Exportation de données vous permet de configurer des flux de données vers des systèmes externes. Pour en savoir plus, consultez l’article [Exporter vos données dans Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Capture d’écran de l’administration d’IoT.":::

La page Administration vous permet de configurer et de personnaliser votre application IoT Central. Ici, vous pouvez modifier le nom de votre application, l’URL, les thèmes, gérer les utilisateurs et les rôles, créer des jetons d’API et exporter votre application. Pour plus d’informations, consultez l’article [Administrer votre application Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble de Azure IoT Central et vous êtes familiarisé avec la mise en page de l’interface utilisateur. L’étape suivante suggérée consiste à effectuer le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).
