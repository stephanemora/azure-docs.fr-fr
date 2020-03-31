---
title: Configurer un pool de capacité pour Azure NetApp Files | Microsoft Docs
description: Décrit comment configurer un pool de capacité afin de pouvoir y créer des volumes.
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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60691080"
---
# <a name="set-up-a-capacity-pool"></a>Configurer un pool de capacité

Configurer un pool de capacité vous permet d’y créer des volumes.  

## <a name="before-you-begin"></a>Avant de commencer 

Vous devez avoir déjà créé un compte NetApp.   

[Créer un compte NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Étapes 

1. Accédez au panneau de gestion de votre compte NetApp, puis, dans le volet de navigation, cliquez sur **Pools de capacité**.  
    
    ![Accéder au pool de capacité](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Cliquez sur **+ Ajouter des pools** pour créer un nouveau pool de capacité.   
    La fenêtre Nouveau pool de capacité s’affiche.

3. Saisissez les informations suivantes pour le nouveau pool de capacité :  
   * **Nom**  
     Spécifiez le nom du pool de capacité.  
     Le nom du pool de capacité doit être unique à chaque compte NetApp.

   * **Niveau de service**   
     Ce champ affiche les performances cibles pour le pool de capacité.  
     Spécifiez le niveau de service du pool de capacité : [**Premium**](azure-netapp-files-service-levels.md#Premium) ou [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Taille**     
     Spécifiez la taille du pool de capacité que vous achetez.        
     La taille de pool de capacité minimale est de 4 Tio. Vous pouvez créer un pool avec une taille dont la valeur est un multiple de 4 Tio.   
      
     ![Nouveau pool de capacité](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes 

- [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
- Consultez la [page des tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) pour connaître le prix des différents niveaux de service
- [Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
