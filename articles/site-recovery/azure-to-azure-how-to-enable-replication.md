---
title: Configurer la réplication pour les machines virtuelles Azure dans Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment configurer la réplication pour les machines virtuelles Azure, d’une région Azure à l’autre à l’aide de Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 86bd41d518006b0601a5c9d18e5429f76d5a4fc5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926655"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Répliquer des machines virtuelles Azure vers une autre région Azure


Cet article décrit comment activer la réplication de machines virtuelles de Azure, d’une région Azure à l’autre.

## <a name="before-you-start"></a>Avant de commencer

Cet article part du principe que vous avez préparé pour le déploiement de Site Recovery, comme décrit dans la [didacticiel de récupération d’urgence Azure vers Azure](azure-to-azure-tutorial-enable-replication.md).

Conditions préalables doivent être en place et vous devez avoir créé un coffre Recovery Services.


## <a name="enable-replication"></a>Activer la réplication

Activez la réplication. Cette procédure suppose que la région principale Azure est Asie Est et que la région secondaire est Asie Sud-Est.

1. Dans le coffre, cliquez sur **+ Répliquer**.
2. Notez les champs suivants :
   - **Source** : point d’origine des machines virtuelles, qui dans ce cas est **Azure**.
   - **Emplacement source** : La région Azure où vous souhaitez protéger vos machines virtuelles. Dans notre exemple, l’emplacement source est « Asie Est »
   - **Modèle de déploiement** : modèle de déploiement Azure des machines sources.
   - **Abonnement source** : L’abonnement auquel appartiennent vos machines virtuelles sources. Il peut s’agir de n’importe quel abonnement au sein du même locataire Azure Active Directory où se trouve votre coffre Recovery services.
   - **Groupe de ressources** : groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles du groupe de ressources sélectionné sont répertoriées pour la protection à l’étape suivante.

     ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez sur chaque machine virtuelle à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.
    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Dans **Paramètres**, vous pouvez éventuellement configurer les paramètres du site cible :

   - **Emplacement cible** : Emplacement où vos données de machines virtuelles sources sont répliquées. En fonction de l’emplacement des machines virtuelles sélectionné, Site Recovery vous fournit la liste des régions cibles appropriées. Nous vous recommandons de conserver le même emplacement cible que l’emplacement du coffre Recovery Services.
   - **Abonnement cible** : abonnement cible utilisé pour la reprise d’activité. Par défaut, l’abonnement cible sera identique à l’abonnement source.
   - **Groupe de ressources cible** : groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées.
       - Par défaut, Site Recovery crée un nouveau groupe de ressources dans la région cible avec un suffixe « asr » dans le nom.
       - Si le groupe de ressources déjà créé par Site Recovery existe, il est réutilisé.
       - Vous pouvez personnaliser les paramètres de groupe de ressources.
       - L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure, à l’exception de la région dans laquelle les machines virtuelles sources sont hébergés.
   - **Réseau virtuel cible** : Par défaut, Site Recovery crée un nouveau réseau virtuel dans la région cible avec un suffixe « asr » dans le nom. Il est mappé à votre réseau source et utilisé pour toute protection ultérieure. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
   - **Les comptes de stockage (machine virtuelle source n’utilise pas les disques gérés) cibles**: par défaut, Site Recovery crée un compte de stockage cible avec la même configuration que celle du compte de stockage de machines virtuelles source. Si le compte de stockage existe déjà, il est réutilisé.
   - **Disques managés de réplica (machine virtuelle source utilise des disques gérés)**: Site Recovery crée des nouveaux disques managés de réplica dans la région cible pour mettre en miroir de disques gérés de la source de la machine virtuelle avec le même type de stockage (Standard ou premium) que celui de la source de machine virtuelle de disque managé.
   - **Comptes de stockage de cache** : Site Recovery a besoin d’un compte de stockage supplémentaire appelé « stockage de cache » dans la région source. Toutes les modifications effectuées sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant leur réplication vers l’emplacement cible.
   - **Groupes à haute disponibilité cibles** : Par défaut, Site Recovery crée un groupe à haute disponibilité dans la région cible avec le suffixe « asr » dans le nom, pour les machines virtuelles qui font partie d’une haute disponibilité dans la région source. Si la disponibilité haute créé par Site Recovery existe déjà, il est réutilisé.
   - **Zones de disponibilité cibles** : par défaut, Site Recovery affecte à la région cible le même nombre de zones que la région source, si la région cible prend en charge les zones de disponibilité.

     Si la région cible ne prend pas en charge les zones de disponibilité, les machines virtuelles cibles sont configurées comme des instances uniques par défaut. Si nécessaire, vous pouvez configurer ces machines virtuelles comme faisant partie de groupes à haute disponibilité dans la région cible, en cliquant sur « Personnaliser ».

     >[!NOTE]
     >Vous ne pouvez plus modifier le type de disponibilité (instance unique, groupe à haute disponibilité ou zone de disponibilité) une fois que vous avez activé la réplication. Vous devez désactiver puis réactiver la réplication pour modifier le type de disponibilité.
     >
    
   - **Stratégie de réplication** : définit les paramètres de l’historique de conservation des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

     ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Activer la réplication des disques ajoutés

