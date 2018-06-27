---
title: Sécuriser l’accès à un compte Azure Cosmos DB à l’aide du point de terminaison de service Réseau virtuel Azure | Microsoft Docs
description: Ce document décrit les étapes nécessaires pour configurer le point de terminaison de service de réseau virtuel Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: 0bd31270ca67dc993cc7ac72ab2bab9bf70005ca
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293993"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Sécuriser l’accès à un compte Azure Cosmos DB à l’aide du point de terminaison de service Réseau virtuel Azure

Les comptes Azure Cosmos DB peuvent être configurés pour autoriser l’accès uniquement à partir de sous-réseaux spécifiques de Réseau virtuel Azure. Quand vous activez un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) pour Azure Cosmos DB à partir d’un réseau virtuel et de son sous-réseau, le trafic est assuré de bénéficier d’un itinéraire optimal et sécurisé vers Azure Cosmos DB.  

Azure Cosmos DB est un service de base de données multimodèle distribué à l’échelle mondiale. Vous pouvez répliquer les données présentes dans un compte Azure Cosmos DB vers plusieurs régions. Quand Azure Cosmos DB est configuré avec un point de terminaison de service de réseau virtuel, chaque réseau virtuel autorise l’accès à partir d’adresses IP appartenant au sous-réseau spécifique. L’image suivante montre une base de données Azure Cosmos avec un point de terminaison de service de réseau virtuel activé :

