---
title: Configurer Azure Private Link pour un compte Azure Cosmos
description: Découvrez comment configurer Azure Private Link pour accéder à un compte Azure Cosmos à l’aide d’une adresse IP privée dans un réseau virtuel.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 34b54459629560ba80e6a38d10edbab32ea44778
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820153"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurer Azure Private Link pour un compte Azure Cosmos (préversion)

À l’aide d’Azure Private Link, vous pouvez vous connecter à un compte Azure Cosmos via un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées dans un sous-réseau au sein de votre réseau virtuel. À l’aide de Liaison privée (Private Link), vous pouvez limiter l’accès à un compte Azure Cosmos donné sur des adresses IP privées. En cas de combinaison avec des stratégies NSG limitées, Liaison privée (Private Link) permet de réduire le risque d’exfiltration de données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](../private-link/private-link-overview.md).

En outre, Liaison privée (Private Link) permet d’accéder à un compte Azure Cosmos à partir du réseau virtuel ou de tout réseau virtuel appairé. Les ressources mappées à Liaison privée (Private Link) sont également accessibles localement via le peering privé par le biais de VPN ou d’ExpressRoute. 

Vous pouvez vous connecter à un compte Azure Cosmos configuré avec Liaison privée (Private Link) en utilisant les méthodes d’approbation « Automatique » ou « Manuel ». Pour plus d’informations, consultez la section sur le [flux de travail d’approbation](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) de la documentation Liaison privée (Private Link). Cet article décrit les étapes permettant de créer une Liaison privée (Private Link) en supposant que vous utilisez la méthode d’approbation Automatique.

## <a name="create-a-private-link-using-the-azure-portal"></a>Créer une Liaison privée (Private Link) au moyen du portail Azure

Pour créer une Liaison privée (Private Link) pour un compte Azure Cosmos existant à l’aide du portail Azure, procédez comme suit :

1. À partir du panneau **Toutes les ressources**, recherchez le compte Azure Cosmos DB que vous souhaitez sécuriser.

