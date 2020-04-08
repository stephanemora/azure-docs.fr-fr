---
title: Utiliser les API Microsoft Graph pour configurer le provisionnement - Azure Active Directory | Microsoft Docs
description: Vous devez configurer l’approvisionnement de plusieurs instances d’une application ? Découvrez comment gagner du temps en utilisant les API Microsoft Graph pour automatiser la configuration du provisionnement automatique.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72217a565071f9531281af1862ba3681e353a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481464"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Configurer l’approvisionnement à l’aide des API Microsoft Graph

Le portail Azure constitue un moyen pratique pour configurer séparément l’approvisionnement d’applications individuelles. Cependant, si vous créez plusieurs ou même des centaines d’instances d’une application, il peut être plus simple d’automatiser la création et la configuration d’applications avec les API Microsoft Graph. Cet article explique comment automatiser la configuration de l’approvisionnement à l’aide d’API. Cette méthode est couramment utilisée pour les applications telles que [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Vue d’ensemble de la procédure d’automatisation de la configuration de l’approvisionnement à l’aide des API Microsoft Graph**


|Étape  |Détails  |
|---------|---------|
|[Étape 1. Créer l’application de galerie](#step-1-create-the-gallery-application)     |Se connecter au client de l’API <br> Récupérer le modèle de l’application de galerie <br> Créer l’application de galerie         |
|[Étape 2. Créer un travail d’approvisionnement à partir d’un modèle](#step-2-create-the-provisioning-job-based-on-the-template)     |Récupérer le modèle pour le connecteur d’approvisionnement <br> Créer le travail d’approvisionnement         |
|[Étape 3. Autoriser l’accès](#step-3-authorize-access)     |Tester la connexion à l’application <br> Enregistrer les informations d’identification         |
|[Étape 4. Démarrer le travail d’approvisionnement](#step-4-start-the-provisioning-job)     |Démarrage du travail         |
|[Étape 5. Surveiller l’approvisionnement](#step-5-monitor-provisioning)     |Vérifier l’état du travail d’approvisionnement <br> Récupérer les journaux d’approvisionnement         |

> [!NOTE]
> Les objets de réponse présentés dans cet article peuvent être raccourcis afin d’améliorer la lisibilité. Toutes les propriétés seront retournées à partir d’un appel réel.

## <a name="step-1-create-the-gallery-application"></a>Étape 1 : Créer l’application de galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Se connecter à l’afficheur Microsoft Graph (recommandé), à Postman ou à tout autre client d’API que vous utilisez

1. Lancer l’[afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Cliquez sur le bouton « Se connecter avec Microsoft » et connectez-vous à l’aide des informations d’identification d’administrateur de l’application ou d’administrateur global d’Azure AD.

    ![Connexion à Graph](./media/application-provisioning-configure-api/wd_export_02.png)

1. Une fois connecté, les détails du compte d’utilisateur apparaissent dans le volet de gauche.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Récupérer l’identificateur du modèle de l’application de galerie
Les applications de la galerie d’applications Azure AD disposent toutes d’un [modèle d’application](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) qui décrit les métadonnées de cette dernière. À l’aide de ce modèle, vous pouvez créer une instance de l’application et du principal de service dans votre locataire à des fins de gestion.

#### <a name="request"></a>*Requête*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Réponse*

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
        "displayName": "Amazon Web Services (AWS)",
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

Utilisez l’ID de modèle récupéré pour votre application lors de la dernière étape pour [créer une instance](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) de l’application et du principal du service dans votre locataire.

#### <a name="request"></a>*Requête*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Réponse*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

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

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Étape 2 : Créer un travail d’approvisionnement à partir d’un modèle

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Récupérer le modèle pour le connecteur d’approvisionnement

Les applications de la galerie dont l’approvisionnement est activé disposent de modèles destinés à simplifier la configuration. Utilisez la requête ci-dessous pour [récupérer le modèle pour la configuration de l’approvisionnement](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Notez que vous devrez fournir l’ID. L’ID fait référence à la ressource précédente, qui, dans ce cas, est ServicePrincipal. 

#### <a name="request"></a>*Requête*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
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
Pour activer l’approvisionnement, vous devez [créer un travail](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http) au préalable. Utilisez la requête ci-dessous pour créer un travail d’approvisionnement. Spécifiez l’ID de modèle susmentionné en tant que modèle à utiliser pour le travail.

#### <a name="request"></a>*Requête*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
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

## <a name="step-3-authorize-access"></a>Étape 3 : Autoriser l’accès

### <a name="test-the-connection-to-the-application"></a>Tester la connexion à l’application

Testez la connexion à l’application tierce. L’exemple ci-dessous concerne une application exigeant deux éléments : clientSecret et secretToken. Chaque application a ses propres exigences. Les applications utilisent souvent BaseAddress à la place de ClientSecret. Pour déterminer les informations d’identification dont a besoin votre application, accédez à la page de configuration du provisionnement de votre application, puis, en mode développeur, cliquez sur Tester la connexion. Le trafic réseau indique les paramètres utilisés pour les informations d’identification. Pour consulter la liste complète des informations d’identification, [cliquez ici](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http). 

#### <a name="request"></a>*Requête*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Enregistrer vos informations d’identification

La configuration de l’approvisionnement nécessite d’établir une relation de confiance entre Azure AD et l’application. Autorisez l’accès à l’application tierce. L’exemple ci-dessous concerne une application exigeant deux éléments : clientSecret et secretToken. Chaque application a ses propres exigences. Consultez la [documentation sur les API](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) pour voir les options disponibles. 

#### <a name="request"></a>*Requête*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Étape 4 : Démarrer le travail d’approvisionnement
Maintenant que le travail d’approvisionnement est configuré, utilisez la commande suivante pour [démarrer le travail](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Requête*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Étape 5 : Surveiller l’approvisionnement

### <a name="monitor-the-provisioning-job-status"></a>Surveiller l’état du travail d’approvisionnement

Maintenant que le travail d’approvisionnement est en cours d’exécution, utilisez la commande suivante pour suivre la progression du cycle d’approvisionnement actuel. Elle vous permet également de voir les statistiques à jour, notamment le nombre d’utilisateurs et de groupes qui ont été créés dans le système cible. 

#### <a name="request"></a>*Requête*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
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
Outre la surveillance de l’état du travail d’approvisionnement, vous pouvez utiliser les [journaux d’approvisionnement](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) pour interroger tous les événements qui se produisent (par exemple, interroger un utilisateur particulier et déterminer s’il a été correctement approvisionné).

#### <a name="request"></a>*Requête*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Réponse*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

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
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
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
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Articles connexes

- [Consultez la documentation Microsoft Graph sur la synchronisation](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Intégrer une application SCIM personnalisée à Azure AD](use-scim-to-provision-users-and-groups.md)
