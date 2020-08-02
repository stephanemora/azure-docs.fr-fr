---
title: Nouveautés Actualisation de la préversion de l’IoT Plug-and-Play | Microsoft Docs
description: Découvrez les nouveautés de la mise en production d’actualisation de la préversion de l’IoT Plug-and-Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c2dcba05e3b1fc5cfc32d6f1b38c22b811b73626
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352648"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Actualisation de la préversion de l’IoT Plug-and-Play

Cet article décrit les principales modifications apportées aux kits de développement logiciel (SDK), aux bibliothèques, aux outils et aux services dans la mise en production d’actualisation de la préversion de l’IoT Plug-and-Play en juillet 2020. La mise en production précédente de la préversion de l’IoT Plug-and-Play était datée d’août 2019.

## <a name="digital-twins-definition-language-dtdl"></a>Digital Twins Definition Language (DTDL)

Cette mise en production ajoute la prise en charge du langage [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) et déprécie le langage [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

La liste suivante présente les principales différences entre les langages DTDL v1 et DTDL v2. En langage DTDL v2 :

- Les ID de modèle ont le préfixe `dtmi` au lieu de `urn`. Les identificateurs de modèle de jumeau numérique (DTMI) remplacent les ID de jumeau numérique portant le préfixe `urn` utilisés dans le langage DTDL v1. La version est désormais précédée d’un point-virgule `;`. Par exemple, au lieu d’utiliser `urn:CompanyName:Model:1` comme précédemment, vous utilisez désormais `dtmi:CompanyName:Model;1`.
- Définissez `@context` sur `dtmi:dtdl:context;2` au lieu de `http://azureiot.com/v1/contexts/IoTModel.json`.
- Utilisez le type **Interface** au lieu des types **CapabilityModel** pour modéliser un appareil.
- Des **composants** remplacent les instances d’**interface**. Vous pouvez définir des **relations** et des **composants** dans le cadre du contenu d’une **interface**.
- Une **interface** peut hériter d’une autre **interface**.
- Vous pouvez étendre le langage DTDL à l’aide de _types sémantiques extensibles_. Ce système de type extensible offre davantage de flexibilité que les types sémantiques codés en dur, tels que la température et l’humidité en langage DTDL v1.
- La propriété **displayUnit** a été supprimée.
- Vous ne pouvez pas utiliser de traits de soulignement de début ou de fin dans un nom. Les traits de soulignement de début dans un nom sont réservés à l’usage du système.

Pour utiliser le langage DTDL v1, vous avez besoin de la version précédente du Kit de développement logiciel (SDK) et de l’Explorateur Azure IoT 0.10.x. Pour utiliser le langage DTDL v2, vous avez besoin de la dernière version du Kit de développement logiciel (SDK) et de l’Explorateur Azure IoT 0.11.x.

## <a name="no-component-and-multiple-component-implementations"></a>Implémentations sans composant et avec plusieurs composants

Des appareils simples utilisant quelques données de télémétrie, commandes ou propriétés peuvent être décrits dans une simple interface sans utiliser de composants. Tout appareil existant peut être converti en IoT Plug-and-Play en annonçant l’**ID de modèle** sans aucune modification de l’implémentation d’appareil existante.

Des appareils plus complexes peuvent regrouper des données de télémétrie, commandes et propriétés sur différentes interfaces pour gérer la complexité et permettre leur réutilisation sur les appareils. Ces appareils doivent être mis à jour pour suivre un ensemble de règles simples définies dans les [conventions de message de la préversion de l’IoT Plug-and-Play](concepts-convention.md).

## <a name="registration-and-discovery"></a>Inscription et découverte

Dans cette version, les appareils annoncent leur **ID de modèle** avec IoT Hub sur chaque connexion. IoT Hub met en cache l’**ID de modèle** activant des solutions back-end pour récupérer l’**ID de modèle** à l’aide de la propriété `modelId` de jumeau d’appareil. Il est également possible de récupérer l’**ID de modèle**à partir de `$metadata.$model` dans le jumeau numérique.

## <a name="microsoft-defined-interfaces"></a>Interfaces définies par Microsoft

Les interfaces définies par Microsoft suivantes sont déconseillées et ne sont pas publiées dans le nouveau référentiel de modèles :

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

L’interface suivante est publiée dans le nouveau référentiel de modèles : `dtmi:azure:DeviceManagement:DeviceInformation;1` disponible dans l’URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

La structure d’événement de l’[événement source](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) **DigitalTwinChangeEvents**  a changé pour utiliser le format de **JSON-Patch**. Cette modification constitue un changement cassant sans prise en charge de la compatibilité descendante.

## <a name="message-routing"></a>Routage de messages

Les messages de télémétrie présentent les modifications suivantes dans la collection [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md).

Celle-ci comprend désormais une propriété **dt-dataschema** qui est l’**ID de modèle** inscrit par l’appareil.

La propriété **dt-subject** représente le composant qui envoie un message de télémétrie.

La propriété **iothub-interface-name** est déconseillée.

## <a name="device-and-service-sdks"></a>Kits de développement logiciel (SDK) de services et d’appareils

Il n’existe pas de compatibilité descendante avec des préversions précédentes des kits de développement logiciel (SDK). Vous devez modifier votre code si vous passez à la dernière préversion d’un Kit de développement logiciel (SDK).

Avec l’approche basée sur une convention, des kits de développement logiciel (SDK) de client d’appareil distincts ne sont pas nécessaires. À partir de cette préversion, les bibliothèques **DigitalTwinClient** existantes sont déconseillées dans toutes les langues. Au lieu de cela, les kits de développement logiciel (SDK) de client d’appareil IoT Hub ont été mis à jour afin d’inclure une option pour annoncer l’**ID de modèle**.

Les appareils qui n’utilisent pas de composants requièrent des modifications de code minimales, en annonçant simplement l’**ID de modèle**. Des appareils plus complexes utilisant plusieurs composants peuvent nécessiter des fonctions réutilisables pour implémenter les [conventions](concepts-convention.md). Les exemples d’appareils incluent un ensemble de fonctions que vous pouvez réutiliser dans l’implémentation de votre appareil.

### <a name="service-sdks"></a>Kits de développement logiciel (SDK) IoT Service

Le Kit de développement logiciel (SDK) du service est disponible en [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) et [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>Extension VS Code

L’extension [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) assure la prise en charge de la création en langage DTDL v1, l’intégration avec la version précédente du référentiel de modèles et la génération de code.

Si vous avez besoin de la prise en charge de la création en langage DTDL v2 dans VS Code, installez la nouvelle [extension DTDL](https://github.com/azure/vscode-dtdl) dans VS Code. L’extension ne fournit pas d’intégration avec le référentiel de modèles ou la génération de code. La gestion des modèles dans le référentiel s’effectue désormais à l’aide d’une [interface utilisateur web](https://aka.ms/iotmodelrepo).

## <a name="digital-twin-service-side-rest-apis"></a>API REST côté service de jumeau numérique

Les [API REST côté service de jumeau numérique](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) et les charges utiles ont changé. Les API prises en charge sont les suivantes :

- Récupérer un jumeau numérique.
- Appeler une commande.
- Mettre à jour un jumeau numérique à l’aide d’une charge utile **JSON-Patch**.

Les API REST existantes continuent d’être prises en charge dans cette mise en production.

## <a name="model-repository"></a>Référentiel de modèles

Il existe désormais un unique référentiel de modèles qui contient à la fois des modèles publiés publics et des modèles d’entreprise protégés par un RBAC privé. Tous les modèles ont un identificateur unique et sont immuables une fois créés.

Les référentiels de modèles d’entreprise existants de la mise en production précédente ne sont pas pris en charge dans cette mise en production. Vous pouvez continuer à utiliser le site web [Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com/products) pour gérer les anciens modèles DTDL v1. Toutefois, vous ne pouvez plus utiliser ce site web pour inscrire, tester et certifier des appareils.

L’extension Azure IoT pour Azure CLI ne prend pas en charge le nouveau référentiel de modèles. Les commandes `az iot pnp` fonctionnent uniquement avec les référentiels de modèles de la mise en production précédente.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central fait actuellement l’objet d’une mise à jour pour prendre en charge la mise en production d’actualisation de la préversion de l’IoT Plug-and-Play.
