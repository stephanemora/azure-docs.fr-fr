---
title: Configurer la réplication pour les machines virtuelles Azure dans Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment configurer la réplication pour les machines virtuelles Azure, d’une région Azure à l’autre à l’aide de Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 797cef2d3a0e2cfeb28411c30a7da8a5b6f2d953
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834517"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Répliquer des machines virtuelles Azure vers une autre région Azure



Cet article décrit comment activer la réplication de machines virtuelles de Azure, d’une région Azure à l’autre.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà configuré Site Recovery pour ce scénario, comme décrit dans le [didacticiel Azure sur Azure](azure-to-azure-tutorial-enable-replication.md). Assurez-vous que vous avez préparé les conditions préalables et créé le coffre Recovery Services.



## <a name="enable-replication"></a>Activer la réplication

Activez la réplication. Cette procédure suppose que la région principale Azure est Asie Est et que la région secondaire est Asie Sud-Est.

1. Dans le coffre, cliquez sur **+ Répliquer**.
2. Notez les champs suivants :
    - **Source** : point d’origine des machines virtuelles, qui dans ce cas est **Azure**.
    - **Emplacement source** : région Azure où vous souhaitez protéger vos machines virtuelles. Dans notre exemple, l’emplacement source est « Asie Est »
    - **Modèle de déploiement** : modèle de déploiement Azure des machines sources.
    - **Abonnement source** : abonnement auquel vos machines virtuelles sources appartiennent. Il peut s’agir de n’importe quel abonnement au sein du même locataire Azure Active Directory où se trouve votre coffre Recovery services.
    - **Groupe de ressources** : groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles du groupe de ressources sélectionné sont répertoriées pour la protection à l’étape suivante.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez sur chaque machine virtuelle à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.
    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Dans **Paramètres**, vous pouvez éventuellement configurer les paramètres du site cible :

    - **Emplacement cible** : Emplacement où vos données de machines virtuelles sources sont répliquées. En fonction de l’emplacement des machines virtuelles sélectionné, Site Recovery vous fournit la liste des régions cibles appropriées. Nous vous recommandons de conserver le même emplacement cible que l’emplacement du coffre Recovery Services.
    - **Abonnement cible** : abonnement cible utilisé pour la reprise d’activité. Par défaut, l’abonnement cible sera identique à l’abonnement source.
    - **Groupe de ressources cible** : groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Azure Site Recovery crée un groupe de ressources dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe de ressources créé par Azure Site Recovery existe déjà, il est réutilisé. Vous pouvez également choisir de le personnaliser, comme indiqué dans la section ci-dessous. L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure à l’exception de la région dans laquelle les machines virtuelles source sont hébergées.
    - **Réseau virtuel cible** : Par défaut, Site Recovery crée un réseau virtuel dans la région cible avec un nom ayant le suffixe « asr ». Il est mappé à votre réseau source et utilisé pour toute protection ultérieure. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
    - **Comptes de stockage cibles (si votre machine virtuelle source n’utilise pas de disques managés)**  : par défaut, Site Recovery crée un compte de stockage cible avec la même configuration que celle du compte de stockage de machines virtuelles source. Si le compte de stockage existe déjà, il est réutilisé.
    - **Disques managés de réplica (si votre machine virtuelle source utilise des disques managés)**  : Site Recovery crée des disques managés de réplica dans la région cible pour mettre en miroir les disques managés de la machine virtuelle source avec le même type de stockage (Standard ou Premium) que celui du disque managé de la machine virtuelle source.
    - **Comptes de stockage de cache** : Site Recovery a besoin d’un compte de stockage supplémentaire appelé « stockage de cache » dans la région source. Toutes les modifications effectuées sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant leur réplication vers l’emplacement cible.
    - **Groupes à haute disponibilité cibles** : par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe « asr », pour les machines virtuelles qui font partie d’un groupe à haute disponibilité dans la région source. Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé.
    - **Zones de disponibilité cibles** : par défaut, Site Recovery affecte à la région cible le même nombre de zones que la région source, si la région cible prend en charge les zones de disponibilité.

    Si la région cible ne prend pas en charge les zones de disponibilité, les machines virtuelles cibles sont configurées comme des instances uniques par défaut. Si nécessaire, vous pouvez configurer ces machines virtuelles comme faisant partie de groupes à haute disponibilité dans la région cible, en cliquant sur « Personnaliser ».

    >[!NOTE]
    >Vous ne pouvez plus modifier le type de disponibilité (instance unique, groupe à haute disponibilité ou zone de disponibilité) une fois que vous avez activé la réplication. Vous devez désactiver puis réactiver la réplication pour modifier le type de disponibilité.
    >
    
    - **Stratégie de réplication** : définit les paramètres de l’historique de conservation des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Vous pouvez modifier les paramètres de cible par défaut utilisés par Site Recovery.

1. Cliquez sur **Personnaliser :** à côté de « Abonnement cible » pour modifier l’abonnement cible par défaut. Sélectionnez l’abonnement dans la liste de tous ceux disponibles, dans le même locataire Azure Active Directory (AAD).

2. Cliquez sur **Personnaliser :** pour modifier les paramètres par défaut :
    - Dans **Groupe de ressources cible**, sélectionnez le groupe de ressources dans la liste de tous les groupes de ressources à l’emplacement cible de l’abonnement.
    - Dans **Réseau virtuel cible**, sélectionnez le réseau à partir d’une liste de tous les réseaux virtuels dans l’emplacement cible.
    - Dans **Groupe à haute disponibilité**, vous pouvez ajouter les paramètres de groupe à haute disponibilité de la machine virtuelle, s’ils font partie d’un groupe à haute disponibilité dans la zone source.
    - Dans **Comptes de stockage cible**, sélectionnez le compte que vous souhaitez utiliser.

        ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Cliquez sur **Créer une ressource cible** > **Activer la réplication**.
3. Une fois que les machines virtuelles sont activées pour la réplication, vous pouvez vérifier leur état d’intégrité sous **Éléments répliqués**

>[!NOTE]
>Pendant la réplication initiale, l’état des peut prendre un certain temps à s’actualiser à défaut de progression. Cliquez sur le bouton **Actualiser** pour obtenir le dernier état.
>

# <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
