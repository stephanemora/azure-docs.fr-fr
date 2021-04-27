---
title: Démarrage rapide – Ajouter un appareil simulé à Azure IoT Central
description: Ce guide de démarrage rapide montre comment créer un modèle d’appareil et ajouter un appareil simulé à votre application IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 2eb0e3ce2ac20c89d9c0176ca3e7b33dc839c923
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718824"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Démarrage rapide : Ajouter un appareil simulé à votre application IoT Central

Un modèle d’appareil définit les fonctionnalités d’un appareil qui se connecte à votre application IoT Central. Les capacités incluent la télémétrie que l’appareil envoie, les propriétés de l’appareil et les commandes auxquelles un appareil répond. À partir d’un modèle d’appareil, vous pouvez ajouter des appareils réels et simulés à une application. Les appareils simulés sont utiles pour tester le comportement de votre application IoT Central avant de connecter des appareils réels.

Dans ce guide de démarrage rapide, vous allez ajouter un modèle d’appareil pour une carte de développement ESP32-Azure IoT Kit et créer un appareil simulé. Pour suivre ce guide de démarrage rapide, vous n’avez pas besoin d’appareil réel, vous travaillez avec un appareil simulé. Un appareil ESP32 :

* Envoie des données de télémétrie comme la température.
* Signale des propriétés propres à l’appareil, telles que la température maximale depuis le redémarrage de l’appareil.
* Répond à des commandes telles que le redémarrage.
* Signale des propriétés d’appareil génériques, telles que la version du microprogramme et le numéro de série.

## <a name="prerequisites"></a>Prérequis

Suivez le guide de démarrage rapide [Créer une application Azure IoT Central](./quick-deploy-iot-central.md) pour créer une application IoT Central avec le modèle **Application personnalisée > Application personnalisée**.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Pour ajouter un nouveau modèle d’appareil à votre application, sélectionnez l’onglet **Modèles d’appareil** dans le volet de gauche.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Capture d’écran montrant une liste vide de modèles d’appareil":::

Un modèle d’appareil comprend un modèle de capacité d’appareil qui définit :

* La télémétrie envoyée par l’appareil.
* Les propriétés de l’appareil.
* Les commandes auxquelles l’appareil répond.

### <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Vous pouvez ajouter un modèle de capacité d’appareil à votre application IoT Central de plusieurs façons. Vous pouvez créer un modèle en partant de zéro, importer un modèle à partir d’un fichier ou sélectionner un appareil dans le catalogue d’appareils. IoT Central prend aussi en charge une approche « *device-first* » (appareil en premier) dans laquelle l’application importe automatiquement un modèle de capacité à partir d’un référentiel lorsqu’un appareil réel se connecte pour la première fois.

Dans ce guide de démarrage rapide, vous choisissez un appareil dans le catalogue d’appareils pour créer un modèle d’appareil.

Les étapes suivantes montrent comment utiliser le catalogue d’appareils pour importer le modèle de capacité d’un appareil **ESP32**. Ces appareils envoient des données de télémétrie, telles que la température, à votre application :

1. Pour ajouter un nouveau modèle d’appareil, sélectionnez **+ Nouveau** dans la page **Modèles d’appareil**.

1. Dans la page **Sélectionner le type**, faites défiler jusqu’à la vignette **ESP32-Azure IoT Kit** dans la section **Utiliser un modèle d’appareil préconfiguré**.

1. Sélectionnez la vignette **ESP32-Azure IoT Kit**, puis sélectionnez **Suivant : Vérification**).

1. Dans la page **Vérifier**, sélectionnez **Créer**.

1. Après quelques secondes, vous pouvez voir votre nouveau modèle d’appareil :

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Capture d’écran montrant le modèle d’appareil pour l’appareil ESP32":::

    Le nom du modèle est **Sensor Controller**. Le modèle de capacité comprend des composants tels que **Sensor Controller**, **SensorTemp** et **Device Information interface**. Les composants définissent les fonctionnalités d’un appareil ESP32, telles que la télémétrie, les propriétés et les commandes.

### <a name="add-cloud-properties"></a>Ajouter des propriétés du cloud

Un modèle d’appareil peut inclure des propriétés du cloud. Les propriétés du cloud existent uniquement dans l’application IoT Central, et ne sont jamais envoyées à un appareil ni reçues à partir d’un appareil. Pour ajouter deux propriétés cloud :

1. Sélectionnez **Propriétés du cloud**, puis **+ Ajouter une propriété cloud**. Utilisez les informations du tableau suivant pour ajouter deux propriétés cloud à votre modèle d’appareil :

    | Nom d’affichage      | Type sémantique | schéma |
    | ----------------- | ------------- | ------ |
    | Dernière date de service | None          | Date   |
    | Nom du client     | None          | String |

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications :

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Capture d’écran montrant deux propriétés cloud":::

## <a name="views"></a>Les vues

Vous pouvez personnaliser l’application de façon à afficher des informations pertinentes au sujet de l’appareil. Les personnalisations permettent de gérer les appareils qui sont connectés à l’application. Vous pouvez créer deux types de vues pour interagir avec des appareils :

* Formulaires pour voir et modifier les propriétés de l’appareil et du cloud
* Tableaux de bord permettant de visualiser les appareils, notamment les données de télémétrie qu’ils envoient

### <a name="default-views"></a>Vues par défaut

Les vues par défaut permettent de visualiser rapidement les informations importantes de votre appareil. Vous pouvez avoir jusqu’à trois vues par défaut générées pour votre modèle d’appareil :

* La vue **Commandes** vous permet d’envoyer des commandes à votre appareil.
* La vue **Vue d’ensemble** utilise des graphiques et des métriques pour afficher les données de télémétrie des appareils.
* La vue **À propos** affiche les propriétés de l’appareil.

Sélectionnez le nœud **Vues** dans le modèle d’appareil. Comme vous pouvez le constater, IoT Central a généré les vues **Vue d’ensemble**, **À propos** et **Données brutes** au moment où vous avez ajouté le modèle.

Pour ajouter un nouveau formulaire chargé de gérer l’appareil :

1. Après avoir sélectionné le nœud **Vues**, sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.

1. Remplacez le nom du formulaire par **Gérer l’appareil**.

1. Sélectionnez les propriétés cloud **Customer Name** et **Last Service Date** ainsi que la propriété **Target Temperature**. Sélectionnez ensuite **Ajouter une section** :

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Capture d’écran montrant le nouveau formulaire ajouté au modèle d’appareil":::

1. Sélectionnez **Enregistrer** pour enregistrer votre nouveau formulaire.

## <a name="publish-device-template"></a>Publier le modèle d’appareil

Avant de pouvoir créer un appareil simulé ou de connecter un appareil réel, vous devez publier votre modèle d’appareil. Même si IoT Central a publié le modèle au moment où vous l’avez créé, vous devez publier la version mise à jour.

Pour publier un modèle d’appareil

1. Accédez à votre modèle d’appareil **Sensor Controller** à partir de la page **Modèles d’appareil**.

1. Sélectionnez **Publier** dans la barre de commandes située en haut de la page.

1. Dans la boîte de dialogue qui s’affiche, sélectionnez **Publier**.

Une fois publié, un modèle d’appareil est visible dans la page **Appareils**. Dans un modèle d’appareil publié, vous ne pouvez pas modifier un modèle de capacité d’appareil sans créer de nouvelle version. En revanche, vous pouvez modifier des propriétés cloud, des personnalisations et des vues dans un modèle d’appareil publié sans gestion des versions. Après avoir apporté des modifications, sélectionnez **Publier** pour pousser (push) ces modifications vers les appareils réels et simulés à utiliser.

## <a name="add-a-simulated-device"></a>Utiliser un appareil simulé

Pour ajouter un appareil simulé à votre application, utilisez le modèle d’appareil **ESP32** que vous avez créé.

1. Pour ajouter un nouvel appareil, choisissez **Appareils** dans le volet de gauche. L’onglet **Appareils** affiche **Tous les appareils** et le modèle d’appareil **Sensor Controller** pour l’appareil ESP32. Sélectionnez **Sensor Controller**.

1. Pour ajouter un appareil DevKit simulé, sélectionnez **+ Nouveau**. Utilisez l’**ID d’appareil** suggéré ou entrez votre propre ID d’appareil. Un ID d’appareil peut contenir des lettres, des chiffres et le caractère `-`. Vous pouvez également entrer un nom pour votre nouvel appareil. Vérifiez que **Simuler cet appareil** a la valeur **Oui**, puis sélectionnez **Créer**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Capture d’écran montrant l’appareil Sensor Controller simulé":::

Vous pouvez maintenant interagir avec les vues qui ont été créées plus tôt en utilisant les données simulées :

1. Sélectionnez votre appareil simulé dans la page **Appareils** :

    * La vue **Vue d’ensemble** affiche un tracé des données de télémétrie simulées :

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Capture d’écran montrant la page de vue d’ensemble de l’appareil simulé":::

    * La vue **À propos** affiche les valeurs de propriétés.

    * La vue **Commandes** vous permet d’exécuter des commandes, par exemple **redémarrer** l’appareil.

    * La vue **Gérer les appareils** est le formulaire que vous avez créé pour gérer l’appareil.

    * La vue **Données brutes** vous permet d’afficher les valeurs de propriété et les données de télémétrie brutes envoyées par l’appareil. Cette vue est utile pour déboguer les appareils.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez vu comment créer un modèle d’appareil **Sensor Controller** pour un appareil ESP32 et comment ajouter un appareil simulé à votre application.

Pour en savoir plus sur la surveillance des appareils connectés à votre application, passez au démarrage rapide :

> [!div class="nextstepaction"]
> [Configurer des règles et des actions](./quick-configure-rules.md)
