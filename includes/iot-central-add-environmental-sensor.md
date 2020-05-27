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
ms.openlocfilehash: 9717c76b42a63479c77f862057bfb141954eacff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673405"
---
## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Créez un dossier nommé `environmental-sensor` sur votre ordinateur local.

Téléchargez le fichier JSON de [modèle de capacité de capteur environnemental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) et enregistrez-le dans le dossier `environmental-sensor`.

Utilisez un éditeur de texte pour remplacer les deux instances de `{YOUR_COMPANY_NAME_HERE}` par le nom de votre société dans le fichier `EnvironmentalSensorInline.capabilitymodel.json` que vous avez téléchargé.

Dans votre application Azure IoT Central, créez un modèle d’appareil appelé *Capteur environnemental* en important le fichier de modèle de capacité d’appareil `EnvironmentalSensorInline.capabilitymodel.json` :

![Modèle d’appareil avec modèle de capacité d’appareil importé](./media/iot-central-add-environmental-sensor/device-template.png)

Le modèle de capacité d’appareil comprend deux interfaces : l’interface standard **Informations de l’appareil** et l’interface personnalisée **Capteur environnemental**. L’interface **Capteur environnemental** définit les capacités suivantes :

| Type | Nom d’affichage | Description |
| ---- | ------------ | ----------- |
| Propriété | État de l’appareil     | L’état de l’appareil. Deux états, en ligne/hors connexion, sont disponibles. |
| Propriété (accessible en écriture) | Nom du client    | Le nom du client qui utilise actuellement l’appareil. |
| Propriété (accessible en écriture) | Niveau de luminosité | Le niveau de luminosité de la lampe sur l’appareil. Vous pouvez spécifier 1 (haut), 2 (moyen) ou 3 (bas). |
| Télémétrie | Température | Température actuelle détectée par l’appareil. |
| Télémétrie | Humidité    | Température actuelle détectée par l’appareil. |
| Commande | blink          | Fait clignoter la LED sur l’appareil pour un temps donné. |
| Commande | turnon         | Allume la LED sur l’appareil. |
| Commande | turnoff        | Éteint la LED sur l’appareil. |
| Commande | rundiagnostics | Cette commande asynchrone lance une série de diagnostics sur l’appareil. |

Pour personnaliser l’affichage de la propriété **État de l’appareil** dans votre application IoT Central, sélectionnez **Personnaliser** dans le modèle d’appareil. Développez l’entrée **État de l’appareil**, entrez _En ligne_ comme **Nom true** et _Hors connexion_ comme **Nom false**. Enregistrez les modifications :

![Personnaliser le modèle d’appareil](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Créer des vues

Les vues vous permettent d’interagir avec les appareils connectés à votre application IoT Central. Par exemple, vous pouvez avoir des vues qui affichent les données de télémétrie, des vues qui affichent les propriétés et des vues qui vous permettent de modifier les propriétés de cloud et inscriptibles. Les vues font partie d’un modèle d’appareil.

Pour ajouter des vues par défaut à votre modèle d’appareil **Capteur environnemental**, accédez à votre modèle d’appareil, sélectionnez **Vues**, puis sélectionnez la vignette **Générer les vues par défaut**. Vérifiez que **Vue d’ensemble** et **À propos** sont définis sur **Activé**, puis sélectionnez **Générer les vues de tableau de bord par défaut**. Vous avez maintenant deux vues par défaut définies dans votre modèle.

L’interface **Capteur environnemental** comprend deux propriétés inscriptibles – **Nom du client** et **Niveau de luminosité**. Pour créer une vue, vous pouvez modifier les propriétés suivantes :

1. Sélectionnez **Vues**, puis sélectionnez la vignette **Modification des données de l’appareil et du cloud**.

1. Entrez _Propriétés_ comme nom de formulaire.

1. Sélectionnez les propriétés **Niveau de luminosité** et **Nom du client**. Sélectionnez ensuite **Ajouter une section**.

1. Enregistrez vos modifications.

![Ajout d’une vue permettant de modifier les propriétés](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publier le modèle

Avant de pouvoir ajouter un appareil qui utilise le modèle d’appareil **Capteur environnemental**, vous devez le publier.

Dans le modèle d’appareil, sélectionnez **Publier**. Dans le panneau **Publier ce modèle d’appareil dans l’application**, sélectionnez **Publier**.

Pour vérifier que le modèle est prêt à l’emploi, accédez à la page **Appareils** de votre application IoT Central. La section **Appareils** affiche la liste des appareils publiés dans l’application :

![Page des modèles publiés sur les appareils](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel au modèle d’appareil que vous avez créé dans la section précédente :

1. Dans la page **Appareils**, sélectionnez le modèle d’appareil **Capteur environnemental**.

    > [!TIP]
    > Veillez à sélectionner le modèle à utiliser avant de sélectionner **+ Nouveau**. Dans le cas contraire, vous créerez un appareil non associé.

1. Sélectionnez **+Nouveau**.

1. Veillez à ce que l’option **Simulé** soit définie sur **Désactivé**. Sélectionnez ensuite **Créer**.

Cliquez sur le nom de l’appareil, puis sélectionnez **Connexion**. Prenez note des informations de connexion de l’appareil dans la page **Connexion de l’appareil** – **Étendue de l’ID**, **ID de l’appareil** et **Clé primaire**. Vous avez besoin de ces valeurs pour créer votre code d’appareil :

![Informations de connexion de l’appareil](./media/iot-central-add-environmental-sensor/device-connection.png)
