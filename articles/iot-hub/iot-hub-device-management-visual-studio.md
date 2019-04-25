---
title: Gestion des appareils Azure IoT avec Cloud Explorer pour Visual Studio | Microsoft Docs
description: Utilisez Cloud Explorer pour Visual Studio pour la gestion des appareils Azure IoT Hub, avec les méthodes directes et les options de gestion des propriétés souhaitées du jumeau.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399554"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Utiliser Cloud Explorer pour Visual Studio pour la gestion des appareils Azure IoT Hub

![Diagramme de bout en bout](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) est une extension Visual Studio utile qui vous permet de visualiser vos ressources Azure, d’inspecter leurs propriétés et d’exécuter des actions de développeur essentielles à partir de Visual Studio. Il est fourni avec des options de gestion que vous pouvez utiliser pour effectuer diverses tâches.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Option de gestion          | Tâche                    |
|----------------------------|--------------------------------|
| Méthodes directes             | Faites agir un appareil comme commençant/arrêtant d’envoyer des messages ou comme redémarrant l’appareil.                                        |
| Lire le jumeau d’appareil           | Obtenez l’état signalé d’un appareil. Par exemple, l’appareil signale que le voyant clignote maintenant.                                    |
| Mettre à jour le jumeau d’appareil         | Mettez un appareil dans certains états, par exemple en réglant un voyant sur le vert ou en définissant l’intervalle d’envoi de télémétrie sur 30 minutes.         |
| Messages Cloud vers appareil   | Envoyez des notifications à un appareil. Par exemple, « Forte probabilité de pluie aujourd'hui. N’oubliez pas de prendre un parapluie avec vous. »              |

Pour plus d’explications sur les différences et des conseils sur l’utilisation de ces options, consultez [l’aide sur la communication appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) et [l’aide sur la communication cloud-à-appareil](iot-hub-devguide-c2d-guidance.md).

Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez. Pour plus d’informations sur les représentations d’appareil, consultez [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenu

Vous allez apprendre à utiliser Cloud Explorer pour Visual Studio avec diverses options de gestion sur votre ordinateur de développement.

## <a name="what-you-do"></a>Procédure

Exécuter Cloud Explorer pour Visual Studio avec diverses options de gestion.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un abonnement Azure actif
- Un Azure IoT Hub sous votre abonnement
- Microsoft Visual Studio 2017 Update 8 ou ultérieur
- Composant Cloud Explorer de Visual Studio Installer (sélectionné par défaut avec la charge de travail Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Mettre à jour Cloud Explorer vers la version la plus récente

Le composant Cloud Explorer de Visual Studio Installer prend uniquement en charge le suivi des messages appareil-à-cloud et cloud-à-appareil. Vous devez télécharger et installer la dernière version de [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) pour accéder aux options de gestion.

## <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

1. Dans la fenêtre **Cloud Explorer** de Visual Studio, cliquez sur l’icône Gestion de compte. Vous pouvez ouvrir la fenêtre Cloud Explorer à partir du menu **Affichage** > **Cloud Explorer**.

    ![Cliquez sur Gestion de compte.](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Cliquez sur **Gérer les comptes** dans Cloud Explorer.
1. Cliquez sur **Ajouter un compte** dans la nouvelle fenêtre pour vous connecter à Azure pour la première fois.
1. Une fois que vous êtes connecté, la liste de vos abonnements Azure s’affiche. Sélectionnez les abonnements Azure que vous souhaitez visualiser, puis cliquez sur **Appliquer**.
1. Développez **Votre abonnement** > **IoT Hubs** > **Votre IoT Hub**, la liste des appareils s’affichent sous le nœud de votre hub IoT. Cliquez avec le bouton droit sur un appareil pour accéder aux options de gestion.

    ![Options de gestion](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Méthodes directes

1. Cliquez avec le bouton droit sur votre appareil et sélectionnez **Appeler une méthode d’appareil directe**.
1. Entrez le nom de la méthode et la charge utile dans la zone d’entrée.
1. Les résultats seront affichés dans le volet de sortie **IoT Hub**.

## <a name="read-device-twin"></a>Lire le jumeau d’appareil

1. Cliquez avec le bouton droit sur votre appareil et sélectionnez **Modifier le jumeau d’appareil**.
1. Un fichier **azure-iot-device-twin.json** s’ouvre avec le contenu du jumeau d’appareil.

## <a name="update-device-twin"></a>Mettre à jour le jumeau d’appareil

1. Apportez quelques modifications aux champs **tags** ou **properties.desired** du fichier **azure-iot-device-twin.json**.
1. Appuyez sur **Ctrl+S** pour mettre à jour le jumeau d’appareil.
1. Les résultats seront affichés dans le volet de sortie **IoT Hub**.

## <a name="send-cloud-to-device-messages"></a>Envoi de messages cloud vers appareil

Pour envoyer un message de votre hub IoT vers votre appareil, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur votre appareil et sélectionnez **Envoyer un message cloud-à-appareil**.
1. Entrez le message dans la zone de saisie.
1. Les résultats seront affichés dans le volet de sortie **IoT Hub**.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris comment utiliser Cloud Explorer pour Visual Studio avec diverses options de gestion.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]