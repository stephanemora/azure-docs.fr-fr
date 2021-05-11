---
title: Activer une identité managée pour votre compte Azure Automation (préversion)
description: Cet article explique comment configurer une identité managée pour les comptes Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1bdba095c18943be5b367bd605d1a22d6eb6499f
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108323666"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Activer une identité managée pour votre compte Azure Automation (préversion)

Cette rubrique vous montre comment créer une identité managée pour un compte Azure Automation et comment l’utiliser pour accéder à d’autres ressources. Pour plus d’informations sur le fonctionnement d’une identité managée avec Azure Automation, consultez [Identités managées](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. L’identité managée et les ressources Azure cibles que votre runbook gère à l’aide de cette identité doivent se trouver dans le même abonnement Azure.

- La dernière version des modules de compte Azure. Il s'agit actuellement de la version 2.2.8. (Pour plus d'informations sur cette version, consultez [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)).

- Une ressource Azure à laquelle vous souhaitez accéder à partir de votre runbook Automation. Cette ressource doit avoir un rôle défini pour l’identité managée, ce qui aide le runbook Automation à authentifier l’accès à la ressource. Pour ajouter des rôles, vous devez être propriétaire de la ressource dans le locataire Azure AD correspondant.

- Si vous souhaitez exécuter des tâches hybrides à l’aide d’une identité managée, mettez à jour le runbook worker hybride vers la dernière version. Les versions minimales requises sont :

   - Runbook worker hybride Windows : version 7.3.1125.0
   - Runbook worker hybride Linux : version 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Activer l’identité attribuée par le système

>[!IMPORTANT]
>La nouvelle identité au niveau du compte Automation remplacera toutes les identités attribuées par le système au niveau de la machine virtuelle précédentes, et décrites dans [Utiliser l’authentification du runbook avec les identités managées](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities). Si vous exécutez des tâches hybrides sur des machines virtuelles Azure qui utilisent l’identité affectée par le système d’une machine virtuelle pour accéder aux ressources du runbook, l’identité du compte Automation sera utilisée pour les tâches hybrides. Cela signifie que l’exécution de votre tâche existante peut être affectée si vous avez utilisé la fonctionnalité de clés gérées par le client (CMK) de votre compte Automation.<br/><br/>Si vous souhaitez continuer à utiliser l’identité managée de la machine virtuelle, vous ne devez pas activer l’identité au niveau du compte Automation. Si vous l’avez déjà activée, vous pouvez désactiver l’identité managée du compte Automation. Consultez [Désactiver votre identité managée de compte Azure Automation](./disable-managed-identity-for-automation.md).

La configuration des identités affectées par le système pour Azure Automation peut être effectuée de deux manières. Vous pouvez utiliser le portail Azure ou l’API REST Azure.

>[!NOTE]
>Les identités affectées par l’utilisateur ne sont pas encore prises en charge.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Activer une identité attribuée par le système sur le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation et sélectionnez **Identité** sous **Paramètres du compte**.

1. Affectez la valeur **On** (Activé) à l’option **Affecté(e) par le système**, puis appuyez sur **Enregistrer**. Lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Activation d’une identité affectée par le système dans le portail Azure.":::

Votre compte Automation peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD (Azure Active Directory) et représentée par un ID d’objet.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="ID d’objet de l’identité managée.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Activer l’identité affectée par le système par le biais de l’API REST

Vous pouvez configurer une identité managée affectée par le système pour le compte Automation à l’aide de l’appel d’API REST suivant.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corps de la demande
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Propriété (JSON) | Valeur | Description|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Identificateur global unique (GUID) de l’objet du principal de service pour l’identité managée qui représente votre compte Automation dans le locataire Azure AD. Ce GUID apparaît parfois sous la forme d’un « ID d’objet » ou objectID. |
| tenantid | \<Azure-AD-tenant-ID\> | Identificateur global unique (GUID) qui représente le locataire Azure AD dont le compte Automation est maintenant membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que le compte Automation. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Accorder à l’identité un accès aux ressources Azure en obtenant un jeton

