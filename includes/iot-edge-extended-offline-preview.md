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
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004677"
---
## <a name="enabling-extended-offline-operation-preview"></a>Activation des opérations hors connexion étendues (préversion)
Depuis la [version v1.0.2](https://aka.ms/edge102) du runtime IoT Edge, l’appareil Edge et les appareils en aval qui y sont connectés peuvent être configurés pour permettre les opérations hors connexion étendues. 

Avec cette fonctionnalité, les modules locaux et les appareils en aval peuvent se réauthentifier auprès de l’appareil Edge et communiquer entre eux à l’aide de messages et de méthodes, même lorsqu’ils sont déconnectés d’IoT Hub. Pour plus d’informations, notamment sur l’étendue de cette fonctionnalité, consultez ce [billet de blog](https://aka.ms/iot-edge-offline) et cet [article de présentation des concepts](../articles/iot-edge/offline-capabilities.md).

Pour activer les opérations hors connexion étendues dans un scénario de passerelle, établissez une relation parent-enfant entre l’appareil Edge et les appareils en aval qui y sont connectés.

1. Sur le portail IoT Hub, dans le panneau d’informations de l’appareil Edge, cliquez sur le bouton **Gérer les appareils enfants (préversion)** dans la barre de commande du haut.

1. Cliquez sur le bouton **+ Ajouter** .

1. Dans la liste des appareils, utilisez la flèche droite pour sélectionner ceux qui doivent être ajoutés en tant qu’enfants.

1. Cliquez sur **OK** pour confirmer.

1. Dans l’écran **Définir des modules** du panneau d’informations de l’appareil Edge, cliquez sur **Configurer les paramètres avancés du runtime Edge**, puis, sous les variables d’environnement **Edge Hub**, ajoutez une entrée **UpstreamProtocol** avec la valeur **MQTT**. Ajoutez la même variable d’environnement et la même valeur pour **l’Agent Edge**. 

1. Cliquez sur **Enregistrer**, puis cliquez sur **Envoyer** pour envoyer les modifications après avoir cliqué deux fois de suite sur **Suivant**.

Les opérations hors connexion étendues sont désormais activées dans l’appareil Edge et ses appareils enfants.  