---
title: Définir des fichiers de NetApp Azure et créez un volume | Microsoft Docs
description: Décrit comment définir des fichiers de NetApp Azure rapidement et de créer un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 363589116cc7b936358ce0b16cbaba9a42c66234
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078072"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Configurer Azure NetApp Files et créer un volume 

Cet article vous montre comment définir des fichiers de NetApp Azure rapidement et de créer un volume. 

## <a name="before-you-begin"></a>Avant de commencer 

Vous devez faire partie du programme Préversion publique et figurer dans la liste verte pour l’accès au fournisseur de ressources Azure Microsoft.NetApp. Pour plus d’informations sur l’adhésion au programme Préversion publique, consultez la [page d’inscription au programme Préversion publique d’Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>S’inscrire pour les fichiers NetApp Azure et le fournisseur de ressources de NetApp

1. À partir du portail Azure, cliquez sur l’icône Azure Cloud Shell dans le coin supérieur droit.

      ![Icône d’Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Spécifier l’abonnement qui a été mis en liste verte pour les fichiers de NetApp Azure :
    
        az account set --subscription <subscriptionId>

3. Inscrire le fournisseur de ressources Azure : 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Le processus d’inscription peut prendre du temps.

## <a name="create-a-netapp-account"></a>Créer un compte NetApp

1. Dans la zone de recherche du portail Azure, entrez **Azure NetApp Files** , puis sélectionnez **fichiers NetApp de Azure (aperçu)** dans la liste qui s’affiche.

      ![Sélectionnez les fichiers NetApp Azure](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Cliquez sur **+Ajouter** pour créer un nouveau compte NetApp.

     ![Créer le nouveau compte NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Dans la fenêtre Nouveau compte NetApp, fournissez les informations suivantes : 
   1. Entrez **myaccount1** pour le nom du compte. 
   2. Sélectionnez votre abonnement.
   3. Sélectionnez **créer** pour créer le nouveau groupe de ressources. Entrez **myRG1** pour le nom de groupe de ressources. Cliquez sur **OK**. 
   4. Sélectionnez l’emplacement de votre compte.  

      ![Nouvelle fenêtre NetApp compte](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Fenêtre de groupe de ressources](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Cliquez sur **créer** pour créer votre nouveau compte NetApp.

## <a name="set-up-a-capacity-pool"></a>Configurer un pool de capacité

1. À partir du Panneau de gestion de fichiers NetApp de Azure, sélectionnez votre compte NetApp (**myaccount1**).

    ![Sélectionnez le compte NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Dans le panneau de gestion de fichiers NetApp de Azure de votre compte NetApp, cliquez sur **pools de capacité**.

    ![Cliquez sur les pools de capacité](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Cliquez sur **+ ajouter des pools**. 

    ![Cliquez sur Ajouter des pools](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Fournissent des informations pour le pool de capacité : 
    1. Entrez **mypool1** en tant que le nom du pool.
    2. Sélectionnez **Premium** pour le niveau de service. 
    3. Spécifiez **4 (TIO)** en tant que la taille du pool. 

5. Cliquez sur **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

1. Dans le panneau de gestion de fichiers NetApp de Azure de votre compte NetApp, cliquez sur **Volumes**.

    ![Cliquez sur Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Cliquez sur **+ Ajouter un volume**.

    ![Cliquez sur Ajouter des volumes](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Dans la création d’une fenêtre de Volume, fournissent des informations pour le volume : 
   1. Entrez **myvol1** comme nom de volume. 
   2. Entrez **myfilepath1** en tant que le chemin d’accès de fichier doit être utilisé pour créer le chemin d’exportation pour le volume.
   3. Sélectionnez votre pool de capacité (**mypool1**).
   4. Utilisez la valeur par défaut pour le quota. 
   5. Sous réseau virtuel, cliquez sur **créer** pour créer un réseau virtuel Azure (Vnet) nouvelle.  Renseignez ensuite les informations suivantes :
       * Entrez **myvnet1** en tant que le nom du réseau virtuel.
       * Spécifiez un espace d’adressage, par exemple, 10.7.0.0/16.
       * Entrez **myANFsubnet** en tant que le nom du sous-réseau.
       * Spécifiez la plage d’adresses de sous-réseau, par exemple, 10.7.0.0/24.
       * Sélectionnez **Microsoft.NetApp/volumes** pour la délégation de sous-réseau.
       * Cliquez sur **OK** pour créer le réseau virtuel.
   6. Dans le sous-réseau, sélectionnez le réseau virtuel qui vient d’être créé (**myvnet1**) en tant que le sous-réseau de délégué.

      ![Créer une fenêtre de volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Créer la fenêtre de réseau virtuel](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Cliquez sur **Revoir + créer**.

    ![Passez en revue et de créer la fenêtre](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Passez en revue les informations pour le volume, puis cliquez sur **créer**.  
    Le volume créé apparaît dans le panneau de Volumes.

    ![Volume créé](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Étapes suivantes  

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Gérer les volumes à l’aide de fichiers NetApp de Azure](azure-netapp-files-manage-volumes.md) 
