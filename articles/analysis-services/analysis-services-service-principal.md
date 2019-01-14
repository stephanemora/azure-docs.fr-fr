---
title: Automatiser les tâches Azure Analysis Services à l’aide de principaux de service | Microsoft Docs
description: Découvrez comment créer des principaux de service pour automatiser les tâches Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 47800ce467beb43c514e5e5474247d8c2029feff
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188230"
---
# <a name="automation-with-service-principals"></a>Automatisation à l’aide de principaux de service

Les principaux de service sont des ressources d’application Azure Active Directory que vous créez à l’intérieur de votre locataire pour effectuer des opérations sans assistance au niveau du service et des ressources. Il s’agit d’un type unique *d’identité utilisateur*, qui est constitué d’un ID d’application, et d’un mot de passe ou d’un certificat. Seules ces autorisations sont nécessaires pour qu’un principal de service effectue les tâches définies par les rôles et les autorisations attribués. 

Dans Analysis Services, les principaux de service sont utilisés avec Azure Automation, avec le mode sans assistance de PowerShell, avec des applications clientes personnalisées et avec des applications web, dans le but d’automatiser les tâches courantes. Par exemple, le provisionnement des serveurs, le déploiement des modèles, l’actualisation des données, la mise à l’échelle et les opérations de suspension/reprise peuvent être automatisés à l’aide de principaux de service. Les autorisations sont attribuées aux principaux de service via l’appartenance au rôle, comme les comptes UPN standard d’Azure AD.

## <a name="create-service-principals"></a>Créer des principaux de service
 
Vous pouvez créer des principaux de service dans le portail Azure ou avec PowerShell. Pour plus d'informations, consultez les rubriques suivantes :

[Créer un principal de service - Portail Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Créer un principal de service - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Stocker des ressources d’informations d’identification et de certificats dans Azure Automation

Les informations d’identification et les certificats des principaux de service peuvent être stockés de manière sécurisée dans Azure Automation pour les opérations de runbook. Pour plus d'informations, consultez les rubriques suivantes :

[Ressources d’informations d’identification dans Azure Automation](../automation/automation-credentials.md)   
[Ressources de certificats dans Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Ajouter des principaux de service au rôle d’administrateur de serveur

Avant de pouvoir utiliser un principal de service pour les opérations de gestion d’un serveur Analysis Services, vous devez l’ajouter au rôle Administrateurs de serveur. Pour plus d’informations, consultez [Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)

## <a name="service-principals-in-connection-strings"></a>Principaux de service dans les chaînes de connexion

Vous pouvez utiliser l’ID d’application et le mot de passe ou le certificat d’un principal de service dans les chaînes de connexion, de la même manière que pour un compte UPN.

### <a name="powershell"></a>PowerShell

Si vous utilisez un principal de service pour les opérations de gestion des ressources avec le module [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices), utilisez l’applet de commande `Login-AzureRmAccount`. Si vous utilisez un principal de service pour les opérations de serveur avec le module [SQLServer](https://www.powershellgallery.com/packages/SqlServer), utilisez l’applet de commande `Add-AzureAnalysisServicesAccount`. 

Dans l’exemple suivant, un ID d’application et un mot de passe sont utilisés pour effectuer une opération d’actualisation d’une base de données model :

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO et ADOMD 

Lorsque vous vous connectez à une application cliente ou à une application web, les packages installables des [bibliothèques clientes AMO et ADOMD](analysis-services-data-providers.md) version 15.0.2 (et ultérieures) issus de NuGet prennent en charge l’utilisation des principaux de service dans les chaînes de connexion à l’aide de la syntaxe suivante : `app:AppID`, puis le mot de passe ou `cert:thumbprint`. 

Dans l’exemple suivant, `appID` et `password` sont utilisés pour effectuer une opération d’actualisation d’une base de données model :

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Étapes suivantes
[Se connecter avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)   