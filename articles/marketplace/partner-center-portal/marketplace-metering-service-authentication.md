---
title: Stratégies d’authentification du service de mesure de la Place de marché | Place de marché Azure
description: Stratégies d’authentification du service de mesure prises en charge dans la Place de marché Azure.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736112"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Stratégies d’authentification du service de mesure de la Place de marché

Le service de mesure de la Place de marché prend en charge deux stratégies d’authentification :

* [jeton de sécurité Azure AD](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Nous allons vous expliquer quand et comment utiliser les différentes stratégies d’authentification pour soumettre en toute sécurité des compteurs personnalisés à l’aide du service de mesure de la Place de marché.

## <a name="using-the-azure-ad-security-token"></a>Utilisation du jeton de sécurité Azure AD

Les types d’offres applicables sont les applications SaaS et Azure avec le type de plan d’application managée.  

Soumettez des compteurs personnalisés à l’aide d’un ID d’application fixe prédéfini pour l’authentification.

Pour les offres SaaS, Azure AD est la seule option disponible.

Pour les applications Azure avec un plan d’application managée, vous devez envisager d’utiliser cette stratégie dans les cas suivants :

* Vous disposez déjà d’un mécanisme de communication avec vos services principaux et souhaitez étendre ce mécanisme pour émettre des compteurs personnalisés à partir d’un service central.
* Vous avez une logique de compteurs personnalisés complexe.  Exécutez cette logique dans un emplacement central au lieu des ressources d’applications managées.

Une fois que vous avez inscrit votre application, vous pouvez demander par programmation un jeton de sécurité Azure AD. Le serveur de publication doit utiliser ce jeton et effectuer une requête pour le résoudre.

Pour plus d’informations sur ces jetons, consultez [Jetons d’accès Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenir un jeton basé sur l’application Azure AD

#### <a name="http-method"></a>Méthode HTTP

**POST**

#### <a name="request-url"></a>*URL de requête*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Paramètre URI*

|  **Nom du paramètre** |  **Obligatoire**  |  **Description**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID de client de l’application Azure AD inscrite.   |
| | | |

#### <a name="request-header"></a>*En-tête de requête*

|  **Nom de l’en-tête**    |  **Obligatoire**  |  **Description**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Type de contenu associé à la requête. La valeur par défaut est `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Corps de la demande*

|  **Nom de la propriété**  |  **Obligatoire**  |  **Description**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Type d’autorisation. La valeur par défaut est `client_credentials`. |
|  `Client_id`        |   True         | Identificateur du client/de l’application associé à l’application Azure AD.|
|  `client_secret`    |   True         | Mot de passe associé à l’application Azure AD.  |
|  `Resource`         |   True         | Ressource cible pour laquelle le jeton est demandé. La valeur par défaut est `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Réponse*

|  **Nom**    |  **Type**  |  **Description**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Demande réussie.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Voici un exemple de token de réponse :

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Utilisation du jeton d’identités managées par Azure

Le type d’offre applicable est l’offre d’applications Azure avec le type de plan d’application managée.

Cette approche permet à l’identité des ressources déployées de s’authentifier pour envoyer des événements d’utilisation de compteurs personnalisés.  Vous pouvez incorporer le code qui émet l’utilisation dans les limites de votre déploiement.

>[!Note]
>Le serveur de publication doit s’assurer que les ressources qui émettent l’utilisation sont verrouillées afin qu’il n’y ait pas de falsification.

Votre application managée peut contenir différents types de ressources, des machines virtuelles aux Azure Functions.  Pour plus d’informations sur la façon de s’authentifier à l’aide d’identités managées pour différents services, consultez [Comment utiliser des identités managées pour des ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources).

Par exemple, suivez les étapes ci-dessous pour vous authentifier à l’aide d’une machine virtuelle Windows.

1. Assurez-vous que l’identité managée est configurée à l’aide de l’une des méthodes suivantes :
    * [Interface utilisateur du portail Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [INTERFACE DE LIGNE DE COMMANDE](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Modèle Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Kits Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Obtenez un jeton d’accès pour l’ID d’application du service de mesure de la Place de marché (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) en utilisant l’identité système, connectez-vous avec le protocole RDP à la machine virtuelle, ouvrez la console PowerShell, puis exécutez la commande ci-dessous :

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Obtenez l’ID d’application managée à partir de la propriété « ManagedBy » des groupes de ressources actuels :

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Le service de mesure de la Place de marché doit signaler l’utilisation sur un `resourceID` et un `resourceUsageId` s’il s’agit d’une application managée.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Utilisez l’[API du service de mesure de la Place de marché](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) pour émettre l’utilisation.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une offre Azure Application](./create-new-azure-apps-offer.md)