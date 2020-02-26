---
title: Comment configurer des identités managées pour le cluster Azure Data Explorer
description: Découvrez comment configurer des identités managées pour le cluster Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373375"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configurer des identités managées pour votre cluster Azure Data Explorer

Une [identité managée issue d’Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) permet à votre cluster d’accéder facilement aux autres ressources protégées par AAD telles qu’Azure Key Vault. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Cet article explique comment créer une identité managée pour des clusters Azure Data Explorer. La configuration des identités managées est actuellement prise en charge uniquement pour [activer les clés gérées par le client pour votre cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Les identités managées pour Azure Data Explorer ne se comportent pas comme prévu si votre application est migrée entre des abonnements ou des locataires. L’application devra obtenir une nouvelle identité, ce qui peut être effectué par la désactivation et la réactivation de la caractéristique au moyen de [supprimer une identité](#remove-an-identity). Les stratégies d’accès des ressources en aval devront également être mises à jour pour utiliser la nouvelle identité.

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

Vous pouvez affecter à votre cluster une **identité attribuée par le système** qui est liée à votre cluster et qui est supprimée si votre cluster est supprimé. Un cluster ne peut avoir qu’une seule identité attribuée par le système. Créer un cluster avec une identité attribuée par le système requiert la définition d’une propriété supplémentaire sur le cluster.

### <a name="add-a-system-assigned-identity-using-c"></a>Ajouter une identité affectée par le système à l’aide du langage C#

Pour configurer une identité managée à l’aide du client C# Azure Data Explorer, procédez comme suit :

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.
* Pour exécuter l’exemple suivant, [créez une application Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) et un principal de service permettant d’accéder aux ressources. Vous pouvez ajouter l’attribution de rôle à l’étendue de l’abonnement et récupérer les `Directory (tenant) ID`, `Application ID` et `Client Secret` requis.

#### <a name="create-or-update-your-cluster"></a>Créer ou mettre à jour votre cluster

1. Créez ou mettez à jour votre cluster à l’aide de la propriété `Identity` :

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Exécutez la commande suivante pour vérifier si votre cluster a été correctement créé ou mis à jour avec une identité :

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Si le résultat contient `ProvisioningState` avec la valeur `Succeeded`, le cluster a été créé ou mis à jour et doit avoir les propriétés suivantes :
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` et `TenantId` sont remplacés par des GUID. La propriété `TenantId` identifie le locataire AAD auquel appartient l’identité. Le `PrincipalId` est un identificateur unique pour la nouvelle identité du cluster. Dans AAD, le principal de service porte le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Ajouter une identité affectée par le système à l’aide d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur Azure Data Explorer, consultez [Créer un cluster Azure Data Explorer et une base de données à l’aide d’un modèle Azure Resource Manager](create-cluster-database-resource-manager.md).

L’ajout du type attribué par le système indique à Azure de créer et de manager l’identité de votre cluster. Vous pouvez créer n’importe quelle ressource de type `Microsoft.Kusto/clusters` avec une identité en incluant la propriété suivante dans la définition de la ressource : 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Par exemple :

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Quand le cluster est créé, il a les propriétés supplémentaires suivantes :

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` et `<PRINCIPALID>` sont remplacés par des GUID. La propriété `TenantId` identifie le locataire AAD auquel appartient l’identité. Le `PrincipalId` est un identificateur unique pour la nouvelle identité du cluster. Dans AAD, le principal de service porte le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

## <a name="remove-an-identity"></a>Supprimer une identité

Si vous supprimez une identité attribuée par le système, vous la supprimez également d’AAD. Les identités attribuées par le système sont aussi automatiquement supprimées d’AAD lorsque la ressource de cluster est supprimée. Une identité affectée par le système peut être supprimée en désactivant la fonctionnalité :

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Sécuriser votre cluster dans Azure Data Explorer – Portail Azure](manage-cluster-security.md) en activant le chiffrement au repos.
 * [Configurer des clés gérées par le client à l’aide de C#](customer-managed-keys-csharp.md)
 * [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
