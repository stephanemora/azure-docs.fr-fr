---
title: Vue d’ensemble des options de kits Azure IoT device SDK
description: Découvrez quel kit Azure IoT device SDK utiliser en fonction de votre rôle et de vos tâches de développement.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 2e8a503731cc8e3774b898241140ef1bfaa72619
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591202"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Vue d’ensemble des kits Azure IoT device SDK

Les kits Azure IoT device SDK sont un ensemble de bibliothèques de client d’appareils, de guides de développement, d’exemples et de documentations. Les SDK d’appareil vous aident à connecter des appareils aux services Azure IoT programmatiquement.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagramme montrant différents SDK Azure IoT":::

Comme le montre le diagramme, plusieurs SDK d’appareil sont disponibles pour répondre à vos besoins en langage de programmation et en appareil. Des conseils sur la sélection du SDK d’appareil approprié sont disponibles dans [Quel SDK utiliser ?](#which-sdk-should-i-use) Des SDK de service Azure IoT sont également disponibles pour connecter votre application cloud au services Azure IoT sur le back-end. Cet article met l’accent sur les SDK d’appareil, mais vous pouvez en savoir plus sur les SDK de service Azure [ici](#service-sdks).

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Pourquoi utiliser les kits Azure IoT device SDK ?

Pour connecter des appareils à Azure IoT, vous pouvez créer une couche de connexion personnalisée ou utiliser des kits Azure IoT device SDK. L’utilisation des kits Azure IoT device SDK offre plusieurs avantages :

| Coût des développements &nbsp; &nbsp; &nbsp; &nbsp; | Couche de connexion personnalisée | Kits Azure IoT device SDK |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Support | Besoin de supporter et de documenter tout ce que vous créez | Avoir accès au support technique de Microsoft (GitHub, Microsoft Q&R, Microsoft Docs, Équipes de support client) |
| Nouvelles fonctionnalités | Besoin d’ajouter de nouvelles fonctionnalités Azure à l’intergiciel (middleware) personnalisé | Possibilité de tirer parti immédiatement des nouvelles fonctionnalités que Microsoft ajoute constamment aux kits SDK IoT |
| Investissement | Investir des centaines d’heures de développement incorporé pour concevoir, créer, tester et maintenir une version personnalisée | Possibilité de tirer parti d’outils open source gratuits. Le seul coût associé aux SDK est la courbe d’apprentissage. |

## <a name="which-sdk-should-i-use"></a>Quel kit SDK utiliser ?

Les kits Azure IoT device SDK sont disponibles dans des langages de programmation connus comme C, C#, Java, Node.js et Python. Deux considérations principales sont à prendre en compte quand vous choisissez un SDK : les fonctionnalités de l’appareil et la connaissance du langage de programmation par votre équipe.

### <a name="device-capabilities"></a>Fonctionnalités de l’appareil

Quand vous choisissez un SDK, vous devez prendre en compte les limites des appareils que vous utilisez. Un appareil contraint est un appareil doté d’un seul microcontrôleur (MCU) et d’une mémoire limitée. Si vous utilisez un appareil contraint, nous vous recommandons d’utiliser le [SDK Embedded C](#embedded-c-sdk). Ce SDK est conçu pour fournir l’ensemble minimal de fonctionnalités permettant de se connecter à Azure IoT. Vous pouvez aussi sélectionner des composants (client MQTT, TLS et bibliothèques de sockets) qui conviennent le mieux pour votre appareil embarqué. Si votre appareil contraint exécute également Azure RTOS, vous pouvez utiliser l’intergiciel (middleware) Azure RTOS pour vous connecter à Azure IoT. Le middleware Azure RTOS encapsule le SDK Embedded C avec des fonctionnalités supplémentaires pour simplifier la connexion de votre appareil Azure RTOS au cloud.

Un appareil non contraint est un appareil doté d’un processeur plus robuste, capable d’exécuter un système d’exploitation pour prendre en charge le runtime d’un langage comme .NET ou Python. Si vous utilisez un appareil non contraint, la considération la plus importante est la connaissance du langage.

### <a name="your-teams-familiarity-with-the-programming-language"></a>La connaissance du langage de programmation par votre équipe

Les kits Azure IoT device SDK sont implémentés dans plusieurs langages : vous pouvez donc choisir celui que vous préférez. Les SDK d’appareil s’intègrent aussi à d’autres outils répandus spécifiques à un langage. La possibilité de travailler avec un langage de développement et des outils connus permet à votre équipe d’optimiser le cycle de développement de la recherche, du prototypage, du développement de produits et de la maintenance continue.

Dans la mesure du possible, sélectionnez un SDK qui est connu de votre équipe de développement. Tous les SDK Azure IoT sont open source et plusieurs exemples sont disponibles pour que votre équipe les évalue et les teste avant de valider un SDK spécifique.

## <a name="how-can-i-get-started"></a>Comment faire pour démarrer ?

Le point de départ consiste à explorer les dépôts GitHub des SDK d’appareil Azure. Vous pouvez également essayer un [guide de démarrage rapide](quickstart-send-telemetry-central.md) qui montre comment utiliser rapidement un SDK pour envoyer de la télémétrie à Azure IoT.

Vos options pour démarrer dépendent du type d’appareil que vous avez :
- Pour les appareils contraints, utilisez le [SDK Embedded C](#embedded-c-sdk). 
- Pour les appareils qui s’exécutent sur Azure RTOS, vous pouvez développer avec le [middleware Azure RTOS](#azure-rtos-middleware). 
- Pour les appareils non contraints, vous pouvez [choisir un SDK](#unconstrained-device-sdks) dans un langage de votre choix. 

### <a name="constrained-device-sdks"></a>SDK d’appareil contraint
Ces SDK sont spécialisés pour s’exécuter sur des appareils avec des ressources de calcul ou de mémoire limitées. Pour en savoir plus sur les types d’appareil courants, consultez [Vue d’ensemble des types d’appareil Azure IoT](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>SDK Embedded C
* [Dépôt GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Exemples](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Documentation de référence](https://azure.github.io/azure-sdk-for-c/)
* [Guide pratique pour générer le SDK Embedded C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Graphique des tailles pour les appareils contraints](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Middleware Azure RTOS

* [Dépôt GitHub](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Guides de démarrage](https://github.com/azure-rtos/getting-started) et [autres exemples](https://github.com/azure-rtos/samples)
* [Documentation de référence](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDK d’appareil non contraint
Ces SDK peuvent s’exécuter sur n’importe quel appareil pouvant prendre en charge un runtime de langage d’un ordre supérieur. Ceci comprend les appareils comme les PC, les Raspberry Pi et les smartphones. Ils se différencient principalement par le langage : vous pouvez donc choisir la bibliothèque qui convient le mieux à votre équipe et à votre scénario.

#### <a name="c-device-sdk"></a>SDK d’appareil C
* [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Exemples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Documentation de référence](/azure/iot-hub/iot-c-sdk-ref/)
* [Documentation de référence sur les modules Edge](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Compiler le kit Device SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Portage du kit SDK C vers d’autres plateformes](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentation du développeur](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) pour des informations sur la compilation croisée et comment démarrer sur les différentes plateformes
* [Informations sur la consommation de ressources du Kit de développement logiciel (SDK) Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>SDK d’appareil C#

* [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Exemples](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Documentation de référence](/dotnet/api/microsoft.azure.devices)
* [Documentation de référence sur les modules Edge](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>SDK d’appareil Java

* [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Package](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Documentation de référence](/java/api/com.microsoft.azure.sdk.iot.device)
* [Documentation de référence sur les modules Edge](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>SDK d’appareil Node.js

* [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Package](https://www.npmjs.com/package/azure-iot-device)
* [Documentation de référence](/javascript/api/azure-iot-device/)
* [Documentation de référence sur les modules Edge](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>SDK d’appareil Python

* [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Package](https://pypi.org/project/azure-iot-device/)
* [Documentation de référence](/python/api/azure-iot-device)
* [Documentation de référence sur les modules Edge](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Kits de développement logiciel (SDK) IoT Service
Azure IoT offre également des SDK de service qui vous permettent de créer des applications côté solution pour gérer des appareils, obtenir des insights, visualiser les données, etc. Ces SDK sont spécifiques à chaque service Azure IoT et sont disponibles en C#, Java, JavaScript et Python pour simplifier votre expérience de développement. 

#### <a name="iot-hub"></a>IoT Hub

Les SDK de service IoT Hub vous permettent de créer des applications qui interagissent facilement avec votre hub IoT pour gérer les appareils et la sécurité. Vous pouvez utiliser ces SDK pour envoyer des messages cloud-à-appareil, pour appeler des méthodes directes sur vos appareils, pour mettre à jour les propriétés des appareils, etc.

[**En savoir plus sur IoT Hub**](https://azure.microsoft.com/services/iot-hub/) | [**Essayer de contrôler un appareil**](../iot-hub/quickstart-control-device-python.md)

**SDK de service IoT Hub C#**  : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) | [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [Exemples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples) | [Documentation de référence](/dotnet/api/microsoft.azure.devices)

**SDK de service IoT Hub Java** : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/service) | [Package](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples) | [Documentation de référence](/java/api/com.microsoft.azure.sdk.iot.service)

**SDK de service IoT Hub JavaScript** : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [Package](https://www.npmjs.com/package/azure-iothub) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Documentation de référence](/javascript/api/azure-iothub/)

**SDK de service IoT Hub Python** : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [Package](https://pypi.python.org/pypi/azure-iot-hub/) | [Exemples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Documentation de référence](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins est une offre de plateforme en tant que service (PaaS) qui permet la création de graphes de connaissances basés sur des modèles numériques d’environnements entiers. Il peut s’agir de bâtiments, d’usines, de batteries de serveurs, de réseaux énergétiques, de chemins de fer, de stades, etc., voire de villes entières. Ces modèles numériques peuvent être utilisés pour obtenir des insights qui permettent d’améliorer les produits, d’optimiser les opérations, de réduire les coûts et de fournir des expériences client exceptionnelles. Azure IoT offre des SDK de service pour faciliter la création d’applications qui utilisent la puissance d’Azure Digital Twins.

[**En savoir plus sur Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/) | [**Coder une application ADT**](../digital-twins/tutorial-code.md)

**SDK de service ADT C#**  : [Dépôt GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [Package](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [Documentation de référence](/dotnet/api/overview/azure/digitaltwins/client)

**SDK de service ADT Java** : [Dépôt GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Package](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [Documentation de référence](/java/api/overview/azure/digitaltwins/client)

**SDK de service ADT Node.js** : [Dépôt GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [Package](https://www.npmjs.com/package/@azure/digital-twins-core) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [Documentation de référence](/javascript/api/@azure/digital-twins-core/)

**SDK de service ADT Python** : [Dépôt GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Package](https://pypi.org/project/azure-digitaltwins-core/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [Documentation de référence](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

Le service IoT Hub Device Provisioning (DPS) est un service d’assistance pour IoT Hub qui autorise un provisionnement sans contact, juste-à-temps sur le hub IoT approprié et sans aucune intervention humaine. DPS permet de provisionner plusieurs millions d’appareils de façon sécurisée et scalable. Les SDK de service DPS vous permettent de créer des applications qui peuvent gérer vos appareils de façon sécurisée en créant des groupes d’inscription et en effectuant des opérations en bloc.

[**En savoir plus sur le service Device Provisioning**](../iot-dps/index.yml) | [**Essayer de créer un groupe d’inscription pour des appareils X.509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**SDK de service Device Provisioning C#**  : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service) | [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [Exemples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples) | [Documentation de référence](/dotnet/api/microsoft.azure.devices.provisioning.service)

**SDK de service Device Provisioning Java** : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src) | [Package](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [Exemples](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [Documentation de référence](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**SDK de service Device Provisioning Node.js** : [Dépôt GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [Package](https://www.npmjs.com/package/azure-iot-provisioning-service) | [Exemples](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [Documentation de référence](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Envoyer des données de télémétrie à IoT Central](quickstart-send-telemetry-central.md)
* [Démarrage rapide : Envoyer des données de télémétrie à IoT Hub](quickstart-send-telemetry-iot-hub.md)
* [Démarrage rapide : Connecter un devkit MXCHIP AZ3166 à IoT Central](quickstart-devkit-mxchip-az3166.md)
* En savoir plus sur les [avantages du développement avec les SDK Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).