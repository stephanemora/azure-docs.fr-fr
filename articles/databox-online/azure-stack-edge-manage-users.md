---
title: Gestion des utilisateurs sur Azure Stack Edge | Microsoft Docs
description: Décrit comment utiliser le portail Azure pour gérer les utilisateurs de votre Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 410f9702184c4ac58fb905b6f2eeebaea3e3c881
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82568918"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-azure-stack-edge"></a>Utiliser le portail Azure pour gérer les utilisateurs de votre Azure Stack Edge

Cet article explique comment gérer les utilisateurs sur votre ressource Azure Stack Edge. Vous pouvez gérer le service Azure Stack Edge via le portail Azure ou via l’interface utilisateur web locale. Utilisez le Portail Azure pour ajouter, modifier ou supprimer des utilisateurs.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Ajouter un utilisateur
> * Modification d’une planification
> * Supprimer un utilisateur

## <a name="about-users"></a>À propos des utilisateurs

Les utilisateurs peuvent être en lecture seule ou disposer de toutes les autorisations. Comme indiqué par les noms, les utilisateurs en lecture seule ne peuvent consulter que les données partagées. Les utilisateurs disposant de toutes les autorisations peuvent consulter les données de partage, écrire dessus, et les modifier ou les supprimer.

 - **Utilisateur disposant de toutes les autorisations** - Utilisateur local disposant de tous les droits d’accès.
 - **Utilisateur en lecture seule** - Utilisateur local disposant d’un accès en lecture seule. Ces utilisateurs sont associés à des partages permettant des opérations en lecture seule.

Les autorisations des utilisateurs sont d’abord définies à la création de l’utilisateur lors de la création du partage. La modification des autorisations au niveau du partage n’est actuellement pas prise en charge.

## <a name="add-a-user"></a>Ajouter un utilisateur

Pour ajouter une planification, procédez comme suit dans le Portail Azure.

1. Sur le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Vue d’ensemble > Utilisateurs**. Sélectionnez **+ Ajouter un utilisateur** dans la barre de commandes.

    ![Sélectionner Ajouter un utilisateur](media/azure-stack-edge-manage-users/add-user-1.png)

2. Indiquez le nom d’utilisateur et le mot de passe de l’utilisateur que vous souhaitez ajouter. Confirmez le mot de passe, puis sélectionnez **Ajouter**.

    ![Spécifier un nom d’utilisateur et un mot de passe](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Les utilisateurs suivants sont réservés par le système et ne devraient pas être utilisés : Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Une notification s’affiche lorsque la création de l’utilisateur commence et se termine. Une fois l’utilisateur est créé, dans la barre de commandes, sélectionnez **Actualiser** pour afficher la liste actualisée des utilisateurs.


## <a name="modify-user"></a>Modification d’une planification

Vous pouvez modifier le mot de passe associé à un utilisateur une fois l’utilisateur créé. Sélectionnez dans la liste des utilisateurs. Entrez et confirmez le nouveau mot de passe. Enregistrez les modifications.
 
![Modification d’une planification](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur, effectuez les étapes suivantes dans le Portail Azure.


1. Sur le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Vue d’ensemble > Utilisateurs**.

    ![Sélectionner un utilisateur à supprimer](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Sélectionnez un utilisateur dans la liste des utilisateurs, puis choisissez **Supprimer**.  

   ![Sélectionner Supprimer](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Quand vous y êtes invité, confirmez la suppression. 

   ![Confirmation de suppression](media/azure-stack-edge-manage-users/delete-user-3.png)

La liste des utilisateurs est actualisée afin de tenir compte de la suppression de l’utilisateur.

![Liste des utilisateurs mise à jour](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](azure-stack-edge-manage-bandwidth-schedules.md).
