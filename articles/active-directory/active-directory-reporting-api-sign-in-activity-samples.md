---
title: Exemples de l’API de création de rapports sur l’activité de connexion Azure Active Directory | Microsoft Docs
description: Prise en main de l'API de création de rapports Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 52d49770014a5fb6a5eec644868e702c8a8d9ef3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224896"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemples de l’API de création de rapports sur l’activité de connexion Azure Active Directory
Cet article fait partie d’un ensemble d’articles relatifs à l’API de création de rapports Azure Active Directory.  
La création de rapports Azure AD fournit une API qui vous permet d’accéder aux données de l’activité de connexion à l’aide de code ou d’outils associés.  
Cet article a pour but de vous fournir un exemple de code pour **l’API d’activité de connexion**.

Consultez l'article :

* [Journaux d’audit](active-directory-reporting-azure-portal.md#activity-reports) pour plus d’informations conceptuelles
* [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md) pour plus d’informations sur l’API de création de rapports.


## <a name="prerequisites"></a>Prérequis
Avant de pouvoir utiliser les exemples de cet article, vous devez respecter la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Script PowerShell

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
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




## <a name="executing-the-script"></a>Exécution du script
Une fois que vous avez terminé la modification du script, exécutez-le, puis vérifiez que les données attendues dans les journaux de connexion sont retournées.

Le script renvoie la sortie du rapport sur la connexion au format JSON. Il crée également un fichier `SignIns.json` avec la même sortie. Vous pouvez expérimenter en modifiant le script pour renvoyer des données à partir d’autres rapports, et également commenter les formats de sortie dont vous n’avez pas besoin.

## <a name="next-steps"></a>Étapes suivantes
* Vous souhaitez personnaliser les exemples de cet article ? Consultez la [référence d’API d’activité de connexion Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Si vous souhaitez obtenir une présentation complète de l’utilisation de l’API de création de rapports Azure Active Directory, consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si vous souhaitez en savoir plus sur la création de rapports Azure Active Directory, consultez le [Guide Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  

