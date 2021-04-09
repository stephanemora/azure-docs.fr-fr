---
title: Qu’est-ce que l’extension SQL Server IaaS Agent ?
description: Cet article explique comment l’extension SQL Server IaaS Agent permet d’automatiser la gestion et des tâches d’administration spécifiques de SQL Server sur les machines virtuelles Azure. Celles-ci incluent des fonctionnalités, telles que la sauvegarde automatisée, la mise à jour corrective automatisée, l’intégration d’Azure Key Vault, la gestion des licences, la configuration du stockage et la gestion centralisée de toutes les instances de machine virtuelle SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225487"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatiser la gestion avec l’extension SQL Server IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


L’extension SQL Server IaaS Agent (SqlIaasExtension) s’exécute dans SQL Server sur Machines virtuelles Azure pour automatiser les tâches d’administration et de gestion. 

Cet article fournit une vue d’ensemble de l’extension. Pour installer l’extension SQL Server IaaS dans SQL Server sur des machines virtuelles Azure, consultez les articles se rapportant à l’[Installation automatique](sql-agent-extension-automatic-registration-all-vms.md), aux [Machines virtuelles uniques](sql-agent-extension-manually-register-single-vm.md) ou aux [Machines virtuelles en bloc](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Vue d’ensemble

L’extension de l’agent IaaS SQL Server permet une intégration avec le portail Azure et, selon le mode de gestion, offre un certain nombre d’avantages en matière de fonctionnalités pour SQL Server sur les machines virtuelles Azure : 

- **Avantages en termes de fonctionnalités** : L’extension déverrouille un certain nombre d’avantages en termes de fonctionnalités d’automatisation, telles que l’administration du portail, la flexibilité des licences, la sauvegarde automatisée, la mise à jour corrective automatisée et bien plus encore. Pour plus d’informations, consultez [Avantages en termes de fonctionnalités](#feature-benefits) plus loin dans cet article. 

- **Conformité** : l’extension offre une méthode simplifiée satisfaisant à l’exigence de notification à Microsoft qu’Azure Hybrid Benefit a été activé, tel que spécifié dans les conditions d’utilisation du produit. Ce processus élimine la nécessité de gérer les formulaires d’inscription de licence pour chaque ressource.  

- **Gratuit** : l’extension dans les trois modes d’administration est entièrement gratuite. Aucun coût supplémentaire n’est associé à l’extension ou au changement de modes de gestion. 

- **Gestion des licences simplifiée**  : l’extension simplifie la gestion des licences SQL Server et vous permet d’identifier rapidement les machines virtuelles SQL Server avec Azure Hybrid Benefit activé à l’aide du [portail Azure](manage-sql-vm-portal.md), de PowerShell ou de l’interface de ligne de commande Azure : 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> L’extension SQL IaaS Agent collecte des données dans le seul but de fournir d’autres avantages aux clients lors de l’utilisation de SQL Server dans Machines virtuelles Azure. Microsoft n’utilisera pas ces données pour les audits de gestion des licences sans le consentement préalable du client. Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).


## <a name="feature-benefits"></a>Avantages en termes de fonctionnalités 

L’extension SQL Server IaaS Agent déverrouille un certain nombre d’avantages en matière de fonctionnalités pour la gestion de votre machine virtuelle SQL Server. 

Le tableau suivant détaille ces avantages : 


| Fonctionnalité | Description |
| --- | --- |
| **Gestion du portail** | Déverrouille la [gestion dans le portail](manage-sql-vm-portal.md), ce qui vous permet de visualiser toutes vos machines virtuelles SQL Server dans un seul emplacement, et d’activer ou de désactiver les fonctionnalités spécifiques à SQL, directement à partir du portail. <br/> Mode d’administration : Léger et complet|  
| **Sauvegarde automatisée** |Automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut ou une instance nommée [correctement installée](frequently-asked-questions-faq.md#administration) de SQL Server sur la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](automated-backup-sql-2014.md). <br/> Mode d’administration : Complète|
| **Mise à jour corrective automatisée** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de sécurité SQL Server et Windows importantes de votre machine virtuelle peuvent avoir lieu ; vous évitez ainsi les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](automated-patching.md). <br/> Mode d’administration : Complète|
| **Intégration d’Azure Key Vault** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Mode d’administration : Complète|
| **Afficher l’utilisation du disque dans le portail** | Vous permet d’afficher une représentation graphique de l’utilisation du disque par vos fichiers de données SQL dans le portail Azure.  <br/> Mode d’administration : Complète | 
| **Gestion des licences flexible** | Économisez sur le coût en [passant en toute transparence](licensing-model-azure-hybrid-benefit-ahb-change.md) de votre propre licence (également appelée Azure Hybrid Benefit) au modèle de licence avec paiement à l’utilisation, et inversement. <br/> Mode d’administration : Léger et complet| 
| **Version/édition flexible** | Si vous décidez de modifier la [version](change-sql-server-version.md) ou l’[édition](change-sql-server-edition.md) de SQL Server, vous pouvez mettre à jour les métadonnées dans le portail Azure sans avoir à redéployer toute la machine virtuelle SQL Server.  <br/> Mode d’administration : Léger et complet| 


## <a name="management-modes"></a>Modes de gestion

Vous pouvez choisir d’inscrire votre extension SQL IaaS à trois modes de gestion différents : 

- Le mode **léger** copie les fichiers binaires d’extension dans la machine virtuelle, mais n’installe pas l’agent et ne redémarre pas le service SQL Server. Le mode léger prend uniquement en charge le changement du type de licence et de l’édition de SQL Server et offre une gestion limitée du portail. Utilisez cette option pour les machines virtuelles SQL Server avec plusieurs instances, ou participant à une instance de cluster de basculement. Le mode léger est le mode d’administration par défaut lors de l’utilisation de la fonctionnalité d’[inscription automatique](sql-agent-extension-automatic-registration-all-vms.md), ou lorsqu’un type de gestion n’est pas spécifié au moment de l’inscription manuelle. Le mode léger n’a aucun impact sur la mémoire ou le processeur et il n’y a aucun coût associé. Nous vous recommandons d’abord d’inscrire votre machine virtuelle SQL Server en mode léger, puis de procéder à une mise à niveau vers le mode complet pendant une fenêtre de maintenance planifiée. 

- Le mode **complet** installe SQL IaaS Agent sur la machine virtuelle et fournit toutes les fonctionnalités, mais nécessite un redémarrage des autorisations SQL Server et administrateur système. Utilisez-la pour gérer une machine virtuelle SQL Server avec une seule instance. Le mode Full installe deux services Windows qui ont un impact minimal sur la mémoire et le processeur. L’activité de ces services est visible dans le gestionnaire des tâches. L’utilisation du mode de gestion Full est gratuite. 

- Le mode **sans agent** est dédié à SQL Server 2008 et à SQL Server 2008 R2 sur Windows Server 2008. Le mode NoAgent n’a aucun impact sur la mémoire ou l’UC. Il n’existe aucun coût associé à l’utilisation du mode de gestion sans agent, le SQL Server n’est pas redémarré et un agent n’est pas installé sur la machine virtuelle. 

Vous pouvez afficher le mode actuel de votre agent SQL Server IaaS en utilisant Azure PowerShell : 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Installation

Inscrivez votre machine virtuelle SQL Server avec l’extension SQL Server IaaS Agent pour créer la _ressource_ de **machine virtuelle SQL** dans votre abonnement ; il s’agit d’une ressource _distincte_ de la ressource de machine virtuelle. Le fait de désinscrire votre machine virtuelle SQL Server de l’extension va supprimer la _ressource_ de **machine virtuelle SQL**, mais pas la machine virtuelle elle-même.

Pendant le déploiement d’une image de machine virtuelle SQL Server de la Place de marché Azure via le portail Azure, la machine virtuelle SQL Server est inscrite automatiquement à l’extension. Toutefois, si vous choisissez d’installer SQL Server vous-même sur une machine virtuelle Azure, ou de provisionner une machine virtuelle Azure à partir d’un disque dur virtuel personnalisé, vous devez inscrire votre machine virtuelle SQL Server à de l’extension SQL IaaS pour débloquer les avantages en termes de fonctionnalités. 

L’inscription de l’extension en mode léger copie les fichiers binaires, mais n’installe pas l’agent sur la machine virtuelle. L’agent est installé sur la machine virtuelle lorsque l’extension est mise à niveau vers le mode de gestion complet. 

Il existe trois possibilités d’inscription à l’extension : 
- [Automatiquement pour toutes les machines virtuelles actuelles et futures dans un abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuellement pour une seule machine virtuelle](sql-agent-extension-manually-register-single-vm.md)
- [Manuellement pour plusieurs machines virtuelles en bloc](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Prise en charge d’une instance nommée

L’extension d’agent IaaS SQL Server fonctionne avec une instance nommée de SQL Server s’il s’agit de la seule instance disponible sur la machine virtuelle. L’installation de l’extension échoue sur les machines virtuelles qui ont plusieurs instances nommées SQL Server s’il n’y a pas d’instance par défaut sur la machine virtuelle. 

Pour utiliser une instance nommée de SQL Server, déployez une machine virtuelle Azure, installez-y une instance SQL Server nommée unique, puis inscrivez-la à l’[extension SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

Sinon, pour utiliser une instance nommée avec une image SQL Server de la Place de marché Azure, suivez ces étapes : 

   1. Déployez une machine virtuelle SQL Server à partir de la Place de marché Azure. 
   1. [Désinscrivez](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) la machine virtuelle SQL Server de l’extension SQL IaaS Agent. 
   1. Désinstallez complètement SQL Server sur la machine virtuelle SQL Server.
   1. Installez SQL Server avec une instance nommée sur la machine virtuelle SQL Server. 
   1. [Inscrivez la machine virtuelle à l’extension SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Vérifier l’état de l’extension

Utilisez le portail Azure ou Azure PowerShell pour vérifier l’état de l’extension. 

### <a name="azure-portal"></a>Portail Azure

Vérifiez que l’extension est installée dans le portail Azure. 

Sélectionnez **Tous les paramètres** dans le volet de la machine virtuelle, puis sélectionnez **Extensions**. L’extension **SslIaaSExtension** doit s’afficher.

![État de l’extension SQL IaaS Agent dans le portail Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez également utiliser la cmdlet Azure PowerShell **Get-AzVMSqlServerExtension** :

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez obtenir des informations d’état sur la sauvegarde automatisée et la mise à jour corrective automatisée à l’aide des commandes suivantes :

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Limites

L’extension SQL IaaS Agent prend uniquement en charge les éléments suivants : 

- Les machines virtuelles SQL Server déployées par le biais d’Azure Resource Manager. Les machines virtuelles SQL Server déployées via le modèle classique ne sont pas prises en charge. 
- Les machines virtuelles SQL Server déployées sur le cloud public ou le cloud Azure Government. Les déploiements sur d’autres clouds privés ou du secteur public ne sont pas pris en charge. 


## <a name="in-region-data-residency"></a>Résidence des données dans la région
La machine virtuelle Azure SQL et l'extension SQL IaaS Agent ne déplacent pas et ne stockent pas les données client en dehors de la région dans laquelle elles sont déployées.

## <a name="next-steps"></a>Étapes suivantes

Pour installer l’extension SQL Server IaaS dans SQL Server sur des machines virtuelles Azure, consultez les articles se rapportant à l’[Installation automatique](sql-agent-extension-automatic-registration-all-vms.md), aux [Machines virtuelles uniques](sql-agent-extension-manually-register-single-vm.md) ou aux [Machines virtuelles en bloc](sql-agent-extension-manually-register-vms-bulk.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Qu’est-ce que SQL Server sur les machines virtuelles Azure ?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Pour en savoir plus, consultez la [Foire aux questions](frequently-asked-questions-faq.md). 
