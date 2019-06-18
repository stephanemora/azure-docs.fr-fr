---
title: fichier à inclure (flux d’appareil en préversion)
description: fichier à inclure (flux d’appareil en préversion)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 93c71fa8b0c39cc16d2a8e24472e8d68717a6c32
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733243"
---
Cette section décrit comment créer un hub IoT à l’aide du [portail Azure](https://portal.azure.com).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource**, puis sélectionnez **Internet des objets**.

1. Dans la liste à droite, sélectionnez **Hub Iot**. La première page pour la création d’un hub IoT s’ouvre.

   ![Création d’un hub IoT dans le portail Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Renseignez les champs :

   a. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement à utiliser pour votre hub IoT.

   b. Pour **Groupe de ressources**, effectuez l’une des opérations suivantes : 
      * Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom que vous voulez utiliser. 
      * Pour utiliser un groupe de ressources existant, sélectionnez **Utiliser l’existant**, puis, dans la liste déroulante, sélectionnez le groupe de ressources. 
      
        Pour plus d’informations, consultez [Gérer des groupes de ressources Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. Dans la liste déroulante **Région**, sélectionnez la région dans laquelle vous voulez placer votre hub. Sélectionnez une région qui prend en charge la préversion des flux d’appareil IoT Hub : **USA Centre** ou **EUAP USA Centre**.

   d. Dans la zone **Nom du hub IoT**, entrez le nom de votre hub IoT. Le nom doit être globalement unique. Si le nom saisi est disponible, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Pour continuer à créer votre hub IoT, sélectionnez **Suivant : Taille et mise à l’échelle**.

   ![Définition de la taille et de la mise à l’échelle d’un nouveau hub IoT avec le portail Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Dans ce volet, vous pouvez accepter les paramètres par défaut et sélectionner **Vérifier + créer** en bas. Examinez les options suivantes :

   * Dans la liste déroulante **Niveau de tarification et de mise à l’échelle**, sélectionnez un des niveaux standard (**S1**, **S2**, ou **S3**) ou **F1 : Niveau Gratuit**. Ce choix peut également être guidé par la taille de votre parc et les charges de travail hors flux que vous prévoyez dans votre hub (par exemple, les messages de télémétrie). Par exemple, le niveau Gratuit est destiné aux tests et à l’évaluation. Il permet la connexion de 500 appareils à IoT Hub, avec jusqu’à 8 000 messages par jour. Chaque abonnement Azure peut créer un hub IoT dans le niveau gratuit. 

   * Pour **Nombre d’unités IoT Hub** : Ce choix dépend de la charge de travail hors flux que vous prévoyez dans votre hub. Vous pouvez sélectionner 1 pour l’instant.

   Pour plus d’informations sur les options de niveau, consultez [Choisir le bon niveau de hub IoT](../articles/iot-hub/iot-hub-scaling.md).

1. Pour passer en revue vos choix, sélectionnez l’onglet **Vérifier + créer**. Le volet qui s’ouvre est similaire à celui-ci :

   ![Informations pour la création du hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Pour créer votre hub IoT, sélectionnez **Créer**. Ce processus prend quelques minutes.
