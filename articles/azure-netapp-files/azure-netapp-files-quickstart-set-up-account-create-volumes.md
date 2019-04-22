---
title: Configurer Azure NetApp Files et créer un volume | Microsoft Docs
description: Explique comment configurer Azure NetApp Files et créer un volume rapidement.
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
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 634f23cf3161fff09f21c79fd8300cb269dcc5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546573"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Configurer Azure NetApp Files et créer un volume 

Cet article explique comment configurer Azure NetApp Files et créer un volume rapidement. 

## <a name="before-you-begin"></a>Avant de commencer 

Vous devez faire partie du programme Préversion publique et figurer dans la liste verte pour l’accès au fournisseur de ressources Azure Microsoft.NetApp. Pour plus d’informations sur l’adhésion au programme Préversion publique, consultez la [page d’inscription au programme Préversion publique d’Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>S’inscrire à Azure NetApp Files et au fournisseur de ressources NetApp

1. Dans le portail Azure, cliquez sur l’icône Azure Cloud Shell en haut à droite.

      ![Icône d’Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Spécifiez l’abonnement qui a été mis en liste verte pour Azure NetApp Files :
    
        az account set --subscription <subscriptionId>

3. Inscrivez le fournisseur de ressources Azure : 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Le processus d’inscription peut prendre du temps.

## <a name="create-a-netapp-account"></a>Créer un compte NetApp

1. Dans la zone de recherche du portail Azure, entrez **Azure NetApp Files**, puis sélectionnez **Azure NetApp Files (préversion)** dans la liste qui s’affiche.

      ![Sélectionner Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Cliquez sur **+Ajouter** pour créer un nouveau compte NetApp.

     ![Créer un compte NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Dans la fenêtre Nouveau compte NetApp, entrez les informations suivantes : 
   1. Entrez **myaccount1** comme nom de compte. 
   2. Sélectionnez votre abonnement.
   3. Sélectionnez **Créer** pour créer un groupe de ressources. Entrez **myRG1** comme nom du groupe de ressources. Cliquez sur **OK**. 
   4. Sélectionnez l’emplacement de votre compte.  

      ![Fenêtre Nouveau compte NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Fenêtre Groupe de ressources](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Cliquez sur **Créer** pour créer votre compte NetApp.

## <a name="set-up-a-capacity-pool"></a>Configurer un pool de capacité

1. Dans le panneau de gestion Azure NetApp Files, sélectionnez votre compte NetApp (**myaccount1**).

    ![Sélectionner le compte NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Dans le panneau de gestion Azure NetApp Files de votre compte NetApp, cliquez sur **Pools de capacités**.

    ![Cliquez sur Pools de capacités](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Cliquez sur **+ Ajouter des pools**. 

    ![Cliquez sur Ajouter des pools](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Fournissez des informations pour le pool de capacités : 
    1. Entrez **mypool1** comme nom du pool.
    2. Sélectionnez **Premium** pour le niveau de service. 
    3. Spécifiez **4 (Tio)** pour la taille du pool. 

5. Cliquez sur **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Créer un volume pour Azure NetApp Files

1. Dans le panneau de gestion Azure NetApp Files de votre compte NetApp, cliquez sur **Volumes**.

    ![Cliquez sur Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Cliquez sur **+ Ajouter un volume**.

    ![Cliquez sur Ajouter des volumes](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Dans la fenêtre Créer un volume, spécifiez les informations concernant le nouveau volume : 
   1. Entrez **myvol1** comme nom du volume. 
   2. Entrez **myfilepath1** comme chemin de fichier à utiliser pour créer le chemin d’exportation du nouveau volume.
   3. Sélectionnez votre pool de capacités (**mypool1**).
   4. Utilisez la valeur par défaut du quota. 
   5. Sous Réseau virtuel, cliquez sur **Créer** pour créer un réseau virtuel Azure.  Ensuite, renseignez les informations suivantes :
       * Entrez **myvnet1** comme nom du réseau virtuel.
       * Spécifiez un espace d’adressage pour votre paramètre, par exemple, 10.7.0.0/16
       * Entrez **myANFsubnet** comme nom du sous-réseau.
       * Spécifiez la plage d’adresses du sous-réseau, par exemple, 10.7.0.0/24. Notez que vous ne pouvez pas partager le sous-réseau dédié avec d’autres ressources.
       * Sélectionnez **Microsoft.NetApp/volumes** pour la délégation de sous-réseau.
       * Cliquez sur **OK** pour créer le réseau virtuel.
   6. Dans le sous-réseau, sélectionnez le réseau virtuel qui vient d’être créé (**myvnet1**) comme sous-réseau délégué.

      ![Fenêtre Créer un volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Fenêtre Créer un réseau virtuel](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Cliquez sur **Revoir + créer**.

    ![Fenêtre Examiner et créer](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Passez en revue les informations concernant le volume, puis cliquez sur **Créer**.  
    Le volume créé s’affiche dans le panneau Volumes.

    ![Volume créé](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Étapes suivantes  

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Gérer les volumes avec Azure NetApp Files](azure-netapp-files-manage-volumes.md) 
