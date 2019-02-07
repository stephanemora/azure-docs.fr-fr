---
title: Visite guidée de l’interface utilisateur de Azure IoT Central | Microsoft Docs
description: En tant que générateur, familiarisez-vous avec les zones clés de l’interface utilisateur de Azure IoT Central que vous utilisez pour créer une solution IoT.
author: dominicbetts
ms.author: dobett
ms.date: 04/13/2018
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5242be4dfe1c79b8f943b3b6d240046d0e8c5181
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658761"
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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="use-the-left-navigation-menu"></a>Utiliser le menu de navigation gauche

Utilisez le menu de navigation gauche pour accéder aux différentes zones de l’application :

| Menu | Description |
| ---- | ----------- |
| ![Menu de navigation gauche](media/overview-iot-central-tour/navigationbar.png) | <ul><li>Le bouton **Accueil** affiche la page d’accueil de votre application. En tant que générateur, vous pouvez personnaliser cette page d’accueil pour vos opérateurs.</li><li>Le bouton **Device Explorer** répertorie les modèles d’appareils définis dans votre application et les appareils simulés et réels associés à chaque modèle de périphérique. En tant qu’opérateur, vous utilisez **Device Explorer** pour gérer vos appareils connectés.</li><li>Le bouton **Device Sets** vous permet d’afficher et de créer des ensembles d’appareils. En tant qu’opérateur, vous pouvez créer des ensembles d’appareils comme une collection logique d’appareils spécifiée par une requête.</li><li>Le bouton **Analytique** montre les analytiques dérivées des données de télémétrie de l’appareil pour les appareils et les ensembles d’appareils. En tant qu’opérateur, vous pouvez créer des vues personnalisées sur des données d’appareils pour dégager des insights à partir de votre application.</li><li>Le bouton **Tâches** permet une gestion des appareils en bloc en vous faisant créer et exécuter des tâches pour effectuer des mises à jour à grande échelle.</li><li>Le bouton **Générateur d’applications** montre les outils utilisés par un générateur, comme l’outil **Créer un modèle d’appareil**.</li><li>Le bouton **Administration** montre les pages d’administration de l’application depuis lesquelles un administrateur peut gérer les paramètres, les utilisateurs et les rôles de l’application.</li></ul> |

## <a name="search-help-and-support"></a>Recherche, aide et support

Le menu supérieur s’affiche sur chaque page :

![Barre d'outils](media/overview-iot-central-tour/toolbar.png)

- Pour rechercher des modèles d’appareils et des appareils, choisissez l’icône **Recherche**.
- Pour obtenir de l’aide et un support, choisissez la liste déroulante **Aide** pour obtenir une liste des ressources.
- Pour contrôler les didacticiels, modifier le thème de l’interface utilisateur, ou vous déconnecter de l’application, choisissez l’icône **Compte**.

Vous pouvez choisir entre un thème clair ou un thème foncé pour l’interface utilisateur :

![Choisir un thème pour l’interface utilisateur](media/overview-iot-central-tour/themes.png)

## <a name="home-page"></a>page d'accueil

![page d'accueil](media/overview-iot-central-tour/homepage.png)

La page d’accueil est la première page que vous voyez lorsque vous vous connectez à votre application Azure IoT Central. En tant que générateur, vous pouvez personnaliser la page d’accueil pour d’autres utilisateurs de l’application en y ajoutant des vignettes. Pour plus d’informations, consultez le didacticiel [Personnaliser la vue d’opérateur de Azure IoT Central](tutorial-customize-operator.md).

## <a name="device-explorer"></a>Explorateur d’appareils

![Page Explorateur](media/overview-iot-central-tour/explorer.png)

La page de l’explorateur affiche les _modèles d’appareil_ et les _appareils_ dans votre application Azure IoT Central.

* Un modèle d’appareil définit un type d’appareil pouvant se connecter à votre application. Pour plus d’informations, consultez [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).
* Un appareil représente un appareil réel ou simulé dans votre application. Pour plus d’informations, consultez l’article [Ajouter un nouvel appareil à votre application Azure IoT Central](tutorial-add-device.md).

## <a name="device-sets"></a>Ensembles d’appareils

![Page Ensembles d’appareils](media/overview-iot-central-tour/devicesets.png)

La page _Ensembles d’appareils_ affiche les ensembles d’appareils créés par le générateur. Un ensemble d’appareils est un regroupement d’appareils associés. Un générateur définit une requête pour identifier les appareils inclus dans un ensemble d’appareils. Vous utilisez des ensembles d’appareils lorsque vous personnalisez les analytiques dans votre application. Pour plus d’informations, consultez l’article [Utiliser des ensembles d’appareils dans votre application Azure IoT Central](howto-use-device-sets.md).

## <a name="analytics"></a>Analytics

![Page Analytics](media/overview-iot-central-tour/analytics.png)

La page Analytics affiche les graphiques qui vous aident à comprendre comment se comportent les appareils connectés à votre application. Un opérateur utilise cette page pour surveiller et étudier les problèmes avec les appareils connectés. Le générateur peut définir les graphiques affichés sur cette page. Pour plus d’informations, consultez l’article [Créer des analytiques personnalisés pour votre application Azure IoT Central](howto-create-analytics.md).

## <a name="jobs"></a>Tâches

![Page Tâches](media/overview-iot-central-tour/jobs.png)

La page Tâches vous permet d’effectuer des opérations de gestion d’appareils en bloc sur vos appareils. Le générateur utilise cette page pour mettre à jour des commandes, des paramètres et des propriétés d’appareil. Pour plus d’informations, consultez l’article [Exécuter une tâche](howto-run-a-job.md).

## <a name="application-builder"></a>Concepteur d’applications

![Page Générateur d’applications](media/overview-iot-central-tour/applicationbuilder.png)

La page Générateur d’applications contient des liens vers les outils qu'un générateur utilise pour créer une application Azure IoT Central, comme la création de modèles d’appareils et la configuration de la page d’accueil. Pour plus d’informations, consultez le didacticiel [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).

## <a name="administration"></a>Administration

![Page d’administration](media/overview-iot-central-tour/administration.png)

La page d’administration contient des liens vers les outils utilisés par un administrateur, comme la définition des utilisateurs et des rôles dans l’application. Pour plus d’informations, consultez l’article [Administrer votre application Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble de Azure IoT Central et vous êtes familiarisé avec la mise en page de l’interface utilisateur. L’étape suivante suggérée consiste à effectuer le guide de démarrage rapide [Créer une application Azure IoT Central](quick-deploy-iot-central.md).