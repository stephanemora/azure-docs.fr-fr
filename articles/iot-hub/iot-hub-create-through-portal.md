---
title: Utiliser le portail Azure pour créer un IoT Hub | Microsoft Azure
description: Comment créer, gérer et supprimer des IoT Hubs Azure via le portail Azure. Inclut des informations sur les niveaux de tarification, l’évolutivité, la sécurité et la configuration de la messagerie.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 8f08141f5c14a734f89ba91045767e2a36a44fd2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985603"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Création d’un IoT Hub à l’aide du portail Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Cet article explique comment créer et gérer des hubs IoT avec le [portail Azure](https://portal.azure.com).

Pour utiliser les étapes de ce tutoriel, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Choisissez +**Créer une ressource**, puis choisissez **Internet des objets**.

3. Cliquez sur **IoT Hub** dans la liste à droite. Vous voyez le premier écran de création d’un hub IoT.

   ![Capture d’écran montrant comment créer un hub dans le portail Azure](./media/iot-hub-create-through-portal/iot-hub-create-screen-basics.png)

   Renseignez les champs.

   **Abonnement** : sélectionnez l’abonnement à utiliser pour votre hub IoT.

   **Groupe de ressources** : vous pouvez créer un groupe de ressources ou utiliser un groupe de ressources existant. Pour créer un nouveau groupe, cliquez sur **Créer** et indiquez le nom que vous voulez utiliser. Pour utiliser un groupe de ressources existant, cliquez sur **Utiliser existant** et sélectionnez le groupe de ressources dans la liste déroulante.

   **Région** : dans la liste déroulante, sélectionnez la région où vous voulez placer votre hub.

   **Nom du hub IoT** : spécifiez le nom de votre hub IoT. Ce nom doit être globalement unique. 

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Cliquez sur **Suivant : Taille et mise à l’échelle** pour accéder à l’écran suivant.

   ![Capture d’écran montrant la définition de la taille et de la mise à l’échelle d’un nouveau hub IoT avec le portail Azure](./media/iot-hub-create-through-portal/iot-hub-create-screen-size-scale.png)

   Dans cet écran, vous pouvez conserver les valeurs par défaut et cliquer simplement en bas sur **Vérifier + créer**. Vous pouvez aussi renseigner les champs en fonction de vos besoins.

   **Niveau de tarification et de mise à l’échelle** : vous pouvez choisir entre plusieurs niveaux, selon le nombre de fonctionnalités souhaitées et le nombre de messages envoyés quotidiennement via votre solution. Le niveau gratuit est destiné aux tests et à l’évaluation. Il permet la connexion de 500 appareils à IoT Hub, avec jusqu’à 8 000 messages par jour. Chaque abonnement Azure peut créer un IoT Hub dans le niveau gratuit. 

   **Unités IoT Hub** : le nombre de messages autorisés par unité par jour dépend du niveau de tarification de votre hub. Par exemple, si vous souhaitez qu’IoT Hub prenne en charge l’arrivée de 700 000 messages, vous choisissez deux unités de niveau S1.

   Pour plus d’informations sur les autres options de niveau, consultez [Choix du bon niveau IoT Hub](iot-hub-scaling.md).

   **Avancé / Partitions appareil-à-cloud** : cette propriété associe les messages appareil-à-cloud au nombre de lecteurs simultanés des messages. La plupart des hubs IoT ont besoin de seulement 4 partitions. 

5. Cliquez sur **Vérifier + créer** pour passer en revue vos choix. Vous voyez quelque chose de similaire à cet écran.

   ![Capture d’écran de la vérification des informations pour la création du nouveau hub IoT](./media/iot-hub-create-through-portal/iot-hub-create-review.png)

5. Cliquez sur **Créer** pour créer votre hub IoT. La création du hub prend quelques minutes.

## <a name="change-the-settings-of-the-iot-hub"></a>Modifier les paramètres du hub IoT

Vous pouvez modifier les paramètres d’un hub IoT existant après sa création, dans le volet IoT Hub.

