---
title: Déployer des modules à partir du Portail Azure – Azure IoT Edge
description: Utilisez votre IoT Hub dans le Portail Azure pour envoyer (push) un module IoT Edge de votre IoT Hub à votre appareil IoT Edge, comme configuré par un manifeste de déploiement.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 754c106db42f3f0695ad023e736993bee82e9757
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82133919"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Déployer des modules Azure IoT Edge à partir du portail Azure

Une fois que vous avez créé des modules IoT Edge avec votre logique métier, vous pouvez les déployer sur vos appareils afin qu’ils opèrent à la périphérie. Si plusieurs modules fonctionnent ensemble pour collecter et traiter les données, vous pouvez les déployer tous à la fois et déclarer les règles de routage qui les connectent.

Cet article explique comment le portail Azure vous aide à créer un manifeste de déploiement et à étendre le déploiement à un appareil IoT Edge. Pour plus d’informations sur la création d’un déploiement ciblant plusieurs appareils en fonction de leurs balises partagées, consultez [Déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.
* Un [appareil IoT Edge](how-to-register-device.md#register-in-the-azure-portal) avec le runtime IoT Edge installé.

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

Le portail Azure comprend un Assistant qui vous guide à travers la création du manifeste de déploiement, vous évitant de générer le document JSON manuellement. Il comporte trois étapes : **Ajouter des modules**, **Spécifier des routes** et **Vérifier le déploiement**.

### <a name="select-device-and-add-modules"></a>Sélectionner l’appareil et ajouter des modules

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Dans le volet gauche, dans le menu, sélectionnez **IoT Edge**.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Dans la barre supérieure, sélectionnez **Définir des modules**.
1. Dans la section **Paramètres de Container Registry** de la page, fournissez les informations d’identification permettant d’accéder à n’importe quels registres de conteneurs privés qui contiennent des images de module.
1. Dans la section **Modules IoT Edge** de la page, sélectionnez **Ajouter**.
1. Examinez les types de modules dans le menu déroulant :

   * **Module IoT Edge** : vous fournissez le nom du module et l’URI de l’image conteneur. Par exemple, l’URI de l’image de l’exemple de module SimulatedTemperatureSensor est `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Si l’image de module est stockée dans un registre de conteneurs privé, ajoutez les informations d’identification sur cette page pour accéder à l’image.
   * **Module Marketplace** : modules hébergés dans la Place de marché Azure. Certains modules de la Place de marché requièrent une configuration supplémentaire. passez donc en revue les détails du module dans la liste [Modules IoT Edge de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).
   * **Module Azure Stream Analytics** : les modules générés à partir d’une charge de travail Azure Stream Analytics.

1. Après avoir ajouté un module, sélectionnez le nom du module dans la liste pour ouvrir les paramètres du module. Renseignez les champs facultatifs si nécessaire. Pour plus d’informations sur les options de création de conteneur, la stratégie de redémarrage et l’état souhaité, consultez [Propriétés souhaitées pour EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Pour plus d’informations sur le jumeau de module, consultez [Définir ou mettre à jour les propriétés souhaitées](module-composition.md#define-or-update-desired-properties).
1. Si nécessaire, répétez les étapes 5 à 8 pour ajouter d’autres modules à votre déploiement.
1. Sélectionnez **Suivant : Itinéraires** pour passer à la section Itinéraires.

### <a name="specify-routes"></a>Spécifier des routes

Sous l’onglet **Itinéraires**, vous définissez la manière dont les messages sont transmis entre les modules et IoT Hub. Les messages sont construits à l’aide de paires nom/valeur. Par défaut, un itinéraire est nommé **route** et défini sous la forme **FROM /messages/\* INTO $upstream**, ce qui signifie que tous les messages issus des modules sont envoyés à votre hub IoT.  

Ajoutez ou mettez à jour les itinéraires avec des informations issues de [Déclarer des itinéraires](module-composition.md#declare-routes), puis sélectionnez **Suivant : Vérifier + créer** pour passer à l’étape suivante de l’Assistant.

### <a name="review-deployment"></a>Vérifier le déploiement

La section de vérification vous montre le manifeste de déploiement JSON qui a été créé en fonction de vos sélections dans les deux sections précédentes. Notez qu’il existe deux modules déclarés que vous n’avez pas ajoutés : **$edgeAgent** et **$edgeHub**. Ces deux modules composent le [runtime IoT Edge](iot-edge-runtime.md) et sont des valeurs par défaut requises dans chaque déploiement.

Vérifiez les informations de votre déploiement, puis sélectionnez **Créer**.

## <a name="view-modules-on-your-device"></a>Afficher les modules sur votre appareil

Une fois les modules déployés sur votre appareil, vous pouvez les voir tous dans la page des détails de l’appareil de votre IoT Hub. Cette page affiche le nom de chaque module déployé, ainsi que des informations utiles telles que le code de sortie et l’état du déploiement.

## <a name="deploy-modules-from-azure-marketplace"></a>Déployer des modules à partir de la Place de marché Azure

La [Place de marché Azure](https://azuremarketplace.microsoft.com/) est un marché d’applications et de services en ligne qui vous permet de naviguer au milieu d’un large éventail d’applications et de solutions d’entreprise certifiées et optimisées pour s’exécuter sur Azure, notamment les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Vous pouvez déployer un module IoT Edge à partir de la Place de marché Azure ou de votre IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Déployer à partir d’Azure Marketplace

Consultez les modules IoT Edge sur la Place de marché et lorsque vous trouvez celui que vous souhaitez déployer, vous pouvez le déployer en sélectionnant **Créer** ou **Obtenir**. Effectuez les étapes de l’Assistant de déploiement qui peuvent varier en fonction du module IoT Edge que vous avez sélectionné :

1. Acceptez les conditions d’utilisation et la politique de confidentialité du fournisseur en sélectionnant **Continuer**. Vous devrez peut-être d’abord fournir des informations de contact.
1. Choisissez votre abonnement et l’IoT Hub auquel l’appareil cible est joint.
1. Choisissez **Déployer sur un appareil**.
1. Entrez le nom de l’appareil ou sélectionnez **Rechercher un appareil** pour naviguer entre les appareils inscrits auprès du hub.
1. Sélectionnez **Créer** pour continuer le processus standard de la configuration d’un manifeste de déploiement, y compris l’ajout d’autres modules si vous le souhaitez. Les détails du nouveau module, tels que l’URI de l’image, les options de création et les propriétés souhaitées, sont prédéfinis mais peuvent être modifiés.

Vérifiez que le module est déployé dans votre IoT Hub dans le Portail Azure. Sélectionnez votre appareil, sélectionnez **Définir des modules** et le module doit être listé dans la section **Modules IoT Edge**.

### <a name="deploy-from-azure-iot-hub"></a>Déployer à partir d’Azure IoT Hub

Vous pouvez rapidement déployer un module à partir de la Place de marché Azure sur votre appareil dans votre IoT Hub dans le Portail Azure.

1. Dans le portail Azure, accédez à votre hub IoT.
1. Dans le volet gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.
1. Sélectionnez l’appareil IoT Edge qui doit recevoir le déploiement.
1. Dans la barre supérieure, sélectionnez **Définir des modules**.
1. Dans la section **Modules IoT Edge**, cliquez sur **Ajouter**, puis sélectionnez **Module de la Place de marché** dans le menu déroulant.

![Ajouter un module dans IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Choisissez un module dans la page **Module de la Place de marché IoT Edge**. Le module que vous sélectionnez est automatiquement configuré pour vos abonnement, groupe de ressources et appareil. Il apparaît ensuite dans votre liste de modules IoT Edge. Certains modules peuvent nécessiter une configuration supplémentaire.

> [!TIP]
> Les informations relatives aux modules IoT Edge à partir du IoT Hub Azure sont limitées. Vous pouvez d’abord en savoir plus sur les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) dans la Place de marché Azure.

Sélectionnez **Suivant : Itinéraires** et poursuivez le déploiement comme décrit dans [Spécifier des itinéraires](#specify-routes) et [Passer en revue le déploiement](#review-deployment) plus haut dans cet article.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-at-scale.md).
