---
title: Configurer un groupe de disponibilité à l'aide d'Azure CLI
description: Utilisez Azure CLI pour créer le cluster de basculement Windows, l’écouteur de groupe de disponibilité et l’équilibreur de charge interne sur une machine virtuelle SQL Server dans Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669339"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Utiliser Azure CLI pour configurer un groupe de disponibilité Always On pour SQL Server sur une machine virtuelle Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article explique comment utiliser [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) pour déployer un cluster de basculement Windows, ajouter des machines virtuelles SQL Server au cluster et créer l’équilibreur de charge interne et l’écouteur pour un groupe de disponibilité Always On. Le déploiement du groupe de disponibilité Always On s’effectue encore manuellement via SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Prérequis
Pour automatiser la configuration d’un groupe de disponibilité Always On à l’aide d’Azure CLI, vous devez avoir les prérequis suivants : 
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un groupe de ressources avec un contrôleur de domaine. 
- Une ou plusieurs [machines virtuelles jointes à un domaine dans Azure qui exécutent SQL Server 2016 (ou version ultérieure) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) dans le *même groupe à haute disponibilité ou dans des zones de disponibilité différentes* et qui sont [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](sql-vm-resource-provider-register.md).  
- [Interface de ligne de commande Azure](/cli/azure/install-azure-cli). 
- Deux adresses IP disponibles (non utilisées par une entité) : une pour l’équilibreur de charge interne et l’autre pour l’écouteur de groupe de disponibilité dans le même sous-réseau que le groupe de disponibilité. Si vous utilisez un équilibreur de charge existant, une seule adresse IP disponible suffit pour l’écouteur de groupe de disponibilité. 

## <a name="permissions"></a>Autorisations
Vous avez besoin des autorisations de compte suivantes pour configurer le groupe de disponibilité Always On à l’aide d’Azure CLI : 

- Un compte d’utilisateur de domaine existant qui dispose d’une autorisation de **création d’objet ordinateur** dans le domaine. Par exemple, un compte d’administrateur de domaine dispose généralement d’une autorisation suffisante (par exemple : account@domain.com). _Ce compte doit également faire partie du groupe administrateur local sur chaque machine virtuelle pour créer le cluster._
- Le compte d’utilisateur du domaine qui contrôle SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Étape 1 : Créer un compte de stockage en tant que témoin de cloud
Le cluster a besoin d’un compte de stockage configuré en tant que témoin de cloud. Vous pouvez utiliser un compte de stockage existant ou en créer un. Si vous voulez utiliser un compte de stockage existant, passez à la section suivante. 

L’extrait de code suivant crée le compte de stockage : 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Vous pouvez rencontrer l’erreur `az sql: 'vm' is not in the 'az sql' command group` si vous utilisez une version obsolète d’Azure CLI. Téléchargez la [dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) pour ne plus avoir cette erreur.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Étape 2 : Définir les métadonnées de cluster de basculement Windows
Le groupe de commandes [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) d’Azure CLI gère les métadonnées du service Cluster de basculement Windows Server (WSFC) qui héberge le groupe de disponibilité. Les métadonnées de cluster englobent le domaine Active Directory, les comptes de cluster, les comptes de stockage à utiliser en tant que témoin de cloud et la version de SQL Server. Utilisez [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) pour définir les métadonnées de WSFC de sorte qu’à la première machine virtuelle SQL Server ajoutée, le cluster soit créé comme défini. 

