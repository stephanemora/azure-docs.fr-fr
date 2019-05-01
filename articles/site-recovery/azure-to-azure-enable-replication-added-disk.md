---
title: Activez la réplication pour un disque ajouté à une machine virtuelle Azure répliquée par Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment activer la réplication pour un disque ajouté à une machine virtuelle Azure prenant en charge pour la récupération d’urgence avec Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928061"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Activez la réplication pour un disque ajouté à une machine virtuelle Azure


Cet article décrit comment activer la réplication des disques de données qui sont ajoutés à une machine virtuelle Azure qui est déjà activée pour la récupération d’urgence vers une autre région Azure, à l’aide de [Azure Site Recovery](site-recovery-overview.md).

Activation de la réplication pour un disque que vous ajoutez à une machine virtuelle est prise en charge pour les machines virtuelles Azure avec des disques gérés.

Lorsque vous ajoutez un nouveau disque à une machine virtuelle Azure qui est répliquée vers une autre région Azure, les événements suivants se produisent :

-   Intégrité de la réplication pour la machine virtuelle affiche un avertissement et une note dans le portail vous informe qu’un ou plusieurs disques sont disponibles pour la protection.
-   Si vous activez la protection pour les disques ajoutés, l’avertissement disparaît après la réplication initiale du disque.
-   Si vous choisissez de ne pas activer la réplication pour le disque, vous pouvez sélectionner pour ignorer l’avertissement.

![Nouveau disque ajouté](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Avant de commencer

Cet article suppose que vous avez déjà défini la récupération d’urgence pour la machine virtuelle à laquelle vous ajoutez le disque. Si vous n’avez pas, suivez la [didacticiel de récupération d’urgence Azure vers Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Activez la réplication pour un disque ajouté 

Pour activer la réplication pour un disque ajouté, procédez comme suit :

1. Dans le coffre > **éléments répliqués**, cliquez sur la machine virtuelle à laquelle vous avez ajouté le disque.
2. Cliquez sur **disques**, puis sélectionnez le disque de données pour lequel vous souhaitez activer la réplication (ces disques ont un **non protégés** état).
3.  Dans **détails du disque**, cliquez sur **activer la réplication**.

    ![Activer la réplication de disque ajouté](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Une fois la tâche d’activation de la réplication s’exécute et la réplication initiale terminée, l’avertissement d’intégrité de réplication pour le problème de disque est supprimé.



# <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
