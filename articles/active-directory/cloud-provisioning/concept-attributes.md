---
title: Présentation du schéma Azure AD et les expressions personnalisées
description: Cette rubrique décrit le schéma Azure AD, les attributs que l’agent de provisionnement transmet et les expressions personnalisées.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795528"
---
# <a name="understanding-the-azure-ad-schema"></a>Présentation du schéma Azure AD
Un objet dans Azure AD, comme un annuaire, est une construction de données globale programmatique qui représente des éléments, tels que des utilisateurs, des groupes et des contacts.  Lorsque vous créez un utilisateur ou un contact dans Azure AD, vous créez une instance de cet objet.  Ces instances peuvent être différenciées en fonction de leurs propriétés.

Dans Azure AD, les propriétés sont les éléments chargés de stocker les informations sur une instance d’un objet dans Azure AD.  

Le schéma Azure AD définit les règles pour lesquelles des propriétés peuvent être utilisées dans une entrée, les types de valeurs que ces propriétés peuvent avoir, et la manière dont les utilisateurs peuvent interagir avec ces valeurs. 

Azure AD comprend deux types de propriétés.  Les propriétés sont les suivantes :
- **Propriétés intégrées** – Propriétés prédéfinies par le schéma Azure AD.  Ces propriétés fournissent différentes utilisations et peuvent être accessibles ou non.
- **Extensions d’annuaire** – Propriétés fournies afin de vous permettre de personnaliser Azure AD pour votre usage personnel.  Par exemple, si vous avez étendu votre instance Active Directory locale avec un certain attribut et que vous souhaitez transmettre cet attribut, vous pouvez utiliser l’une des propriétés personnalisées qui sont fournies. 

## <a name="attributes-and-expressions"></a>Attributs et expressions
Lorsqu’un objet, tel qu’un utilisateur, est provisionné dans Azure AD, une nouvelle instance de l’objet utilisateur est créée.  Cette création comprend les propriétés de cet objet, qui sont également appelées « attributs ».  Au départ, les attributs de l’objet nouvellement créé sont définis sur des valeurs déterminées par les règles de synchronisation.  Ces attributs sont ensuite tenus à jour par le biais de l’agent de provisionnement cloud.

![](media/concept-attributes/attribute1.png)

Par exemple, si un utilisateur fait partie de l’équipe du département marketing, son attribut « département » Azure AD est créé au départ lors de son provisionnement, et la valeur est définie sur « Marketing ».  Voilà que, six mois plus tard, il passe au département Ventes.  Son attribut de département Active Directory local est remplacé par « Ventes ».  Ce changement est alors synchronisé sur Azure AD et apparaît sur son objet utilisateur Azure AD.

La synchronisation d’attributs peut être directe : la valeur dans Azure AD est directement définie sur la valeur de l’attribut local.  Ou, il peut exister une expression programmatique qui gère cette synchronisation.  Une expression programmatique s’avère nécessaire lorsqu’une logique ou une détermination doit être appliquée pour remplir la valeur.

Par exemple, s’il existait un attribut e-mail ("john.smith@contoso.com") et que la partie"@contoso.com" devait être supprimée pour transmettre uniquement la valeur « john.smith », voici ce qu’il faudrait utiliser :

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (e-mail) : "john.smith@contoso.com"
* **SORTIE** : "john.smith"

Pour plus d’informations sur l’écriture d’expressions personnalisées et la syntaxe, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

La liste suivante regroupe les attributs courants et la façon dont ils sont synchronisés dans Azure AD.


|Active Directory local|Type de mappage|Azure AD|
|-----|-----|-----|
|cn|Directement|commonName
|countryCode|Directement|countryCode|
|displayName|Directement|displayName|
|givenName|Expression|givenName|
|objectGUID|Directement|sourceAnchorBinary|  
|userprincipalName|Directement|userPrincipalName|
|ProxyAdress|Directement|ProxyAddress|

## <a name="viewing-the-schema"></a>Affichage du schéma
Pour voir le schéma et le vérifier, effectuez les étapes suivantes :

1.  Accédez à l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer).
2.  Connectez-vous avec votre compte d’administrateur général.
3.  Sur la gauche, cliquez sur **modifier les autorisations** et vérifiez que l’autorisation **Directory.ReadWrite.All** est acceptée.
4.  Exécutez la requête suivante : https://graph.microsoft.com/beta/serviceprincipals/.  Cette requête retourne une liste de principaux de service.
5.  Recherchez "appDisplayName" : "Active Directory to Azure Active Directory Provisioning" et notez la valeur de "id :".
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. Remplacez l’{ID principal de service} par votre valeur et exécutez la requête suivante : `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Recherchez la section "id" : "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976" et notez l’"id :".
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. À présent, exécutez la requête suivante : `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`.
 
    Exemple : https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Remplacez {ID principal de service} et {ID de provisionnement ADversADD} par vos valeurs.

9. Cette requête retourne le schéma.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)
