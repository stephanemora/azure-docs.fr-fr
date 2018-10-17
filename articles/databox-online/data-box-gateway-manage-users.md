---
title: Gestion des utilisateurs par Azure Data Box Gateway | Microsoft Docs
description: Décrit comment utiliser le Portail Azure pour gérer les utilisateurs de votre Azure Data Box Gateway.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: 3bd368cf19edf1370aaeef54d3ba0e4fd26ac239
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070438"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Utilisation du Portail Azure pour gérer les utilisateurs de votre Azure Data Box Gateway 

Cet article explique comment gérer les utilisateurs de votre Azure Data Box Gateway. Vous pouvez gérer l’Azure Data Box Gateway via le Portail Azure ou via l’interface utilisateur Web locale. Utilisez le Portail Azure pour ajouter, modifier ou supprimer des utilisateurs.

> [!IMPORTANT]
> - Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Ajouter un utilisateur
> * Modifier un utilisateur
> * Supprimer un utilisateur 

## <a name="about-users"></a>À propos des utilisateurs

Les utilisateurs peuvent être en lecture seule ou disposer de toutes les autorisations. Comme indiqué par les noms, les utilisateurs en lecture seule ne peuvent consulter que les données partagées. Les utilisateurs disposant de toutes les autorisations peuvent consulter les données de partage, écrire dessus, et les modifier ou les supprimer. 

 - **Utilisateur disposant de toutes les autorisations** - Utilisateur local disposant de tous les droits d’accès. 
 - **Utilisateur en lecture seule** - Utilisateur local disposant d’un accès en lecture seule. Ces utilisateurs sont associés à des partages permettant des opérations en lecture seule.

Les autorisations des utilisateurs sont d’abord définies à la création de l’utilisateur lors de la création du partage. Une fois que les autorisations associées à un utilisateur sont définies, elles peuvent être modifiées à l’aide de l’explorateur de fichiers. 


## <a name="add-a-user"></a>Ajouter un utilisateur

Pour ajouter un utilisateur, effectuez les étapes suivantes dans le Portail Azure.

1. Dans le Portail Azure, accédez à votre ressource Data Box Gateway, puis accédez à **Vue d’ensemble**. Cliquez sur **+ Ajouter un utilisateur** dans la barre de commandes.

    ![Cliquez sur Ajouter un utilisateur](media/data-box-gateway-manage-users/add-user-1.png)

2. Indiquez le nom d’utilisateur et le mot de passe de l’utilisateur que vous souhaitez ajouter. Confirmez le mot de passe et cliquez sur **Ajouter**.

    ![Cliquez sur Ajouter un utilisateur](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Ces utilisateurs sont réservés par le système et ne doivent pas être utilisés : Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Vous êtes averti lorsque la création de l’utilisateur commence et se termine. Une fois que l’utilisateur est créé, dans la barre de commandes, cliquez sur **Actualiser** pour afficher la liste actualisée des utilisateurs.


## <a name="modify-user"></a>Modifier un utilisateur

Vous pouvez modifier le mot de passe associé à un utilisateur une fois l’utilisateur créé. Sélectionnez et cliquez dans la liste des utilisateurs. Entrez et confirmez le nouveau mot de passe. Enregistrez les modifications.
 
![Modifier un utilisateur](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur, effectuez les étapes suivantes dans le Portail Azure.

1. Sélectionnez et cliquez sur un utilisateur dans la liste des utilisateurs, puis cliquez sur **Supprimer**.  

   ![Supprimer un utilisateur](media/data-box-gateway-manage-users/delete-user-1.png)

2. Quand vous y êtes invité, confirmez la suppression. 

   ![Supprimer un utilisateur](media/data-box-gateway-manage-users/delete-user-2.png)

La liste des utilisateurs est actualisée afin de tenir compte de la suppression de l’utilisateur.

![Supprimer un utilisateur](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](data-box-gateway-manage-bandwidth-schedules.md).