Si vous ajoutez des disques à une machine virtuelle Azure pour lesquels la réplication est activée, les événements suivants se produisent :
-   Intégrité de la réplication pour la machine virtuelle affiche un avertissement et une note informe vous indiquant qu’un ou plusieurs disques sont disponibles pour la protection.
-   Si vous activez la protection pour les disques ajoutés, l’avertissement disparaît après la réplication initiale du disque.
-   Si vous choisissez de ne pas activer la réplication pour le disque, vous pouvez sélectionner pour ignorer l’avertissement.

    
    ![Nouveau disque ajouté](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Pour activer la réplication pour un disque ajouté, procédez comme suit :

1.  Dans le coffre > **éléments répliqués**, cliquez sur la machine virtuelle à laquelle vous avez ajouté le disque.
2.  Cliquez sur **disques**, puis sélectionnez le disque de données pour lequel vous souhaitez activer la réplication (ces disques ont un **non protégés** état).
3.  Dans **détails du disque**, cliquez sur **activer la réplication**.

    ![Activer la réplication de disque ajouté](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Après l’exécution de tâche Activer la réplication et la réplication initiale terminée, l’avertissement d’intégrité de réplication pour le problème de disque est supprimé.


  
## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Vous pouvez modifier les paramètres de cible par défaut utilisés par Site Recovery.

1. Cliquez sur **Personnaliser :** à côté de « Abonnement cible » pour modifier l’abonnement cible par défaut. Sélectionnez l’abonnement dans la liste de tous ceux disponibles, dans le même locataire Azure Active Directory (AAD).

2. Cliquez sur **Personnaliser :** pour modifier les paramètres par défaut :
    - Dans **Groupe de ressources cible**, sélectionnez le groupe de ressources dans la liste de tous les groupes de ressources à l’emplacement cible de l’abonnement.
    - Dans **Réseau virtuel cible**, sélectionnez le réseau à partir d’une liste de tous les réseaux virtuels dans l’emplacement cible.
    - Dans **Groupe à haute disponibilité**, vous pouvez ajouter les paramètres de groupe à haute disponibilité de la machine virtuelle, s’ils font partie d’un groupe à haute disponibilité dans la zone source.
    - Dans **Comptes de stockage cible**, sélectionnez le compte que vous souhaitez utiliser.

        ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Cliquez sur **Personnaliser :** pour modifier les paramètres de réplication.
4. Dans **cohérence Multimachine virtuelle**, sélectionnez les machines virtuelles que vous souhaitez répliquer ensemble.
    - Toutes les machines d’un groupe de réplication ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent.
    - L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail (comme c’est beaucoup de ressources processeur). Elle doit être activée uniquement si les machines exécutent la même charge de travail, et vous avez besoin de cohérence sur plusieurs ordinateurs.
    - Par exemple, si une application a 2 machines virtuelles de SQL Server et deux serveurs web, vous devez ajouter uniquement les machines virtuelles SQL Server à un groupe de réplication.
    - Vous pouvez choisir d’avoir un maximum de 16 machines virtuelles dans un groupe de réplication.
    - Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004.
    - Vérifiez qu’il n’existe aucun dispositif de pare-feu bloque la communication interne entre les machines virtuelles sur le port 20004.
    - Si vous souhaitez que les machines virtuelles Linux fassent partie d’un groupe de réplication, assurez-vous que le trafic sortant sur le port 20004 est ouvert manuellement en fonction des conseils pour la version de Linux spécifique.
![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Cliquez sur **Créer une ressource cible** > **Activer la réplication**.
6. Une fois que les machines virtuelles sont activées pour la réplication, vous pouvez vérifier leur état d’intégrité sous **Éléments répliqués**

>[!NOTE]
>Pendant la réplication initiale, l’état des peut prendre un certain temps à s’actualiser à défaut de progression. Cliquez sur le bouton **Actualiser** pour obtenir le dernier état.
>

# <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
