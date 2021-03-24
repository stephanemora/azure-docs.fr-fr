---
title: Visite guidée de l’interface utilisateur de Azure IoT Central | Microsoft Docs
description: Familiarisez-vous avec les zones clés de l’interface utilisateur d’Azure IoT Central que vous utilisez pour créer, gérer et utiliser votre solution IoT.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 569a1365e73acbc2fdaf351f2e2cff21181241e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523460"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Visite guidée de l’interface utilisateur de Azure IoT Central

Cet article vous présente l’interface utilisateur de Microsoft Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour créer, gérer et utiliser une solution Azure IoT Central et ses appareils connectés.

En tant que _créateur de solution_, vous utilisez l’interface utilisateur Azure IoT Central pour définir votre solution Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour :

* Définir les types d’appareils qui se connectent à votre solution.
* Configurer les règles et les actions pour vos appareils. 
* Personnaliser l’interface utilisateur pour un _opérateur_ qui utilise votre solution.

En tant qu’_opérateur_, vous utilisez l’interface utilisateur Azure IoT Central pour gérer votre solution Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour :

* Surveiller vos appareils.
* Configurer vos appareils.
* Dépanner et corrige les problèmes avec vos appareils.
* Approvisionner de nouveaux appareils.

## <a name="iot-central-homepage"></a>Page d’accueil d’IoT Central

