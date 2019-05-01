---
title: Exemples et définitions de journaux d’audit dans Azure Active Directory B2C | Microsoft Docs
description: Guide et exemples relatifs à l’accès aux journaux d’audit Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fdc3578fe78e60086a148e4204019a8293030b83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688763"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Accès aux journaux d’audit Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) produit des journaux d’audit contenant des informations sur les activités liées aux ressources, aux jetons émis et à l’accès administrateur B2C. Cet article fournit une rapide vue d’ensemble des informations disponibles par le biais des journaux d’audit, ainsi que des instructions pour accéder à ces données pour votre locataire Azure AD B2C.

> [!IMPORTANT]
> Les journaux d’audit sont uniquement conservés pendant sept jours. Envisagez de télécharger et stocker vos journaux d’activité en utilisant l’une des méthodes indiquées ci-dessous si vous avez besoin d’une période de rétention plus longue.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Vue d’ensemble des activités disponibles dans la catégorie B2C des journaux d’audit
La catégorie **B2C** des journaux d’audit contient les types d’activités suivants :

|Type d’activité |Description  |
|---------|---------|
|Authorization |Activités relatives à l’autorisation d’un utilisateur d’accéder à des ressources B2C (par exemple, un administrateur qui accède à la liste des stratégies B2C)         |
|Répertoire |Activités relatives aux attributs d’annuaire récupérés quand un administrateur se connecte à l’aide du portail Azure |
|Application | Opérations CRUD sur des applications B2C |
|Clé |Opérations CRUD sur les clés stockées dans le conteneur de clé B2C |
|Ressource |Opérations CRUD sur les ressources B2C (par exemple, stratégies et fournisseurs d’identité)
|Authentication |Validation des informations d’identification des utilisateurs et de l’émission des jetons|

> [!NOTE]
> Pour les activités CRUD des objets utilisateur, reportez-vous à la catégorie **Annuaire principal**.

## <a name="example-activity"></a>Exemple d’activité
L’exemple ci-dessous montre les données capturées quand un utilisateur se connecte avec un fournisseur d’identité externe : ![Journaux d’audit - exemple](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Accès aux journaux d’audit par le biais du portail Azure
1. Accédez au [portail Azure](https://portal.azure.com). Vérifiez que vous êtes dans votre annuaire B2C.
2. Cliquez sur **Azure Active Directory** dans la barre des favoris à gauche.
    
    ![Journaux d’audit - bouton AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Sous **Activité**, cliquez sur **Journaux d’audit**.

    ![Journaux d’audit - section Journaux d’activité](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Dans la liste déroulante **Catégorie**, sélectionnez **B2C**.
3. Cliquez sur **Appliquer**.

    ![Journaux d’audit - Catégorie](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

La liste des activités journalisées pendant les sept derniers jours apparaît.
- Utilisez la liste déroulante **Type de ressource d’activité** pour filtrer selon les types d’activité soulignés ci-dessus.
- Utilisez la liste déroulante **Plage de dates** pour filtrer la plage de dates des activités présentées.
- Si vous cliquez sur une ligne spécifique dans la liste, une zone contextuelle affiche sur la droite d’autres attributs associés à l’activité.
- Cliquez sur **Télécharger** pour télécharger les activités dans un fichier csv.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Accès aux journaux d’audit par le biais de l’API de création de rapports Azure AD
Les journaux d’audit sont publiés dans le même pipeline que les autres activités Azure Active Directory, afin d’être accessibles par le biais de l’[API de création de rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Conditions préalables
Pour vous authentifier auprès de l’API de création de rapports Azure AD, vous devez tout d’abord inscrire une application. Veillez à suivre les étapes indiquées dans [Prérequis pour accéder à l’API de création de rapports Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Accès à l’API
Pour télécharger les journaux d’audit Azure AD B2C par le biais de l’API, filtrez les journaux d’activité sur la catégorie **B2C**. Pour filtrer par catégorie, utilisez le paramètre de chaîne de requête lors de l’appel du point de terminaison de l’API de création de rapport Azure AD, comme indiqué ci-dessous :

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Script PowerShell
Le script suivant donne un exemple d’utilisation de PowerShell pour interroger l’API de création de rapports Azure AD et stocker les résultats sous forme de fichier JSON :

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
