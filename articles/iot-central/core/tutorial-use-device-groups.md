---
title: Tutoriel – Utiliser des groupes d’appareils dans votre application Azure IoT Central | Microsoft Docs
description: 'Tutoriel : Découvrez comment utiliser des groupes d’appareils pour analyser la télémétrie des appareils de votre application Azure IoT Central.'
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da7c1c0268f04b183ba48491bd5f0d0b01e15b41
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224106"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutoriel : Utiliser un groupe d’appareils pour analyser les données de télémétrie des appareils

Cet article décrit comment utiliser des groupes d’appareils afin d’analyser la télémétrie des appareils dans votre application Azure IoT Central.

Un groupe d’appareils est une liste d’appareils qui sont regroupés, car ils correspondent à certains critères spécifiés. Les groupes d’appareils vous permettent de gérer, de visualiser et d’analyser des appareils à grande échelle en regroupant les appareils dans des groupes logiques plus petits. Par exemple, vous pouvez créer un groupe d’appareils répertoriant tous les appareils de climatisation à Seattle pour permettre à un technicien de rechercher les appareils dont il est responsable.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un groupe d’appareils
> * Utiliser un groupe d'appareils pour analyser les données de télémétrie des appareils

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes de ce tutoriel, vous devez disposer des éléments suivants :

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="add-and-customize-a-device-template"></a>Ajouter et personnaliser un modèle d’appareil

Ajoutez un modèle d’appareil à partir du catalogue d’appareils. Ce tutoriel utilise le modèle d’appareil **ESP32-Azure IoT Kit** :

1. Pour ajouter un nouveau modèle d’appareil, sélectionnez **+ Nouveau** dans la page **Modèles d’appareil**.

1. Dans la page **Sélectionner le type**, faites défiler jusqu’à la vignette **ESP32-Azure IoT Kit** dans la section **Utiliser un modèle d’appareil préconfiguré**.

1. Sélectionnez la vignette **ESP32-Azure IoT Kit**, puis sélectionnez **Suivant : Vérification**).

1. Dans la page **Vérifier**, sélectionnez **Créer**.

Le nom du modèle que vous avez créé est **Sensor Controller**. Le modèle de capacité comprend des composants tels que **Sensor Controller**, **SensorTemp** et **Device Information interface**. Les composants définissent les fonctionnalités d’un appareil ESP32, telles que la télémétrie, les propriétés et les commandes.

Ajoutez deux propriétés cloud au modèle d’appareil **Contrôleur de capteur** :

1. Sélectionnez **Propriétés du cloud**, puis **+ Ajouter une propriété cloud**. Utilisez les informations du tableau suivant pour ajouter deux propriétés cloud à votre modèle d’appareil :

    | Nom d’affichage      | Type sémantique | schéma |
    | ----------------- | ------------- | ------ |
    | Dernière date de service | None          | Date   |
    | Nom du client     | None          | String |

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

Ajoutez un nouveau formulaire au modèle d’appareil pour gérer l’appareil :

1. Après avoir sélectionné le nœud **Vues**, sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.

1. Remplacez le nom du formulaire par **Gérer l’appareil**.

1. Sélectionnez les propriétés cloud **Customer Name** et **Last Service Date** ainsi que la propriété **Target Temperature**. Sélectionnez ensuite **Ajouter une section**.

1. Sélectionnez **Enregistrer** pour enregistrer votre nouveau formulaire.

À présent, publiez le modèle d’appareil.

## <a name="create-simulated-devices"></a>Créer des appareils simulés

Avant de créer un groupe d’appareils, ajoutez au moins cinq appareils simulés à partir du modèle d’appareil **Contrôleur de capteur** à utiliser dans ce tutoriel :

:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Capture d’écran montrant cinq contrôleurs de capteur simulés":::

Pour quatre des capteurs simulés, utilisez la vue **Gérer l’appareil** afin de définir le nom du client sur *Contoso*, puis sélectionnez **Enregistrer**.

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Capture d’écran montrant comment définir la propriété cloud Nom du client":::

## <a name="create-a-device-group"></a>Créer un groupe d’appareils

1. Sélectionnez **Groupes d’appareils** dans le volet gauche pour accéder à la page des groupes d’appareils.

1. Sélectionnez **+Nouveau**.

1. Nommez votre groupe d’appareils *Appareils Contoso*. Vous pouvez également ajouter une description. Un groupe d’appareils peut contenir seulement des appareils d’un même modèle d’appareil. Choisissez le modèle d’appareil **Contrôleur de capteur** à utiliser pour ce groupe.

1. Pour personnaliser le groupe d'appareils afin de n'y inclure que les appareils appartenant à **Contoso**, sélectionnez **+ Filtre**. Sélectionnez la propriété **Nom du client**, l'opérateur de comparaison **Égal** et la valeur **Contoso**. Vous pouvez ajouter plusieurs filtres, et les appareils qui répondent à **tous** les critères sont placés dans le groupe d'appareils. Le groupe d’appareils que vous créez est accessible à toute personne ayant accès à l’application : toutes ces personnes peuvent donc voir, modifier ou supprimer le groupe d’appareils.

    > [!TIP]
    > Le groupe d’appareils est une requête dynamique. Chaque fois que vous visualisez la liste des appareils, des appareils différents peuvent figurer dans la liste. La liste varie selon les appareils qui répondent actuellement aux critères de la requête.

1. Choisissez **Enregistrer**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Capture d’écran montrant la configuration des requêtes du groupe d’appareils":::

> [!NOTE]
> Pour les appareils Azure IoT Edge, sélectionnez les modèles Azure IoT Edge pour créer un groupe d’appareils.

## <a name="analytics"></a>Analytics

Vous pouvez utiliser **Analytics** avec un groupe d’appareils pour analyser les données de télémétrie des appareils dans le groupe. Par exemple, vous pouvez tracer la température moyenne signalée par tous les capteurs environnementaux de Contoso.

Pour analyser les données de télémétrie d’un groupe d’appareils :

1. Choisissez **Analytics** dans le volet gauche.

1. Sélectionnez le groupe d’appareils intitulé **Appareils Contoso** que vous avez créé. Ajoutez ensuite les types de données de télémétrie **Température** et **Humidité** :

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Capture d’écran présentant les types de télémétrie sélectionnés pour l’analyse":::

    Utilisez les icônes de points de suspension en regard des types de données de télémétrie pour sélectionner un type d’agrégation. La valeur par défaut est **Moyenne**. Utilisez **Grouper par** pour modifier la façon dont les données agrégées sont affichées. Par exemple, si vous sélectionnez **Analyser** alors que vous fractionnez par ID d’appareil, un tracé s’affiche pour chaque appareil.

1. Sélectionnez **Analyser** pour afficher les valeurs de télémétrie moyennes :

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Capture d’écran affichant les valeurs moyennes de tous les appareils Contoso":::

    Vous pouvez personnaliser l’affichage, modifier la période affichée et exporter les données au format CSV ou afficher les données sous forme de table.

    :::image type="content" source="media/tutorial-use-device-groups/export-data.png" alt-text="Capture d’écran montrant comment exporter des données pour les appareils Contoso":::

Pour en savoir plus sur l’analyse, consultez [Comment utiliser l’analytique pour analyser des données d’appareils](howto-create-analytics.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des groupes d’appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](tutorial-create-telemetry-rules.md)
