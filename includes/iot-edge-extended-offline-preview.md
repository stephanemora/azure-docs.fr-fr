---
title: Fichier Include
description: IoT Edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264065"
---
## <a name="enabling-extended-offline-operation-preview"></a>Activation des opérations hors connexion étendues (préversion)
Avec la [version v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) du runtime IoT Edge, l’appareil Edge et les appareils en aval qui y sont connectés peuvent être configurés pour permettre les opérations hors connexion étendues. 

Avec cette fonctionnalité, les modules locaux et les appareils en aval peuvent se réauthentifier auprès de l’appareil Edge et communiquer entre eux à l’aide de messages et de méthodes, même lorsqu’ils sont déconnectés d’IoT Hub. Pour plus d’informations, notamment sur l’étendue de cette fonctionnalité, consultez ce [billet de blog](https://aka.ms/iot-edge-offline) et cet [article de présentation des concepts](../articles/iot-edge/offline-capabilities.md).

Pour activer les opérations hors connexion étendues dans un scénario de passerelle, établissez une relation parent-enfant entre l’appareil Edge et les appareils en aval qui y sont connectés.

1. Sur le portail IoT Hub, dans le panneau d’informations de l’appareil Edge, cliquez sur le bouton **Gérer les appareils enfants (préversion)** dans la barre de commande du haut.

1. Cliquez sur le bouton **+ Ajouter** .

1. Dans la liste des appareils, utilisez la flèche droite pour sélectionner ceux qui doivent être ajoutés en tant qu’enfants.

1. Cliquez sur **OK** pour confirmer.

Les opérations hors connexion étendues sont désormais activées dans l’appareil Edge et ses appareils enfants.  