![Architecture de point de terminaison de service de réseau virtuel](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Une fois qu’un compte Azure Cosmos DB est configuré avec un point de terminaison de service de réseau virtuel, il est accessible uniquement à partir du sous-réseau spécifié ; tout accès Internet/public est impossible. Pour en savoir plus sur les points de terminaison de service, consultez l’article Azure [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

> [!NOTE]
> Actuellement, les points de terminaison de service de réseau virtuel peuvent être configurés pour les comptes d’API SQL Azure Cosmos DB ou d’API Mongo. La possibilité de configurer des points de terminaison de service pour d’autres API et clouds souverains tels qu’Azure Allemagne ou Azure Government sera prochainement disponible. Si vous avez un pare-feu IP existant configuré pour votre compte Azure Cosmos DB, notez la configuration du pare-feu, supprimez le pare-feu IP, puis configurez la liste ACL du point de terminaison de service. Après avoir configuré le point de terminaison de service, vous pouvez réactiver le pare-feu IP si nécessaire.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Configurer le point de terminaison de service à l’aide du portail Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurer le point de terminaison de service pour un réseau virtuel et un sous-réseau Azure existants

1. À partir du panneau **Toutes les ressources**, recherchez le réseau virtuel pour lequel vous souhaitez configurer le point de terminaison de service pour Azure Cosmos DB. Accédez au panneau **Points de terminaison de service** et vérifiez que le sous-réseau du réseau virtuel a été activé pour le point de terminaison de service « Azure.CosmosDB ».  

   ![Confirmer que le point de terminaison de service est activé](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.  

3. Avant d’activer le point de terminaison de service de réseau virtuel, copiez les informations sur le pare-feu IP associé à votre compte Azure Cosmos DB pour un usage ultérieur. Vous pourrez réactiver le pare-feu IP après avoir configuré le point de terminaison de service.  

4. Sélectionnez **Pare-feu et réseaux virtuels Azure** dans le menu Paramètres et choisissez Autoriser l’accès dans **Réseaux sélectionnés**.  

3. Pour accorder l’accès au sous-réseau d’un réseau virtuel existant, sous Réseaux virtuels, sélectionnez **Ajouter un réseau virtuel Azure existant**.  

4. Sélectionnez l’**Abonnement** à partir duquel vous souhaitez ajouter le réseau virtuel Azure. Sélectionnez les **Réseaux virtuels** et **Sous-réseaux** Azure auxquels vous voulez fournir l’accès à votre compte Azure Cosmos DB. Ensuite, sélectionnez **Activer** pour activer les réseaux sélectionnés avec les points de terminaison de service pour « Microsoft.AzureCosmosDB ». Une fois terminé, sélectionnez **Ajouter**.  

   ![Sélectionner un réseau virtuel et un sous-réseau](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Si aucun point de terminaison de service pour Azure Cosmos DB n’a encore été configuré pour les réseaux virtuels et les sous-réseaux Azure sélectionnés, vous pouvez le configurer dans le cadre de cette opération. L’activation de l’accès prendra jusqu’à 15 minutes. Il est très important de désactiver le pare-feu IP après avoir noté le contenu de la liste de contrôle d'accès du pare-feu afin de les réactiver plus tard. 

   ![Réseau virtuel et sous-réseau configurés correctement](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Désormais, votre compte Azure Cosmos DB autorisera uniquement le trafic provenant de ce sous-réseau choisi. Si vous aviez précédemment activé le pare-feu IP, réactivez-le en utilisant les informations précédentes.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurer le point de terminaison de service pour un nouveau réseau virtuel et un nouveau sous-réseau Azure

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.  

> [!NOTE]
> Si vous avez un pare-feu IP existant configuré pour votre compte Azure Cosmos DB, notez la configuration du pare-feu, supprimez le pare-feu IP, puis configurez le point de terminaison de service. Si vous activez le point de terminaison de service sans désactiver le pare-feu, le trafic à partir de cette plage IP perdra l’identité de l’IP virtuelle et il est supprimé avec un message d’erreur de filtre IP. Donc pour éviter cette erreur, vous devez toujours désactiver les règles de pare-feu, les copier, activer le point de terminaison de service à partir du sous-réseau puis activer l’ACL du sous-réseau à partir de Cosmos DB. Une fois le point de terminaison de service configuré et l’ACL ajoutée, vous pouvez réactiver le pare-feu IP si nécessaire.

2. Avant d’activer le point de terminaison de service de réseau virtuel, copiez les informations sur le pare-feu IP associé à votre compte Azure Cosmos DB pour une utilisation ultérieure. Vous pourrez réactiver le pare-feu IP après avoir configuré le point de terminaison de service.  

3. Sélectionnez **Pare-feu et réseaux virtuels Azure** dans le menu Paramètres et choisissez Autoriser l’accès dans **Réseaux sélectionnés**.  

4. Pour accorder l’accès à un nouveau réseau virtuel, sous Réseaux virtuels, sélectionnez **Ajouter un nouveau réseau virtuel**.  

5. Fournissez les détails nécessaires pour créer un réseau virtuel, puis sélectionnez Créer. Le sous-réseau sera créé avec un point de terminaison de service pour « Microsoft.AzureCosmosDB » activé.

   ![Sélectionner le réseau virtuel et le sous-réseau pour le nouveau réseau virtuel](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Autoriser l’accès à partir du portail Azure

Une fois que les points de terminaison de service de réseau virtuel Azure sont activés pour votre compte de base de données Azure Cosmos DB, l’accès à partir du portail ou d’autres services Azure est désactivé par défaut. L’accès à votre compte de base de données Azure Cosmos DB à partir d’ordinateurs situés à l’extérieur du sous-réseau configuré est bloqué, y compris l’accès à partir du portail.

![Autoriser l’accès à partir du portail](./media/vnet-service-endpoint/allow-access-from-portal.png)

Si votre compte Azure Cosmos DB est utilisé par d’autres services Azure tels que Recherche Azure, ou s’il est accessible à partir de Stream Analytics ou de Power BI, vous pouvez autoriser l’accès en cochant **Autoriser l’accès aux services Azure**.

Pour être sûr d’avoir accès aux métriques Azure Cosmos DB à partir du portail, vous devez activer l’option **Autoriser l’accès au portail Azure**. Pour en savoir plus sur ces options, consultez les sections [Connexions à partir du Portail Azure](firewall-support.md#connections-from-the-azure-portal) et [Connexions à partir d’autres services Azure PaaS](firewall-support.md#connections-from-public-azure-datacenters-or-azure-paas-services). Après avoir sélectionné l’accès, sélectionnez **Enregistrer** pour enregistrer les paramètres.

## <a name="remove-a-virtual-network-or-subnet"></a>Supprimer un réseau virtuel ou un sous-réseau 

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB pour lequel vous avez affecté des points de terminaison de service.  

2. Sélectionnez **Pare-feu et réseaux virtuels** dans le menu Paramètres.  

3. Pour supprimer une règle de réseau virtuel ou de sous-réseau, sélectionnez «... » en regard du réseau virtuel ou du sous-réseau et sélectionnez **Supprimer**.

   ![Supprimer un réseau virtuel](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Cliquez sur **Enregistrer** pour enregistrer les changements.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Configurer le point de terminaison de service à l’aide d’Azure PowerShell 

Effectuez les étapes suivantes afin de configurer le point de terminaison de service pour un compte Azure Cosmos DB à l’aide d’Azure PowerShell :  

1. Installez la dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) et [connectez-vous](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Prenez note des paramètres de pare-feu IP et supprimez complètement le pare-feu IP avant d’activer le point de terminaison de service pour le compte.


> [!NOTE]
> Si vous avez un pare-feu IP existant configuré pour votre compte Azure Cosmos DB, notez la configuration du pare-feu, supprimez le pare-feu IP, puis configurez le point de terminaison de service. Si vous activez le point de terminaison de service sans désactiver le pare-feu, le trafic à partir de cette plage IP perdra l’identité de l’IP virtuelle et il est supprimé avec un message d’erreur de filtre IP. Donc pour éviter cette erreur, vous devez toujours désactiver les règles de pare-feu, les copier, activer le point de terminaison de service à partir du sous-réseau puis activer l’ACL du sous-réseau à partir de Cosmos DB. Une fois le point de terminaison de service configuré et l’ACL ajoutée, vous pouvez réactiver le pare-feu IP si nécessaire.

2. Avant d’activer le point de terminaison de service de réseau virtuel, copiez les informations sur le pare-feu IP associé à votre compte Azure Cosmos DB pour une utilisation ultérieure. Vous réactiverez le pare-feu IP après avoir configuré le point de terminaison de service.  

3. Activez le point de terminaison de service pour un sous-réseau existant d’un réseau virtuel.  

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

4. Préparez-vous à l’activation de la liste ACL sur le compte Cosmos DB en vérifiant que le réseau virtuel et le sous-réseau ont le point de terminaison service activé pour Azure Cosmos DB.

   ```powershell
   $subnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName  | Get-AzureRmVirtualNetworkSubnetConfig -Name $sname
   $vnProp = Get-AzureRmVirtualNetwork `-Name $vnName  -ResourceGroupName $rgName
   ```

5. Obtenez les propriétés du compte Azure Cosmos DB en exécutant l’applet de commande suivante :  

   ```powershell
   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Initialisez les variables pour une utilisation ultérieure. Configurez toutes les variables à partir de la définition de compte existante. Si vous disposez de plusieurs emplacements, vous devez les ajouter en tant que partie du tableau. Lors de cette étape, vous configurez également le point de terminaison de service de réseau virtuel en affectant la valeur « True » à la variable « accountVNETFilterEnabled ». Cette valeur est assignée ultérieurement au paramètre « isVirtualNetworkFilterEnabled ».  

   ```powershell
   $locations = @(@{})
   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $subnetName  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Mettez à jour les propriétés du compte Azure Cosmos DB avec la nouvelle configuration en exécutant les applets de commande suivantes : 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource ``
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Exécutez la commande suivante pour vérifier que votre compte Azure Cosmos DB est mis à jour avec le point de terminaison de service de réseau virtuel que vous avez configuré à l’étape précédente :

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Ajouter un point de terminaison de service de réseau virtuel pour un compte Azure Cosmos DB qui a le pare-feu IP activé

1. Tout d’abord, désactivez l’accès du pare-feu IP au compte Azure Cosmos DB.  

2. Activez le point de terminaison de réseau virtuel pour le compte Azure Cosmos DB en appliquant l’une des méthodes décrites dans les sections précédentes.  

3. Réactivez l’accès du pare-feu IP. 

## <a name="test-the-access"></a>Tester l’accès

Pour vérifier si les points de terminaison de service pour Azure Cosmos DB sont configurés comme prévu, effectuez les étapes suivantes :

* Configurez deux sous-réseaux sur un réseau virtuel en tant que frontend et backend, puis activez le point de terminaison de service Cosmos DB pour le sous-réseau backend.  

* Activez l’accès dans le compte Cosmos DB pour le sous-réseau backend.  

* Créez deux machines virtuelles : une sur le sous-réseau backend et une autre sur le sous-réseau frontend.  

* Essayez d’accéder au compte Azure Cosmos DB à partir des deux machines virtuelles. Vous devez pouvoir vous connecter à partir de la machine virtuelle créée sur le sous-réseau backend, mais pas à partir de celle créée sur le sous-réseau frontend. La demande génère une erreur 404 quand vous tentez de vous connecter à partir du sous-réseau frontend, ce qui confirme que l’accès à Azure Cosmos DB est sécurisé à l’aide du point de terminaison de service de réseau virtuel. La machine sur le sous-réseau backend fonctionnera normalement.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Que se passe-t-il quand vous accédez à un compte Azure Cosmos DB qui a une liste de contrôle d’accès (ACL) réseau virtuel activée ?  

Une erreur HTTP 404 est retournée.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Les sous-réseaux d’un réseau virtuel créés dans des régions différentes sont-ils autorisés à accéder à un compte Azure Cosmos DB dans une autre région ? Par exemple, si un compte Azure Cosmos DB est dans la région États-Unis de l’Ouest ou États-Unis de l’Est et que les réseaux virtuels sont dans plusieurs régions, le réseau virtuel peut-il accéder à Azure Cosmos DB ?  

Oui, les réseaux virtuels créés dans des régions différentes peuvent accéder à l’aide de la nouvelle fonctionnalité. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Un compte Azure Cosmos DB peut-il avoir à la fois un point de terminaison de service de réseau virtuel et un pare-feu ?  

Oui, un point de terminaison de service de réseau virtuel et un pare-feu peuvent coexister. En règle générale, vous devez vérifier que l’accès au portail est toujours activé avant de configurer un point de terminaison de service de réseau virtuel pour vous permettre de voir les métriques associées au conteneur.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Puis-je « autoriser l’accès à d’autres services Azure à partir d’une région Azure donnée » quand l’accès au point de terminaison de service est activé pour Azure Cosmos DB ?  

Ceci est nécessaire uniquement quand vous souhaitez que votre compte Azure Cosmos DB soit accessible par d’autres services Azure internes tels qu’Azure Data Factory, Recherche Azure ou tout autre service déployé dans une région Azure donnée.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Quelle est la quantité de points de terminaison de service de réseau virtuel autorisée pour Azure Cosmos DB ?  

64 points de terminaison de service de réseau virtuel sont autorisés pour un compte Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Quelle est la relation entre le point de terminaison de service et les règles du groupe de sécurité réseau (NSG) ?  

Les règles du groupe de sécurité réseau dans Azure Cosmos DB vous permettent de restreindre l’accès à la plage d’adresses IP de Azure Cosmos DB. Si vous souhaitez autoriser l’accès à une instance de Azure Cosmos DB présente dans une [région](https://azure.microsoft.com/global-infrastructure/regions/) spécifique, vous pouvez spécifier la région au format suivant : 

    AzureCosmosDB.<region name>

Pour en savoir plus sur les balises NSG, consultez l’article [Balises de service de réseau virtuel](../virtual-network/security-overview.md#service-tags). 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Quelle est la relation entre un pare-feu IP et la fonctionnalité de point de terminaison de service de réseau virtuel ?  

Ces deux fonctionnalités se complètent afin de garantir l’isolation et la sécurisation des actifs Azure Cosmos DB. L’utilisation du pare-feu IP permet de s’assurer que les adresses IP statiques peuvent accéder au compte Azure Cosmos DB.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>L’adresse IP d’un appareil local connecté par le biais de la passerelle ExpressRoute ou de la passerelle Réseau virtuel Azure (VPN) peut-elle accéder au compte Azure Cosmos DB ?  

L’adresse IP ou plage d’adresses IP de l’appareil local doit être ajoutée à la liste des adresses IP statiques pour pouvoir accéder au compte Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Que se passe-t-il si l’on supprime un réseau virtuel qui a un point de terminaison de service configuré pour Azure Cosmos DB ?  

Quand un réseau virtuel est supprimé, les informations de liste ACL associées à cette base de données Azure Cosmos DB sont supprimées, ce qui élimine l’interaction entre le réseau virtuel et le compte Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Que se passe-t-il si l’on supprime un compte Azure Cosmos DB qui a un point de terminaison de service de réseau virtuel activé ?

Les métadonnées associées au compte Azure Cosmos DB en question sont supprimées du sous-réseau. Il incombe à l’utilisateur final de supprimer le sous-réseau et le réseau virtuel utilisés.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Puis-je utiliser un réseau virtuel appairé pour créer un point de terminaison de service pour Azure Cosmos DB ?  

Non, seuls un réseau virtuel direct et ses sous-réseaux peuvent créer des points de terminaison de service Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Qu’arrive-t-il à l’adresse IP source des ressources telles que les machines virtuelles sur le sous-réseau ayant le point de terminaison de service Azure Cosmos DB activé ?

Quand des points de terminaison de service de réseau virtuel sont activés, les adresses IP sources des ressources sur le sous-réseau de votre réseau virtuel n’utilisent plus des adresses IPV4 publiques mais des adresses privées Réseau virtuel Azure pour le trafic vers Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB réside-t-il sur le réseau virtuel Azure fourni par le client ?  

Azure Cosmos DB est un service multilocataire avec une adresse IP publique. Quand vous limitez l’accès à un sous-réseau d’un Réseau virtuel Azure à l’aide de la fonctionnalité de point de terminaison de service, l’accès est restreint pour votre compte Azure Cosmos DB par le biais du Réseau virtuel Azure donné et de son sous-réseau.  Le compte Azure Cosmos DB ne réside pas sur ce Réseau virtuel Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Que se passe-t-il si des événements sont enregistrés dans Log Analytics/OMS s’il est activé ?  

Azure Cosmos DB envoie les journaux avec l’adresse IP (sans le dernier octet) avec l’état 403 pour les requêtes bloquées par la liste ACL.  

## <a name="next-steps"></a>Étapes suivantes
Pour configurer un pare-feu pour Azure Cosmos DB, consultez l’article sur la [prise en charge du pare-feu](firewall-support.md).

