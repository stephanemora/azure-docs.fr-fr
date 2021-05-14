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
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: d75bd3580f66d7948d28b0d9ff34e247bc51c6b5
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108795021"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Changer dynamiquement le niveau de service d’un volume

Vous pouvez modifier le niveau de service d’un volume existant en déplaçant le volume vers un autre pool de capacité qui utilise le [niveau de service](azure-netapp-files-service-levels.md) souhaité pour le volume. Cette modification sur place du niveau du service pour le volume ne nécessite pas de migration des données. Elle n’affecte pas non plus l’accès au volume.  

Cette fonctionnalité vous permet de répondre aux besoins de votre charge de travail à la demande.  Vous pouvez modifier un volume existant pour utiliser un niveau de service supérieur afin d’améliorer les performances, ou pour utiliser un niveau de service inférieur afin d’optimiser les coûts. Par exemple, si le volume est actuellement dans un pool de capacité qui utilise le niveau de service *Standard* et que vous souhaitez que le volume utilise le niveau de service *Premium*, vous pouvez déplacer le volume de façon dynamique vers un pool de capacité qui utilise le niveau de service *Premium*.  

Le pool de capacité vers lequel vous souhaitez déplacer le volume doit déjà exister. Le pool de capacité peut contenir d’autres volumes.  Si vous souhaitez déplacer le volume vers un tout nouveau pool de capacité, vous devez [créer le pool de capacité](azure-netapp-files-set-up-capacity-pool.md) avant de déplacer le volume.  

## <a name="considerations"></a>Considérations

* Une fois le volume déplacé vers un autre pool de capacité, vous n’avez plus accès aux journaux d’activité et aux métriques de volume précédent. Le volume commence avec les nouveaux journaux d’activité et les métriques sous le nouveau pool de capacité.

* Si vous déplacez un volume vers un pool de capacité d’un niveau de service supérieur (par exemple, du niveau de service *Standard* au niveau *Premium* ou *Ultra*), vous devez attendre au moins sept jours avant de pouvoir *re-déplacer* le volume vers un pool de capacité d’un niveau de service inférieur (par exemple, du niveau de service *Ultra* au niveau *Premium* ou *Standard*).  

## <a name="register-the-feature"></a>Inscrire la fonctionnalité

La fonctionnalité de déplacement d’un volume vers un autre pool de capacité est actuellement disponible en préversion. Si vous utilisez cette fonctionnalité pour la première fois, vous devez d’abord l’inscrire.

Si vous avez plusieurs abonnements Azure, vérifiez que vous vous inscrivez à l’abonnement voulu à l’aide de la commande [« Set-AzContext »](/powershell/module/az.accounts/set-azcontext). <!-- GitHub #74191 --> 

1. Inscrivez la fonctionnalité : 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Vérifiez l’état d’inscription de la fonctionnalité : 

    > [!NOTE]
    > **RegistrationState** peut rester à l’état `Registering` jusqu’à 60 minutes avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit **Inscrit**.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Déplacer un volume vers un autre pool de capacité

1.  Dans la page Volumes, cliquez avec le bouton droit sur le volume dont vous souhaitez modifier le niveau de service. Sélectionnez **Modifier le pool**.

    ![Cliquez avec le bouton droit sur Volume.](../media/azure-netapp-files/right-click-volume.png)

2. Dans la fenêtre Modifier le pool, sélectionnez le pool de capacités vers lequel vous souhaitez déplacer le volume. 

    ![Modifier le pool](../media/azure-netapp-files/change-pool.png)

3.  Cliquez sur **OK**.


## <a name="next-steps"></a>Étapes suivantes  

* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
* [Résoudre les problèmes pour modifier le pool de capacité d’un volume](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
