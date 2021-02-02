---
title: Synchronisation entrante pour la synchronisation cloud à l’aide de l’API MS Graph
description: Cette rubrique explique comment activer la synchronisation entrante à l’aide de l’API Graph.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3796b3d86f647e38cf2ff018e8c0c903d9a64e41
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682036"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>Synchronisation entrante pour la synchronisation cloud à l’aide de l’API MS Graph

Le document suivant explique comment répliquer un profil de synchronisation à partir de zéro en utilisant uniquement des API MS Graph.  
Les étapes nécessaires sont les suivantes :  Il s'agit des éléments suivants :

- [Configuration de base](#basic-setup)
- [Créer des principaux de service](#create-service-principals)
- [Créer une tâche de synchronisation](#create-sync-job)
- [Mettre à jour un domaine ciblé](#update-targeted-domain)
- [Activer les hachages de mot de passe de synchronisation](#enable-sync-password-hashes-on-configuration-blade)
- [Démarrer la tâche de synchronisation](#start-sync-job)
- [Vérifier l’état](#review-status)

Utilisez ces commandes [Module Microsoft Azure Active Directory pour Windows PowerShell](/powershell/module/msonline/) afin d’activer la synchronisation d’un locataire de production. Il s’agit là d’un prérequis pour appeler le service web Administration du locataire.

## <a name="basic-setup"></a>Configuration de base

### <a name="enable-tenant-flags"></a>Activer les indicateurs de locataire

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
La première de ces deux commandes nécessite des informations d’identification Azure Active Directory. Ces applets de commande identifient implicitement le locataire et activent sa synchronisation.

## <a name="create-service-principals"></a>Créer des principaux de service
Ensuite, nous devons créer [l’application ou le principal de service AD2AAD](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http).

Vous devez utiliser l’ID d’application 1a4721b3-e57f-4451-ae87-ef078703ec94. Le displayName correspond à l’URL du domaine Active Directory, si vous en utilisez une dans le portail (par exemple, contoso.com). Toutefois, elle peut porter un autre nom.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Créer une tâche de synchronisation
La sortie de la commande ci-dessus retourne l’objectId du principal de service qui a été créé. Pour cet exemple, l’objectId est 614ac0e9-A59B-481F-bd8f-79a73d167e1c.  Utilisez Microsoft Graph pour ajouter un synchronizationJob à ce principal de service.  

La documentation sur la création d’une tâche de synchronisation est [disponible ici](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta).

Si vous n’avez pas enregistré l’ID ci-dessus, vous pouvez trouver le principal de service en exécutant l’appel MS Graph suivant. Vous aurez besoin des autorisations Directory.Read.All pour effectuer cet appel :
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Recherchez ensuite le nom de votre application dans la sortie.

Exécutez les deux commandes suivantes pour créer deux tâches : une pour le provisionnement d’utilisateurs ou de groupes, et une autre pour la synchronisation du hachage de mot de passe. Il s’agit de la même requête, mais avec des ID de modèle différents.


Appelez les deux requêtes suivantes :

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Vous aurez besoin de deux appels si vous souhaitez créer les deux requêtes.

Exemple de valeur renvoyée (pour le provisionnement) :

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Mettre à jour un domaine ciblé
Pour ce locataire, l’identificateur d’objet et l’identificateur d’application du principal de service sont les suivants :

ObjectId : 8895955e-2e6c-4d79-8943-4d72ca36878f AppId : 00000014-0000-0000-c000-000000000000 DisplayName : testApp

Vous allez devoir mettre à jour le domaine qui est ciblé par cette configuration. Pour cela, mettez à jour les secrets de ce domaine.

Vérifiez que le nom de domaine que vous utilisez correspond à l’URL que vous avez définie pour votre contrôleur de domaine local.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Ajoutez la paire clé/valeur suivante dans le tableau de valeurs ci-dessous en fonction de ce que vous essayez de faire :
 - Activez les indicateurs de locataire PHS et de synchronisation { clé : "AppKey", valeur : "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Activez uniquement l’indicateur de locataire de synchronisation (n’activez pas PHS) { clé : "AppKey", valeur : "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

La réponse attendue est... HTTP/204 No Content

Ici, la valeur « Domain » correspond au nom du domaine Active Directory local à partir duquel les entrées doivent être provisionnées pour Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Activer les hachages de mot de passe de synchronisation dans le panneau de configuration

 Cette section aborde l’activation des hachages de mot de passe de synchronisation pour une configuration particulière. Cela diffère de la clé secrète AppKey qui active l’indicateur de fonctionnalité au niveau locataire, uniquement pour un domaine ou une configuration. Vous devez définir la clé d’application sur le PHS un pour que cela fonctionne de bout en bout.

1. Saisissez le schéma (c’est assez grand) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Prenez ce mappage d’attribut CredentialData :
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Recherchez les mappages d’objets suivants avec les noms suivants dans le schéma
 - Approvisionnez des utilisateurs Active Directory
 - Approvisionnez des inetOrgPersons Active Directory

 Les mappages d’objets se trouvent dans schema.synchronizationRules[0].objectMappings (pour le moment, vous pouvez supposer qu’il n’y a qu’une seule règle de synchronisation)

4. Effectuez le mappage CredentialData de l’étape (2) et insérez-le dans les mappages d’objets à l’étape (3)

 Votre mappage d’objet ressemble à ceci :
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Copiez/collez le mappage de l’étape **Créer des travaux AD2AADProvisioning et AD2AADPasswordHash** ci-dessus dans le tableau attributeMappings. 

 L’ordre des éléments dans ce tableau n’a pas d’importance (le serveur principal effectuera le tri pour vous). Veillez à ajouter ce mappage d’attribut si le nom existe déjà dans le tableau (par exemple, s’il existe déjà un élément dans attributeMappings qui a le targetAttributeName CredentialData). Il se peut que vous rencontriez des erreurs de conflit, ou que les mappages préexistants et nouveaux soient combinés (ce qui n’est généralement pas le résultat souhaité). Le serveur principal ne déduplique pas pour vous. 

 N’oubliez pas de faire cela pour les utilisateurs et les inetOrgpersons

5. Enregistrez le schéma que vous avez créé 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Ajoutez le schéma dans le corps de la demande. 

## <a name="start-sync-job"></a>Démarrer la tâche de synchronisation
La tâche peut être récupérée à nouveau à l’aide de la commande suivante :

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

La documentation concernant la récupération des tâches est [disponible ici](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta). 
 
Pour démarrer la tâche, émettez cette requête à l’aide de l’objectId du principal de service créé à la première étape, et de l’identificateur de tâche retourné par la requête qui a créé la tâche.

La documentation expliquant comment démarrer une tâche est [disponible ici](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

La réponse attendue est... HTTP/204 No Content.

Les autres commandes permettant de contrôler les tâches sont [documentées ici](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Pour redémarrer une tâche, utilisez...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Vérifier l’état
Récupérez l’état de vos tâches via...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Pour plus de détails, regardez sous la section « status » de l’objet retourné.

## <a name="next-steps"></a>Étapes suivantes 

- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
- [Transformations](how-to-transformation.md)
- [API de synchronisation Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta)