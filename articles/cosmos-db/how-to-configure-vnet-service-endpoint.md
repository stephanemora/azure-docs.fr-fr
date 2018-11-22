---
title: Guide pratique pour configurer l’accès à votre compte Azure Cosmos à partir d’un réseau virtuel et d’un sous-réseau
description: Ce document décrit les étapes nécessaires pour configurer le point de terminaison de service de réseau virtuel Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633680"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Guide pratique pour accéder aux ressources Azure Cosmos DB à partir de réseaux virtuels

Les comptes Azure Cosmos DB peuvent être configurés pour autoriser l’accès uniquement à partir de sous-réseaux spécifiques de Réseau virtuel Azure. Il y a deux étapes à suivre pour limiter l’accès au compte Azure Cosmos avec des connexions issues d’un sous-réseau dans un réseau virtuel.
 
1. Permettez au sous-réseau d’envoyer l’identité du sous-réseau et du réseau virtuel à Azure Cosmos DB. Pour cela, activez le point de terminaison de service d’Azure Cosmos DB sur le sous-réseau concerné.

1. Ajoutez au compte Azure Cosmos une règle spécifiant ce sous-réseau comme source permettant d’accéder au compte.

> [!NOTE]
> Une fois le point de terminaison de service du compte Azure Cosmos activé sur un sous-réseau, la source du trafic qui atteint Azure Cosmos DB bascule de l’adresse IP publique vers le réseau virtuel et le sous-réseau. Ce basculement du trafic s’applique à tous les comptes Azure Cosmos accessibles à partir de ce sous-réseau. Si votre ou vos comptes Azure Cosmos utilisent un pare-feu basé sur l’adresse IP pour autoriser ce sous-réseau, les demandes issues du sous-réseau avec service ne correspondent plus aux règles de pare-feu IP et sont rejetées. Pour plus d’informations, voir les étapes décrites dans la section [Migrer de la règle de pare-feu IP à une liste de contrôle d’accès de réseau virtuel](#migrate-from-firewall-to-vnet) de cet article. 

Les sections suivantes expliquent comment configurer le point de terminaison de service du réseau virtuel pour un compte Azure Cosmos DB.

## <a id="configure-using-portal"></a>Configurer le point de terminaison de service avec le Portail Azure

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurer le point de terminaison de service pour un réseau virtuel et un sous-réseau Azure existants

1. Dans le panneau **Toutes les ressources**, recherchez le compte Azure Cosmos que vous souhaitez sécuriser.

1. Sélectionnez **Pare-feu et réseaux virtuels Azure** dans le menu Paramètres et choisissez Autoriser l’accès dans **Réseaux sélectionnés**.

1. Pour accorder l’accès au sous-réseau d’un réseau virtuel existant, sélectionnez **Ajouter un réseau virtuel Azure existant** sous **Réseaux virtuels**.

1. Sélectionnez l’**Abonnement** à partir duquel vous souhaitez ajouter le réseau virtuel Azure. Sélectionnez les **Réseaux virtuels** et **Sous-réseaux** Azure auxquels vous voulez donner accès à votre compte Azure Cosmos. Ensuite, sélectionnez **Activer** pour activer les réseaux sélectionnés avec les points de terminaison de service pour « Microsoft.AzureCosmosDB ». Une fois terminé, sélectionnez **Ajouter**. 

   ![Sélectionner un réseau virtuel et un sous-réseau](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Lorsque le compte Azure Cosmos se voit autoriser l’accès à partir d’un réseau virtuel, il autorise uniquement le trafic provenant de ce sous-réseau. Le réseau virtuel et le sous-réseau que vous avez ajoutés devraient se présenter comme dans la capture d’écran suivante :

   ![Réseau virtuel et sous-réseau configurés correctement](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Les autorisations d’abonnement suivantes sont nécessaires pour activer les points de terminaison de service du réseau virtuel :
  * Abonnement avec un réseau virtuel : contributeur réseau
  * Abonnement avec un compte Azure Cosmos : contributeur de compte DocumentDB

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurer le point de terminaison de service pour un nouveau réseau virtuel et un nouveau sous-réseau Azure

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.  

2. Avant d’activer le point de terminaison de service de réseau virtuel, copiez les informations sur le pare-feu IP associé à votre compte Azure Cosmos DB pour un usage ultérieur. Vous pourrez réactiver le pare-feu IP après avoir configuré le point de terminaison de service.  

3. Sélectionnez **Pare-feu et réseaux virtuels Azure** dans le menu Paramètres et choisissez Autoriser l’accès dans **Réseaux sélectionnés**.  

4. Pour accorder l’accès à un nouveau réseau virtuel Azure, sélectionnez **Ajouter un nouveau réseau virtuel** sous Réseaux virtuels.  

5. Fournissez les détails nécessaires pour créer un réseau virtuel, puis sélectionnez Créer. Le sous-réseau sera créé avec un point de terminaison de service pour « Microsoft.AzureCosmosDB » activé.

   ![Sélectionner le réseau virtuel et le sous-réseau pour le nouveau réseau virtuel](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Si votre compte Azure Cosmos est utilisé par d’autres services Azure comme la Recherche Azure, ou s’il est accessible à partir de Stream Analytics ou de Power BI, vous pouvez autoriser l’accès en cochant Autoriser l’accès aux services Azure.

Pour être sûr d’avoir accès aux métriques Azure Cosmos DB à partir du portail, vous devez activer l’option **Autoriser l’accès au portail Azure**. Pour plus d’informations sur ces options, consultez les sections Demandes provenant du Portail Azure et Demande provenant des services PaaS Azure de l’article [Configurer un pare-feu IP](how-to-configure-firewall.md). Après avoir sélectionné l’accès, sélectionnez **Enregistrer** pour enregistrer les paramètres.

## <a id="remove-vnet-or-subnet"></a>Supprimer un réseau virtuel ou un sous-réseau 

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB pour lequel vous avez affecté des points de terminaison de service.  

2. Sélectionnez **Pare-feu et réseaux virtuels** dans le menu Paramètres.  

3. Pour supprimer une règle de réseau virtuel ou de sous-réseau, sélectionnez «... » en regard du réseau virtuel ou du sous-réseau et sélectionnez **Supprimer**.

   ![Supprimer un réseau virtuel](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Cliquez sur **Enregistrer** pour enregistrer les changements.

## <a id="configure-using-powershell"></a>Configurer le point de terminaison de service avec Azure PowerShell 

Suivez les étapes ci-dessous afin de configurer le point de terminaison de service pour un compte Azure Cosmos DB avec Azure PowerShell :  

1. Installez la dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Préparez-vous à l’activation de la liste ACL sur le compte Azure Cosmos en vérifiant que le point de terminaison service du réseau virtuel et du sous-réseau est activé pour votre compte.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Obtenez les propriétés du compte Azure Cosmos DB en exécutant l’applet de commande suivante :  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisez les variables pour une utilisation ultérieure. Configurez toutes les variables à partir de la définition de compte existante. Si vous disposez de plusieurs emplacements, vous devez les ajouter en tant que partie du tableau. Lors de cette étape, vous configurez également le point de terminaison de service de réseau virtuel en affectant la valeur « True » à la variable « accountVNETFilterEnabled ». Cette valeur est assignée ultérieurement au paramètre « isVirtualNetworkFilterEnabled ». 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Mettez à jour les propriétés du compte Azure Cosmos DB avec la nouvelle configuration en exécutant les applets de commande suivantes : 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. Exécutez la commande suivante pour vérifier que votre compte Azure Cosmos DB est mis à jour avec le point de terminaison de service de réseau virtuel que vous avez configuré à l’étape précédente :

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurer le point de terminaison de service avec Azure CLI 

1. Activez le point de terminaison de service pour un sous-réseau dans un réseau virtuel.

1. Mettez à jour le compte Azure Cosmos existant avec des listes ACL de sous-réseau.

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Créez un compte Azure Cosmos avec des listes ACL de sous-réseau.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrer de la règle de pare-feu IP à une liste ACL de réseau virtuel 

Les étapes suivantes ne sont nécessaires que pour les comptes Azure Cosmos qui comportent déjà des règles de pare-feu IP autorisant un sous-réseau, si vous souhaitez utiliser des listes ACL de réseau virtuel et de sous-réseau plutôt qu’une règle de pare-feu IP.

Lorsque le point de terminaison de service du compte Azure Cosmos est activé pour un sous-réseau, les demandes sont envoyées avec la source contenant les informations relatives au réseau virtuel et au sous-réseau, plutôt que l’adresse IP publique. Par conséquent, il n’y a pas de correspondance entre ces demandes et un filtre IP. Ce changement de source se produit pour tous les comptes Azure Cosmos accessibles à partir du sous-réseau avec le point de terminaison de service activé. Afin d’éviter tout temps d’arrêt, suivez les étapes ci-dessous :

1. Récupérez les propriétés du compte Azure Cosmos en exécutant la cmdlet suivante :

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialisez les variables pour pouvoir les utiliser par la suite. Configurez toutes les variables à partir de la définition de compte existante. Ajoutez la liste ACL VNET à tous les comptes Azure Cosmos accessibles à partir du sous-réseau avec l’indicateur `ignoreMissingVNetServiceEndpoint`.  

   Si vous avez plusieurs emplacements, ajoutez-les dans le tableau. Lors de cette étape, vous configurez également le point de terminaison de service de réseau virtuel en affectant la valeur « True » à la variable « accountVNETFilterEnabled ». Cette valeur est assignée ultérieurement au paramètre « isVirtualNetworkFilterEnabled ».

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Mettez à jour les propriétés du compte Azure Cosmos avec la nouvelle configuration en exécutant les cmdlets suivantes :

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Répétez les étapes 1 à 3 pour tous les comptes Azure Cosmos auxquels vous accédez à partir du sous-réseau.

1.  Attendez 15 minutes, puis mettez à jour le sous-réseau en activant le point de terminaison de service.

1.  Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Supprimer la règle de pare-feu IP du sous-réseau.

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer un pare-feu pour Azure Cosmos DB, voir l’article [Prise en charge du pare-feu](firewall-support.md).

