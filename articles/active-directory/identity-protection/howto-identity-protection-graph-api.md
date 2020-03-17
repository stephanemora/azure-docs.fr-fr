---
title: API Microsoft Graph pour Azure Active Directory Identity Protection
description: Découvrez comment interroger Microsoft Graph pour obtenir les détections de risques et les informations associées à partir d’Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671619"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph

Microsoft Graph est le point de terminaison d’API unifiée de Microsoft et accueille également les API [d’Azure Active Directory Identity Protection](../active-directory-identityprotection.md). Il existe quatre API qui exposent des informations sur les utilisateurs à risque et les connexions. La première API, **riskDetection**, vous permet d’interroger Microsoft Graph pour obtenir la liste des détections de risque liées à l’utilisateur et à la connexion, ainsi que des informations associées sur la détection. La deuxième API, **riskyUsers**, vous permet d’interroger Microsoft Graph pour obtenir des informations sur les utilisateurs que le service Identity Protection a détecté comme étant à risque. La troisième API, **signIn**, vous permet d’interroger Microsoft Graph pour obtenir des informations sur des connexions Azure AD avec des propriétés spécifiques relatives au risque en termes de niveau, de détails et d’état. La quatrième API, **identityRiskEvents**, vous permet d’interroger Microsoft Graph pour obtenir une liste [des détections de risques](../reports-monitoring/concept-risk-events.md) et des informations associées. L’API identityRiskEvents sera déconseillée le 10 janvier 2020 ; nous vous conseillons d’utiliser l’API **riskDetections** à la place. Cet article vous permet de vous familiariser avec la connexion à Microsoft Graph et l'interrogation de ces API. Pour obtenir des informations détaillées ainsi qu’un accès à l’Explorateur Graph, consultez le [site de Microsoft Graph](https://graph.microsoft.io/) ou la documentation de référence propre à ces API :

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API signIn](/graph/api/resources/signin?view=graph-rest-beta)
* L’[API identityRiskEvents](/graph/api/resources/identityriskevent?view=graph-rest-beta) *est déconseillée depuis le 10 janvier 2020*

## <a name="connect-to-microsoft-graph"></a>Se connecter à Microsoft Graph

L’accès aux données d’Identity Protection par le biais de Microsoft Graph se fait en quatre étapes :

1. Récupérer votre nom de domaine.
2. Créer une inscription d’application 
3. Utilisez cette clé secrète et d’autres éléments d’information pour vous authentifier auprès de Microsoft Graph ; ce dernier vous transmettra un jeton d’authentification. 
4. Utilisez ce jeton pour faire des demandes auprès du point de terminaison d’API et récupérer des données d’Identity Protection.

Avant de commencer, vous aurez besoin des éléments suivants :

* Des privilèges d’administrateur pour créer l’application dans Azure AD
* Le nom de domaine de votre client, par exemple contoso.onmicrosoft.com.

## <a name="retrieve-your-domain-name"></a>Récupérer votre nom de domaine 

1. [Connectez-vous](https://portal.azure.com) à votre portail Azure en tant qu’administrateur. 
1. Dans le volet de navigation gauche, cliquez sur **Active Directory**. 

   ![Création d’une application](./media/howto-identity-protection-graph-api/41.png)

1. Dans la section **Gérer**, cliquez sur **Propriétés**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/42.png)

1. Copiez votre nom de domaine.

## <a name="create-a-new-app-registration"></a>Créer une nouvelle inscription d’application

1. Dans la page **Active Directory**, dans la section **Gérer**, cliquez sur **Inscriptions des applications**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/42.png)

1. Dans le menu du haut, cliquez sur **Nouvelle inscription d’application**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/43.png)

1. Dans la page **Créer**, effectuez les étapes suivantes :

   ![Création d’une application](./media/howto-identity-protection-graph-api/44.png)

   1. Dans la zone de texte **Nom**, entrez le nom de votre application (par exemple : Application API Détection des risques Azure AD).

   1. Pour **Type**, sélectionnez **Application web et/ou API web**.

   1. Dans la zone de texte **URL d’authentification**, tapez `http://localhost`.

   1. Cliquez sur **Créer**.
1. Pour ouvrir la page **Paramètres**, dans la liste des applications, cliquez sur l’inscription d’application que vous venez de créer. 
1. Copiez **l’ID de l’application**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Autorisation d'utilisation de l'API pour votre application

1. Dans la page **Paramètres**, cliquez sur **Autorisations requises**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/15.png)

1. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/16.png)

1. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner une API**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/17.png)

1. Dans la page **Sélectionner une API**, sélectionnez **Microsoft Graph**, puis cliquez sur **Sélectionner**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/18.png)

1. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner des autorisations**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/19.png)

1. Dans la page **Activer l’accès**, cliquez sur **Lire toutes les informations d’événement de risque pour l’identité**, puis cliquez sur **Sélectionner**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/20.png)