![Capture d’écran montrant les paramètres pour le hub IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Voici quelques-unes des propriétés que vous pouvez définir pour un hub IoT :

**Tarification et mise à l’échelle** : vous pouvez utiliser cette propriété pour migrer vers un autre niveau ou pour définir le nombre d’unités IoT Hub. 

**Surveillance des opérations** : activez ou désactivez les différentes catégories de surveillance, comme la journalisation pour les événements liés à des messages appareil-à-cloud ou cloud-à-appareil.

**Filtre IP** : spécifiez une plage d’adresses IP qui sont acceptées ou rejetées par le hub IoT.

**Propriétés** : fournit la liste des propriétés que vous pouvez copier et utiliser ailleurs, comme l’ID de ressource, le groupe de ressources, l’emplacement, etc.

### <a name="shared-access-policies"></a>Stratégies d’accès partagé

Vous pouvez également afficher ou modifier la liste des stratégies d’accès partagé en cliquant sur **Stratégies d’accès partagé** dans la section **Paramètres**. Ces stratégies définissent les autorisations pour que les appareils et les services se connectent au hub IoT. 

Cliquez sur **Ajouter** pour ouvrir le panneau **Ajouter une stratégie d’accès partagé**.  Vous pouvez entre le nom de la nouvelle stratégie et les autorisations que vous voulez associer à celle-ci, comme illustré dans la figure suivante :

![Capture d’écran montrant l’ajout d’une stratégie d’accès partagé](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Les stratégies **Lecture de registre** et **Écriture de registre** accordent les autorisations en lecture et en écriture au registre d’identité. Le choix de l'option en écriture active automatiquement l'option en lecture.

* La stratégie de **connexion de service** autorise l’accès aux points de terminaison de service tels que la **réception appareil-à-cloud**. 

* La stratégie de **connexion d’appareil** accorde des autorisations pour envoyer et recevoir des messages à l’aide des points de terminaison côté appareil de IoT Hub.

Cliquez sur **Créer** pour ajouter la stratégie créée à la liste existante.

## <a name="message-routing-for-an-iot-hub"></a>Routage des messages pour un hub IoT

Cliquez sur **Routage des messages** sous **Messagerie** pour afficher le volet Routage des messages, où vous définissez les routes et les points de terminaison personnalisés pour le hub. Le [routage des messages](iot-hub-devguide-messages-d2c.md) vous permet de gérer la façon dont les données sont envoyées depuis vos appareils à vos points de terminaison. La première étape consiste à ajouter une nouvelle route. Vous pouvez ensuite ajouter un point de terminaison existant à la route ou créer un point de terminaison d’un des types pris en charge, comme Stockage Blob. 

![Volet Routage des messages](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Itinéraires

Routes est le premier onglet du volet Routage des messages. Pour ajouter une nouvelle route, cliquez sur +**Ajouter**. Vous voyez l’écran suivant. 

![Capture d’écran montrant l’ajout d’un itinéraire](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nommez votre hub. Le nom doit être unique au sein de la liste des routes pour ce hub. 

Pour **Point de terminaison**, vous pouvez en sélectionner un dans la liste déroulante ou en ajouter un nouveau. Dans cet exemple, un compte de stockage et un conteneur sont déjà disponibles. Pour les ajouter comme point de terminaison, cliquez sur +**Ajouter** en regard de la liste déroulante Point de terminaison et sélectionnez **Stockage Blob**. L’écran suivant montre l’endroit où le compte de stockage et le conteneur sont spécifiés.

![Capture d’écran montrant l’ajout d’un point de terminaison de stockage pour la règle de routage](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Cliquez sur **Choisir un conteneur** pour sélectionner le compte de stockage et le conteneur. Une fois que vous avez sélectionné ces champs, vous revenez au volet Point de terminaison. Utilisez les valeurs par défaut pour le reste des champs, puis sélectionnez **Créer** pour créer le point de terminaison pour le compte de stockage et l’ajouter aux règles de routage.

Pour **Source de données**, sélectionnez Messages de télémétrie des appareils. 

Ensuite, ajoutez une requête de routage. Dans cet exemple, les messages qui ont une propriété d’application nommée `level` avec une valeur égale à `critical` sont routés vers le compte de stockage.

![Capture d’écran montrant l’enregistrement d’une nouvelle règle de routage](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Cliquez sur **Enregistrer** pour enregistrer la règle de routage. Vous revenez au volet Routage des messages, et votre nouvelle règle de routage s’affiche.

### <a name="custom-endpoints"></a>Points de terminaison personnalisés

Cliquez sur l’onglet **Points de terminaison personnalisés**. Vous voyez les points de terminaison personnalisés déjà créés. Vous pouvez ajouter ici de nouveaux points de terminaison ou supprimer des points de terminaison existants. 

> [!NOTE]
> Si vous supprimez une route, cela ne supprime pas les points de terminaison affectés à cette route. Pour supprimer un point de terminaison, cliquez sur l’onglet Points de terminaison personnalisés, sélectionnez le point de terminaison que vous voulez supprimer, puis cliquez sur Supprimer.
>

Pour plus d’informations sur les points de terminaison personnalisés, consultez [Référence - Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md).

Vous pouvez définir jusqu’à 10 points de terminaison personnalisés pour un hub IoT. 

Pour voir un exemple complet montrant comment utiliser des points de terminaison personnalisés avec le routage, consultez [Routage des messages avec IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Rechercher un hub IoT spécifique

Voici deux façons de rechercher un hub IoT spécifique dans votre abonnement :

1. Si vous connaissez le groupe de ressources auquel appartient le hub IoT, cliquez sur **Groupes de ressources**, puis sélectionnez le groupe de ressources dans la liste. L’écran du groupe de ressources montre toutes les ressources de ce groupe, notamment les hubs IoT. Cliquez sur le hub que vous recherchez.

2. Cliquez sur **Toutes les ressources**. Dans le volet **Toutes les ressources**, une liste déroulante affiche par défaut `All types`. Cliquez sur la liste déroulante et décochez la case `Select all`. Recherchez `IoT Hub` et cochez la case correspondante. Cliquez sur la zone de liste déroulante pour la fermer : les entrées sont alors filtrées, affichant seulement vos hubs IoT.

## <a name="delete-the-iot-hub"></a>Supprimez IoT Hub

Pour supprimer un hub IoT, recherchez le hub IoT que vous voulez supprimer, puis cliquez sur le bouton **Supprimer** en dessous du nom du hub IoT.

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Routage des messages avec IoT Hub](tutorial-routing.md)
* [Métriques d’IoT Hub](iot-hub-metrics.md)
* [Surveillance des opérations](iot-hub-operations-monitoring.md)