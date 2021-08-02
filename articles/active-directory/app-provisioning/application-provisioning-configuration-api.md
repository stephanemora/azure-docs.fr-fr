---
title: Configurer l’approvisionnement à l’aide des API Microsoft Graph
description: Découvrez comment gagner du temps en utilisant les API Microsoft Graph pour automatiser la configuration du provisionnement automatique.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/03/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 12ee5a02b0451fd70df0e7155e9460290943f5b2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962049"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Configurer l’approvisionnement à l’aide des API Microsoft Graph

Le portail Azure constitue un moyen pratique pour configurer séparément l’approvisionnement d’applications individuelles. Cependant, si vous créez plusieurs ou même des centaines d’instances d’une application, il peut être plus simple d’automatiser la création et la configuration d’applications avec les API Microsoft Graph. Cet article explique comment automatiser la configuration de l’approvisionnement à l’aide d’API. Cette méthode est couramment utilisée pour les applications telles que [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Vue d’ensemble de la procédure d’automatisation de la configuration de l’approvisionnement à l’aide des API Microsoft Graph**


|Étape  |Détails  |
|---------|---------|
|[Étape 1 : Créer l’application de galerie](#step-1-create-the-gallery-application)     |Se connecter au client de l’API <br> Récupérer le modèle de l’application de galerie <br> Créer l’application de galerie         |
|[Étape 2 : Créer un travail d’approvisionnement basé sur un modèle](#step-2-create-the-provisioning-job-based-on-the-template)     |Récupérer le modèle pour le connecteur d’approvisionnement <br> Créer le travail d’approvisionnement         |
|[Étape 3 : Autoriser l’accès](#step-3-authorize-access)     |Tester la connexion à l’application <br> Enregistrer les informations d’identification         |
|[Étape 4 : Démarrer le travail d’approvisionnement](#step-4-start-the-provisioning-job)     |Démarrage du travail         |
|[Étape 5 : Superviser l’approvisionnement](#step-5-monitor-provisioning)     |Vérifier l’état du travail d’approvisionnement <br> Récupérer les journaux d’approvisionnement         |

## <a name="step-1-create-the-gallery-application"></a>Étape 1 : Créer l’application de galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Se connecter à l’afficheur Microsoft Graph (recommandé), à Postman ou à tout autre client d’API que vous utilisez

1. Démarrez l’[Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).
1. Cliquez sur le bouton « Se connecter avec Microsoft » et connectez-vous à l’aide des informations d’identification d’administrateur de l’application ou d’administrateur global d’Azure AD.
1. Une fois connecté, les détails du compte d’utilisateur apparaissent dans le volet de gauche.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Récupérer l’identificateur du modèle de l’application de galerie
Les applications de la galerie d’applications Azure AD disposent toutes d’un [modèle d’application](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta) qui décrit les métadonnées de cette dernière. À l’aide de ce modèle, vous pouvez créer une instance de l’application et du principal de service dans votre locataire à des fins de gestion.

#### <a name="request"></a>Requête

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```
#### <a name="response"></a>response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json
{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Single-Account Access",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Créer l’application de galerie

Utilisez l’ID de modèle récupéré pour votre application lors de la dernière étape pour [créer une instance](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta) de l’application et du principal du service dans votre locataire.

#### <a name="request"></a>Requête


```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json
{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>response

```http
HTTP/1.1 201 OK
Content-type: application/json
{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Étape 2 : Créer un travail d’approvisionnement basé sur un modèle

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Récupérer le modèle pour le connecteur d’approvisionnement

Les applications de la galerie dont l’approvisionnement est activé disposent de modèles destinés à simplifier la configuration. Utilisez la requête ci-dessous pour [récupérer le modèle pour la configuration de l’approvisionnement](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta). Notez que vous devrez fournir l’ID. L’ID fait référence à la ressource précédente, qui, dans ce cas, est la ressource servicePrincipal. 

#### <a name="request"></a>Requête

```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```

#### <a name="response"></a>response
```http
HTTP/1.1 200 OK
{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Créer le travail d’approvisionnement
Pour activer l’approvisionnement, vous devez [créer un travail](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta) au préalable. Utilisez la requête suivante pour créer un travail d’approvisionnement. Spécifiez l’ID de modèle susmentionné en tant que modèle à utiliser pour le travail.

#### <a name="request"></a>Requête

```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json
{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>response
```http
HTTP/1.1 201 OK
Content-type: application/json
{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Étape 3 : Autoriser l’accès

### <a name="test-the-connection-to-the-application"></a>Tester la connexion à l’application

Testez la connexion à l’application tierce. L’exemple suivant est destiné à une application nécessitant une clé secrète client et un jeton secret. Chaque application possède ses propres exigences. Les applications utilisent souvent une adresse de base à la place d’une clé secrète client. Pour déterminer les informations d’identification requises pour votre application, accédez à la page de configuration de l’approvisionnement de votre application, puis, en mode développeur, cliquez sur **Tester la connexion**. Le trafic réseau indique les paramètres utilisés pour les informations d’identification. Pour obtenir une liste complète des informations d’identification, consultez [synchronizationJob: validateCredentials](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta). La plupart des applications, telles qu’Azure Databricks, s’appuient sur BaseAddress et SecretToken. BaseAddress est désigné comme une URL de locataire dans le Portail Azure. 

#### <a name="request"></a>Requête
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    "credentials": [ 
        { "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" },
        { "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>response
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Enregistrer vos informations d’identification

La configuration de l’approvisionnement nécessite d’établir une relation de confiance entre Azure AD et l’application. Autorisez l’accès à l’application tierce. L’exemple suivant est destiné à une application nécessitant une clé secrète client et un jeton secret. Chaque application possède ses propres exigences. Consultez la [documentation sur les API](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta) pour voir les options disponibles. 

#### <a name="request"></a>Requête
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    "value": [ 
        { "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" },
        { "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>response
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Étape 4 : Démarrer le travail d’approvisionnement
Maintenant que le travail d’approvisionnement est configuré, utilisez la commande suivante pour [démarrer le travail](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 


#### <a name="request"></a>Requête

```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```


#### <a name="response"></a>response
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Étape 5 : Superviser l’approvisionnement

### <a name="monitor-the-provisioning-job-status"></a>Surveiller l’état du travail d’approvisionnement

Maintenant que le travail d’approvisionnement est en cours d’exécution, utilisez la commande suivante pour suivre la progression du cycle d’approvisionnement actuel. Elle vous permet également de voir les statistiques à jour, notamment le nombre d’utilisateurs et de groupes qui ont été créés dans le système cible. 

#### <a name="request"></a>Requête
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>response
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577
{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Surveiller les événements d’approvisionnement à l’aide des journaux d’approvisionnement
Outre la supervision de l’état du travail d’approvisionnement, vous pouvez utiliser les [journaux d’approvisionnement](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta) pour interroger tous les événements qui se produisent. Par exemple, interrogez un utilisateur particulier et déterminez s’il a été correctement approvisionné.

#### <a name="request"></a>Requête
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>response
```http
HTTP/1.1 200 OK
Content-type: application/json
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "AWS Contoso",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "AWS Contoso"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            }
            ]
       }
    ]
}
```
## <a name="see-also"></a>Voir aussi

- [Consultez la documentation Microsoft Graph sur la synchronisation](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Intégrer une application SCIM personnalisée à Azure AD](./use-scim-to-provision-users-and-groups.md)