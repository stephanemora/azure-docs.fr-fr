---
title: Gestion des utilisateurs sur GPU Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment utiliser le portail Azure pour gérer les utilisateurs sur Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: e19c2b8a6785eb29426e150e7aa29097974765a1
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436513"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Utiliser le portail Azure pour gérer les utilisateurs sur Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment gérer les utilisateurs sur votre ressource Azure Stack Edge Pro. Vous pouvez gérer Azure Stack Edge Pro par le biais du Portail Azure ou de l’interface utilisateur web locale. Utilisez le Portail Azure pour ajouter, modifier ou supprimer des utilisateurs.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Ajouter un utilisateur
> * Modification d’une planification
> * Supprimer un utilisateur

## <a name="about-users"></a>À propos des utilisateurs

Les utilisateurs peuvent être en lecture seule ou disposer de toutes les autorisations. Les utilisateurs en lecture seule ne peuvent consulter que les données partagées. Les utilisateurs disposant de toutes les autorisations peuvent consulter les données de partage, écrire dessus, et les modifier ou les supprimer.

 - **Utilisateur disposant de toutes les autorisations** - Utilisateur local disposant de tous les droits d’accès.
 - **Utilisateur en lecture seule** - Utilisateur local disposant d’un accès en lecture seule. Ces utilisateurs sont associés à des partages permettant des opérations en lecture seule.

Les autorisations des utilisateurs sont d’abord définies à la création de l’utilisateur lors de la création du partage. Elles peuvent être modifiées à l’aide de l’Explorateur de fichiers.


## <a name="add-a-user"></a>Ajouter un utilisateur

Pour ajouter une planification, procédez comme suit dans le Portail Azure.

1. Sur le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Utilisateurs**. Sélectionnez **+ Ajouter un utilisateur** dans la barre de commandes.

    ![Sélectionner Ajouter un utilisateur](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Indiquez le nom d’utilisateur et le mot de passe de l’utilisateur que vous souhaitez ajouter. Confirmez le mot de passe, puis sélectionnez **Ajouter**.

    ![Spécifier un nom d’utilisateur et un mot de passe](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Les utilisateurs suivants sont réservés par le système et ne devraient pas être utilisés : Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Une notification s’affiche lorsque la création de l’utilisateur commence et se termine. Une fois l’utilisateur est créé, dans la barre de commandes, sélectionnez **Actualiser** pour afficher la liste actualisée des utilisateurs.


## <a name="modify-user"></a>Modification d’une planification

Vous pouvez modifier le mot de passe associé à un utilisateur une fois l’utilisateur créé. Sélectionnez dans la liste des utilisateurs. Entrez et confirmez le nouveau mot de passe. Enregistrez les modifications.

![Modification d’une planification](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur, effectuez les étapes suivantes dans le Portail Azure.


1. Sur le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Utilisateurs**.

    ![Sélectionner un utilisateur à supprimer](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Sélectionnez un utilisateur dans la liste des utilisateurs, puis choisissez **Supprimer**. Quand vous y êtes invité, confirmez la suppression.

    ![Sélectionner un utilisateur à supprimer 2](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

La liste des utilisateurs est actualisée afin de tenir compte de la suppression de l’utilisateur.

![Liste des utilisateurs mise à jour](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
