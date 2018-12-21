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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412909"
---
# <a name="set-up-a-capacity-pool"></a>Configurer un pool de capacité
Configurer un pool de capacité vous permet d’y créer des volumes.  

## <a name="before-you-begin"></a>Avant de commencer 
Vous devez avoir déjà créé un compte NetApp.   

[Créer un compte NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Étapes 

1. Accédez au panneau de gestion de votre compte NetApp, puis, dans le volet de navigation, sélectionnez **Pools de capacité**.

2. Cliquez sur **+ Ajouter des pools** pour créer un nouveau pool de capacité.   
    La fenêtre Nouveau pool de capacité s’affiche.

3. Saisissez les informations suivantes pour le nouveau pool de capacité :  
  * **Nom**  
    Spécifiez le nom du pool de capacité.  
    Le nom du pool de capacité doit être unique à chaque compte NetApp.

  * **Niveau de service**   
    Ce champ affiche les performances cibles pour le pool de capacité.  
    Actuellement, seul le niveau de service Premium est disponible. 

  *  **Taille**     
      Spécifiez la taille du pool de capacité que vous achetez.        
      La taille de pool de capacité minimale est de 4 Tio. Vous pouvez créer un pool avec une taille dont la valeur est un multiple de 4 Tio.   
      
      ![Nouveau pool de capacité](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes 

[Déléguer un sous-réseau à Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


