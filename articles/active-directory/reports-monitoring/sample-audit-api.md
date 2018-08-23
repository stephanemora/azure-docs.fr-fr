---
title: Exemples d’API d’audit de création de rapports Azure Active Directory | Microsoft Docs
description: Prise en main de l'API de création de rapports Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 345daaa8ac991cad9f098b317a9b59d52de73ead
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "41918327"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Exemples d’API d’audit de création de rapports Azure Active Directory
Cet article fait partie d’un ensemble d’articles relatifs à l’API de création de rapports Azure Active Directory.  
La création de rapports Azure AD fournit une API qui vous permet d’accéder aux données d’audit à l’aide de code ou d’outils associés.
Cet article a pour but de vous fournir un exemple de code pour **l’API d’audit**.

Consultez l'article :

* [Journaux d’audit](overview-reports.md#activity-reports) pour plus d’informations conceptuelles
* [Prise en main de l’API de création de rapports Azure Active Directory](concept-reporting-api.md) pour plus d’informations sur l’API de création de rapports.

Si vous avez des questions, des problèmes ou des commentaires, veuillez contacter [Aide à la création de rapports AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Prérequis
Avant de pouvoir utiliser les exemples de cet article, vous devez respecter la [configuration requise pour accéder à l’API de création de rapports Azure AD](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="known-issue"></a>Problème connu
L’authentification de l’application ne fonctionne pas si votre client se trouve dans la région de l’Union européenne. Utilisez l’authentification de l’utilisateur pour accéder à l’API d’audit comme solution de contournement jusqu'à ce que ce problème soit résolu. 

## <a name="powershell-script"></a>Script PowerShell


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>Exécution du script PowerShell
Une fois que vous avez terminé la modification du script, exécutez-le, puis vérifiez que les données attendues dans les journaux d’audit sont retournées.

Le script renvoie la sortie du rapport d’audit au format JSON. Il crée également un fichier `Audits.json` avec la même sortie. Vous pouvez expérimenter en modifiant le script pour renvoyer des données à partir d’autres rapports, et également commenter les formats de sortie dont vous n’avez pas besoin.





## <a name="next-steps"></a>Étapes suivantes
* Vous souhaitez personnaliser les exemples de cet article ? Consultez la [référence d’API d’audit Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Si vous souhaitez obtenir une présentation complète de l’utilisation de l’API de création de rapports Azure Active Directory, consultez [Prise en main de l’API de création de rapports Azure Active Directory](concept-reporting-api.md).
* Si vous souhaitez en savoir plus sur la création de rapports Azure Active Directory, consultez le [Guide Azure Active Directory Reporting Guide](overview-reports.md).  

