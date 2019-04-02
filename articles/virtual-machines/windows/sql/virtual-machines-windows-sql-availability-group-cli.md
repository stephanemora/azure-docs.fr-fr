---
title: Utilisation d’une machine virtuelle SQL Azure CLI pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure
description: 'Utiliser Azure CLI pour créer le Cluster de basculement Windows, l’écouteur de groupe de disponibilité et l’équilibrage de charge interne sur une machine virtuelle SQL Server dans Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762869"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Utilisation d’une machine virtuelle SQL Azure CLI pour configurer le groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure
Cet article décrit comment utiliser [CLI de machine virtuelle SQL Azure](/cli/azure/sql/vm?view=azure-cli-latest/) déployer un Cluster de basculement Windows (WSFC) et ajouter des machines virtuelles SQL Server au cluster, ainsi que pour créer l’équilibreur de charge interne et un écouteur pour un groupe de disponibilité Always On.  Le déploiement réel du groupe de disponibilité Always On est quand même effectué manuellement par le biais de SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Conditions préalables
Pour automatiser l’installation d’un groupe de disponibilité Always On à l’aide de la CLI de machine virtuelle SQL Azure, vous devez déjà disposer les conditions préalables suivantes : 
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un groupe de ressources avec un contrôleur de domaine. 
- Un ou plusieurs joints au domaine [machines virtuelles dans Azure en cours d’exécution SQL Server 2016 (ou supérieure) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) dans le *même groupe à haute disponibilité ou différentes zones de disponibilité* qui ont été [enregistré avec le fournisseur de ressources SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Azure CLI](/cli/azure/install-azure-cli). 
- Deux disponibles (non utilisé par une entité) des adresses IP, un pour l’équilibreur de charge interne et un pour l’écouteur de groupe de disponibilité dans le même sous-réseau que le groupe de disponibilité. Si un équilibreur de charge existant est utilisé, une seule adresse IP disponible est nécessaire pour l’écouteur de groupe de disponibilité. 

## <a name="permissions"></a>Autorisations
Les autorisations de compte suivantes sont nécessaires pour configurer le groupe de disponibilité Always On à l’aide de la CLI de machine virtuelle SQL Azure. 

- Un domaine compte d’utilisateur existant qui a l’autorisation de « Créer un objet ordinateur » dans le domaine.  Par exemple, un compte d’administrateur de domaine a généralement une autorisation suffisante (ex : account@domain.com). _Ce compte doit également faire partie du groupe administrateur local sur chaque machine virtuelle pour créer le cluster._
- Le compte d’utilisateur de domaine qui contrôle le service SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Étape 1 : créer le compte de stockage comme un témoin cloud
Le cluster a besoin d’un compte de stockage en tant que le témoin de cloud. Vous pouvez utiliser n’importe quel compte de stockage existant, ou vous pouvez créer un compte de stockage. Si vous souhaitez utiliser un compte de stockage existant, passez directement à la section suivante. 

L’extrait de code suivant crée le compte de stockage : 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Vous pouvez rencontrer l’erreur `az sql: 'vm' is not in the 'az sql' command group` si vous utilisez une version obsolète de l’interface CLI Azure. Téléchargez le [version la plus récente d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) pour éviter cette erreur.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Étape 2 : définir les métadonnées de Cluster de basculement Windows
La CLI de machine virtuelle SQL Azure [groupe de machines virtuelles sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) commande groupe gère les métadonnées du service de Cluster de basculement Windows (WSFC) qui héberge le groupe de disponibilité. Métadonnées de cluster incluent le domaine Active Directory, les comptes de cluster, les comptes de stockage à utiliser en tant que le témoin de cloud et la version de SQL Server. Utilisez [créer de groupe de machines virtuelles sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) pour définir les métadonnées pour le cluster WSFC afin que lorsque le premier ordinateur virtuel de SQL Server est ajouté, le cluster est créé comme défini. 

L’extrait de code suivant définit les métadonnées pour le cluster :
```azurecli
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Étape 3 : ajouter des machines virtuelles SQL Server au cluster
Ajout de la première machine virtuelle de SQL Server au cluster crée le cluster. Le [az sql vm Ajouter-groupe](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) commande crée le cluster avec le nom précédemment attribué, installe le rôle de cluster sur les machines virtuelles de SQL Server et les ajoute au cluster. Les utilisations suivantes de la `az sql vm add-to-group` commande ajoute les autres machines virtuelles SQL Server au cluster nouvellement créé. 

L’extrait de code suivant crée le cluster et lui ajoute le premier ordinateur virtuel de SQL Server : 

```azurecli
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Utilisez cette commande pour ajouter les autres machines virtuelles SQL Server au cluster, uniquement la modification de la `-n` paramètre pour le nom de la machine virtuelle SQL Server. 

