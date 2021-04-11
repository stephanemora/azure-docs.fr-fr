---
title: Déployer Microsoft OPC Publisher
description: Dans ce tutoriel, vous allez apprendre à déployer OPC Publisher en mode autonome.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787233"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Tutoriel : Déployer OPC Publisher

OPC Publisher est un produit Microsoft entièrement pris en charge, développé publiquement, qui fait le lien entre les ressources industrielles et le cloud Microsoft Azure. Pour ce faire, il se connecte à des ressources compatibles avec OPC UA ou à un logiciel de connectivité industrielle et publie des données de télémétrie dans [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) dans différents formats, notamment le format standard IEC62541 OPC UA PubSub (à partir de la version 2.6).

Il s’exécute sur [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) en tant que module ou sur Docker en tant que conteneur. Étant donné qu’il tire parti du [runtime multiplateforme .NET](https://docs.microsoft.com/dotnet/core/introduction), il s’exécute également en mode natif sur Linux et Windows 10.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Déployer OPC Publisher
> * Exécuter la dernière version publiée d’OPC Publisher en tant que conteneur
> * Spécifiez les options de création de conteneur dans le portail Azure

Si vous n’avez pas d’abonnement Azure, créez un compte d’essai gratuit.

## <a name="prerequisites"></a>Prérequis

- Un hub IoT doit être créé
- Un appareil IoT Edge doit être créé

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Déployer OPC Publisher à partir de la Place de marché Azure

1. Choisissez l’abonnement Azure à utiliser. Si aucun abonnement Azure n’est disponible, vous devez en créer un.
2. Sélectionnez le hub IoT auquel OPC Publisher est censé envoyer des données. Si aucun hub IoT n’est disponible, vous devez en créer un.
3. Sélectionnez l’appareil IoT Edge sur lequel OPC Publisher est censé s’exécuter (ou entrez le nom d’un appareil IoT Edge à créer).
4. Cliquez sur Créer. La page « Définir des modules sur l’appareil » associée à l’appareil IoT Edge sélectionné s’ouvre.
5. Cliquez sur « OPCPublisher » pour ouvrir la page « Mettre à jour le module IoT Edge » d’OPC Publisher, puis sélectionnez « Options de création de conteneur ».
6. Spécifiez des options de création de conteneur supplémentaires en fonction de votre utilisation d’OPC Publisher ; consultez la section suivante ci-après.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Accès manuel aux conteneurs Docker Microsoft Container Registry pour OPC Publisher

La dernière version publiée d’OPC Publisher peut être exécutée manuellement par le biais de la commande suivante :

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Où « name » est le nom du conteneur.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Spécification des options de création de conteneur dans le portail Azure
Lors du déploiement d’OPC Publisher par le biais du portail Azure, vous pouvez spécifier des options de création de conteneur dans la page Mettre à jour le module IoT Edge d’OPC Publisher. Ces options de création doivent être au format JSON. Les arguments de ligne de commande d’OPC Publisher peuvent être spécifiés par le biais de la clé Cmd, par exemple :
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Voici un ensemble typique d’options de création de conteneur de module IoT Edge pour OPC Publisher :
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Quand ces options sont spécifiées, OPC Publisher lit le fichier de configuration `./pn.json`. Le répertoire de travail d’OPC Publisher étant défini sur `/appdata` au démarrage, OPC Publisher lit le fichier `/appdata/pn.json` à l’intérieur de son conteneur Docker. Le fichier journal d’OPC Publisher est écrit dans `/appdata`, où est également crée le répertoire `CertificateStores` (utilisé pour les certificats OPC UA). Pour rendre ces fichiers disponibles dans le système de fichiers hôte IoT Edge, la configuration du conteneur nécessite un volume de montage de liaison. La liaison `/iiotedge:/appdata` mappe le répertoire `/appdata` au répertoire hôte `/iiotedge` (qui est créé par le runtime IoT Edge s’il n’existe pas).
**Sans ce volume de montage de liaison, tous les fichiers de configuration d’OPC Publisher sont perdus lors du redémarrage du conteneur.**

Vous pouvez obtenir une connexion à un serveur OPC UA utilisant son nom d’hôte sans qu’un serveur DNS soit configuré sur le réseau, en ajoutant une entrée `ExtraHosts` à la section `HostConfig` :

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes 
Le module de périphérie d’OPC Publisher étant déployé, vous pouvez le configurer :

> [!div class="nextstepaction"]
> [Configurer OPC Publisher](tutorial-publisher-configure-opc-publisher.md)