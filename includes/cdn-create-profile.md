---
title: Fichier Include
description: Fichier Include
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>Créer un profil CDN

Un profil CDN est un conteneur pour les points de terminaison CDN, qui spécifie un niveau tarifaire.

1. Dans le portail Azure en haut à gauche, sélectionnez **Créer une ressource**.
    
    Le volet **Nouveau** s’affiche.
   
2. Sélectionnez **Web + Mobile**, puis **CDN**.
   
    ![Sélectionnez une ressource CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Le volet du **profil CDN** s’affiche.

    Utilisez les paramètres spécifiés dans le tableau à la suite de l’image.
   
    ![Nouveau profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Paramètre  | Valeur |
    | -------- | ----- |
    | **Name** | Entrez *my-cdn-profile-123* comme nom de profil. Ce nom doit être globalement unique. S’il est déjà utilisé, vous pouvez en entrer un autre. |
    | **Abonnement** | Sélectionnez un abonnement Azure dans la liste déroulante.|
    | **Groupe de ressources** | Sélectionnez **Créer** et entrez *my-resource-group-123* comme nom pour votre groupe de ressources. Ce nom doit être globalement unique. S’il est déjà utilisé, vous pouvez en entrer un autre. | 
    | **Emplacement du groupe de ressources** | Sélectionnez **Centre des États-Unis** dans la liste déroulante. |
    | **Niveau tarifaire** | Sélectionnez **Standard Verizon** dans la liste déroulante. |
    | **Créer un point de terminaison CDN maintenant** | Laissez non sélectionné. |  
   
3. Sélectionnez **Épingler au tableau de bord** pour enregistrer le profil sur votre tableau de bord après l’avoir créé.
    
4. Cliquez sur **Créer** pour créer le profil. 