## <a name="step-4---create-availability-group"></a>Étape 4 : créer le groupe de disponibilité
Créer manuellement le groupe de disponibilité, comme vous le feriez normalement, à l’aide [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Faire **pas** créer un écouteur pour l’instant, car cette opération est effectuée via Azure CLI dans les sections suivantes.  

## <a name="step-5---create-internal-load-balancer"></a>Étape 5 : créer l’équilibreur de charge interne

L’écouteur de groupe (AG) de disponibilité Always On nécessite un équilibreur de charge Azure interne (ILB). Ce dernier fournit une adresse IP « flottante » à l’écouteur de groupe de disponibilité qui permet des opérations de basculement et de reconnexion plus rapides. Si les machines virtuelles SQL Server d’un groupe de disponibilité font partie du même groupe à haute disponibilité, alors vous pouvez utiliser un équilibreur de charge basique ; dans le cas contraire, vous devez utiliser un équilibreur de charge standard.  **L’équilibreur de charge interne doit se trouver dans le même réseau virtuel que les instances de machine virtuelle SQL Server.** 

L’extrait de code suivant crée l’équilibreur de charge interne :

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > La ressource d’adresse IP publique de chaque machine virtuelle SQL Server doit avoir une référence SKU standard compatible avec l’équilibreur de charge standard. Pour déterminer la référence SKU de la ressource d’adresse IP publique de votre machine virtuelle, accédez à votre **groupe de ressources**, sélectionnez votre ressources d’**adresse IP publique** pour la machine virtuelle SQL Server souhaitée et recherchez la valeur sous **Référence SKU** dans le volet **Vue d’ensemble**.  

## <a name="step-6---create-availability-group-listener"></a>Étape 6 : créer l’écouteur du groupe de disponibilité
Une fois que le groupe de disponibilité a été créé manuellement, vous pouvez créer l’écouteur à l’aide de [az ag-listener de machine virtuelle sql](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- Le **ID de ressource de sous-réseau** est la valeur de `/subnets/<subnetname>` ajouté à l’ID de ressource de la ressource de réseau virtuel. Pour identifier l’ID de ressource de sous-réseau, procédez comme suit :
   1. Accédez à votre groupe de ressources dans le [Azure portal](https://portal.azure.com). 
   1. Sélectionnez la ressource de réseau virtuel. 
   1. Sélectionnez **propriétés** dans le **paramètres** volet. 
   1. Identifier l’ID de ressource pour le réseau virtuel et ajoutez `/subnets/<subnetname>`à la fin de celle-ci pour créer l’ID de ressource de sous-réseau. Par exemple : 
        - Mon ID de ressource de réseau virtuel est : `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Mon nom du sous-réseau est `default`.
        - Par conséquent, mon ID de ressource de sous-réseau est : `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


L’extrait de code suivant crée l’écouteur de groupe de disponibilité :

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Modifier le nombre de réplicas dans le groupe de disponibilité
Il existe une couche supplémentaire de complexité lors du déploiement d’un groupe de disponibilité pour les machines virtuelles SQL Server hébergé dans Azure, comme les ressources sont désormais gérés par le fournisseur de ressources et par le `virtual machine group`. Par conséquent, lors de l’ajout ou suppression de réplicas pour le groupe de disponibilité, il est une étape supplémentaire de mise à jour les métadonnées de l’écouteur avec des informations sur les machines virtuelles de SQL Server. Par conséquent, lorsque vous modifiez le nombre de réplicas du groupe de disponibilité, vous devez également utiliser le [mise à jour de l’écouteur de groupe de disponibilité groupe sql vm az](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) commande pour mettre à jour l’écouteur avec les métadonnées des machines virtuelles SQL Server. 


### <a name="add-a-replica"></a>Ajouter un réplica

Pour ajouter un nouveau réplica au groupe de disponibilité, procédez comme suit :

1. Ajouter la machine virtuelle SQL Server au cluster :
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Utilisez SQL Server Management Studio (SSMS) pour ajouter l’instance de SQL Server en tant que réplica dans le groupe de disponibilité.
1. Ajouter les métadonnées de la machine virtuelle SQL Server à l’écouteur :
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Supprimer un réplica

Pour supprimer un réplica du groupe de disponibilité, procédez comme suit :

1. Supprimer le réplica du groupe de disponibilité à l’aide de SQL Server Management Studio (SSMS). 
1. Supprimer les métadonnées de la machine virtuelle SQL Server à partir de l’écouteur :
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Supprimer la machine virtuelle SQL Server du cluster :
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Supprimer l’écouteur de groupe de disponibilité
Si vous avez besoin plus tard supprimer l’écouteur du groupe de disponibilité configuré avec Azure CLI, vous devez passer par le fournisseur de ressources SQL VM. Dans la mesure où l’écouteur est inscrit par le biais du fournisseur de ressources de machine virtuelle SQL, le supprimer par le biais de SQL Server Management Studio ne suffit pas. Il doit réellement être supprimé via le fournisseur de ressources SQL VM à l’aide d’Azure CLI. Ainsi, vous supprimez du fournisseur de ressources de machine virtuelle SQL les métadonnées de l’écouteur de groupe de disponibilité et vous supprimez physiquement l’écouteur du groupe de disponibilité. 

L’extrait de code suivant supprime l’écouteur de groupe de disponibilité SQL à la fois du fournisseur de ressources SQL et de votre groupe de disponibilité : 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Forum aux questions sur la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Notes de publication de la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Changement de modèles de licence pour une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md)
* [Vue d’ensemble des groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuration d’une Instance de serveur pour les groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration d’un groupe de disponibilité &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Surveillance des groupes de disponibilité &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Vue d’ensemble des instructions Transact-SQL pour les groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Vue d’ensemble des applets de commande PowerShell pour les groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
