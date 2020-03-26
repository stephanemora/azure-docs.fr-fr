---
title: Fichier Include
description: Fichier Include
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67593989"
---
## <a name="create-a-new-cdn-profile"></a>Créer un profil CDN

Un profil CDN est un conteneur pour les points de terminaison CDN, qui spécifie un niveau tarifaire.

1. Dans le portail Azure en haut à gauche, sélectionnez **Créer une ressource**. 
    
    Le volet **Nouveau** s’affiche.
   
2. Sélectionnez **Web + Mobile**, puis **CDN**.
   
    ![Sélectionnez une ressource CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Le volet du **profil CDN** s’affiche.

3. Pour les paramètres de profil CDN, utilisez les valeurs spécifiées dans le tableau suivant :
   
    | Paramètre  | Valeur |
    | -------- | ----- |
    | **Nom** | Entrez *my-cdn-profile-123* comme nom de profil. Ce nom doit être globalement unique. S’il est déjà utilisé, vous pouvez en saisir un autre. |
    | **Abonnement** | Sélectionnez un abonnement Azure dans la liste déroulante. |
    | **Groupe de ressources** | Sélectionnez **Créer** et entrez *my-resource-group-123* comme nom pour votre groupe de ressources. S’il est déjà utilisé, vous pouvez entrer un nom différent, ou vous pouvez sélectionner **Utiliser l’existant**, puis **my-resource-group-123** dans la liste déroulante. | 
    | **Emplacement du groupe de ressources** | Sélectionnez **USA Centre** dans la liste déroulante. |
    | **Niveau tarifaire** | Sélectionnez **Standard Verizon** dans la liste déroulante. |
    | **Créer un point de terminaison CDN maintenant** | Laissez non sélectionné. |  
   
    ![Nouveau profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Sélectionnez **Épingler au tableau de bord** pour enregistrer le profil sur votre tableau de bord après l’avoir créé.
    
5. Cliquez sur **Créer** pour créer le profil. 

    Pour finaliser les profils **Azure CDN standard fournis par Microsoft** uniquement, un délai de deux heures est habituellement nécessaire. 

