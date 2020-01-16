---
title: Configurer l’accès à un compte Azure Cosmos à partir d’un réseau virtuel
description: Ce document décrit les étapes nécessaires pour configurer un point de terminaison de service de réseau virtuel pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 36f6152e52d6cb45d0a30b385678596331232560
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980691"
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

## <a id="configure-using-portal"></a>Configurer un point de terminaison de service avec le portail Azure

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

## <a id="remove-vnet-or-subnet"></a>Supprimer un réseau virtuel ou un sous-réseau

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB pour lequel vous avez affecté des points de terminaison de service.  

2. Sélectionnez **Pare-feu et réseaux virtuels** dans le menu Paramètres.  

3. Pour supprimer une règle de réseau virtuel ou de sous-réseau, sélectionnez **...** en regard du réseau virtuel ou du sous-réseau et sélectionnez **Supprimer**.

   ![Supprimer un réseau virtuel](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Sélectionnez **Enregistrer** pour enregistrer vos modifications.

## <a id="configure-using-powershell"></a>Configurer un point de terminaison de service avec Azure PowerShell

> [!NOTE]
> Lorsque vous utilisez PowerShell ou l’interface Azure CLI, n’oubliez pas de spécifier la liste complète des filtres IP et les ACL du réseau virtuel dans Paramètres, pas seulement ceux qui doivent être ajoutés.

Effectuez les étapes suivantes afin de configurer un point de terminaison de service pour un compte Azure Cosmos DB à l’aide d’Azure PowerShell :  

1. Installez [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Obtenez les informations relatives au réseau virtuel.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Obtenez les propriétés du compte Azure Cosmos DB en exécutant la cmdlet suivante :  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisez les variables pour une utilisation ultérieure. Configurez toutes les variables à partir de la définition de compte existante.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Mettez à jour les propriétés du compte Azure Cosmos DB avec la nouvelle configuration en exécutant les applets de commande suivantes : 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Exécutez la commande suivante pour vérifier que votre compte Azure Cosmos DB est mis à jour avec le point de terminaison de service de réseau virtuel que vous avez configuré à l’étape précédente :

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurer un point de terminaison de service avec Azure CLI

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

## <a id="migrate-from-firewall-to-vnet"></a>Migrer à partir d’une règle de pare-feu IP à une liste de contrôle d’accès de réseau virtuel

Utilisez les étapes suivantes uniquement pour les comptes Azure Cosmos DB qui comportent déjà des règles de pare-feu IP autorisant un sous-réseau, si vous souhaitez utiliser des listes ACL de réseau virtuel et de sous-réseau plutôt qu’une règle de pare-feu IP.

Lorsqu’un point de terminaison de service d’un compte Azure Cosmos DB est activé pour un sous-réseau, les requêtes sont envoyées avec une source contenant les informations relatives au réseau virtuel et au sous-réseau, plutôt qu’une adresse IP publique. Ces requêtes ne correspondent pas à un filtre IP. Ce changement de source se produit pour tous les comptes Azure Cosmos DB accessibles à partir du sous-réseau avec un point de terminaison de service activé. Afin d’éviter tout temps d’arrêt, suivez les étapes ci-dessous :

1. Obtenez les propriétés du compte Azure Cosmos DB en exécutant la cmdlet suivante :

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisez les variables pour pouvoir les utiliser par la suite. Configurez toutes les variables à partir de la définition de compte existante. Ajoutez la liste ACL de réseau virtuel à tous les comptes Azure Cosmos DB accessibles à partir du sous-réseau avec l’indicateur `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Mettez à jour les propriétés du compte Azure Cosmos DB avec la nouvelle configuration en exécutant les applets de commande suivantes :

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Répétez les étapes 1 à 3 pour tous les comptes Azure Cosmos DB auxquels vous accédez à partir du sous-réseau.

1.  Attendez 15 minutes, puis mettez à jour le sous-réseau pour activer le point de terminaison de service.

1.  Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Supprimez la règle de pare-feu IP pour le sous-réseau.

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer un pare-feu pour Azure Cosmos DB, voir l’article [Prise en charge du pare-feu](firewall-support.md).
