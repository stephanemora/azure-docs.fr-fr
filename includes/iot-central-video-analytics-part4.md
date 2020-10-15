---
title: Fichier include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876670"
---
### <a name="add-relationships"></a>Ajouter des relations

Dans le modèle d’appareil **LVA Edge Gateway**, sous **Modules/LVA Edge Gateway Module**, sélectionnez **Relations**. Sélectionnez **+ Ajouter une relation** et ajoutez les deux relations suivantes :

|Nom d’affichage               |Nom          |Cible |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |Utiliser la valeur par défaut   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |Utiliser la valeur par défaut   |LVA Edge Object Detector Device |

Ensuite, sélectionnez **Enregistrer**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Ajouter des relations":::

### <a name="add-views"></a>Ajout de vues

Le modèle d’appareil **LVA Edge Gateway** n’inclut aucune définition d’affichage.

Pour ajouter une vue au modèle d’appareil :

1. Dans le modèle d’appareil **LVA Edge Gateway**, accédez à **Affichages**, puis sélectionnez la vignette **Visualisation de l'appareil**.

1. Entrez *Appareil LVA Edge Gateway* comme nom d’affichage.

1. Ajoutez les vignettes suivantes l’affichage :

    * Vignette avec les propriétés **Informations sur l’appareil** : **Modèle d’appareil**, **Fabricant**, **Système d’exploitation**, **Architecture du processeur**, **Version du logiciel**, **Mémoire totale** et **Stockage total**.
    * Vignette de graphique en courbes avec valeurs de télémétrie **Mémoire libre** et **Pulsation système**.
    * Vignette d’historique des événements avec les événements suivants : **Créer une caméra**, **Supprimer une caméra**, **Redémarrer le module**, **Module démarré**, **Module arrêté**.
    * Vignette de dernière valeur connue 2x1 indiquant la télémétrie **État du client IoT Central**.
    * Vignette de dernière valeur connue 2x1 indiquant la télémétrie **État du module**.
    * Vignette de dernière valeur connue 1x1 indiquant la télémétrie **Pulsation système**.
    * Vignette de dernière valeur connue1x1 indiquant la télémétrie **Caméras connectées**.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Ajouter des relations":::

1. Sélectionnez **Enregistrer**.

### <a name="publish-the-device-template"></a>Publier le modèle d’appareil

Avant d’ajouter un appareil à l’application, vous devez publier le modèle d’appareil :

1. Dans le modèle d’appareil **LVA Edge Gateway**, sélectionnez **Publier**.

1. Sur la page **Publier ce modèle d’appareil dans l’application**, sélectionnez **Publier**.

**LVA Edge Gateway** est maintenant disponible en tant que type d’appareil à utiliser sur la page **Appareils** de l’application.

## <a name="add-a-gateway-device"></a>Ajouter un appareil de passerelle

Pour ajouter un appareil **LVA Edge Gateway** à l’application :

1. Accédez à la page **Appareils** et sélectionnez le modèle d’appareil **LVA Edge Gateway**.

1. Sélectionnez **+Nouveau**.

1. Dans la boîte de dialogue **Créer un nouvel appareil**, remplacez le nom de l’appareil par *LVA Gateway 001*, puis l’ID de l’appareil par *lva-gateway-001*.

    > [!NOTE]
    > L’ID de l’appareil doit être unique dans l’application.

1. Sélectionnez **Create** (Créer).

L’état de l’appareil correspond à **Inscrit**.

### <a name="get-the-device-credentials"></a>Obtenir les informations d’identification de l’appareil

Vous avez besoin des informations d’identification qui permettent à l’appareil de se connecter à votre application IoT Central. Pour obtenir les informations d’identification de l’appareil :

1. Sur la page **Appareils**, sélectionnez l’appareil **lva-gateway-001** que vous avez créé.

1. Sélectionnez **Connecter**.

1. Sur la page **Connexion de l’appareil**, notez les valeurs *Étendue de l’ID*, **ID de l’appareil** et **Clé primaire** dans le fichier **scratchpad.txt**. Vous utiliserez ces valeurs plus tard.

1. Assurez-vous que la méthode de connexion est définie sur **Signature d'accès partagé**.

1. Sélectionnez **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de créer une application IoT Central à l’aide du modèle d’application **Analytique vidéo - Détection d’objets et de mouvements**, de créer un modèle d’appareil pour l’appareil de passerelle et d’ajouter un appareil de passerelle à l’application.

Si vous souhaitez tester l’application Analytique vidéo - Détection d’objets et de mouvements à l’aide des modules IoT Edge exécutant une machine virtuelle cloud avec des flux vidéo simulés :

> [!div class="nextstepaction"]
> [Créer une instance IoT Edge pour l’analytique vidéo (machine virtuelle Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Si vous souhaitez tester l’application Analytique vidéo - Détection d’objets et de mouvements à l’aide des modules IoT Edge exécutant un appareil réel avec la caméra réelle **ONVIF** :

> [!div class="nextstepaction"]
> [Créer une instance IoT Edge pour l’analytique vidéo (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
