---
title: Interfaces communes – Préversion d’IoT Plug-and-Play | Microsoft Docs
description: Description des interfaces communes pour les développeurs IoT Plug-and-Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770484"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfaces communes de la préversion d’IoT Plug-and-Play

Tous les appareils IoT Plug-and-Play sont censés implémenter des interfaces communes. Les interfaces communes bénéficient des solutions IoT, car elles offrent des fonctionnalités cohérentes. La [certification](tutorial-build-device-certification.md) requiert que votre appareil implémente plusieurs interfaces communes. Vous pouvez récupérer des définitions d’interfaces communes dans le référentiel de modèles public.

## <a name="summary-of-common-interfaces"></a>Résumé des interfaces communes

| Nom | id | Description | Implémentée par le kit de développement logiciel (SDK) Azure IoT | Doit être déclarée dans le modèle de capacité |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informations sur le modèle | urn:azureiot:ModelDiscovery:ModelInformation:1 | Pour que les appareils déclarent l’ID et les interfaces du modèle de capacité. Requise pour tous les appareils IoT Plug-and-Play. | Oui | Non |
| Informations sur le kit de développement logiciel (SDK) de client de jumeau numérique | urn:azureiot:Client:SDKInformation:1 | Kit de développement logiciel (SDK) pour connecter l’appareil à Azure. Requis pour la [certification](tutorial-build-device-certification.md) | Oui | Non |
| Informations sur l'appareil | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informations sur le matériel et le système d’exploitation de l’appareil. Requis pour la [certification](tutorial-build-device-certification.md) | Non | Oui |
| Définition de modèle | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Pour que les appareils déclarent la définition complète de leur modèle de capacité et des interfaces. Doit être implémentée lorsque les définitions de modèle ne sont pas hébergées dans un référentiel de modèles. | Non | Oui |
| Jumeau numérique | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Afin que les développeurs de solutions récupèrent l’ID de modèle de capacité et les ID d’interfaces pour un jumeau numérique. Cette interface n’est ni déclarée ni implémentée par un appareil IoT Plug-and-Play. | Non | Non |

- Implémentée par le kit de développement logiciel (SDK) Azure IoT : indique si le kit de développement logiciel (SDK) Azure IoT implémente les fonctionnalités déclarées dans les interfaces. Les appareils IoT Plug-and-Play qui utilisent le kit de développement logiciel (SDK) Azure IoT n’ont pas besoin d’implémenter cette interface.
- Doit être déclaré dans le modèle de capacité : si « oui », cette interface doit être déclarée dans la section `"implements":` du modèle de capacité de l’appareil pour cet appareil IoT Plug-and-Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Récupérer les définitions d’interfaces dans le référentiel public

### <a name="cli"></a>Interface de ligne de commande

Vous pouvez utiliser l’extension Azure IoT pour que l’interface de ligne de commande Azure récupère les interfaces communes dans le référentiel de modèles public.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>Visual Studio Code

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez IoT Plug-and-Play   **: Ouvrir le référentiel de modèles**. Choisissez **Public repository (Référentiel public)** . Le référentiel de modèles public s’ouvre dans VS Code.

1. Dans le référentiel de modèles public, entrez le nom de l’interface dans le champ de recherche.

1. Pour créer une copie locale de l’interface, sélectionnez-la dans les résultats de la recherche, puis sélectionnez **Télécharger**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert les interfaces communes, voici quelques ressources supplémentaires :

- [Langage de définition de jumeau numérique (DTDL)](https://aka.ms/DTDL)
- [SDK d’appareils C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST IoT](https://docs.microsoft.com/rest/api/iothub/device)
