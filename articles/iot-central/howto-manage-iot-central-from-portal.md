---
title: Gérer IoT Central à l’aide du Portail Azure | Microsoft Docs
description: Gérez IoT Central à l’aide du Portail Azure.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963825"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gérer IoT Central à l’aide du Portail Azure 
Outre la création et la gestion d’applications IoT Central depuis le site web IoT Central, vous pouvez également gérer IoT Central depuis le Portail Azure. Cet article vous explique en détail ce qui possible et comment.

## <a name="create-iot-central-applications"></a>Créer des applications IoT Central
Pour créer une nouvelle application, accédez au [Portail Azure](https://ms.portal.azure.com) et cliquez sur « Créer une ressource » dans le menu de navigation principal situé sur la gauche. 

![Portail de gestion : menu de navigation](media/howto-manage-iot-central-from-portal/image0.png)

Dans la barre de recherche, tapez le terme « IoT Central ».

![Portail de gestion : recherche](media/howto-manage-iot-central-from-portal/image0a.png)

Cliquez sur la ligne Application IoT Central dans les résultats de la recherche.

![Portail de gestion : résultats de la recherche](media/howto-manage-iot-central-from-portal/image0b.png)

Cliquez maintenant sur le bouton « Créer » pour afficher un formulaire à remplir.

![Portail de gestion : Ressource IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Renseignez tous les champs dans le formulaire. Ce formulaire ressemble à celui que vous devez remplir pour créer des applications à partir du site web IoT Central. Pour en savoir plus sur ce que vous devez renseigner dans chaque champ, consultez le démarrage rapide [Créer une application IoT Central](quick-deploy-iot-central.md). 

![Portail de gestion : créer une ressource IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Après avoir renseigné tous les champs, cliquez sur le bouton « Créer ».

## <a name="manage-existing-iot-central-applications"></a>Gérer des applications IoT Central existantes
Si vous disposez déjà d’une application Azure IoT Central, vous pouvez la supprimer ou la déplacer vers un abonnement ou groupe de ressources différent dans le portail Azure. Les applications avec essai ne sont pas visibles dans le portail Azure car aucun abonnement ne prend en charge ces essais.

Pour commencer, cliquez sur « Toutes les ressources » dans le menu de navigation principal situé sur la gauche. Saisissez le nom de votre application dans la zone de recherche, et cherchez-la dans la liste de vos ressources. Cliquez ensuite sur l’application IoT Central que vous souhaitez gérer.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image2.png)

Pour accéder à l’application, cliquez sur l’URL de l’application IoT Central.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image3.png)

Pour déplacer l’application vers un groupe de ressources différent, cliquez sur le lien **modifier** à côté du groupe de ressources. Choisissez le groupe de ressources vers lequel vous souhaitez migrer l’application dans la boîte de dialogue qui s’affiche.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image4.png)

Pour déplacer l’application vers un abonnement différent, cliquez sur le lien **modifier** à côté de l’abonnement. Choisissez l’abonnement vers lequel vous souhaitez migrer l’application dans la boîte de dialogue qui s’affiche.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central depuis le portail Azure, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)