1. Sélectionnez **Connexion au point de terminaison privé** dans le menu Paramètres, puis sélectionnez l’option **Point de terminaison privé** :

   ![Créer un point de terminaison privé au moyen du portail Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Dans **Créer un point de terminaison privé (préversion) - Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez un groupe de ressources.|
    | **Détails de l’instance** |  |
    | Nom | Entrez un nom pour votre point de terminaison privé. Si ce nom est déjà utilisé, créez-en un qui soit unique. |
    |Région| Sélectionnez la région dans laquelle vous souhaitez déployer la Liaison privée (Private Link). Le point de terminaison privé doit être créé dans le même emplacement que celui où se trouve votre réseau virtuel.|
    |||
1. Sélectionnez **Suivant : Ressource**.
1. Dans **Créer un point de terminaison privé - Ressource**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez Se connecter à une ressource Azure dans mon répertoire. <br/><br/> Cette option vous permet de choisir l’une de vos ressources pour configurer une Liaison privée (Private Link) ou vous connecter à la ressource d’une autre personne avec un ID de ressource ou un alias qu’elle a partagé avec vous.|
    | Subscription| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Ressource |Sélectionnez votre compte Azure Cosmos |
    |Sous-ressource cible |Sélectionnez le type d’API Cosmos DB que vous souhaitez mapper. Par défaut, il n’y a qu’un seul choix pour les API SQL, MongoDB et Cassandra. Pour les API Gremlin et Table, vous pouvez également choisir *Sql*, car ces API sont interopérables avec l’API SQL. |
    |||

1. Sélectionnez **Suivant : Configuration**.
1. Dans **Créer un point de terminaison privé (préversion) - Configuration**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Mise en réseau**| |
    | Réseau virtuel| Sélectionnez votre réseau virtuel. |
    | Subnet | Sélectionnez votre sous-réseau. |
    |**Intégration à un DNS privé**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. <br><br/> Pour vous connecter en privé à votre point de terminaison privé, vous avez besoin d’un enregistrement DNS. Il est recommandé d’intégrer votre point de terminaison privé à une zone DNS privée. Vous pouvez également utiliser vos propres serveurs DNS, ou créer des enregistrements DNS à l’aide des fichiers hôtes sur vos machines virtuelles. |
    |Zone DNS privée |Sélectionnez *privatelink.documents.azure.com* <br><br/> La zone DNS privée est déterminée automatiquement et ne peut pas être modifiée actuellement par l’intermédiaire du portail Azure.|
    |||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

Si vous avez approuvé des Liaisons privées (Private Links) pour un compte Azure Cosmos, dans le portail Azure l’option **Tous les réseaux** dans le volet **Pare-feu et réseaux virtuels** est grisée.

Le tableau suivant montre le mappage entre les différents types d’API de compte Azure Cosmos, les sous-ressources prises en charge et les noms de zones privées correspondants. Les comptes Gremlin et API Table sont également accessibles via l’API SQL, il y a donc 2 entrées pour ces API :

|Type d’API de compte Azure Cosmos  |Sous-ressources prises en charge (ou groupIds) |Nom de zone privée  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Récupérer les adresses IP privées

Une fois le point de terminaison privé provisionné, vous pouvez interroger les adresses IP. Pour afficher les adresses IP à partir du portail Azure. Sélectionnez **Toutes les ressources**, recherchez le point de terminaison privé que vous avez créé précédemment, dans le cas présent il s’agit de « dbPrivateEndpoint3 » et sélectionnez l’onglet Vue d’ensemble pour afficher les paramètres DNS et les adresses IP :

![Adresses IP privées dans le portail Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Plusieurs adresses IP sont créées par point de terminaison privé :

* Une pour le point de terminaison global (indépendant de la région) du compte Azure Cosmos.
* Une pour chaque région dans laquelle le compte Azure Cosmos est déployé.

## <a name="create-a-private-link-using-azure-powershell"></a>Créer une Liaison privée (Private Link) au moyen d’Azure PowerShell

Exécutez le script PowerSehll suivant pour créer une Liaison privée (Private Link) nommée « MyPrivateEndpoint » pour un compte Azure Cosmos existant. Remplacez les valeurs des variables par les valeurs propres à votre environnement.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Intégrer le point de terminaison privé à une zone DNS privée

Après avoir créé le point de terminaison privé, vous pouvez l’intégrer à une zone DNS privée à l’aide du script PowerSehll suivant :

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Récupérer les adresses IP privées

Une fois le point de terminaison privé provisionné, vous pouvez interroger les adresses IP et le mappage FQDNS à l’aide du script PowerShell suivant :

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Créer une Liaison privée (Private Link) au moyen d’un modèle Resource Manager

Vous pouvez configurer une Liaison privée (Private Link) en créant un point de terminaison privé dans un sous-réseau de réseau virtuel. Pour ce faire, utilisez un modèle Azure Resource Manager. Créez un modèle Resource Manager nommé « PrivateEndpoint_template.json » avec le code suivant. Ce modèle crée un point de terminaison privé pour un compte d’API SQL Azure Cosmos existant dans un réseau virtuel existant :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

### <a name="define-the-template-parameters-file"></a>Définir le fichier des paramètres du modèle

Créez un fichier de paramètres pour le modèle, puis nommez-le « PrivateEndpoint_parameters.json ». Ajoutez le code suivant au fichier de paramètres :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Déployer le modèle à l’aide d’un script PowerShell

Créez ensuite un script PowerShell avec le code suivant. Avant d’exécuter le script, veillez à remplacer l’ID d’abonnement, le nom du groupe de ressources et d’autres valeurs de variables par les valeurs spécifiques à votre environnement :

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

Dans le script PowerShell, la variable « GroupId » ne peut contenir qu’une seule valeur, qui est le type d’API du compte. Les valeurs autorisées sont les suivantes : SQL, MongoDB, Cassandra, Gremlin et Table. Certains types de comptes Azure Cosmos sont accessibles par le biais de plusieurs API. Par exemple :

* Un compte d’API Gremlin est accessible à partir de comptes d’API Gremlin et SQL.
* Un compte d’API Table est accessible à partir de comptes d’API Table et SQL.

Pour ces comptes, vous devez créer un point de terminaison privé pour chaque type d’API, avec le type d’API correspondant spécifié dans le tableau « GroupId ».

Une fois que le modèle a bien été déployé, vous pouvez voir une sortie similaire à ce qui est illustré dans l’image suivante. La valeur provisioningState est « Succeeded » si les points de terminaison privés sont configurés correctement.

![Sortie du déploiement du modèle Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Une fois le modèle déployé, les adresses IP privées sont réservées dans le sous-réseau. La règle de pare-feu du compte Azure Cosmos est configurée pour accepter uniquement les connexions à partir du point de terminaison privé.

## <a name="configure-custom-dns"></a>Configurer un système DNS personnalisé

Vous devez utiliser un DNS privé au sein du sous-réseau où le point de terminaison privé a été créé. Et configurez les points de terminaison de sorte que chacune des adresses IP privées soit mappée à une entrée DNS (consultez la propriété « fqdns » dans la réponse affichée ci-dessus).

Lors de la création du point de terminaison privé, vous pouvez intégrer celui-ci à une zone DNS privée dans Azure. Si vous choisissez de ne pas intégrer votre point de terminaison privé à une zone DNS privée dans Azure, et d’utiliser à la place un DNS personnalisé, vous devez configurer votre DNS de telle sorte qu’il ajoute des enregistrements DNS pour toutes les adresses IP privées destinées au point de terminaison privé.

## <a name="firewall-configuration-with-private-link"></a>Configuration du pare-feu avec Liaison privée (Private Link)

Voici différentes situations et résultats lorsque vous utilisez Liaison privée (Private Link) en association avec des règles de pare-feu :

* Si aucune règle de pare-feu n’est configurée, alors par défaut, un compte Azure Cosmos est accessible par tout le trafic.

* Si le trafic public ou le point de terminaison de service est configuré et que des points de terminaison privés sont créés, alors différents types de trafic entrant sont autorisés par le type de règle de pare-feu correspondant.

* Si aucun trafic public ou point de terminaison de service n’est configuré et que des points de terminaison privés sont créés, alors le compte Azure Cosmos est uniquement accessible via les points de terminaison privés. Si aucun trafic public ou point de terminaison de service n’est configuré, après le rejet ou la suppression de tous les points de terminaison privés approuvés, le compte est ouvert sur tout le réseau.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Mettre à jour un point de terminaison privé lors de l’ajout ou de la suppression d’une région

Pour ajouter ou supprimer des régions dans un compte Azure Cosmos, vous devez ajouter ou supprimer des entrées DNS pour ce compte. Ces modifications doivent être mises à jour en conséquence dans le point de terminaison privé. Pour le moment, vous devez effectuer cette modification manuellement en procédant comme suit :

1. L’administrateur Azure Cosmos DB ajoute ou supprime des régions. Ensuite, les administrateurs réseau sont avertis des modifications en attente. Le point de terminaison privé mappé à un compte Azure Cosmos voit ses propriétés « ActionsRequired » modifiées de « Aucun » à « Recréer ». Ensuite, l’administrateur réseau met à jour le point de terminaison privé en émettant une requête PUT avec la même charge utile Resource Manager que celle utilisée pour le créer.

1. Après cette opération, le DNS privé du sous-réseau doit également être mis à jour pour refléter les entrées DNS ajoutées ou supprimées et leurs adresses IP privées correspondantes.

Par exemple, si vous déployez un compte Azure Cosmos dans 3 régions : « USA Ouest », « USA Centre » et « Europe Ouest ». Lorsque vous créez un point de terminaison privé pour votre compte, 4 adresses IP privées sont réservées dans le sous-réseau. Une pour chaque région, donc 3 au total, et une pour le point de terminaison global/indépendant de la région.

Plus tard, si vous ajoutez une nouvelle région, par exemple « USA Est » au compte Azure Cosmos. Par défaut, la nouvelle région n’est pas accessible à partir du point de terminaison privé existant. L’administrateur du compte Azure Cosmos doit actualiser la connexion au point de terminaison privé avant d’y accéder à partir de la nouvelle région. 

Lorsque vous exécutez la commande ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, la sortie de la commande contient le paramètre `actionsRequired`, qui est défini sur « Recréer ». Cette valeur indique que le point de terminaison privé doit être actualisé. Ensuite, l’administrateur du compte Azure Cosmos exécute la commande `Set-AzPrivateEndpoint` pour déclencher l’actualisation du point de terminaison privé.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Une nouvelle adresse IP privée est automatiquement réservée dans le sous-réseau sous ce point de terminaison privé et la valeur `actionsRequired` devient `None`. Si vous n’avez pas d’intégration de zone DNS privée (en d’autres termes, si vous utilisez un DNS privé personnalisé), vous devez configurer votre DNS privé pour ajouter un nouvel enregistrement DNS pour l’adresse IP privée correspondant à la nouvelle région.

Vous pouvez utiliser les mêmes étapes lorsque vous supprimez une région. L’adresse IP privée de la région supprimée est récupérée automatiquement et l’indicateur `actionsRequired` devient `None`. Si vous n’avez pas d’intégration de zone DNS privée, vous devez configurer votre DNS privé pour supprimer l’enregistrement DNS de la région supprimée.

Les enregistrements DNS dans la zone DNS privée ne sont pas supprimés automatiquement, lorsqu’un point de terminaison privé est supprimé ou qu’une région du compte Azure Cosmos est retirée. Vous devez supprimer manuellement les enregistrements DNS.

## <a name="current-limitations"></a>Limitations actuelles

Les limitations suivantes s’appliquent lorsque vous utilisez Liaison privée (Private Link) avec un compte Azure Cosmos :

* La prise en charge de Liaison privée (Private Link) pour les comptes Azure Cosmos et les réseaux virtuels est disponible uniquement dans des régions spécifiques. Pour obtenir la liste des régions prises en charge, reportez-vous à la section [Régions disponibles](../private-link/private-link-overview.md#availability) de l’article Liaison privée (Private Link). **Le réseau virtuel et le compte Azure Cosmos doivent se trouver dans les régions prises en charge pour pouvoir créer un point de terminaison privé**.

* Lorsque vous utilisez des Liaisons privées (Private Links) avec un compte Azure Cosmos à l’aide d’une connexion en mode direct, vous ne pouvez utiliser que le protocole TCP. Le protocole HTTP n’est pas encore pris en charge

* Lors de l’utilisation de l’API Azure Cosmos DB pour les comptes MongoDB, le point de terminaison privé est pris en charge pour les comptes sur le serveur version 3.6 uniquement (c’est-à-dire les comptes utilisant le point de terminaison au format `*.mongo.cosmos.azure.com`). La Liaison privée (Private Link) n’est pas prise en charge pour les comptes sur le serveur version 3.2 (c’est-à-dire les comptes utilisant le point de terminaison au format `*.documents.azure.com`). Pour utiliser Liaison privée (Private Link), vous devez migrer les anciens comptes vers la nouvelle version.

* Lors de l’utilisation de l’API Azure Cosmos DB pour les comptes MongoDB qui ont une Liaison privée (Private Link), vous ne pouvez pas utiliser d’outils tels que Robo 3T, Studio 3T, Mongoose, etc. Le point de terminaison peut prendre en charge la Liaison privée (Private Link) uniquement si le paramètre appName=<account name> est spécifié. Par exemple : replicaSet=globaldb&appName=mydbaccountname. Étant donné que ces outils ne transmettent pas le nom de l’application dans la chaîne de connexion au service, vous ne pouvez pas utiliser Liaison privée (Private Link). Toutefois, vous pouvez toujours accéder à ces comptes avec les pilotes SDK version 3.6.

* Un réseau virtuel ne peut pas être déplacé ou supprimé s’il contient Liaison privée (Private Link).

* Un compte Azure Cosmos ne peut pas être supprimé s’il est attaché à un point de terminaison privé.

* Un compte Azure Cosmos ne peut pas être basculé vers une région qui n’est pas mappée à tous les points de terminaison privés attachés au compte. Pour plus d’informations, consultez Ajout ou suppression de régions dans la section précédente.

* Un administrateur réseau doit disposer au moins de l’autorisation «*/PrivateEndpointConnectionsApproval » au niveau de l’étendue du compte Azure Cosmos par un administrateur pour créer des points de terminaison privés approuvés automatiquement.

### <a name="private-dns-zone-integration-limitations"></a>Limitations de l’intégration à une zone DNS privée

Les enregistrements DNS dans la zone DNS privée ne sont pas supprimés automatiquement, lorsqu’un point de terminaison privé est supprimé ou qu’une région du compte Azure Cosmos est retirée. Vous devez supprimer manuellement les enregistrements DNS avant d’effectuer les actions suivantes :

* Ajout d’un nouveau point de terminaison privé lié à cette zone DNS privée.
* Ajout d’une nouvelle région à un compte de base de données qui dispose de points de terminaison privés liés à cette zone DNS privée.

Sans le nettoyage des enregistrements DNS, des problèmes de plan de données inattendus peuvent se produire, tels que l’indisponibilité des données dans les régions ajoutées après la suppression du point de terminaison privé ou le retrait de la région.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres fonctionnalités de sécurité d’Azure Cosmos DB, consultez l’article suivant :

* Pour configurer un pare-feu pour Azure Cosmos DB, consultez [Prise en charge du pare-feu](firewall-support.md).

* [Configurer un point de terminaison de service de réseau virtuel pour votre compte Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Pour en savoir plus sur Liaison privée (Private Link), consultez la documentation [Azure Private Link](../private-link/private-link-overview.md).
