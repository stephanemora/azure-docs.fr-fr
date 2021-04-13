---
title: Fichier include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491086"
---
<!-- All needs updating -->
En tant qu’opérateur dans votre application Azure IoT Central, vous pouvez effectuer les opérations suivantes :

* Affichez la télémétrie envoyée par les deux composants de thermostat dans la page **Vue d’ensemble** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Visualiser la télémétrie d’un appareil":::

* Affichez les propriétés de l’appareil dans la page **À propos**. Cette page affiche les propriétés du composant d’informations sur l’appareil et des deux composants de thermostat :

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Voir les propriétés de l’appareil":::

## <a name="customize-the-device-template"></a>Personnaliser le modèle d’appareil

En tant que développeur de solutions, vous pouvez personnaliser le modèle d’appareil qu’IoT Central a créé automatiquement lors de la connexion de l’appareil contrôleur de température.

Pour ajouter une propriété cloud afin de stocker le nom du client associé à l’appareil

1. Dans votre application IoT Central, accédez au modèle d’appareil **Contrôleur de température** dans la page **Modèles d’appareil**.

1. Dans le modèle d’appareil **Contrôleur de température**, sélectionnez **Propriétés du cloud**.

1. Sélectionnez **Ajouter une propriété cloud**. Entrez *Customer name* comme **Nom d’affichage** et choisissez **Chaîne** comme **Schéma**. Ensuite, sélectionnez **Enregistrer**.

Pour personnaliser l’affichage des commandes **Get Max-Min report** dans votre application IoT Central :

1. Sélectionnez **Personnaliser** dans le menu du modèle d’appareil.

1. Pour **getMaxMinReport (thermostat1)** , remplacez *Get Max-Min report* par *Get thermostat1 status report*.

1. Pour **getMaxMinReport (thermostat2)** , remplacez *Get Max-Min report* par *Get thermostat2 status report*.

1. Sélectionnez **Enregistrer**.

Pour personnaliser la façon dont les propriétés accessibles en écriture de **Température cible** s’affichent dans votre application IoT Central :

1. Sélectionnez **Personnaliser** dans le menu du modèle d’appareil.

1. Pour **targetTemperature (thermostat1)** , remplacez *Température cible* par *Température cible (1)* .

1. Pour **targetTemperature (thermostat2)** , remplacez *Température cible* par *Température cible (2)* .

1. Sélectionnez **Enregistrer**.

Les composants de thermostat du modèle **Contrôleur de température** comprennent la propriété accessible en écriture **Température cible**, et le modèle d’appareil comprend la propriété cloud **Nom du client**. Créez une vue utilisable par un opérateur pour modifier ces propriétés :

1. Sélectionnez **Vues**, puis sélectionnez la vignette **Modification des données de l’appareil et du cloud**.

1. Entrez _Propriétés_ comme nom de formulaire.

1. Sélectionnez les propriétés **Température cible (1)** , **Température cible (2)** et **Nom du client**. Sélectionnez ensuite **Ajouter une section**.

1. Enregistrez vos modifications.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Vue pour la mise à jour des valeurs de propriétés":::

## <a name="publish-the-device-template"></a>Publier le modèle d’appareil

Pour qu’un opérateur puisse voir et utiliser les personnalisations que vous avez effectuées, vous devez publier le modèle d’appareil.

À partir du modèle d’appareil **Thermostat**, sélectionnez **Publier**. Dans le panneau **Publier ce modèle d’appareil dans l’application**, sélectionnez **Publier**.

Un opérateur peut maintenant utiliser la vue **Propriétés** pour mettre à jour les valeurs de propriétés, et appeler des commandes nommée **Get thermostat1 status report** et **Get thermostat2 status report** dans la page de commandes de l’appareil :

* Mettez à jour les valeurs des propriétés inscriptibles dans la page **Propriétés** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Mettre à jour les propriétés de l’appareil":::

* Appelez les commandes à partir de la page **Commandes** :

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Appeler la commande":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Afficher la réponse de la commande":::
