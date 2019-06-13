---
title: Visite guidée de l’interface utilisateur de Azure IoT Central | Microsoft Docs
description: En tant que générateur, familiarisez-vous avec les zones clés de l’interface utilisateur de Azure IoT Central que vous utilisez pour créer une solution IoT.
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 53306b2087a47f5a61fc2a228e2d1f527127f746
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476314"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Visite guidée de l’interface utilisateur de Azure IoT Central

Cet article vous présente l’interface utilisateur de Microsoft Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour créer, gérer et utiliser une solution Azure IoT Central et ses appareils connectés.

En tant que _générateur_, vous utilisez l’interface utilisateur Azure IoT Central pour définir votre solution Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour :

- Définir les types d’appareils qui se connectent à votre solution.
- Configurer les règles et les actions pour vos appareils.
- Personnaliser l’interface utilisateur pour un _opérateur_ qui utilise votre solution.

En tant qu’_opérateur_, vous utilisez l’interface utilisateur Azure IoT Central pour gérer votre solution Azure IoT Central. Vous pouvez utiliser l’interface utilisateur pour :

- Surveiller vos appareils.
- Configurer vos appareils.
- Dépanner et corrige les problèmes avec vos appareils.
- Approvisionner de nouveaux appareils.

## <a name="use-the-left-navigation-menu"></a>Utiliser le menu de navigation gauche

Utilisez le menu de navigation gauche pour accéder aux différentes zones de l’application. Vous pouvez développer ou réduire la barre de navigation en sélectionnant **<** ou **>**  :

| Menu | Description |
| ---- | ----------- |
| ![Menu de navigation gauche](media/overview-iot-central-tour/navigationbar.png) | <ul><li>Le bouton **Tableau de bord** affiche le tableau de bord de votre application. En tant que créateur, vous pouvez personnaliser le tableau de bord pour vos opérateurs. Les utilisateurs peuvent également créer leurs propres tableaux de bord.</li><li>Le bouton **Device Explorer** liste les appareils simulés et réels associés à chaque modèle d’appareil dans l’application. En tant qu’opérateur, vous utilisez **Device Explorer** pour gérer vos appareils connectés.</li><li>Le bouton **Device Sets** vous permet d’afficher et de créer des ensembles d’appareils. En tant qu’opérateur, vous pouvez créer des ensembles d’appareils comme une collection logique d’appareils spécifiée par une requête.</li><li>Le bouton **Analytique** montre les analytiques dérivées des données de télémétrie de l’appareil pour les appareils et les ensembles d’appareils. En tant qu’opérateur, vous pouvez créer des vues personnalisées sur des données d’appareils pour dégager des insights à partir de votre application.</li><li>Le bouton **Tâches** permet une gestion des appareils en bloc en vous faisant créer et exécuter des tâches pour effectuer des mises à jour à grande échelle.</li><li>Le bouton **Modèles d’appareil** montre les outils qu’un générateur utilise pour créer et gérer des modèles d’appareil.</li><li>Le bouton **Exportation de données continue** sert à un administrateur pour configurer une exportation continue vers d’autres services Azure, tels que le stockage et les files d’attente.</li><li>Le bouton **Administration** montre les pages d’administration de l’application depuis lesquelles un administrateur peut gérer les paramètres, les utilisateurs et les rôles de l’application.</li></ul> |

## <a name="search-help-and-support"></a>Recherche, aide et support

Le menu supérieur s’affiche sur chaque page :

