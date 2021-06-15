---
title: Classifier vos données Azure SQL à l’aide d’étiquettes Azure Purview
description: Importer votre classification à partir d’Azure Purview dans votre instance Azure SQL Database et Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto, mathoma
ms.date: 02/17/2021
ms.openlocfilehash: d1064c6e60c8bdc80251228ddc2a36b5c3a77852
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110694808"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Classifier vos données Azure SQL à l’aide d’étiquettes Azure Purview
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Ce document explique comment ajouter des étiquettes Azure Purview dans votre instance Azure SQL Database et Azure Synapse Analytics (anciennement SQL DW).

## <a name="create-an-application"></a>Créer une application

1. À partir du portail Azure, ouvrez **Azure Active Directory**.
2. Sous **Gérer**, sélectionnez **Inscription d’application**.
3. Créez une application Azure Active Directory en sélectionnant **Nouvelle application**.
4. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
5. Une fois votre application créée, ouvrez **Certificats et secrets** sous **Gestionnaire**.
6. Créez un secret client en sélectionnant **Nouveau secret client** sous **Secrets client**.
7. Ajoutez une description à votre secret client, sélectionnez une période d’expiration, puis sélectionnez **Ajouter**.
8. Conservez la **valeur** pour une utilisation ultérieure.

   > [!NOTE]
   > Une fois que vous avez fermé la page, la valeur est masquée. Vous ne pourrez pas récupérer la valeur du secret client si vous revenez à la page. Vous devrez générer un nouveau secret client.

9. Revenez à la page Vue d’ensemble de l’application que vous venez de créer, puis copiez les valeurs suivantes pour une utilisation ultérieure :
    1. ID d’application (client)
    1. ID de l’annuaire (locataire)

## <a name="provide-permissions-to-the-application"></a>Fournir des autorisations à l'application

1. Sur votre portail Azure, lancez une recherche sur **Comptes Purview**.
2. Sélectionnez le compte Purview où sont classifiées vos bases de données SQL et Synapse.
3. Ouvrez **Contrôle d’accès (IAM)** , sélectionnez **Ajouter**.

4. Sélectionnez **Ajouter une attribution de rôle**.
5. Dans la section **Rôle**, lancez une recherche sur **Lecteur de données Purview**, puis sélectionnez-le.
6. Dans la section **Sélectionner**, recherchez l’application que vous avez créée précédemment, sélectionnez-la, puis cliquez sur **Enregistrer**.

## <a name="extract-the-classification-from-azure-purview"></a>Extraire la classification d’Azure Purview

1. Ouvrez votre compte Purview puis, dans la page d’accueil, recherchez votre instance Azure SQL Database ou Azure Synapse Analytics dans laquelle vous souhaitez copier les étiquettes.
2. Copiez le qualifiedName sous **Propriétés** et conservez-le pour une utilisation ultérieure.
3. Ouvrez votre interpréteur de commandes PowerShell.

4. Copiez l’un des scripts ci-dessous en fonction de votre type de ressource SQL (Azure SQL Database ou Azure Synapse).
5. Complétez les paramètres avec les valeurs que vous avez copiées ci-dessus :

   a. $TenantID : section 1, étape 9
   
   b. $ClientID : section 1, étape 9
   
   c. $SecretID : section 1, étape 8
   
   d. $purviewAccountName : section 2, étape 2
   
   e. $sqlDatabaseName : section 3, étape 2

6. Copiez la sortie du script pour une utilisation ultérieure.

### <a name="for-azure-sql-database"></a>Pour Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Pour Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Exécuter la commande T-SQL sur votre ressource SQL

1. Connectez-vous à votre instance Azure SQL Database ou Synapse Azure avec l’outil de votre choix.
2. Exécutez la commande T-SQL que vous avez copiée dans la section précédente.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Pour plus d’informations sur Azure Purview, consultez la [documentation Azure Purview](../../purview/index.yml).