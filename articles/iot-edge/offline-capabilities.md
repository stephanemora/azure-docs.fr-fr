---
title: Fonctionnalités hors connexion d’Azure IoT Edge | Microsoft Docs
description: Découvrez de quelle manière les appareils et modules IoT Edge peuvent fonctionner en mode hors connexion durant de longues périodes, et comment IoT Edge permet aussi aux appareils IoT standards de continuer à fonctionner hors connexion.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 30b85f15d8718e21af66634db5a4afd5623a77e6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340169"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Comprendre les fonctionnalités hors connexion étendues pour les appareils, modules et appareils enfants IoT Edge (préversion)

Azure IoT Edge prend en charge les opérations hors connexion étendues sur vos appareils IoT Edge et permet également certaines opérations hors connexion sur les appareils enfants non Edge. Dès lors qu’un appareil IoT Edge s’est connecté au moins une fois à IoT Hub, cet appareil et tous les appareils enfants assignés peuvent continuer à fonctionner même s’ils sont déconnectés de façon prolongée ou intermittente. 

>[!NOTE]
>La prise en charge du mode hors connexion pour IoT Edge est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Fonctionnement

Quand un appareil IoT Edge passe en mode hors connexion, le hub Edge assure trois rôles. Tout d’abord, il stocke tous les messages à acheminer en amont jusqu’à ce que l’appareil puisse se reconnecter. Ensuite, il authentifie, pour le compte d’IoT Hub, les modules et les appareils enfants afin qu’ils puissent continuer à fonctionner. Enfin, il rend possible la communication entre les appareils enfants qui communiquent normalement par le biais d’IoT Hub. 

L’exemple de scénario IoT Edge suivant montre le fonctionnement en mode hors connexion :

1. **Configurer un appareil IoT Edge.**

   Sur les appareils IoT Edge, les fonctionnalités hors connexion sont automatiquement activées. Pour étendre ces fonctionnalités à d’autres appareils IoT, vous devez déclarer une relation parent-enfant entre les différents appareils dans IoT Hub. 

2. **Synchroniser avec IoT Hub.**

   Après l’installation du runtime IoT Edge, l’appareil IoT Edge doit être connecté au moins une fois pour pouvoir se synchroniser avec IoT Hub. Lors de cette synchronisation, l’appareil IoT Edge obtient des informations sur tous les appareils enfants qui lui sont assignés. De plus, l’appareil IoT Edge met à jour en toute sécurité son cache local pour permettre les opérations hors connexion et il récupère les paramètres du stockage local des messages de télémétrie. 

3. **Passer en mode hors connexion.**

   Même quand ils ne sont plus connectés à IoT Hub, l’appareil IoT Edge, ses modules déployés et ses appareils IoT enfants peuvent continuer à fonctionner pendant une durée indéterminée. Les modules et les appareils enfants hors connexion peuvent démarrer et redémarrer en s’authentifiant auprès du hub Edge. Les données de télémétrie liées qui sont transmises en amont à IoT Hub sont stockées localement. La communication entre les modules ou entre les appareils IoT enfants est maintenue par le biais de méthodes ou messages directs. 

4. **Reconnecter et resynchroniser avec IoT Hub.**

   Après la restauration de la connexion à IoT Hub, l’appareil IoT Edge est resynchronisé. Les messages stockés localement sont remis dans l’ordre dans lequel ils ont été stockés. Les éventuelles différences entre les propriétés désirées et rapportées des modules et des appareils sont rapprochées. L’appareil IoT Edge apporte les modifications nécessaires aux appareils IoT enfants qui lui sont assignés.

## <a name="restrictions-and-limits"></a>Restrictions et limites

