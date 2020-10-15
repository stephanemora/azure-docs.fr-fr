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
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876663"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Créer l’appareil de passerelle Azure IoT Edge

L’application Analytique vidéo - Détection d’objets et de mouvements comprend un modèle d’appareil **LVA Edge Object Detector** et un modèle d’appareil **LVA Edge Motion Detection**. Dans cette section, vous allez créer un modèle d’appareil de passerelle à l’aide du manifeste de déploiement et ajouter cet appareil à votre application IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Créer un modèle d’appareil pour l’appareil LVA Edge Gateway

Pour importer le manifeste de déploiement et créer le modèle d’appareil **LVA Edge Gateway** :

1. Dans votre application Azure IoT Central, accédez à **Modèles d’appareil**, puis sélectionnez **+ Nouveau**.

1. Dans la page **Sélectionner un type de modèle**, sélectionnez la vignette **Azure IoT Edge**. Ensuite, sélectionnez **Next: Personnaliser**.

1. Sur la page **Charger un manifeste de déploiement Azure IoT Edge**, entrez *LVA Edge Gateway* comme nom de modèle, puis cochez **Appareil de passerelle avec des appareils en aval**.

    Ne recherchez pas le manifeste de déploiement pour le moment. Ce faisant, l’Assistant Déploiement attendrait une interface pour chaque module alors que vous devez uniquement exposer l’interface pour **LvaEdgeGatewayModule**. Vous chargerez le manifeste au cours d’une étape ultérieure.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Ne pas télécharger le manifeste de déploiement":::

    Sélectionnez **Suivant : Vérification**).

1. Dans la page **Vérifier**, sélectionnez **Créer**.

### <a name="import-the-device-capability-model"></a>Importer le modèle de capacité d’appareil

Le modèle d’appareil doit inclure un modèle de capacité d’appareil. Sur la page **LVA Edge Gateway**, sélectionnez la vignette **Importer le modèle de capacité**. Accédez au dossier *lva-configuration* créé précédemment, puis sélectionnez le fichier *LvaEdgeGatewayDcm.json*.

Le modèle d’appareil **LVA Edge Gateway** comprend désormais le **module LVA Edge Gateway**, ainsi que trois interfaces : **Informations sur l’appareil**, **Paramètres LVA Edge Gateway** et **Interface LVA Edge Gateway**.
