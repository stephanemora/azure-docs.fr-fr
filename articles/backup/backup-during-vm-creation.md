---
title: Activer la sauvegarde des machines virtuelles Azure lors de la création
description: Comment activer la sauvegarde de machine virtuelle pendant le processus de création.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780441"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Activer la sauvegarde lorsque vous créez une machine virtuelle Azure

Utiliser le service Azure Backup pour sauvegarder des machines virtuelles (VM) Azure. Machines virtuelles sont sauvegardées selon une planification spécifiée dans une stratégie de sauvegarde, et les points de récupération sont créés à partir de sauvegardes. Points de récupération sont stockés dans des coffres Recovery Services.

Cet article explique comment activer la sauvegarde pendant que vous créez une machine virtuelle (VM) dans le portail Azure.  

## <a name="sign-in-to-azure"></a>Connexion à Azure

Si vous n’êtes pas déjà connecté à votre compte, connectez-vous à la [Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Créer une machine virtuelle avec sauvegarde configurée 

1. Dans le coin supérieur gauche du portail Azure, sélectionnez **New**.

1. Sélectionnez **calcul**, puis sélectionnez une image de la machine virtuelle.

1. Entrez les informations pour la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous fournissez seront être utilisés pour se connecter à la machine virtuelle. Lorsque vous avez terminé, sélectionnez **OK**. 

1. Choisissez la taille de la machine virtuelle.  

1. Sous **paramètres** > **sauvegarde**, sélectionnez **activé** pour ouvrir les paramètres de configuration de la sauvegarde.

   - Pour accepter les valeurs par défaut, sélectionnez **OK** sur le **paramètres** page. Vous allez ensuite accéder à la **Résumé** page pour créer la machine virtuelle. Ignorez les étapes 6 à 8.
   - Pour modifier les valeurs de configuration de la sauvegarde, suivez les étapes suivantes.  

1. Créez ou sélectionnez un coffre Recovery Services pour stocker les sauvegardes de la machine virtuelle. Si vous créez un coffre Recovery Services, vous pouvez choisir un groupe de ressources pour le coffre.  

    ![Paramètres de configuration de la sauvegarde dans la page de création de machine virtuelle](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Le groupe de ressources pour le coffre Recovery Services peut être différent dans le groupe de ressources pour la machine virtuelle.  

1. Par défaut, une stratégie de sauvegarde est sélectionnée pour vous permettre de protéger la machine virtuelle. Une stratégie de sauvegarde spécifie la fréquence à laquelle prendre des instantanés de sauvegarde et la durée de conservation de ces copies de sauvegarde. 

   - Vous pouvez accepter la stratégie par défaut, ou créer ou sélectionner une autre stratégie de sauvegarde. 
   - Pour modifier la stratégie de sauvegarde, sélectionnez **stratégie de sauvegarde** et modifiez les valeurs.  

1. Lorsque vous avez terminé de définir les valeurs de configuration de la sauvegarde, sélectionnez **OK** sur le **paramètres** page.  

1. Sur le **Résumé** page, une fois la validation réussie, sélectionnez **créer** pour créer une machine virtuelle qui utilise les paramètres de sauvegarde configurés. 

## <a name="start-a-backup-after-creating-the-vm"></a>Démarrer une sauvegarde après avoir créé la machine virtuelle 

Même si vous avez configuré une stratégie de sauvegarde pour votre machine virtuelle, il est conseillé de créer une sauvegarde initiale. 

Une fois le modèle de création de machine virtuelle terminée, accédez à **opérations** dans le menu de gauche, puis sélectionnez **sauvegarde** pour afficher les informations de sauvegarde pour la machine virtuelle. Vous pouvez utiliser cette page pour :

- Déclencher une sauvegarde à la demande.
- Restaurer une machine virtuelle complète ou tous ses disques.
- Restaurer des fichiers à partir d’une sauvegarde de machine virtuelle.
- Modifier la stratégie de sauvegarde associée à la machine virtuelle.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Utiliser un modèle Resource Manager pour déployer une machine virtuelle protégée

Les étapes précédentes expliquent comment utiliser le portail Azure pour créer une machine virtuelle et la protéger dans un coffre Recovery Services. Pour rapidement déployer un ou plusieurs machines virtuelles et les protéger dans un coffre Recovery Services, consultez le modèle [déployer une machine virtuelle Windows et activer la sauvegarde](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Les images de machine virtuelle prennent en charge la configuration de la sauvegarde lors de la création de machine virtuelle ?

Les images principales suivantes publiées par Microsoft sont pris en charge pour l’activation de la sauvegarde lors de la création de machine virtuelle. Pour les autres machines virtuelles, vous pouvez activer la sauvegarde une fois que la machine virtuelle est créée. Pour plus d’informations, consultez [activer la sauvegarde une fois que la machine virtuelle est créée](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, Windows Server 2016 Datacenter Core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -serveur Ubuntu 17.10, un serveur Ubuntu 17.04, Ubuntu Server 16.04 LTS (), Ubuntu Server 14.04 (LTS) 
- **Red Hat** : RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** : SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** - Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Est le coût de sauvegarde inclus dans le coût de la machine virtuelle ? 

Non. Les coûts de sauvegarde sont distincts des coûts d’une machine virtuelle. Pour plus d’informations sur la tarification de sauvegarde, consultez [tarification sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quelles autorisations sont-elles requises pour activer la sauvegarde sur une machine virtuelle ? 

Si vous êtes un contributeur de machine virtuelle, vous pouvez activer la sauvegarde sur la machine virtuelle. Si vous utilisez un rôle personnalisé, vous devez disposer des autorisations suivantes pour activer la sauvegarde sur la machine virtuelle : 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Si votre coffre Recovery Services et la machine virtuelle ont différents groupes de ressources, assurez-vous que vous disposez des autorisations d’écriture dans le groupe de ressources pour le coffre Recovery Services.  

## <a name="next-steps"></a>Étapes suivantes 

Maintenant que vous avez protégé votre machine virtuelle, consultez les articles suivants pour apprendre à gérer et restaurer des machines virtuelles :

- [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md) 
- [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md) 
