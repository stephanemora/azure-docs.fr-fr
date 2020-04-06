---
title: Configurer l’accès à un compte Azure Cosmos à partir d’un réseau virtuel
description: Ce document décrit les étapes nécessaires pour configurer un point de terminaison de service de réseau virtuel pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366231"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configurer l’accès à partir de réseaux virtuels (VNet)

Vous pouvez configurer les comptes Azure Cosmos DB pour autoriser l’accès uniquement à partir d’un sous-réseau spécifique de réseau virtuel Azure. Pour limiter l’accès à un compte Azure Cosmos DB avec des connexions issues d’un sous-réseau dans un réseau virtuel :

1. Autorisez le sous-réseau à envoyer l’identité du sous-réseau et du réseau virtuel à Azure Cosmos DB. Pour cela, activez un point de terminaison de service pour Azure Cosmos DB sur le sous-réseau concerné.

1. Ajoutez au compte Azure Cosmos DB une règle spécifiant le sous-réseau comme source permettant d’accéder au compte.

> [!NOTE]
> Une fois qu’un point de terminaison de service pour votre compte Azure Cosmos DB est activé sur un sous-réseau, la source du trafic qui atteint Azure Cosmos DB bascule d’une adresse IP publique vers un réseau virtuel et un sous-réseau. Le basculement du trafic s’applique à tous les comptes Azure Cosmos DB accessibles à partir de ce sous-réseau. Si vos comptes Azure Cosmos DB utilisent un pare-feu basé sur l’adresse IP pour autoriser ce sous-réseau, les requêtes issues du sous-réseau avec service ne correspondent plus aux règles de pare-feu IP et sont rejetées.
>
> Pour plus d’informations, voir les étapes décrites dans la section [Migrer à partir d’une règle de pare-feu IP à une liste de contrôle d’accès de réseau virtuel](#migrate-from-firewall-to-vnet) de cet article.

Les sections suivantes expliquent comment configurer un point de terminaison de service de réseau virtuel pour un compte Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configurer un point de terminaison de service avec le portail Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurer un point de terminaison de service pour un réseau virtuel et un sous-réseau Azure existants

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.

1. Sélectionnez **Pare-feu et réseaux virtuels** dans le menu Paramètres et choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**.

1. Pour accorder l’accès au sous-réseau d’un réseau virtuel existant, sélectionnez **Ajouter un réseau virtuel Azure existant** sous **Réseaux virtuels**.

1. Sélectionnez **l’abonnement** à partir duquel vous souhaitez ajouter un réseau virtuel Azure. Sélectionnez les **Réseaux virtuels** et **Sous-réseaux** Azure auxquels vous voulez fournir l’accès à votre compte Azure Cosmos DB. Ensuite, sélectionnez **Activer** pour activer les réseaux sélectionnés avec les points de terminaison de service pour « Microsoft.AzureCosmosDB ». Une fois terminé, sélectionnez **Ajouter**.

   ![Sélectionner un réseau virtuel et un sous-réseau](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Lorsque le compte Azure Cosmos DB se voit autoriser l’accès à partir d’un réseau virtuel, il autorise uniquement le trafic provenant de ce sous-réseau. Le réseau virtuel et le sous-réseau que vous avez ajoutés devraient se présenter comme dans la capture d’écran suivante :

   ![Réseau virtuel et sous-réseau configurés correctement](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Les autorisations d’abonnement suivantes sont nécessaires pour activer les points de terminaison de service de réseau virtuel :
>   * Abonnement avec un réseau virtuel : Contributeur de réseau
>   * Abonnement avec un compte Azure Cosmos DB : Contributeur de compte DocumentDB
>   * Si votre réseau virtuel et votre compte Azure Cosmos DB se trouvent dans des abonnements différents, assurez-vous que l’abonnement contenant le réseau virtuel possède également le fournisseur de ressources `Microsoft.DocumentDB` inscrit. Pour inscrire un fournisseur de ressources, consultez l’article [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

Voici les instructions pour inscrire un abonnement avec le fournisseur de ressources.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurer un point de terminaison de service pour un nouveau réseau virtuel et un nouveau sous-réseau Azure

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.  

1. Sélectionnez **Pare-feu et réseaux virtuels Azure** dans le menu Paramètres et choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**.  

1. Pour accorder l’accès à un nouveau réseau virtuel Azure, sélectionnez **Ajouter un nouveau réseau virtuel** sous **Réseaux virtuels**.  

1. Fournissez les détails nécessaires pour créer un réseau virtuel, puis sélectionnez **Créer**. Le sous-réseau sera créé avec un point de terminaison de service pour « Microsoft.AzureCosmosDB » activé.

   ![Sélectionner un réseau virtuel et un sous-réseau pour un nouveau réseau virtuel](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Si votre compte Azure Cosmos DB est utilisé par d’autres services Azure, comme la Recherche cognitive Azure, ou s’il est accessible à partir de Stream Analytics ou de Power BI, sélectionnez **Accepter les connexions provenant des centres de données Azure globaux** pour en autoriser l’accès.

Pour être sûr d’avoir accès aux métriques Azure Cosmos DB à partir du portail, vous devez activer l’option **Autoriser l’accès à partir du portail Azure**. Pour en savoir plus sur ces options, consultez l’article [Configurer un pare-feu IP](how-to-configure-firewall.md). Après avoir activé l’accès, sélectionnez **Enregistrer** pour enregistrer les paramètres.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Supprimer un réseau virtuel ou un sous-réseau

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB pour lequel vous avez affecté des points de terminaison de service.  

1. Sélectionnez **Pare-feu et réseaux virtuels** dans le menu Paramètres.  

1. Pour supprimer une règle de réseau virtuel ou de sous-réseau, sélectionnez **...** en regard du réseau virtuel ou du sous-réseau et sélectionnez **Supprimer**.

   ![Supprimer un réseau virtuel](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Sélectionnez **Enregistrer** pour enregistrer vos modifications.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configurer un point de terminaison de service avec Azure PowerShell

> [!NOTE]
> Lorsque vous utilisez PowerShell ou l’interface Azure CLI, n’oubliez pas de spécifier la liste complète des filtres IP et les ACL du réseau virtuel dans Paramètres, pas seulement ceux qui doivent être ajoutés.

Effectuez les étapes suivantes afin de configurer un point de terminaison de service pour un compte Azure Cosmos DB à l’aide d’Azure PowerShell :  

1. Installez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Obtenez les informations relatives au réseau virtuel.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Préparer une règle de réseau virtuel Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Mettez à jour les propriétés du compte Azure Cosmos DB avec la nouvelle configuration du point de terminaison du réseau virtuel : 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Exécutez la commande suivante pour vérifier que votre compte Azure Cosmos DB est mis à jour avec le point de terminaison de service de réseau virtuel que vous avez configuré à l’étape précédente :

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Configurer un point de terminaison de service avec Azure CLI

Des comptes Azure Cosmos peuvent être configurés pour des points de terminaison de service créés ou mis à jour ultérieurement si le sous-réseau est déjà configuré pour ceux-ci. Des points de terminaison de service peuvent également être activés sur le compte Cosmos où le sous-réseau n’est pas encore configuré pour ceux-ci. Ils commenceront à fonctionner lorsque le sous-réseau sera configuré. Cette flexibilité permet aux administrateurs qui n’ont pas accès aux ressources du compte Cosmos et du réseau virtuel de rendre leurs configurations indépendantes les unes des autres.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Créer un compte Cosmos et le connecter à un sous-réseau backend pour un nouveau réseau virtuel

Dans cet exemple, le réseau virtuel et le sous-réseau sont créés avec des points de terminaison de service activés pour les deux lors de leur création.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Connecter et configurer un compte Cosmos sur un sous-réseau backend de manière indépendante

Cet exemple vise à montrer comment connecter un compte Azure Cosmos à un nouveau réseau virtuel dont le sous-réseau n’est pas encore configuré pour des points de terminaison de service. Pour ce faire, utilisez le paramètre `--ignore-missing-vnet-service-endpoint`. Cela permet de configurer le compte Cosmos sans erreur avant que la configuration du sous-réseau du réseau virtuel soit complète. Une fois le sous-réseau configuré, le compte Cosmos est alors accessible via le sous-réseau configuré.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrer à partir d’une règle de pare-feu IP à une liste de contrôle d’accès de réseau virtuel

Pour migrer un compte Azure Cosmos DB utilisant des règles de pare-feu IP afin qu’il utilise des points de terminaison de service de réseau virtuel, procédez comme suit.

Après la configuration d’un compte Azure Cosmos DB pour un point de terminaison de service pour un sous-réseau, les demandes de ce sous-réseau sont envoyées à Azure Cosmos DB avec les informations sur le réseau virtuel et le sous-réseau source au lieu d’une adresse IP publique source. Ces demandes ne correspondent plus à un filtre IP configuré sur le compte Azure Cosmos DB, ce qui explique pourquoi les étapes suivantes sont nécessaires afin d’éviter les temps d’arrêt.

Avant de continuer, activez le point de terminaison de service Azure Cosmos DB sur le réseau virtuel et le sous-réseau à l’aide de l’étape indiquée ci-dessus dans « Activer le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel ».

1. Obtenez les informations relatives au réseau virtuel et au sous-réseau :

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Préparez un nouvel objet de règle Réseau virtuel pour le compte Azure Cosmos DB :

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Mettez à jour le compte Azure Cosmos DB pour activer l’accès au point de terminaison de service à partir du sous-réseau :

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Répétez les étapes précédentes pour tous les comptes Azure Cosmos DB faisant l’objet d’un accès à partir du sous-réseau.

1. Dans les règles de pare-feu du compte Azure Cosmos DB, supprimez la règle de pare-feu IP pour le sous-réseau.

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer un pare-feu pour Azure Cosmos DB, voir l’article [Prise en charge du pare-feu](firewall-support.md).