La page [Page d’accueil IoT Central](https://aka.ms/iotcentral-get-started) vous permet d’en savoir plus sur les dernières nouvelles et fonctionnalités disponibles de IoT Central, de créer des applications et d’afficher et lancer votre application existante.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Page d’accueil d’IoT Central":::

### <a name="create-an-application"></a>Créer une application

Dans la section de création, vous pouvez parcourir la liste des modèles IoT Central sectoriels pour démarrer rapidement, ou en créer une à l’aide d’un modèle d’application personnalisée.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Page de génération d’IoT Central":::

Pour plus d’informations, consultez le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Lancer votre application

Vous pouvez lancer votre application IoT Central en accédant à l’URL que vous ou votre créateur de solution avez choisie lors de la création de l’application. Vous pouvez également consulter la liste de toutes les applications auxquelles vous avez accès dans le [gestionnaire d’applications IoT Central](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Gestionnaire d’applications IoT Central":::

## <a name="navigate-your-application"></a>Parcourir votre application

Une fois que vous êtes dans votre application IoT, utilisez le volet gauche pour accéder aux différentes zones. Vous pouvez développer ou réduire le volet de gauche en sélectionnant l’icône représentant trois lignes située en haut du volet :

> [!NOTE]
> Les éléments affichés dans le volet gauche varient en fonction de votre rôle d’utilisateur. Apprenez-en plus sur [la gestion des utilisateurs et des rôles](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="volet gauche":::

  :::column-end:::
  :::column span="2":::
     La page **Tableau de bord** affiche le tableau de bord de votre application. En tant que *créateur de solutions*, vous pouvez personnaliser le tableau de bord global pour vos opérateurs. En fonction de leur rôle d’utilisateur, les opérateurs peuvent également créer leurs propres tableaux de bord personnels.
     
     **Appareils** vous permet de gérer vos appareils connectés, réels et simulés.

     **Groupes d’appareils** vous permet d’afficher et de créer des collections logiques d’appareils spécifiées par une requête. Vous pouvez enregistrer cette requête et utiliser des groupes d’appareils à l’aide de l’application pour effectuer des opérations en bloc.

     **Règles** vous permet de créer et de modifier des règles pour surveiller vos appareils. Les règles sont évaluées en fonction des données de télémétrie des appareils et déclenchent des actions personnalisables.

     **Analytics** vous permet de créer des vues personnalisées sur des données d’appareils pour dégager des insights à partir de votre application.

     **Tâches** vous permet de gérer vos appareils à grande échelle en exécutant des opérations en bloc.

     **Modèles d’appareil** vous permet de créer et gérer les caractéristiques des appareils qui se connectent à votre application.

     **Exportation de données** vous permet de configurer une exportation continue vers d’autres services externes, comme le stockage et les files d’attente.

     **Administration** vous permet de gérer les paramètres, la personnalisation, la facturation, les utilisateurs et les rôles de votre application.

     **IoT Central** permet aux *administrateurs* de revenir au gestionnaire d’applications de IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Recherche, aide, thème et support

Le menu supérieur s’affiche sur chaque page :

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="Barre d’outils d’IoT Central":::

* Pour rechercher des modèles d’appareils et des appareils, entrez une valeur de **recherche**.
* Pour modifier la langue de l’interface utilisateur ou le thème, choisissez l’icône des **paramètres**. En savoir plus sur la [gestion de vos préférences d’application](howto-manage-preferences.md)
* Pour obtenir de l’aide et un support, choisissez la liste déroulante **Aide** pour obtenir une liste des ressources. Vous pouvez [obtenir des informations sur votre application](./howto-get-app-info.md) à partir du lien **À propos de votre application**. Dans une application relevant du plan tarifaire gratuit, les ressources de support incluent un accès à la [conversation en direct](howto-show-hide-chat.md).
* Pour vous déconnecter de l’application, choisissez l’icône **Compte**.

Vous pouvez choisir entre un thème clair ou un thème foncé pour l’interface utilisateur :

> [!NOTE]
> La possibilité de choisir entre des thèmes clairs et foncés n’est pas disponible si votre administrateur a configuré un thème personnalisé pour l’application.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Capture d’écran d’IoT Central - Choisir un thème.":::

### <a name="dashboard"></a>tableau de bord

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Capture d’écran du tableau de bord IoT Central.":::

* Le tableau de bord est la première page que vous voyez lorsque vous vous connectez à votre application Azure IoT Central. En tant que *créateur de solution*, vous pouvez créer et personnaliser plusieurs tableaux de bord d’application globaux pour d’autres utilisateurs. En savoir plus sur l’[ajout de vignettes à votre tableau de bord](howto-add-tiles-to-your-dashboard.md)

* En tant qu’*opérateur*, si votre rôle d’utilisateur le permet, vous pouvez créer des tableaux de bord personnels pour surveiller ce qui vous intéresse. Pour en savoir plus, consultez l’article [Créer des tableaux de bord personnels Azure IoT Central](howto-create-personal-dashboards.md).

### <a name="devices"></a>Appareils

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Capture d’écran de la page Appareils.":::

La page de l’explorateur affiche les _appareils_ dans votre application Azure IoT Central, groupés par _modèle d’appareil_. 

* Un modèle d’appareil définit un type d’appareil pouvant se connecter à votre application.
* Un appareil représente un appareil réel ou simulé dans votre application.

Pour en savoir plus, consultez le démarrage rapide [Surveiller vos appareils](./quick-monitor-devices.md). 

### <a name="device-groups"></a>Groupes d’appareils

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Page Groupe d’appareils":::

Un groupe d’appareils est une collection d’appareils associés. Un *créateur de solution* définit une requête pour identifier les appareils inclus dans un groupe d’appareils. Vous utilisez des groupes d’appareils pour effectuer des opérations en bloc dans votre application. Pour plus d’informations, consultez l’article [Utiliser des groupes d’appareils dans votre application Azure IoT Central](tutorial-use-device-groups.md).

### <a name="rules"></a>Règles
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Capture d’écran de la page Règles.":::

La page Règles vous permet de définir des règles en fonction des données de télémétrie, de l’état ou des événements de l’appareil. Quand une règle est déclenchée, elle peut déclencher une ou plusieurs actions, telles que l’envoi d’un e-mail, l’envoi d’une notification à un système externe via des alertes Webhook, etc. Pour en savoir plus, consultez le didacticiel [Configuration des règles](tutorial-create-telemetry-rules.md). 

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Capture d’écran de la page Analytique.":::

L’analytique vous permet de créer des vues personnalisées sur des données d’appareils pour dériver des insights de votre application. Pour en savoir plus, consultez l’article [Créer des analytiques pour votre application Azure IoT Central](howto-create-analytics.md).

### <a name="jobs"></a>travaux

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Page Travaux":::

La page Tâches vous permet d’effectuer des opérations de gestion d’appareils en bloc sur vos appareils. Vous pouvez mettre à jour les propriétés de l’appareil, ses paramètres et exécuter des commandes sur des groupes d’appareils. Pour plus d’informations, consultez l’article [Exécuter une tâche](howto-run-a-job.md).

### <a name="device-templates"></a>Modèles d’appareil

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Capture d’écran des modèles d’appareils.":::

La page des modèles d’appareil est l’endroit où un générateur crée et gère les modèles d’appareil dans l’application. Un modèle d’appareil spécifie les caractéristiques de ce dernier, notamment :

* les mesures de télémétrie, d’état et d’événement
* Propriétés
* Commandes
* Les vues

Le *créateur de solutions* peut également créer des formulaires et des tableaux de bord qui permettent aux opérateurs de gérer des appareils.

Pour plus d’informations, consultez le didacticiel [Définir un nouveau type d’appareil dans votre application Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Exportation de données

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Exportation de données":::

Exportation de données vous permet de configurer des flux de données, comme la télémétrie, de l’application à des systèmes externes. Pour en savoir plus, consultez l’article [Exporter vos données dans Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administration

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Capture d’écran de l’administration d’IoT.":::

La page Administration vous permet de configurer et de personnaliser votre application IoT Central. Ici, vous pouvez modifier le nom de votre application, l’URL, les thèmes, gérer les utilisateurs et les rôles, créer des jetons d’API et exporter votre application. Pour plus d’informations, consultez l’article [Administrer votre application Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble de Azure IoT Central et vous êtes familiarisé avec la mise en page de l’interface utilisateur. L’étape suivante suggérée consiste à effectuer le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).
