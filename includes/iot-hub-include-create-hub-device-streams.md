---
title: fichier à inclure (flux d’appareil en préversion)
description: fichier à inclure (flux d’appareil en préversion)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ea82bdca23d8b34898261eb60fd908d281451fd0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825419"
---
Cette section décrit comment créer un hub IoT à l’aide du [portail Azure](https://portal.azure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Choisissez +**Créer une ressource**, puis choisissez **Internet des objets**.

3. Cliquez sur **IoT Hub** dans la liste à droite. Vous voyez le premier écran de création d’un hub IoT.

   ![Capture d’écran montrant comment créer un hub dans le portail Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   Renseignez les champs :

   **Abonnement**: Sélectionnez l’abonnement à utiliser pour votre hub IoT.

   **Groupe de ressources** : Vous pouvez créer un groupe de ressources ou utiliser un groupe existant. Pour créer un nouveau groupe, cliquez sur **Créer** et indiquez le nom que vous voulez utiliser. Pour utiliser un groupe de ressources existant, cliquez sur **Utiliser existant** et sélectionnez le groupe de ressources dans la liste déroulante. Pour plus d’informations, consultez [Gérer des groupes de ressources Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Région** : Il s’agit de la région dans laquelle vous voulez placer votre hub. Veillez à sélectionner une région prise en charge (par exemple USA Centre ou USA Centre - EUAP).

   **Nom du hub IoT** : Indiquez le nom de votre hub IoT. Ce nom doit être globalement unique. Si le nom saisi est disponible, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Cliquez sur **Suivant : taille et échelle** pour poursuivre la création de votre hub IoT.

   ![Capture d’écran montrant la définition de la taille et de la mise à l’échelle d’un nouveau hub IoT avec le portail Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   Dans cet écran, vous pouvez conserver les valeurs par défaut et cliquer simplement en bas sur **Vérifier + créer**. 

   **Tarification et niveau de mise à l’échelle** : Veillez à sélectionner le niveau Standard (S1, S2, S3) ou le niveau Gratuit (F1). Ce choix peut également être guidé par la taille de votre parc et les charges de travail hors flux que vous prévoyez dans votre hub (par exemple les messages de télémétrie). Par exemple, le niveau Gratuit est destiné aux tests et à l’évaluation. Il permet la connexion de 500 appareils à IoT Hub, avec jusqu’à 8 000 messages par jour. Chaque abonnement Azure peut créer un IoT Hub dans le niveau gratuit. 

   **Unités IoT Hub** : Ce choix dépend de la charge de la charge de travail hors flux que vous prévoyez dans votre hub : vous pouvez sélectionner 1 pour l’instant.

   Pour plus d’informations sur les autres options de niveau, consultez [Choix du bon niveau IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

5. Cliquez sur **Vérifier + créer** pour passer en revue vos choix. Vous voyez quelque chose de similaire à cet écran.

   ![Capture d’écran de la vérification des informations pour la création du nouveau hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. Cliquez sur **Créer** pour créer votre hub IoT. La création du hub prend quelques minutes.
