---
title: Gestion des comptes de stockage sur Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit comment utiliser le portail Azure pour gérer un compte de stockage sur votre appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/13/2021
ms.author: alkohli
ms.openlocfilehash: 3c5cb61bf7450ea4668c6368d75615cdf43c2c40
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122525872"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le portail Azure pour gérer des comptes de stockage Edge sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique la gestion des comptes de stockage Edge sur votre ressource Azure Stack Edge Pro. Vous pouvez gérer l’appareil Azure Stack Edge Pro avec GPU via le portail Azure ou l’interface utilisateur web locale. Utiliser le portail Azure pour ajouter ou supprimer des comptes de stockage Edge sur votre appareil.

## <a name="about-edge-storage-accounts"></a>À propos des comptes de stockage Edge

Vous pouvez transférer des données depuis votre appareil Azure Stack Edge Pro avec GPU via les protocoles SMB, NFS ou REST. Pour transférer des données vers le stockage Blob à l’aide des API REST, vous devez créer des comptes de stockage Edge sur votre périphérique. 

Les comptes de stockage Edge que vous ajoutez sur l’appareil Azure Stack Edge Pro avec GPU sont mappés à des comptes de stockage Azure. Toutes les données écrites dans les comptes de stockage Edge sont automatiquement envoyées (push) vers le cloud.

Un diagramme détaillant les deux types de comptes et la façon dont les données de chacun de ces comptes sont transmises à Azure est illustré ci-dessous :

![Diagramme des comptes de stockage Blob](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Ajouter un compte de stockage Edge
> * Supprimer un compte de stockage Edge


## <a name="add-an-edge-storage-account"></a>Ajouter un compte de stockage Edge

Pour créer un compte de stockage Edge, procédez comme suit :

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Supprimer un compte de stockage Edge

Pour supprimer un compte de stockage Edge, procédez comme suit.

1. Accédez à **Configuration > Comptes de stockage** dans votre ressource. Dans la liste des comptes de stockage, sélectionnez le compte de stockage que vous souhaitez supprimer. Dans la barre de commandes supérieure, sélectionnez **Supprimer le compte de stockage**.

    ![Accéder à la liste des comptes de stockage](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. Dans le panneau **Supprimer le compte de stockage**, confirmez le compte de stockage à supprimer et sélectionnez **Supprimer**.

    ![Confirmer et supprimer le compte de stockage](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

La liste des comptes de stockage est actualisée afin de tenir compte de la suppression.


## <a name="add-delete-a-container"></a>Ajouter et supprimer un conteneur

Vous pouvez également ajouter ou supprimer les conteneurs de ces comptes de stockage.

Pour ajouter un conteneur, procédez comme suit :

1. Sélectionnez le compte de stockage que vous souhaitez gérer. Dans la barre de commandes supérieure, sélectionnez **+ Ajouter un conteneur**.

    ![Sélectionner le compte de stockage auquel ajouter un conteneur](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Donnez un nom à votre conteneur. Ce conteneur est créé dans votre compte de stockage Edge, ainsi que dans le compte de stockage Azure mappé à ce compte. 

    ![Ajouter un conteneur Edge](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

La liste des conteneurs est actualisée afin de tenir compte du conteneur qui vient d’être ajouté.

![Liste de conteneurs mise à jour](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

Vous pouvez maintenant sélectionner un conteneur dans cette liste et sélectionner **+ Supprimer un conteneur** dans la barre de commandes supérieure. 

![Supprimer un conteneur](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Synchroniser des clés de stockage

Chaque compte de stockage Azure possède 2 clés d’accès de stockage de 512 bits, qui servent à l’authentification lors de l’accès au compte de stockage. Vous devez fournir l’une de ces deux clés lorsque votre appareil Azure Stack Edge accède à votre fournisseur de services de stockage cloud (dans ce cas, Azure).

Un administrateur Azure peut régénérer ou modifier la clé d’accès en accédant directement au compte de stockage (via le service Stockage Azure). Le service Azure Stack Edge et l’appareil ne voient pas cette modification automatiquement.
 
Pour informer Azure Stack Edge de la modification, vous devez accéder au service Azure Stack Edge, accéder au compte de stockage, puis synchroniser la clé d’accès. Le service obtient alors la clé la plus récente, chiffre les clés, puis envoie la clé chiffrée à l’appareil. Lorsque l’appareil obtient la nouvelle clé, il peut continuer à transférer les données vers le compte de stockage Azure. 
 
Pour fournir les nouvelles clés à l’appareil, accédez au portail Azure et synchronisez les clés d’accès de stockage. Procédez comme suit : 

1. Dans votre ressource, sélectionnez le compte de stockage que vous souhaitez gérer. Dans la barre de commandes supérieure, sélectionnez **Synchroniser la clé de stockage**.

    ![Sélectionner la clé de stockage de synchronisation](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Quand vous êtes invité à confirmer l’opération, sélectionnez **Oui**.

    ![Sélectionner la clé de stockage de synchronisation 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer des utilisateurs via le Portail Azure](azure-stack-edge-gpu-manage-users.md).