Un compte Automation peut utiliser son identité managée pour obtenir des jetons afin d’accéder à d’autres ressources protégées par Azure AD, comme Azure Key Vault. Ces jetons ne représentent pas un utilisateur spécifique de l’application. Au lieu de cela, ils représentent l’application qui accède à la ressource. Dans ce cas, par exemple, le jeton représente un compte Automation.

Avant de pouvoir vous servir de l’identité managée affectée par le système dans le cadre de l’authentification, configurez l’accès de cette identité à la ressource Azure où vous prévoyez de l’utiliser. Pour effectuer cette tâche, attribuez le rôle approprié à cette identité sur la ressource Azure cible.

Cet exemple utilise Azure PowerShell pour montrer comment attribuer le rôle Contributeur dans l’abonnement à la ressource Azure cible. Le rôle Contributeur est utilisé à titre d’exemple et peut ne pas être requis dans votre cas.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Authentifier l’accès avec des identités managées

Après avoir activé l’identité managée pour votre compte Automation et accordé à une identité l’accès à la ressource cible, vous pouvez spécifier cette identité dans les runbooks pour les ressources qui prennent en charge l’identité managée. Pour la prise en charge des identités, utilisez l’applet de commande `Connect-AzAccount` de l’applet de commande Az. Consultez [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) dans les informations de référence sur PowerShell.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Si votre organisation utilise toujours les applets de commande AzureRM dépréciées, vous pouvez utiliser `Connect-AzureRMAccount -Identity`.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Générer un jeton d’accès sans utiliser les applets de commande Azure

Pour les points de terminaison HTTP, vérifiez les points suivants.
- L’en-tête de métadonnées doit être présent et avoir la valeur « true ».
- Une ressource doit être passée avec la demande, en tant que paramètre de requête pour une demande GET et en tant que données de formulaire pour une demande POST.
- L’en-tête X-IDENTITY-HEADER doit être défini sur la valeur de la variable d’environnement IDENTITY_HEADER pour les runbooks workers hybrides. 
- Le type de contenu de la demande Post doit être « application/x-www-form-urlencoded ». 

### <a name="sample-get-request"></a>Exemple de demande GET

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Exemple de demande POST
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Exemples de runbooks utilisant une identité managée

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Exemple de runbook pour accéder à une base de données SQL sans utiliser les applets de commande Azure

Assurez-vous d'avoir activé une identité avant d'essayer ce script. Consultez [Activer l'identité attribuée par le système](#enable-system-assigned-identity).

Pour plus d'informations sur l'approvisionnement de l'accès à une base de données Azure SQL, consultez [Approvisionner un administrateur Azure AD (SQL Database)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Exemple de runbook pour accéder à un coffre de clés à l’aide d’applets de commande Azure

Assurez-vous d'avoir activé une identité avant d'essayer ce script. Consultez [Activer l'identité attribuée par le système](#enable-system-assigned-identity).

Pour plus d'informations, consultez [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret).

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Exemple de runbook Python pour obtenir un jeton

Assurez-vous d'avoir activé une identité avant d'essayer ce runbook. Consultez [Activer l'identité attribuée par le système](#enable-system-assigned-identity).

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Étapes suivantes

- Si l’exécution de vos runbooks ne se termine pas correctement, consultez [Résoudre les problèmes d’identité managée Azure Automation (préversion)](troubleshoot/managed-identity.md).

- Si vous devez désactiver une identité managée, consultez [Désactiver votre identité managée de compte Azure Automation (préversion)](disable-managed-identity-for-automation.md).

- Pour obtenir une vue d’ensemble de la sécurité du compte Azure Automation, consultez [Vue d’ensemble de l’authentification du compte Automation](automation-security-overview.md).