L’extrait de code suivant définit les métadonnées du cluster :
```azurecli-interactive
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Étape 3 : Ajouter des machines virtuelles SQL Server au cluster
Ajouter la première machine virtuelle SQL Server au cluster permet de créer le cluster. La commande [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) crée le cluster avec le nom précédemment attribué, installe le rôle de cluster sur les machines virtuelles SQL Server et les ajoute au cluster. Les utilisations suivantes de la commande `az sql vm add-to-group` ajoutent des machines virtuelles SQL Server supplémentaires au cluster nouvellement créé. 

L’extrait de code suivant crée le cluster et lui ajoute la première machine virtuelle SQL Server : 

```azurecli-interactive
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
Utilisez cette commande pour ajouter d’autres machines virtuelles SQL Server au cluster. Modifiez uniquement le paramètre `-n` pour le nom de machine virtuelle SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Étape 4 : Créez le groupe de disponibilité
Créez manuellement le groupe de disponibilité comme vous le feriez normalement, en utilisant [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Ne créez *pas* d’écouteur pour l’instant, car cette opération sera effectuée via Azure CLI dans les sections suivantes.  

## <a name="step-5-create-the-internal-load-balancer"></a>Étape 5 : Créer l’équilibreur de charge interne

L’écouteur de groupe de disponibilité Always On a besoin d’une instance interne d’Azure Load Balancer. L’équilibreur de charge interne fournit une adresse IP « flottante » pour l’écouteur de groupe de disponibilité afin d’accélérer les opérations de basculement et de reconnexion. Si les machines virtuelles SQL Server d’un groupe de disponibilité font partie du même groupe à haute disponibilité, vous pouvez utiliser un équilibreur de charge de base. Dans le cas contraire, vous devez utiliser un équilibreur de charge standard.  

> [!NOTE]
> L’équilibreur de charge interne doit se trouver dans le même réseau virtuel que les instances de machine virtuelle SQL Server. 

L’extrait de code suivant crée l’équilibreur de charge interne :

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> La ressource d’adresse IP publique de chaque machine virtuelle SQL Server doit avoir une référence SKU standard compatible avec l’équilibreur de charge standard. Pour déterminer la référence SKU de la ressource d’adresse IP publique de votre machine virtuelle, accédez à **Groupe de ressources**, sélectionnez votre ressources **Adresse IP publique** pour la machine virtuelle SQL Server souhaitée, puis recherchez la valeur sous **Référence SKU** dans le volet **Vue d’ensemble**.  

## <a name="step-6-create-the-availability-group-listener"></a>Étape 6 : Créer l'écouteur de groupe de disponibilité
Après avoir créé manuellement le groupe de disponibilité, vous pouvez créer l’écouteur à l’aide de la commande [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

L’*ID de ressource de sous-réseau* est la valeur de `/subnets/<subnetname>` ajoutée à l’ID de ressource de la ressource de réseau virtuel. Pour identifier l’ID de ressource de sous-réseau :
   1. Accédez au groupe de ressources sur le [portail Azure](https://portal.azure.com). 
   1. Sélectionnez la ressource de réseau virtuel. 
   1. Sélectionnez **Propriétés** dans le volet **Paramètres**. 
   1. Identifiez l’ID de ressource du réseau virtuel et ajoutez `/subnets/<subnetname>` à la fin de celui-ci pour créer l’ID de ressource de sous-réseau. Par exemple :
      - Votre ID de ressource de réseau virtuel est : `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Le nom de votre sous-réseau est : `default`
      - Votre ID de ressource de sous-réseau est donc : `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


L’extrait de code suivant crée l’écouteur de groupe de disponibilité :

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modifier le nombre de réplicas dans un groupe de disponibilité
Le déploiement d’un groupe de disponibilité sur des machines virtuelles SQL Server hébergées dans Azure ajoute une couche de complexité supplémentaire. Le fournisseur de ressources et le groupe de machines virtuelles gèrent désormais les ressources. De ce fait, quand vous ajoutez ou supprimez des réplicas dans le groupe de disponibilité, il est nécessaire d’effectuer une étape supplémentaire de mise à jour des métadonnées de l’écouteur avec des informations sur les machines virtuelles SQL Server. Quand vous modifiez le nombre de réplicas dans le groupe de disponibilité, vous devez aussi utiliser la commande [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) pour mettre à jour l’écouteur avec les métadonnées des machines virtuelles SQL Server. 


### <a name="add-a-replica"></a>Ajouter un réplica

Pour ajouter un nouveau réplica au groupe de disponibilité :

1. Ajoutez la machine virtuelle SQL Server au cluster :
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Utilisez SQL Server Management Studio pour ajouter l’instance SQL Server en tant que réplica dans le groupe de disponibilité.
1. Ajoutez les métadonnées de la machine virtuelle SQL Server à l’écouteur :
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Supprimer un réplica

Pour supprimer un réplica du groupe de disponibilité :

1. Supprimez le réplica du groupe de disponibilité à l’aide de SQL Server Management Studio. 
1. Supprimez les métadonnées de la machine virtuelle SQL Server de l’écouteur :
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Supprimez la machine virtuelle SQL Server du cluster :
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Supprimer l’écouteur de groupe de disponibilité
Si, par la suite, vous avez besoin de supprimer l’écouteur de groupe de disponibilité configuré avec Azure CLI, vous devez passer par le fournisseur de ressources de machine virtuelle SQL. Comme l’écouteur est inscrit par l’intermédiaire du fournisseur de ressources de machine virtuelle SQL, le supprimer via SQL Server Management Studio ne suffit pas. 

La meilleure méthode consiste à le supprimer par l’intermédiaire du fournisseur de ressources de machine virtuelle SQL en utilisant l’extrait de code suivant dans Azure CLI. Cela a pour effet de supprimer les métadonnées de l’écouteur de groupe de disponibilité au niveau du fournisseur de ressources de machine virtuelle SQL. De même, l’écouteur de groupe de disponibilité est supprimé physiquement du groupe de disponibilité. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble des machines virtuelles SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Questions fréquentes (FAQ) sur les machines virtuelles SQL Server](frequently-asked-questions-faq.md)
* [Notes de publication pour les machines virtuelles SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Changement de modèles de licence pour une machine virtuelle SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Vue d’ensemble des groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuration d’une instance de serveur pour des groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration d’un groupe de disponibilité &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Supervision de groupes de disponibilité &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Vue d’ensemble des instructions Transact-SQL pour les groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Vue d’ensemble des applets de commande PowerShell pour les groupes de disponibilité Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
