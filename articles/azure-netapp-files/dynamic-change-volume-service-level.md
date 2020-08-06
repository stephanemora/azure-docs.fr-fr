---
title: Modifier de façon dynamique le niveau de service d’un volume pour Azure NetApp Files | Microsoft Docs
description: Décrit comment modifier de façon dynamique le niveau de service d’un volume.
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
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: bd28f949d35d38c9e64af7ff4196aa1754fbc37a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172520"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Changer dynamiquement le niveau de service d’un volume

Vous pouvez modifier le niveau de service d’un volume existant en déplaçant le volume vers un autre pool de capacité qui utilise le [niveau de service](azure-netapp-files-service-levels.md) souhaité pour le volume. Cette modification sur place du niveau du service pour le volume ne nécessite pas de migration des données. Elle n’affecte pas non plus l’accès au volume.  

> [!IMPORTANT] 
> L’utilisation de cette fonctionnalité nécessite une mise en liste verte. Envoyez un e-mail anffeedback@microsoft.com avec votre ID d’abonnement pour demander cette fonctionnalité.

Cette fonctionnalité vous permet de répondre aux besoins de votre charge de travail à la demande.  Vous pouvez modifier un volume existant pour utiliser un niveau de service supérieur afin d’améliorer les performances, ou pour utiliser un niveau de service inférieur afin d’optimiser les coûts. Par exemple, si le volume est actuellement dans un pool de capacité qui utilise le niveau de service *Standard* et que vous souhaitez que le volume utilise le niveau de service *Premium*, vous pouvez déplacer le volume de façon dynamique vers un pool de capacité qui utilise le niveau de service *Premium*.  

Le pool de capacité vers lequel vous souhaitez déplacer le volume doit déjà exister. Le pool de capacité peut contenir d’autres volumes.  Si vous souhaitez déplacer le volume vers un tout nouveau pool de capacité, vous devez [créer le pool de capacité](azure-netapp-files-set-up-capacity-pool.md) avant de déplacer le volume.  

## <a name="considerations"></a>Considérations

* Une fois le volume déplacé vers un autre pool de capacité, vous n’avez plus accès aux journaux d’activité et aux métriques de volume précédent. Le volume commence avec les nouveaux journaux d’activité et les métriques sous le nouveau pool de capacité.

* Si vous déplacez un volume vers un pool de capacité d’un niveau de service supérieur (par exemple, du niveau de service *Standard* au niveau *Premium* ou *Ultra*), vous devez attendre au moins sept jours avant de pouvoir re-déplacer le volume vers un pool de capacité d’un niveau de service inférieur (par exemple, du niveau de service *Ultra* au niveau *Premium* ou *Standard*).  
Ce délai d’attente ne s’applique pas si vous déplacez le volume vers un pool de capacité offrant le même niveau de service ou un niveau de service inférieur.

## <a name="steps"></a>Étapes

1.  Dans la page Volumes, cliquez avec le bouton droit sur le volume dont vous souhaitez modifier le niveau de service. Sélectionnez **Modifier le pool**.

    ![Cliquez avec le bouton droit sur Volume.](../media/azure-netapp-files/right-click-volume.png)

2. Dans la fenêtre Modifier le pool, sélectionnez le pool de capacités vers lequel vous souhaitez déplacer le volume. 

    ![Modifier le pool](../media/azure-netapp-files/change-pool.png)

3.  Cliquez sur **OK**.


## <a name="next-steps"></a>Étapes suivantes  

* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
