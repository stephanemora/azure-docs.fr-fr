---
title: Identité gérée pour Data Factory | Microsoft Docs
description: En savoir plus sur l’identité gérée pour Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153421"
---
# <a name="managed-identity-for-data-factory"></a>Identité managée pour Data Factory

Cet article vous aide à comprendre ce qui est l’identité gérée pour Data Factory (anciennement connu en tant que Managed Service Identity/MSI) et son fonctionnement.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Présentation

Lorsque vous créez une fabrique de données, une identité gérée peut être créée en même temps que la création de fabrique. L’identité gérée est une application managée inscrite à Azure Active Directory et représentant la fabrique de données spécifique.

Une identité gérée pour Data Factory avantages pour les fonctionnalités suivantes :

- [Store d’informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md), auquel cas l’identité gérée de fabrique de données est utilisée pour l’authentification Azure Key Vault.
- Connecteurs, notamment [Stockage Blob Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) et [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Activité web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Générer l’identité gérée

Une identité gérée pour Data Factory est générée comme suit :

- Lors de la création de fabrique de données grâce au **portail Azure ou PowerShell**managé identité est toujours créée automatiquement.
- Lors de la création de fabrique de données à l’aide **SDK**managé identité sera créée uniquement si vous spécifiez « Identity = new FactoryIdentity() » dans l’objet de fabrique pour la création. Consultez l’exemple dans [Démarrage rapide .NET - Créer une fabrique de données](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Lors de la création de fabrique de données grâce au **API REST**managé identité sera créée uniquement si vous spécifiez la section « identity » dans le corps de la demande. Consultez l’exemple dans [Démarrage rapide REST - Créer une fabrique de données](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si vous trouvez votre fabrique de données n’a pas une identité gérée associée suivant [récupérer l’identité gérée](#retrieve-managed-identity) obtenir des instructions, vous pouvez générer explicitement une en mettant à jour la fabrique de données avec l’initiateur d’identité par programmation :

- [Générer une identité gérée à l’aide de PowerShell](#generate-managed-identity-using-powershell)
- [Générer une identité gérée à l’aide de l’API REST](#generate-managed-identity-using-rest-api)
- [Générer une identité gérée à l’aide d’un modèle Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Générer une identité gérée à l’aide du Kit de développement logiciel](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Identité gérée ne peut pas être modifiée. La mise à jour une fabrique de données qui dispose déjà d’une identité gérée n’a aucun effet, l’identité gérée reste inchangée.
>- Si vous mettez à jour une fabrique de données qui dispose déjà d’une identité gérée sans spécifier de paramètre « identity » dans l’objet de fabrique ou sans spécifier la section « identity » dans le corps de la demande REST, vous obtiendrez une erreur.
>- Lorsque vous supprimez une fabrique de données, l’identité gérée associée sera également supprimée.

### <a name="generate-managed-identity-using-powershell"></a>Générer une identité gérée à l’aide de PowerShell

Appelez **Set-AzDataFactoryV2** commande là encore, vous voyez les champs « Identity » qui vient d’être généré :

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Générer une identité gérée à l’aide de l’API REST

Appelez ensuite l’API avec la section« identity » dans le corps de la requête :

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corps de la requête** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse**: identité gérée est créée automatiquement et la section « identity » est remplie en conséquence.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Générer une identité gérée à l’aide d’un modèle Azure Resource Manager

**Modèle** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Générer une identité gérée à l’aide du Kit de développement logiciel

Appelez la fabrique de données pour créer ou mettre à jour la fonction avec Identity=new FactoryIdentity(). Exemple de code utilisant NET :

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Récupérer l’identité gérée

Vous pouvez récupérer l’identité gérée à partir du portail Azure ou par programmation. Les sections suivantes vous montrent quelques exemples.

>[!TIP]
> Si vous ne voyez pas l’identité gérée, [générer l’identité gérée](#generate-managed-identity) en mettant à jour de votre fabrique.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Récupérer l’identité gérée à l’aide du portail Azure

Vous pouvez trouver les informations d’identité gérée à partir du portail Azure -> votre fabrique de données -> Propriétés :

- ID objet de l'identité managée
- Locataire de l'identité managée
- **ID d’Application identité géré** > Copiez cette valeur

![Récupérer l’identité gérée](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Récupérer l’identité gérée à l’aide de PowerShell

L’identité gérée ID principal et l’ID de client s’affichera lorsque vous recevez une fabrique de données spécifique, comme suit :

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiez l’ID principal, puis exécutez la commande Azure Active Directory ci-dessous avec l’ID principal comme paramètre pour obtenir **l’ID d’application**, que vous allez utiliser pour obtenir l’accès :

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques suivantes qui expliquent quand et comment utiliser data factory gérés d’identité :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md)
- [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md)

Consultez [identités gérées pour la vue d’ensemble de ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour plus d’informations sur les identités pour les ressources Azure, identité gérée de la fabrique de données est basé. 
