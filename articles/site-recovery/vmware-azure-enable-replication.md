---
title: Activer la réplication des machines virtuelles VMware pour la récupération d’urgence vers Azure avec Azure Site Recovery | 'Microsoft Docs
description: Cet article décrit comment activer des machines virtuelles VMware pour la réplication vers Azure pour la récupération d’urgence à l’aide d’Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922747"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Activer la réplication des machines virtuelles VMware dans Azure

Cet article explique comment activer la réplication des machines virtuelles VMware locales dans Azure.

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez :

- [Configurer votre environnement de la source locale](vmware-azure-set-up-source.md).
- [Configurer votre environnement cible dans Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Avant de commencer
Lorsque vous répliquez des machines virtuelles VMware, n’oubliez pas ces informations :

* Votre compte d’utilisateur Azure doit disposer de certaines [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) pour activer la réplication d’une machine virtuelle dans Azure.
* Les machines virtuelles VMware sont découvertes toutes les 15 minutes, Il peut prendre 15 minutes ou plus pour les machines virtuelles apparaissent dans le portail Azure après la découverte. De même, la découverte peut prendre 15 minutes ou plus lorsque vous ajoutez un nouveau serveur vCenter server ou un hôte vSphere.
* Il peut prendre 15 minutes ou plus pour les modifications de l’environnement sur la machine virtuelle (par exemple, l’installation des outils VMware) à mettre à jour dans le portail.
* Vous pouvez vérifier l’heure de dernière découverte des machines virtuelles VMware : Consultez le **dernier Contact à** champ sur le **serveurs de Configuration** page pour le serveur vCenter/hôte vSphere.
* Pour ajouter des machines virtuelles à répliquer sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (mais ne cliquez pas dessus), puis sélectionnez **Actualiser**.
* Lorsque vous activez la réplication, si la machine virtuelle est prête, le serveur de processus installe automatiquement le service mobilité Azure Site Recovery sur ce dernier.

## <a name="enable-replication"></a>Activer la réplication

Avant de suivre les étapes décrites dans cette section, notez les informations suivantes :
* Azure Site Recovery réplique désormais directement vers des disques gérés pour tous les nouveaux échantillons. Le serveur de processus écrit les journaux de réplication dans un compte de stockage de cache dans la région cible. Ces journaux sont utilisés pour créer des points de récupération dans les disques managés de réplica.
* Au moment du basculement, le point de récupération que vous sélectionnez est utilisé pour créer le disque géré de la cible.
* Machines virtuelles qui ont été précédemment configurées pour répliquer vers les comptes de stockage cibles ne sont pas affectés.
* Réplication sur les comptes de stockage pour un nouvel ordinateur virtuel est uniquement disponible via un Representational State Transfer (REST) API et Powershell. Utilisez la version 2016-08-10 ou 2018-01-10-API REST Azure pour la réplication vers les comptes de stockage.

1. Accédez à **étape 2 : Répliquer l’application** > **Source**. Une fois que vous activez la réplication pour la première fois, sélectionnez **+ répliquer** dans le coffre pour activer la réplication des machines virtuelles supplémentaires.
1. Dans la page **Source** > **Source**, sélectionnez le serveur de configuration.
1. Pour **type de Machine**, sélectionnez **Machines virtuelles** ou **Machines physiques**.
1. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte. Ce paramètre n’est pas utile si vous répliquez des ordinateurs physiques.
1. Sélectionnez le serveur de processus, qui sera le serveur de configuration si vous n’avez pas créé de serveur de processus supplémentaires. Sélectionnez ensuite **OK**.

    ![Activer la fenêtre de source de réplication](./media/vmware-azure-enable-replication/enable-replication2.png)

1. Pour **cible**, sélectionnez l’abonnement et groupe de ressources dans lequel vous souhaitez créer des machines virtuelles ayant basculé. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure pour les machines virtuelles ayant basculé.

1. Sélectionnez le réseau Azure et le sous-réseau auquel les machines virtuelles Azure se connectera après le basculement. Le réseau doit se trouver dans la même région que le coffre du service Site Recovery.

   Sélectionnez **configurer maintenant pour les machines sélectionnées** pour appliquer le paramètre de réseau pour toutes les machines virtuelles que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau par machine virtuelle Azure. Si vous n'avez pas de réseau, vous devez en créer un. Pour créer un réseau à l’aide d’Azure Resource Manager, sélectionnez **créer un nouveau**. Sélectionnez un sous-réseau, le cas échéant, puis **OK**.
   
   ![Activer la fenêtre de cible de réplication](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Pour **machines virtuelles** > **sélectionner les machines virtuelles**, sélectionnez chaque machine virtuelle que vous souhaitez répliquer. Vous pouvez uniquement sélectionner les machines virtuelles pour lesquelles la réplication peut être activée. Sélectionnez ensuite **OK**. Si vous ne pouvez pas voir ou sélectionnez n’importe quel ordinateur virtuel particulier, consultez [machine Source n’est pas répertoriée dans le portail Azure](https://aka.ms/doc-plugin-VM-not-showing) pour résoudre le problème.

    ![Activer la fenêtre Sélectionner des machines virtuelles de réplication](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Pour **propriétés** > **configurer les propriétés**, sélectionnez le compte utilisé par le serveur de processus pour installer automatiquement le service mobilité Site Recovery sur la machine virtuelle. En outre, choisissez le type de cible de disque géré pour répliquer vers vos données selon des modèles d’activité.
10. Par défaut, tous les disques d’une machine virtuelle source sont répliquées. Pour exclure les disques de la réplication, désactivez le **Include** case à cocher pour tous les disques que vous ne souhaitez pas répliquer. Sélectionnez ensuite **OK**. Vous pouvez opter pour une définition ultérieure des propriétés. En savoir plus sur [l’exclusion de disques](vmware-azure-exclude-disk.md).

    ![Activer la réplication configurer la fenêtre Propriétés](./media/vmware-azure-enable-replication/enable-replication6.png)

1. À **paramètres de réplication** > **configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée. Vous pouvez modifier les paramètres de stratégie de réplication à **paramètres** > **stratégies de réplication** > ***nom de la stratégie***  >  **Modifier les paramètres**. Les modifications que vous appliquez à une stratégie s’appliquent également à réplication et aux nouvelles machines virtuelles.
1. Activer **cohérence Multimachine virtuelle** si vous souhaitez regrouper les machines virtuelles dans un groupe de réplication. Spécifiez un nom pour le groupe, puis sélectionnez **OK**.

    > [!NOTE]
    >    * Machines virtuelles dans un groupe de réplication répliquées ensemble et avez partagé des points de récupération cohérents d’incident et la cohérence des applications lorsqu’elles basculent.
    >    * Rassemblez les machines virtuelles et les serveurs physiques afin qu’ils reflètent vos charges de travail. L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail. Cela uniquement si les machines virtuelles exécutent la même charge de travail, et que vous avez besoin de cohérence.

    ![Activer la fenêtre de réplication](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Sélectionnez **Activer la réplication**. Vous pouvez suivre la progression de la **activer la Protection** du travail à **paramètres** > **travaux** > **travaux Site Recovery**. Lorsque la tâche **Finaliser la protection** s’exécute, la machine virtuelle est prête à être basculée.

## <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés des machines virtuelles

Ensuite, vérifiez les propriétés de l’ordinateur virtuel source. N’oubliez pas que le nom de la machine virtuelle Azure doit respecter la [configuration requise pour les machines virtuelles Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Accédez à **paramètres** > **éléments répliqués**, puis sélectionnez la machine virtuelle. Le **Essentials** page affiche des informations sur les paramètres et l’état de la machine virtuelle.
1. Dans **Propriétés**, vous pouvez afficher les informations sur la réplication et le basculement de la machine virtuelle.
1. Dans **calcul et réseau** > **propriétés de calcul**, vous pouvez modifier plusieurs propriétés de machine virtuelle. 

    ![Fenêtre de propriétés de calcul et réseau](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nom de machine virtuelle Azure : Modifiez le nom afin de répondre aux exigences d’Azure, si nécessaire.
    * Taille de machine virtuelle cible ou le type de machine virtuelle : La taille de machine virtuelle par défaut est choisie en fonction de certains paramètres qui incluent le nombre de disques, nombre de cartes réseau, nombre de cœurs du processeur, mémoire et des tailles de rôle machine virtuelle disponibles dans la région Azure cible. Azure Site Recovery choisit la première taille de machine virtuelle disponible qui satisfait à tous les critères. Vous pouvez sélectionner une taille de machine virtuelle différente selon vos besoins à tout moment avant le basculement. Notez que la taille de disque de machine virtuelle est également basée sur la taille du disque source, et il ne peut être changé après le basculement. En savoir plus sur les tailles de disque et les taux d’e/s à [cibles d’évolutivité et les performances pour les disques de machine virtuelle sur Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Groupe de ressources : Vous pouvez sélectionner un [groupe de ressources](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), à partir de laquelle une machine virtuelle devient une partie d’un basculement de post. Vous pouvez modifier ce paramètre à tout moment avant le basculement. Après le basculement, si vous migrez la machine virtuelle vers un autre groupe de ressources, les paramètres de protection pour cette machine virtuelle saut.
    * Groupe à haute disponibilité : Vous pouvez sélectionner un [à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) si votre machine virtuelle doit faire partie d’un après le basculement. Lorsque vous sélectionnez un groupe à haute disponibilité, gardez à l’esprit les informations suivantes :

        * Uniquement à haute disponibilité qui appartiennent au groupe de ressources spécifié est répertoriés.  
        * Machines virtuelles qui se trouvent sur différents réseaux virtuels ne peut pas faire partie du même ensemble de disponibilité.
        * Seules les machines virtuelles de même taille peuvent faire partie d’un groupe à haute disponibilité.
1. Vous pouvez également ajouter des informations sur le réseau cible, le sous-réseau et l’adresse IP affectée à la machine virtuelle Azure.
2. Les disques de données et du système d’exploitation de la machine virtuelle qui seront répliqués s’affichent dans **Disques** .

### <a name="configure-networks-and-ip-addresses"></a>Configurer les réseaux et les adresses IP

Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse, la machine virtuelle ayant basculé utilise DHCP. Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue. Si l’adresse est disponible dans le réseau de test de basculement, vous pouvez utiliser la même adresse IP cible pour le test de basculement.

Le nombre de cartes réseau est déterminé par la taille que vous spécifiez pour la machine virtuelle cible, comme suit :

- Si le nombre de cartes réseau sur l’ordinateur virtuel source est inférieur ou égal au nombre de cartes qui sont autorisés pour la taille de la cible de la machine virtuelle, la cible a le même nombre de cartes comme source.
- Si le nombre de cartes de la machine virtuelle source dépasse le nombre autorisé pour la taille de la cible de la machine virtuelle, la taille cible maximale est utilisée. Par exemple, si une machine virtuelle source possède deux cartes réseau et la taille de la cible de la machine virtuelle en accepte quatre, la machine virtuelle cible présente deux cartes. Si la machine virtuelle source présente deux cartes, mais la taille cible prend uniquement en charge une, la machine virtuelle cible n'a qu’une seule carte.
- Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau. En outre, le premier adaptateur qui est indiqué dans la liste devient la *par défaut* carte réseau dans la machine virtuelle Azure. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Clients Microsoft Software Assurance peuvent utiliser Azure Hybrid Benefit pour réduire les coûts de licence pour les ordinateurs Windows Server qui sont migrées vers Azure. L’avantage s’applique également à la récupération d’urgence Azure. Si vous êtes éligible, vous pouvez affecter l’avantage à la machine virtuelle que Site Recovery crée s’il existe un basculement. Pour ce faire, procédez comme suit :
1. Accédez à la **ordinateur et des propriétés réseau** de la machine virtuelle répliquée.
2. Répondre lorsque vous êtes invité si vous avez une licence de Windows Server qui vous permet de profiter d’Azure Hybrid Benefit.
3. Vérifiez que vous disposez d’une licence Windows Server éligible avec Software Assurance que vous pouvez utiliser pour appliquer l’avantage à la machine virtuelle qui sera créée au moment du basculement.
4. Enregistrer les paramètres de la machine virtuelle répliquée.

En savoir plus sur [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Résoudre les problèmes courants

* Chaque disque doit être inférieure à 4 To.
* Le disque du système d’exploitation doit être un disque de base, pas un disque dynamique.
* Pour les machines virtuelles de génération 2/activées UEFI, la famille de système d’exploitation doit être Windows, et le disque de démarrage doit être inférieur à 300 Go.

## <a name="next-steps"></a>Étapes suivantes

Une fois que la machine virtuelle atteint un état protégé, essayez une [basculement](site-recovery-failover.md) pour vérifier si votre application s’affiche dans Azure.

* Apprenez à [nettoyer les paramètres d'inscription et de protection](site-recovery-manage-registration-and-protection.md) pour désactiver la réplication.
* Découvrez comment [automatiser la réplication pour vos machines virtuelles à l’aide de Powershell](vmware-azure-disaster-recovery-powershell.md).
