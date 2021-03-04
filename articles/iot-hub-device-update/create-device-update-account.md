---
title: Créer un compte Device Update dans Device Update pour Azure IoT Hub | Microsoft Docs
description: Créez un compte Device Update dans Device Update pour Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0201882b74d2acf57c246d2dc63530ca8b1f010
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692606"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Gestion des ressources Device Update pour IoT Hub

Pour démarrer avec Device Update, vous devez créer un compte et une instance Device Update, et définir des rôles de contrôle d’accès. 

## <a name="prerequisites"></a>Prérequis

* Accès à un hub IoT. Il est recommandé d’utiliser un niveau S1 (Standard) ou supérieur. 
* Navigateurs pris en charge :
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Créer un compte Device Update

1. Accédez au [portail Azure](https://portal.azure.com).

2. Cliquez sur Créer une ressource et recherchez « Device Update pour IoT Hub ».

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Capture d’écran d’une ressource Device Updatel pour IoT Hub." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Cliquez sur Créer -> Device Update pour IoT Hub.

4. Spécifiez l’abonnement Azure à associer à votre compte et à votre groupe de ressources Device Update.

5. Spécifiez un nom et un emplacement pour votre compte Device Update.

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Capture d’écran des détails du compte." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Vous pouvez accéder à la [page Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) pour découvrir les régions où Device Update pour IoT Hub est disponible. Si Device Update pour IoT Hub n’est pas disponible dans votre région, vous pouvez choisir de créer un compte dans une région disponible la plus proche de vous. 

6. Cliquez sur « Suivant : Vérifier + créer> ».

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Capture d’écran de la vérification des détails du compte." lightbox="media/create-device-update-account/account-review.png":::

7. Passez en revue les détails, puis sélectionnez « Créer ». Vous voyez que votre déploiement est en cours. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Capture d’écran du déploiement du compte en cours." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. La modification de l’état du déploiement passe à « terminé » au bout de quelques minutes. Cliquez sur « Accéder à la ressource ».

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Capture d’écran du déploiement du compte terminé." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Créer une instance Device Update 

Une instance Device Update est associée à un seul hub IoT. Sélectionnez le hub IoT qui sera utilisé avec Device Update. Nous allons créer une stratégie d’accès partagé au cours de cette étape pour garantir que Device Update utilise seulement les autorisations nécessaires pour travailler avec IoT Hub (écriture dans le registre et connexion au service). Cette stratégie garantit que l’accès est limité seulement à Device Update.

Pour créer une instance Device Update une fois qu’un compte a été créé

1. Une fois que vous êtes dans la ressource de compte que vous venez de créer, accédez au panneau « Instances » de la gestion des instances.

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Capture d’écran de la gestion des instances au sein du compte." lightbox="media/create-device-update-account/instance-blade.png":::

2. Cliquez sur « Créer et spécifier un nom d’instance » et sélectionnez votre hub IoT.

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Capture d’écran des détails de l’instance." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Le hub IoT que vous liez à votre ressource Device Update n’a pas besoin d’être dans la même région que votre compte Device Update. Cependant, pour de meilleures performances, il est recommandé que votre hub IoT se trouve dans une région identique ou proche de la région de votre compte Device Update. 

3. Cliquez sur « Créer ». Vous voyez l’instance dans un état « Création ». 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Capture d’écran de la création de l’instance." lightbox="media/create-device-update-account/instance-creating.png":::

4. Le déploiement de l’instance prend de 5 à 10 minutes. Actualisez l’état jusqu’à ce que « État de provisionnement » passe à « Réussite ».

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Capture d’écran de la création de l’instance terminée." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configurer IoT Hub 

Pour que Device Update reçoive des notifications de modification d’IoT Hub, Device Update s’intègre au hub d’événements « Intégré ». Le fait de cliquer sur le bouton « Configurer IoT Hub » configure les routes de messages et la stratégie d’accès nécessaires pour communiquer avec les appareils IoT. 

Pour configurer IoT Hub

1. Une fois que « État de provisionnement » de l’instance devient « Réussite », sélectionnez l’instance dans le panneau Gestion des instances. Cliquez sur « Configurer IoT Hub ».

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Capture d’écran de la configuration d’IoT Hub pour une instance." lightbox="media/create-device-update-account/instance-configure.png":::

2. Sélectionnez « J’accepte d’effectuer ces modifications ».

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Capture d’écran de l’acceptation de la configuration d’IoT Hub pour une instance." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Cliquez sur « Mettre à jour ».

[Découvrez les routes de messages qui sont configurées.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configurer les rôles de contrôle d’accès

Pour permettre à d’autres utilisateurs d’accéder à Device Update, l’accès à cette ressource doit être accordé à ces utilisateurs. 

1. Accédez à Contrôle d’accès (IAM) dans le compte Device Update.

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Capture d’écran du contrôle d’accès dans le compte Device Update." lightbox="media/create-device-update-account/account-access-control.png":::

2. Cliquez sur « Ajouter des attributions de rôle ».

3. Sous « Sélectionner un rôle », sélectionnez un rôle Device Update dans les options présentées.
     - Administrateur Device Update
     - Lecteur Device Update
     - Administrateur de contenu Device Update
     - Lecteur du contenu Device Update
     - Administrateur des déploiements Device Update
     - Lecteur des déploiements Device Update
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Capture d’écran des attributions de rôle de contrôle d’accès dans le compte Device Update." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Découvrez le contrôle d’accès en fonction du rôle dans Device Update pour IoT Hub](device-update-control-access.md) 
    
4. Attribuer l’accès à un utilisateur ou à un groupe de Azure AD
5. Cliquez sur Enregistrer.
6. Vous êtes maintenant prêt à utiliser l’expérience Device Update depuis votre hub IoT.

## <a name="next-steps"></a>Étapes suivantes

[Importer une mise à jour en utilisant Device Update pour IoT Hub.](import-update.md)

[Découvrir les comptes et les instances Device Update.](device-update-resources.md) 

[Découvrez les rôles de contrôle d’accès Device Update.](device-update-control-access.md) 