Les fonctionnalités hors connexion étendues décrites dans cet article sont disponibles dans [IoT Edge version 1.0.2 ou version ultérieure](https://github.com/Azure/azure-iotedge/releases). Les versions antérieures fournissent toutefois certaines fonctionnalités hors connexion. Les appareils IoT Edge existants qui n’ont pas de fonctionnalités hors connexion étendues ne peuvent pas être mis à niveau en changeant de version de runtime. Pour bénéficier de ces fonctionnalités, ils doivent être reconfigurés avec une nouvelle identité d’appareil IoT Edge. 

La prise en charge des fonctionnalités hors connexion étendues est proposée dans toutes les régions où IoT Hub est disponible, à l’exception des régions USA Est et Europe Ouest. 

Seuls des appareils non IoT Edge peuvent être ajoutés en tant qu’appareils enfants. 

Les appareils IoT Edge et leurs appareils enfants assignés peuvent fonctionner en mode hors connexion pendant une durée indéterminée dès lors qu’ils ont été synchronisés une fois. Toutefois, le stockage des messages dépend du paramètre de durée de vie (TTL) et de l’espace disque disponible pour stocker les messages. 

## <a name="set-up-an-edge-device"></a>Configurer un appareil Edge

Pour chaque appareil IoT Edge amené à fonctionner hors connexion pendant de longues périodes, configurez le runtime IoT Edge pour communiquer via MQTT. 

Pour étendre les fonctionnalités hors connexion étendues d’un appareil IoT Edge aux appareils IoT enfants assignés, déclarez les relations parent-enfant appropriées dans le portail Azure.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Définir MQTT comme protocole amont

Configurez le hub Edge et l’agent Edge pour utiliser MQTT comme protocole de communication en amont. Déclarez ce protocole à l’aide de variables d’environnement dans le manifeste de déploiement. 

Dans le portail Azure, vous pouvez accéder aux définitions des modules du hub Edge et de l’agent Edge en sélectionnant le bouton **Configurer les paramètres avancés du runtime Edge** quand vous configurez les modules d’un déploiement. Pour les deux modules, créez une variable d’environnement appelée **UpstreamProtocol** et définissez sa valeur sur **MQTT**. 

Dans le modèle de déploiement JSON, les variables d’environnement sont déclarées de la manière suivante : 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>Assigner des appareils enfants

Les appareils enfants sont des appareils non Edge inscrits auprès du même IoT Hub. Vous pouvez gérer leurs relations parent-enfant au moment où vous créez un appareil, ou à partir de la page de détails de l’appareil IoT Edge parent ou de l’appareil IoT enfant. 

   ![Gérer les appareils enfants à partir de la page de détails de l’appareil IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Un appareil parent peut avoir plusieurs appareils enfants, mais un appareil enfant ne peut avoir qu’un seul appareil parent.

## <a name="optional-offline-settings"></a>Paramètres hors connexion facultatifs

Si vos appareils sont susceptibles de rester hors connexion pendant de longues périodes, configurez le hub Edge pour qu’il puisse stocker tous les messages générés jusqu’à ce que ces derniers soient collectés une fois la connexion rétablie. Vous devez apporter deux modifications au hub Edge pour permettre le stockage prolongé des messages. Augmentez la valeur du paramètre de durée de vie et ajoutez de l’espace disque dédié au stockage des messages. 

### <a name="time-to-live"></a>Durée de vie

Le paramètre de durée de vie définit la durée (en secondes) pendant laquelle un message peut attendre d’être remis avant d’expirer. La durée par défaut est de 7 200 secondes (deux heures). 

Ce paramètre est une propriété désirée du hub Edge, qui est stockée dans le jumeau du module. Vous pouvez le configurer dans le portail Azure, dans la section **Configurer les paramètres avancés du runtime Edge** ou directement dans le manifeste de déploiement. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Stockage hors connexion supplémentaire

Par défaut, les messages sont stockés dans le système de fichiers du conteneur du hub Edge. Si l’espace de stockage n’est pas suffisant pour vos besoins hors connexion, vous pouvez dédier un stockage local sur l’appareil IoT Edge. Pour cela, créez une variable d’environnement pour le hub Edge qui pointe vers un dossier de stockage dans le conteneur. Utilisez ensuite les options de création pour lier ce dossier de stockage à un dossier situé sur la machine hôte. 

Vous pouvez configurer des variables d’environnement et les options de création pour le module du hub Edge dans la section **Configurer les paramètres avancés du runtime Edge** du portail Azure. Vous pouvez aussi les configurer directement dans le manifeste de déploiement. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"<HostStoragePath>:<ModuleStoragePath>\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Remplacez `<HostStoragePath>` et `<ModuleStoragePath>` par le chemin de stockage de votre hôte et de votre module. Les deux chemins doivent être des chemins absolus.  Par exemple, `\"Binds\":[\"/etc/iotedge/storage/:/iotedge/storage/"` signifie que le chemin de l’hôte `/etc/iotedge/storage` est mappé au chemin du conteneur `/iotedge/storage/`.  Vous trouverez aussi plus de détails sur createOptions dans la [documentation de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Étapes suivantes

Rendre possibles les opérations hors connexion étendues dans vos scénarios de passerelle transparente pour des appareils [Linux](how-to-create-transparent-gateway-linux.md) ou [Windows](how-to-create-transparent-gateway-windows.md).
