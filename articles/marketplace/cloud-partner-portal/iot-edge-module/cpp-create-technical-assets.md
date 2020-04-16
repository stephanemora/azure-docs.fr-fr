---
title: Créer des ressources techniques de module Azure IoT Edge | Place de marché Azure
description: Créez des ressources techniques pour un module IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b36d03695c107054549fba716f4cc27ecb7bba26
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983481"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Préparer vos ressources techniques de module IoT Edge

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres de module IoT Edge vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Créer une offre de module Azure IoT Edge](https://aka.ms/AzureIoTTechAsset) pour gérer vos offres migrées.

Cet article décrit les exigences que vos ressources techniques de module IoT Edge doivent remplir avant d’être publiées sur la Place de marché Microsoft Azure.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Présentation des modules IoT Edge et mise en route

Un module IoT Edge est un conteneur compatible avec Docker conçu pour être exécuté sur un appareil IoT Edge.

- Pour en savoir plus sur les modules IoT Edge, consultez [Présentation des modules Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Pour commencer le développement de votre module IoT Edge, consultez les [exigences et outils de développement de modules IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Exigences techniques

Les exigences techniques suivantes doivent être remplies pour que votre module IoT Edge puisse être certifié et publié sur la Place de marché Microsoft Azure.

### <a name="platform-support"></a>Plateforme prise en charge

Votre module IoT Edge doit prendre en charge une des options de plateforme suivantes.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plateformes de niveau 1 prises en charge par IoT Edge

Prendre en charge toutes les plateformes de niveau 1 prises en charge par IoT Edge (tel qu’enregistré dans [Prise en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Nous recommandons cette option, car elle fournit une meilleure expérience client. Les modules correspondant à ce critère sont présentés. Un module utilisant cette option de plateforme doit :

- Fournir une balise `latest` et une balise de version (par exemple, `1.0.1`), qui sont des balises manifeste générées avec [manifest-tool](https://github.com/estesp/manifest-tool) de GitHub.
- Utiliser l’[onglet Place de marché](./cpp-marketplace-tab.md) pour ajouter un lien vers les [appareils certifiés compatibles IoT Edge](https://aka.ms/iot-edge-certified). Ce lien correspond à `https://aka.ms/iot-edge-certified`, un site web où les clients peuvent parcourir ou rechercher des appareils certifiés. Ce site web est également appelé le catalogue d’appareils [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Un sous-ensemble de plateformes de niveau 1 prises en charge par IoT Edge
  
Prendre en charge un sous-ensemble (au moins un) de plateformes de niveau 1 prises en charge par IoT Edge (tel qu’enregistré dans [Prise en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Un module utilisant cette option de plateforme doit :

- Fournir une balise `latest` et une balise de version (par exemple, `1.0.1`), qui sont des balises manifeste générées avec [manifest-tool](https://github.com/estesp/manifest-tool) de GitHub si plusieurs plateformes sont prises en charge. Les balises manifeste ne sont facultatives que si une seule plateforme est prise en charge.
- Utiliser l’[onglet Place de marché](./cpp-marketplace-tab.md) pour fournir un lien vers au moins un appareil IoT Edge dans le catalogue d’appareils [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

### <a name="device-dimensions"></a>Dimensions de l’appareil

Les dimensions de module IoT Edge (UC/RAM/stockage/GPU/etc.) sur les appareils IoT Edge ciblés doivent remplir les exigences suivantes :

- Le module doit **fonctionner avec au moins un appareil certifié IoT Edge** dans le catalogue d’appareils [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).
- La **configuration matérielle minimale** doit correspondre au dernier paragraphe de la description de l’offre (sous l’[onglet Place de marché](./cpp-marketplace-tab.md)). Si vous le souhaitez, vous pouvez également répertorier la configuration matérielle recommandée si elles diffèrent considérablement. Par exemple, ajoutez la section suivante à la fin de la description de votre offre :

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuration

Cela inclut également des paramètres de configuration par défaut pour rendre le déploiement vers un appareil IoT Edge aussi simple que possible. Le conteneur peut également inclure le SDK IoT Edge pour permettre la communication avec le hub edgeHub et IoT Hub.

#### <a name="default-configuration"></a>Configuration par défaut

Les modules IoT Edge doivent être en mesure de démarrer avec les paramètres par défaut fournis dans l’[onglet de référence SKU du portail Cloud Partner](./cpp-skus-tab.md). Les paramètres par défaut suivants sont disponibles :

- **Itinéraires** par défaut
- **Propriétés souhaitées du jumeau d’appareil** par défaut
- **Variables d’environnement** par défaut
- **createOptions** par défaut

Dans un scénario où un paramètre requis pour une valeur par défaut n’est pas judicieux (par exemple, l’adresse IP du serveur d’un client), vous ajoutez un paramètre comme valeur par défaut. Cette valeur est placée entre crochets et en majuscules. Pour cet exemple, vous définissez la variable d’environnement par défaut suivante :

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentation de configuration

Tous les paramètres de configuration d’un module IoT Edge doivent être clairement documentés (comment utiliser ses itinéraires, propriétés souhaitées du jumeau, variables d’environnement, createOptions, etc.) Fournissez un lien vers la documentation, ou la documentation doit faire partie de la description de votre offre/référence SKU.

### <a name="tags-and-versioning"></a>Balises et contrôle de version

Les clients doivent être en mesure de déployer facilement un module et d’obtenir automatiquement les mises à jour de la Place de marché (dans un scénario pour développeur.) Ils doivent également être en mesure d’utiliser et de figer une version exacte qu’ils ont testée (dans un scénario de production.)

Pour répondre à ces attentes des clients et être publiés dans la Place de marché, les modules IoT Edge doivent remplir les exigences suivantes :

- Inclure une balise `latest` de manifeste, qui pointe vers la dernière version sur toutes les plateformes prises en charge.
- Les balises de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers.
- Inclure une balise « version », telle que `1.0.1`, qui pointe vers une version spécifique sur toutes les plateformes prises en charge.
- Ne mettez pas à jour les balises « version », telles que `1.0.1`, car elles doivent être fixes.

>[!Note]
>Si vous le souhaitez, le contrôle de version peut inclure des balises « déploiement de version », telles que `2.0` et `1.0`. Cela permet la prise en charge de la gestion de plusieurs versions principales en parallèle.

### <a name="telemetry"></a>Télémétrie

Les modules utilisant le Kit de développement logiciel (SDK) de module IoT doivent définir l’identificateur sur `PublisherId.OfferId.SkuId` à des fins de télémétrie. Un identificateur unique permet à la Place de marché Microsoft Azure d’identifier le nombre d’instances de module en cours d’exécution.

 Utilisez les méthodes suivantes des kits de développement logiciel (SDK) de module IoT pour définir `ProductInfo` sur cet identificateur :

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Pour les modules qui n’utilisent pas le Kit de développement logiciel (SDK) de module IoT, des insights moins précis sont disponibles sur le portail Cloud Partner, comme le nombre de téléchargements.

### <a name="security"></a>Sécurité

Les modules IoT Edge doivent demander l’accès à l’hôte avec le moins de privilèges possible. Les [modules privilégiés](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) doivent être évités.

### <a name="module-iot-sdk"></a>Kit de développement logiciel (SDK) de module IoT

Si la certification ne passe pas nécessairement par l’inclusion du Kit de développement logiciel (SDK) de module IoT, celle-ci peut offrir une meilleure expérience utilisateur avec, par exemple, la prise en charge du routage, l’envoi de message au cloud.

Le Kit de développement logiciel (SDK) de module IoT est nécessaire pour obtenir des données de télémétrie sur le nombre d’instances de module en cours d’exécution.


## <a name="recertification-process"></a>Processus de renouvellement de certification

<!-- Add legal time windows-->
Les partenaires reçoivent une notification à chaque changement cassant qui affecte leurs modules, tel que :

- Matrice de prise en charge du système d’exploitation/arch de niveau 1 prise en charge par IoT Edge
- Kit de développement logiciel (SDK) de module IoT
- Runtime IoT Edge
- Directives de certification de module IoT Edge

Les partenaires doivent mettre à jour leurs modules et renouveler leur certification à l’aide de l’outil du portail Cloud Partner.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Héberger votre module IoT Edge dans un Azure Container Registry

Pour charger votre module IoT Edge dans le portail Cloud Partner, vous devez tout d’abord l’héberger dans un [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Le module doit inclure toutes les balises que vous souhaitez publier, y compris les balises d’image référencées par une balise de manifeste.


## <a name="next-steps"></a>Étapes suivantes

- [Create your IoT Edge module offer](./cpp-create-offer.md) (Créer votre offre de module IoT Edge)