1. Dans la page **Ajouter un accès d’API**, cliquez sur **Terminé**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/21.png)

1. Dans la page **Autorisations requises**, cliquez sur **Accorder les autorisations**, puis cliquez sur **Oui**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Obtenir une clé d’accès

1. Dans la page **Paramètres**, cliquez sur **Clés**.

   ![Création d’une application](./media/howto-identity-protection-graph-api/23.png)

1. Dans la page **Clés**, effectuez les étapes suivantes :

   ![Création d’une application](./media/howto-identity-protection-graph-api/24.png)

   1. Dans la zone de texte **Description de la clé**, tapez une description (par exemple, *Détection des risques Azure AD*).
   1. Pour **Durée**, sélectionnez **Dans 1 an**.
   1. Cliquez sur **Enregistrer**.
   1. Copiez la valeur de la clé, puis collez-la dans un emplacement sûr.   
   
   > [!NOTE]
   > Si vous perdez cette clé, vous devrez revenir à cette section et créer une nouvelle clé. Gardez cette clé secrète : toute personne la possédant peut accéder à vos données.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Authentifiez-vous auprès de Microsoft Graph et interrogez l’API de détections de risques concernant l’identité

À ce stade, vous devez avoir :

- le nom de domaine de votre client ;
- L’ID client 
- La clé 

Pour l’authentification, envoyez une demande POST à `https://login.microsoft.com` , avec les paramètres suivants dans le corps :

- grant_type : « **client_informationsidentification** »
- resource : `https://graph.microsoft.com`
- client_id : \<votre ID client\>
- client_secret : \<votre clé\>

Si l’opération réussit, elle retourne un jeton d’authentification.  
Pour appeler l’API, créez un en-tête avec le paramètre suivant :

```
`Authorization`="<token_type> <access_token>"
```

Lors de l’authentification, le jeton retourné contient le type de jeton ainsi que le jeton d’accès.

Envoyez cet en-tête en tant que requête à l’URL de l’API suivante : `https://graph.microsoft.com/beta/identityRiskEvents`

La réponse, en cas de réussite, consiste en une collection de détections de risques concernant l’identité ainsi que les données associées au format JSON OData, qui peuvent être analysées et gérées selon vos besoins.

Voici un exemple de code pour l’authentification et l’appel de l’API par le biais de PowerShell.  
Il suffit d’ajouter l’ID client, la clé secrète, ainsi que le domaine du locataire.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Interroger les API

Ces trois API fournissent une multitude d’opportunités de récupérer des informations sur les connexions et les utilisateurs à risque dans votre organisation. Voici quelques cas d’utilisation habituelle de ces API avec les exemples de requête associés. Vous pouvez exécuter ces requêtes à l’aide de l’exemple de code ci-dessus, ou par le biais de l’[Explorateur Graph](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Récupérez toutes les détections de risque hors connexion (API riskDetection)

Avec les stratégies de risque de connexion d’Identity Protection, vous pouvez appliquer des conditions lorsque le risque est détecté en temps réel. Mais qu’en est-il des détections qui sont découvertes hors connexion ? Pour comprendre quelles détections ont eu lieu hors ligne et qui donc n'auraient pas déclenché la stratégie de risque de connexion, vous pouvez interroger l'API riskDetection.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obtenir tous les utilisateurs qui ont réussi l’authentification MFA déclenchée par la stratégie de connexion risquée (API riskyUsers)

Pour comprendre l’impact que les stratégies Identity Protection reposant sur les risques ont sur votre organisation, vous pouvez interroger tous les utilisateurs qui ont réussi une authentification MFA déclenchée par une stratégie de connexion à risque. Ces informations peuvent vous aider à comprendre quels utilisateurs Identity Protection ont peut-être été détectés, à tort, comme présentant des risques, et lesquels, parmi vos utilisateurs légitimes, peuvent effectuer des actions que l’intelligence artificielle considère à risque.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Obtenir toutes les connexions à risque d’un utilisateur en particulier (API signIn)

Lorsque vous pensez qu’un utilisateur a peut-être été compromis, vous pouvez mieux comprendre l’état du risque qu’il encourt en récupérant toutes ses connexions risquées. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous venez de créer votre premier appel à Microsoft Graph !  
Vous pouvez à présent interroger les détections de risques concernant l’identité et utiliser les données comme bon vous semble.

Pour en savoir plus sur Microsoft Graph et comment créer des applications à l’aide de l’API Graph, consultez la [documentation](/graph/overview) afférente et bien plus sur le [site de Microsoft Graph](https://developer.microsoft.com/graph). 

Pour plus d’informations, consultez :

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Types de détections de risques détectées par Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph (Vue d’ensemble de Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection Service Root (Racine de service d’Azure AD Identity Protection)](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