![Barre d'outils](media/overview-iot-central-tour/toolbar.png)

- Pour rechercher des modèles d’appareils et des appareils, entrez une valeur de **recherche**.
- Pour modifier la langue de l’interface utilisateur ou le thème, choisissez l’icône des **paramètres**.
- Pour vous déconnecter de l’application, choisissez l’icône **Compte**.
- Pour obtenir de l’aide et un support, choisissez la liste déroulante **Aide** pour obtenir une liste des ressources. Dans une application d’essai, les ressources de support incluent l’accès à une [messagerie instantanée](howto-show-hide-chat.md).

Vous pouvez choisir entre un thème clair ou un thème foncé pour l’interface utilisateur :

![Choisir un thème pour l’interface utilisateur](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> La possibilité de choisir entre des thèmes clairs et foncés n’est pas disponible si votre administrateur a configuré un thème personnalisé pour l’application.

## <a name="dashboard"></a>tableau de bord

![tableau de bord](media/overview-iot-central-tour/homepage.png)

* Le tableau de bord est la première page que vous voyez lorsque vous vous connectez à votre application Azure IoT Central. En tant que créateur, vous pouvez personnaliser le tableau de bord pour d’autres utilisateurs de l’application en y ajoutant des vignettes. Pour plus d’informations, consultez le didacticiel [Personnaliser la vue d’opérateur de Azure IoT Central](tutorial-customize-operator.md).

* En tant qu’opérateur, vous pouvez créer des tableaux de bord personnalisés et passer de ces tableaux vers le tableau de bord par défaut. Pour plus d’informations, consultez l’article sur les procédures [Créer et gérer des tableaux de bord personnels](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Explorateur d’appareils

![Page Explorateur](media/overview-iot-central-tour/explorer.png)

La page de l’explorateur affiche les _appareils_ dans votre application Azure IoT Central, groupés par _modèle d’appareil_.

* Un modèle d’appareil définit un type d’appareil pouvant se connecter à votre application. Pour plus d’informations, consultez [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).
* Un appareil représente un appareil réel ou simulé dans votre application. Pour plus d’informations, consultez l’article [Ajouter un nouvel appareil à votre application Azure IoT Central](tutorial-add-device.md).

## <a name="device-sets"></a>Ensembles d’appareils

![Page Ensembles d’appareils](media/overview-iot-central-tour/devicesets.png)

La page _Ensembles d’appareils_ affiche les ensembles d’appareils créés par le générateur. Un ensemble d’appareils est un regroupement d’appareils associés. Un générateur définit une requête pour identifier les appareils inclus dans un ensemble d’appareils. Vous utilisez des ensembles d’appareils lorsque vous personnalisez les analytiques dans votre application. Pour plus d’informations, consultez l’article [Utiliser des ensembles d’appareils dans votre application Azure IoT Central](howto-use-device-sets.md).

## <a name="analytics"></a>Analytics

![Page Analytics](media/overview-iot-central-tour/analytics.png)

La page Analytics affiche les graphiques qui vous aident à comprendre comment se comportent les appareils connectés à votre application. Un opérateur utilise cette page pour surveiller et étudier les problèmes avec les appareils connectés. Le générateur peut définir les graphiques affichés sur cette page. Pour plus d’informations, consultez l’article [Créer des analytiques personnalisés pour votre application Azure IoT Central](howto-use-device-sets.md).

## <a name="jobs"></a>Tâches

![Page Tâches](media/overview-iot-central-tour/jobs.png)

La page Tâches vous permet d’effectuer des opérations de gestion d’appareils en bloc sur vos appareils. Le générateur utilise cette page pour mettre à jour des commandes, des paramètres et des propriétés d’appareil. Pour plus d’informations, consultez l’article [Exécuter une tâche](howto-run-a-job.md).

## <a name="device-templates"></a>Modèles d’appareil

![Page Modèles d’appareil](media/overview-iot-central-tour/templates.png)

La page des modèles d’appareil est l’endroit où un générateur crée et gère les modèles d’appareil dans l’application. Un modèle d’appareil spécifie les caractéristiques de celui-ci, notamment :

- La télémétrie, l’état et les mesures d’événement.
- Les paramètres et les propriétés.
- Les commandes.
- Les règles basées sur des événements ou des valeurs de télémétrie.

Pour plus d’informations, consultez le didacticiel [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Exportation de données continue

![Page Exportation de données continue](media/overview-iot-central-tour/export.png)

La page d’exportation continue des données est l’endroit où un administrateur définit la façon de diffuser en continu des données, comme les données de télémétrie, à partir de l’application. D’autres services peuvent stocker les données exportées ou les utiliser à des fins d’analyse. Pour en savoir plus, consultez l’article [Exporter vos données dans Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administration

![Page d’administration](media/overview-iot-central-tour/administration.png)

La page d’administration contient des liens vers les outils utilisés par un administrateur, comme la définition des utilisateurs et des rôles dans l’application, ou la personnalisation de l’interface utilisateur. Pour plus d’informations, consultez l’article [Administrer votre application Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble de Azure IoT Central et vous êtes familiarisé avec la mise en page de l’interface utilisateur. L’étape suivante suggérée consiste à effectuer le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).