---
title: Automatiser l’approvisionnement des applications avec SCIM dans Azure Active Directory | Microsoft Docs
description: Azure Active Directory peut configurer automatiquement les utilisateurs et les groupes sur une application ou un magasin d’identités avec en façade un service web avec l’interface définie dans Spécification du protocole SCIM.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/06/2019
ms.author: mimart
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 143919cb804be771d547e2913818d486c7f8adda
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824485"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications

## <a name="overview"></a>Présentation

SCIM est le protocole normalisé et le schéma qui vise à cohérence supérieure de lecteur dans la gestion des identités entre les systèmes. Lorsqu’une application prend en charge un point de terminaison SCIM pour la gestion de l’utilisateur, l’utilisateur Azure AD service d’approvisionnement peut envoyer des demandes à créer, modifier ou supprimer des utilisateurs et groupes à ce point de terminaison. 

Un grand nombre des applications pour qui prend en charge Azure AD [intégrées préalablement à l’approvisionnement automatique](../saas-apps/tutorial-list.md) implémenter SCIM comme les moyens de l’utilisateur de recevoir des notifications de modifications.  Outre ces options, les clients peuvent connecter des applications qui prennent en charge d’un profil spécifique de la [spécification du protocole SCIM 2.0](https://tools.ietf.org/html/rfc7644) à l’aide de l’option d’intégration de « non issue de la galerie » générique dans le portail Azure. 

Le principal objectif de cet article est sur le profil de SCIM 2.0 qui implémente de Azure AD dans le cadre de son connecteur SCIM générique pour les applications hors galerie. Toutefois, réussi le test d’une application qui prend en charge SCIM avec Azure AD générique connecteur est une étape pour qu’une application répertoriée dans la galerie Azure AD comme prenant en charge l’approvisionnement des utilisateurs. Pour plus d’informations sur l’obtention de votre application répertoriée dans la galerie d’applications Azure AD, consultez [Comment : Afficher votre application dans la galerie d’applications Azure AD](../develop/howto-app-gallery-listing.md).
 

>[!IMPORTANT]
>La dernière mise à jour du comportement de l’implémentation SCIM d’Azure AD a été effectuée le 18 décembre 2018. Pour plus d’informations sur ce qui a changé, consultez [Conformité au protocole SCIM 2.0 du service de provisionnement des utilisateurs Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Figure 1 : Approvisionnement d’Azure Active Directory à un magasin d’application ou d’identité qui implémente SCIM*

Cet article est divisé en quatre sections :

* **[Approvisionnement d’utilisateurs et groupes à des applications tierces qui prennent en charge SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  : Si votre organisation utilise une application tierce qu’implémente le profil SCIM 2.0 qu’Azure AD prend en charge, vous pouvez commencer à automatiser les deux approvisionner et retirer automatiquement des utilisateurs et groupes dès aujourd'hui.

* **[Comprendre l’implémentation de SCIM d’Azure AD](#understanding-the-azure-ad-scim-implementation)**  -si vous créez une application qui prend en charge une API de gestion utilisateur SCIM 2.0, cette section décrit en détail comment le client de SCIM d’Azure AD est implémenté et comment vous devez modéliser votre protocole SCIM demander la gestion et les réponses.
  
* **[Création d’un point de terminaison SCIM à l’aide des bibliothèques CLI de Microsoft](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -bibliothèques de Common Language Infrastructure (CLI), ainsi que des exemples de code vous montrent comment développer un point de terminaison SCIM et convertir les messages SCIM.  

* **[Référence du schéma utilisateur et groupe](#user-and-group-schema-reference)**  -décrit le schéma de groupe et d’utilisateur pris en charge par l’implémentation de SCIM d’Azure AD pour les applications hors galerie. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Approvisionnement d’utilisateurs et de groupes pour les applications qui prennent en charge SCIM
Azure AD peut être configuré pour attribuer automatiquement des utilisateurs et groupes pour les applications qui implémentent un profil spécifique de la [protocole SCIM 2.0](https://tools.ietf.org/html/rfc7644). Les détails du profil sont documentées dans [comprendre l’implémentation de SCIM d’Azure AD](#understanding-the-azure-ad-scim-implementation).

Vérifiez avec votre fournisseur d’application ou dans la documentation du fournisseur de votre application la conformité à ces exigences.

>[!IMPORTANT]
>L’implémentation de SCIM d’Azure AD repose sur l’approvisionnement de service, qui est conçu en permanence aux utilisateurs de synchroniser entre Azure AD d’utilisateurs Azure AD et l’application cible et implémente un ensemble très spécifique d’opérations standards. Il est important de comprendre ces comportements de comprendre le comportement du client SCIM d’Azure AD. Pour plus d’informations, consultez [que se passe-t-il pendant l’approvisionnement ?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Prise en main
Les applications qui prennent en charge le profil SCIM décrit dans cet article peuvent être connectées à Azure Active Directory à l’aide de la fonctionnalité « application ne figurant pas dans la galerie » dans la galerie d’applications Azure AD. Une fois connecté, Azure AD exécute toutes les 40 minutes un processus de synchronisation au cours duquel il interroge le point de terminaison de SCIM de l’application concernant les utilisateurs et les groupes assignés, et les crée ou les modifie en fonction des détails de l’attribution.

**Pour connecter une application qui prend en charge SCIM :**

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com). 

1. Sélectionnez **applications d’entreprise** dans le volet gauche. Une liste de toutes les applications configurées est indiquée, y compris les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez **+ nouvelle application** > **tous les** > **application de la galerie de Non**.

1. Entrez un nom pour votre application, puis sélectionnez **ajouter** pour créer un objet d’application. La nouvelle application est ajoutée à la liste des applications d’entreprise et s’ouvre à son écran de gestion d’application.
    
   ![][1]
   *Figure 2 : galerie d’applications Azure AD*
    
1. Dans l’écran de gestion d’application, sélectionnez **approvisionnement** dans le volet gauche.
1. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
   ![][2]
   *Figure 3 : configuration de l’approvisionnement dans le Portail Azure*
    
1. Dans le champ **URL du locataire**, entrez l’URL du point de terminaison SCIM de l’application. Exemple : https://api.contoso.com/scim/v2/
1. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). Si ce champ est laissé vide, Azure AD inclut un jeton de porteur OAuth émis par Azure AD avec chaque demande. Les applications qui utilisent Azure AD comme fournisseur d'identité peuvent valider ce jeton émis par Azure AD.
1. Sélectionnez **tester la connexion** pour qu’Azure Active Directory tentent de se connecter au point de terminaison SCIM. Si la tentative échoue, les informations d’erreur s’affiche.  

    >[!NOTE]
    >**Tester la connexion** interroge le point de terminaison SCIM pour un utilisateur qui n’existe pas, en utilisant un GUID aléatoire en tant que propriété correspondante sélectionnée dans la configuration Azure AD. La réponse correcte attendue est HTTP 200 OK avec un message SCIM ListResponse vide. 

1. Si les tentatives de connexion à l’application réussit, puis sélectionnez **enregistrer** pour enregistrer les informations d’identification d’administrateur.
1. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

    >[!NOTE]
    >Vous pouvez éventuellement désactiver la synchronisation des objets de groupe en désactivant le mappage « Groupes ». 

1. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et les groupes qui sont synchronisés. Sélectionnez **synchronisation uniquement utilisateurs et groupes attribués** (recommandé) pour synchroniser uniquement les utilisateurs et groupes affectés dans le **utilisateurs et groupes** onglet.
1. Une fois que votre configuration est terminée, définissez la **état d’approvisionnement** à **sur**.
1. Sélectionnez **enregistrer** pour démarrer le service d’approvisionnement AD Azure. 
1. Si la synchronisation affecté uniquement les utilisateurs et groupes (recommandés), veillez à sélectionner le **utilisateurs et groupes** onglet, puis attribuez les utilisateurs ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale a démarré, vous pouvez sélectionner **journaux d’Audit** dans le volet gauche pour surveiller la progression, qui montre toutes les actions effectuées par le service d’approvisionnement sur votre application. Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](check-status-user-account-provisioning.md).

> [!NOTE]
> La synchronisation initiale prend plus de temps que les synchronisations ultérieures, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Comprendre l’implémentation de SCIM d’Azure AD

Si vous créez une application qui prend en charge une API de gestion utilisateur SCIM 2.0, cette section décrit en détail comment le client de SCIM d’Azure AD est implémenté et comment vous devez modéliser votre protocole SCIM demander la gestion et les réponses. Une fois que vous avez implémenté votre point de terminaison SCIM, vous pouvez le tester en suivant la procédure décrite dans la section précédente.

Dans le [spécification du protocole SCIM 2.0](http://www.simplecloud.info/#Specification), votre application doit répondre à ces exigences :

* Prend en charge la création d’utilisateurs et éventuellement également les groupes, en fonction de la section [3.3 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Prend en charge la modification des utilisateurs ou des groupes de demandes de correctif, comme par [section 3.5.2 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Prend en charge la récupération d’une ressource connue pour un utilisateur ou un groupe créé précédemment, comme par [section 3.4.1 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Prend en charge l’interrogation des utilisateurs ou des groupes, en fonction de la section [3.4.2 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Par défaut, les utilisateurs sont récupérés par leurs `id` et interrogées par leurs `username` et `externalid`, et les groupes sont interrogés par `displayName`.  
* Prend en charge l’interrogation d’utilisateur par ID, par le gestionnaire, conformément à la section 3.4.2 du protocole SCIM.  
* Prend en charge l’interrogation des groupes par ID et par membre, conformément à la section 3.4.2 du protocole SCIM.  
* Accepte un jeton du porteur unique pour l’authentification et l’autorisation d’Azure AD à votre application.

Suivez ces recommandations générales lors de l’implémentation d’un point de terminaison SCIM pour garantir la compatibilité avec Azure AD :

* `id` est une propriété obligatoire pour toutes les ressources. Chaque réponse qui retourne une ressource doit vérifier chaque ressource a cette propriété, à l’exception de `ListResponse` avec aucun membre.
* Réponse à une demande de requête/filtre doit toujours être un `ListResponse`.
* Les groupes sont facultatifs, mais uniquement pris en charge si l’implémentation de SCIM prend en charge les demandes de correctif.
* Il n’est pas nécessaire d’inclure la ressource complète dans la réponse de correctif.
* Microsoft Azure AD utilise uniquement les opérateurs suivants :  
     - `eq`
     - `and`
* Ne nécessitent pas une correspondance respectant la casse sur les éléments structurels dans SCIM, dans le correctif particulier `op` les valeurs d’opération, tel que défini dans https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD émet les valeurs de 'op' en tant que `Add`, `Replace`, et `Remove`.
* Microsoft Azure AD effectue des requêtes pour extraire un utilisateur aléatoire et un groupe pour vous assurer que le point de terminaison et les informations d’identification sont valides. Il est également effectuée dans le cadre de **tester la connexion** circuler dans les [Azure portal](https://portal.azure.com). 
* L’attribut les ressources peuvent être interrogées sur doit être définie comme un attribut correspondant sur l’application dans le [Azure portal](https://portal.azure.com). Pour plus d’informations, consultez [personnalisation des mappages d’attributs d’approvisionnement utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement utilisateur
L’illustration suivante montre les messages qu’Azure Active Directory envoie à un service SCIM pour gérer le cycle de vie d’un utilisateur dans le magasin d’identités de votre application.  

![][4]
*Figure 4 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’utilisateurs*

### <a name="group-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement d’un groupe
Groupe d’approvisionnement et annulation d’approvisionnement est facultatif. Lorsque implémentée et activée, l’illustration suivante montre les messages qu’Azure AD envoie à un service SCIM pour gérer le cycle de vie d’un groupe dans le magasin d’identités de votre application.  Ces messages diffèrent des messages sur les utilisateurs de deux manières : 

* Demandes d’extraction de groupes de spécifient que l’attribut des membres doit être exclu des ressources fournies en réponse à la demande.  
* Les requêtes permettant de déterminer si un attribut de référence a une certaine valeur sont des requêtes sur les attributs membres.  

![][5]
*Figure 5 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’un groupe*

### <a name="scim-protocol-requests-and-responses"></a>Demandes de protocole SCIM et réponses
Cette section fournit des exemples de demandes SCIM émis par le client de SCIM d’Azure AD et un exemple de réponses attendu. Pour de meilleurs résultats, vous devez coder votre application pour gérer ces demandes dans ce format et émettre les réponses attendues.

>[!IMPORTANT]
>Pour comprendre comment et quand l’utilisateur Azure AD service d’approvisionnement émet les opérations décrites ci-dessous, consultez [que se passe-t-il pendant l’approvisionnement ?](user-provisioning.md#what-happens-during-provisioning).

- [Opérations de l’utilisateur](#user-operations)
  - [Créer utilisateur](#create-user)
    - [Requête](#request)
    - [Réponse](#response)
  - [Obtenir l’utilisateur](#get-user)
    - [Requête](#request-1)
    - [Réponse](#response-1)
  - [Obtenir l’utilisateur par requête](#get-user-by-query)
    - [Requête](#request-2)
    - [Réponse](#response-2)
  - [Obtenir l’utilisateur par requête - résultats nuls](#get-user-by-query---zero-results)
    - [Requête](#request-3)
    - [Réponse](#response-3)
  - [Mettre à jour utilisateur [propriétés à valeurs multiples]](#update-user-multi-valued-properties)
    - [Requête](#request-4)
    - [Réponse](#response-4)
  - [Mettre à jour utilisateur [propriétés à valeur unique]](#update-user-single-valued-properties)
    - [Requête](#request-5)
    - [Réponse](#response-5)
  - [Supprimer l’utilisateur](#delete-user)
    - [Requête](#request-6)
    - [Réponse](#response-6)
- [Opérations de groupe](#group-operations)
  - [Créer le groupe](#create-group)
    - [Requête](#request-7)
    - [Réponse](#response-7)
  - [Obtenir un groupe](#get-group)
    - [Requête](#request-8)
    - [Réponse](#response-8)
  - [Obtenir un groupe par displayName](#get-group-by-displayname)
    - [Requête](#request-9)
    - [Réponse](#response-9)
  - [Groupe de mises à jour [attributs Non-membre]](#update-group-non-member-attributes)
    - [Requête](#request-10)
    - [Réponse](#response-10)
  - [Groupe de mise à jour [ajouter des membres]](#update-group-add-members)
    - [Requête](#request-11)
    - [Réponse](#response-11)
  - [Groupe de mises à jour [supprimer des membres]](#update-group-remove-members)
    - [Requête](#request-12)
    - [Réponse](#response-12)
  - [Supprimer le groupe](#delete-group)
    - [Requête](#request-13)
    - [Réponse](#response-13)

### <a name="user-operations"></a>Opérations de l’utilisateur

* Les utilisateurs peuvent être interrogées par `userName` ou `email[type eq "work"]` attributs.  

#### <a name="create-user"></a>Create User

###### <a name="request"></a>Requête
*POST /utilisateurs*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>response
*HTTP/1.1 201 créé*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Obtenir l’utilisateur

###### <a name="request"></a>Requête
*OBTENIR /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Obtenir l’utilisateur par requête

##### <a name="request"></a>Requête
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obtenir l’utilisateur par requête - résultats nuls

##### <a name="request"></a>Requête
*GET /utilisateurs ? filtre = eq de nom d’utilisateur « utilisateur inexistantes »*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Mettre à jour utilisateur [propriétés à valeurs multiples]

##### <a name="request"></a>Requête
*PATCH/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Mettre à jour utilisateur [propriétés à valeur unique]

##### <a name="request"></a>Requête
*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Supprimer l'utilisateur

##### <a name="request"></a>Requête
*SUPPRIMER /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 204 aucun contenu*

### <a name="group-operations"></a>Opérations de groupe

* Groupes doivent toujours être créés avec une liste des membres vides.
* Les groupes peuvent être interrogées par le `displayName` attribut.
* Mise à jour à la demande de correctif logiciel de groupe doit céder un *HTTP 204 No Content* dans la réponse. Renvoyant un corps avec une liste de tous les membres n’est pas recommandé.
* Il n’est pas nécessaire prendre en charge le retour de tous les membres du groupe.

#### <a name="create-group"></a>Créer un groupe

##### <a name="request"></a>Requête
*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>response
*HTTP/1.1 201 créé*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obtenir un groupe

##### <a name="request"></a>Requête
*GET/groupes/40734ae655284ad3abcc ? excludedAttributes = membres HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obtenir un groupe par displayName

##### <a name="request"></a>Requête
*GET /Groups ? excludedAttributes = & filtre de membres = displayName eq « displayName » HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Groupe de mises à jour [attributs Non-membre]

##### <a name="request"></a>Requête
*PATCH/groupes/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a>response
*HTTP/1.1 204 aucun contenu*

### <a name="update-group-add-members"></a>Groupe de mise à jour [ajouter des membres]

##### <a name="request"></a>Requête
*PATCH/groupes/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>response
*HTTP/1.1 204 aucun contenu*

#### <a name="update-group-remove-members"></a>Groupe de mises à jour [supprimer des membres]

##### <a name="request"></a>Requête
*PATCH/groupes/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a>response
*HTTP/1.1 204 aucun contenu*

#### <a name="delete-group"></a>Suppression d’un groupe

##### <a name="request"></a>Requête
*SUPPRIMER /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 204 aucun contenu*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Création d’un point de terminaison SCIM à l’aide des bibliothèques CLI de Microsoft
En créant un service web SCIM interagissant avec Azure Active Directory, vous pouvez activer l’approvisionnement automatique pour pratiquement n’importe quel magasin de l’application ou d’identité.

Fonctionnement :

1. Azure AD fournit une bibliothèque common language infrastructure (CLI) nommée Microsoft.SystemForCrossDomainIdentityManagement, inclus avec le code décrivent les exemples ci-dessous. Les développeurs et les intégrateurs système peuvent utiliser cette bibliothèque pour créer et déployer un point de terminaison de service web basé sur les SCIM qui peut se connecter Azure AD au magasin d’identités de n’importe quelle application.
2. Les mappages sont mis en œuvre dans le service web pour mapper le schéma utilisateur normalisé et le protocole requis par l’application. 
3. L’URL de point de terminaison est inscrite dans Azure AD dans le cadre d’une application personnalisée dans la galerie d’applications.
4. Les utilisateurs et les groupes sont affectés à cette application dans Azure AD. Lors de l’affectation, ils sont placés dans une file d’attente pour être synchronisé à l’application cible. Le processus de synchronisation qui gère la file d’attente s’exécute toutes les 40 minutes.

### <a name="code-samples"></a>Exemples de code
Pour faciliter ce processus, [exemples de code](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) sont fournis, qui créent un SCIM de point de terminaison de service web et de démontrer l’approvisionnement automatique. L’exemple est d’un fournisseur qui gère un fichier avec des lignes de valeurs séparées par des virgules représentant des utilisateurs et groupes.    

**Composants requis**

* Visual Studio 2013 ou une version ultérieure
* [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/)
* Machine Windows qui prend en charge l’infrastructure ASP.NET 4.5 à utiliser comme point de terminaison SCIM. Cet ordinateur doit être accessible à partir du cloud.
* [Dans un abonnement Azure avec une version d’évaluation ou sous licence d’Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Mise en route
Le moyen le plus simple d’implémenter un point de terminaison SCIM qui peut accepter des demandes de configuration d’Azure AD consiste à générer et déployer l’exemple de code qui génère les utilisateurs configurés dans un fichier de valeurs séparées par des virgules (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Pour créer un exemple de point de terminaison SCIM

1. Télécharger le package d’exemples de code à l’emplacement [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Décompressez le package et placez-le sur votre machine Windows à un emplacement tel que C:\AzureAD-BYOA-Provisioning-Samples\.
1. Dans ce dossier, lancez le projet FileProvisioning\Host\FileProvisioningService.csproj dans Visual Studio.
1. Sélectionnez **outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de Package**et exécutez les commandes suivantes pour le Projet FileProvisioningService pour résoudre les références de la solution :

   ```
    Update-Package -Reinstall
   ```

1. Générez le projet FileProvisioningService.
1. Lancez l'application d'invite de commandes de Windows (en tant qu'administrateur) et utilisez la commande **cd** pour modifier le répertoire en votre dossier **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Exécutez la commande suivante, en remplaçant `<ip-address>` avec le nom de domaine ou d’adresse IP de l’ordinateur Windows :

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Dans Windows sous **Windows paramètres** > **paramètres Internet & réseau**, sélectionnez le **Windows Firewall**  >   **Paramètres avancés**et créer un **règle de trafic entrant** qui autorise l’accès entrant au port 9000.
1. Si l’ordinateur Windows est placé derrière un routeur, le routeur doit être configuré pour s’exécuter de traduction NAT entre son port 9000 présenté à internet et le port 9000 sur l’ordinateur Windows. Cette configuration est requise pour Azure AD pour accéder à ce point de terminaison dans le cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Pour enregistrer le point de terminaison SCIM exemple dans Azure AD

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com). 

1. Sélectionnez **applications d’entreprise** dans le volet gauche. Une liste de toutes les applications configurées est indiquée, y compris les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez **+ nouvelle application** > **tous les** > **application de la galerie de Non**.

1. Entrez un nom pour votre application, puis sélectionnez **ajouter** pour créer un objet d’application. L’objet d’application créé doit représenter l’application cible que vous devez configurer et sur laquelle mettre en œuvre l’authentification unique. Il ne s’agit pas d’un simple point de terminaison SCIM.

1. Dans l’écran de gestion d’application, sélectionnez **approvisionnement** dans le volet gauche.

1. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
   ![][2]
   *Figure 6 : configuration de l’approvisionnement dans le Portail Azure*
    
1. Dans le champ **URL du locataire**, saisissez l’URL côté Internet et le port de votre point de terminaison SCIM. L'entrée pourrait se présenter comme suit : http://testmachine.contoso.com:9000 ou http://\<ip-address>:9000/, où \<ip-address> correspond à l'adresse IP exposée sur Internet. 

1. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). Si ce champ est laissé vide, Azure AD inclura un jeton de porteur OAuth émis par Azure AD avec chaque demande. Les applications qui utilisent Azure AD comme fournisseur d’identité peuvent valider ce jeton émis par Azure AD.

1. Sélectionnez **tester la connexion** pour qu’Azure Active Directory tentent de se connecter au point de terminaison SCIM. Si la tentative échoue, les informations d’erreur s’affiche.  

    >[!NOTE]
    >**Tester la connexion** interroge le point de terminaison SCIM pour un utilisateur qui n’existe pas, en utilisant un GUID aléatoire en tant que propriété correspondante sélectionnée dans la configuration Azure AD. La réponse correcte attendue est HTTP 200 OK avec un message SCIM ListResponse vide

1. Si les tentatives de connexion à l’application réussit, puis sélectionnez **enregistrer** pour enregistrer les informations d’identification d’administrateur.

1. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

1. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et/ou les groupes qui sont synchronisés. Sélectionnez **« synchronisation uniquement utilisateurs et groupes attribués** (recommandé) pour synchroniser uniquement les utilisateurs et groupes affectés dans le **utilisateurs et groupes** onglet.

1. Une fois que votre configuration est terminée, définissez la **état d’approvisionnement** à **sur**.

1. Sélectionnez **enregistrer** pour démarrer le service d’approvisionnement AD Azure. 

1. Si la synchronisation affecté uniquement les utilisateurs et groupes (recommandés), veillez à sélectionner le **utilisateurs et groupes** onglet, puis attribuez les utilisateurs ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale a démarré, vous pouvez sélectionner **journaux d’Audit** dans le volet gauche pour surveiller la progression, qui montre toutes les actions effectuées par le service d’approvisionnement sur votre application. Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](check-status-user-account-provisioning.md).

La dernière étape de vérification de l’exemple consiste à ouvrir le fichier TargetFile.csv dans le dossier \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sur votre machine Windows. Une fois le processus de déploiement exécuté, ce fichier affiche les détails de tous les utilisateurs et groupes affectés et configurés.

### <a name="development-libraries"></a>Bibliothèques de développement
Pour développer un service web conforme à la spécification SCIM, commencez par vous familiariser avec les bibliothèques suivantes fournies par Microsoft afin d’accélérer le processus de développement : 

- Les bibliothèques CLI (Common Language Infrastructure) sont proposées pour une utilisation avec les langages basés sur cette infrastructure, notamment C#. Une de ces bibliothèques, Microsoft.SystemForCrossDomainIdentityManagement.Service, déclare une interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, indiquée dans l’illustration suivante. Un développeur qui utilise les bibliothèques doit mettre en œuvre cette interface avec une classe pouvant être référencée de manière générale comme fournisseur que fournisseur. Les bibliothèques permettent au développeur de déployer un service web qui est conforme à la spécification SCIM. Le service web peut être soit hébergé dans Internet Information Services, ou un assembly exécutable de la CLI. La requête est convertie en appels aux méthodes du fournisseur, qui doivent être programmées par le développeur pour fonctionner sur un magasin d’identités.
  
   ![][3]
  
- [Des gestionnaires de routeur Express](https://expressjs.com/guide/routing.html) sont disponibles pour les objets de requête node.js d’analyse représentant des appels (comme défini par la spécification SCIM) envoyés à un service web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Création d’un point de terminaison SCIM personnalisé
Les développeurs qui utilisent les bibliothèques CLI peuvent héberger leurs services dans un assembly exécutable de la CLI, ou dans Internet Information Services. Voici un exemple de code pour l’hébergement d’un service dans un assembly exécutable, à l’adresse http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Ce service doit avoir une adresse HTTP et un certificat d’authentification émanant du serveur dont l’autorité de certification racine est l’un des noms suivants : 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificat d’authentification serveur peut être associé à un port sur un hôte Windows à l’aide de l’utilitaire d’environnement réseau : 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Ici, la valeur fournie pour l’argument certhash est l’empreinte numérique du certificat, tandis que la valeur fournie pour l’argument appid est un identificateur global unique arbitraire.  

Pour héberger le service dans Internet Information Services, un développeur crée un assembly de bibliothèque de code CLI avec une classe nommée démarrage dans l’espace de noms par défaut de l’assembly.  Voici un exemple de classe de ce type : 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Gestion de l’authentification du point de terminaison
Les demandes d’Azure Active Directory incluent un jeton de support OAuth 2.0.   Tout service qui reçoit la demande doit authentifier l’émetteur comme étant Azure Active Directory pour le locataire Azure Active Directory attendu, pour l’accès au service web Azure Active Directory Graph.  Dans le jeton, l’émetteur est identifié par une revendication iss, comme « iss » : « https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/».  Dans cet exemple, l’adresse de base de la valeur de revendication, https://sts.windows.net, identifie Azure Active Directory en tant que l’émetteur, tandis que le segment, f37db0fed422 du 9bf5 45fa cbb1a5ac f33b d’adresse relative, est un identificateur unique du locataire pour Azure Active Directory le jeton a été délivré.  Si le jeton a été émis pour accéder au service web du graphique Azure Active Directory, l’identificateur de ce service, 00000002-0000-0000-c000-000000000000, doit être dans la valeur de revendication du jeton AD.  Chacune des applications qui sont enregistrées dans un seul client peut recevoir le même `iss` avec SCIM demandes de revendication.

Les développeurs qui utilisent les bibliothèques CLI fournies par Microsoft pour la création d’un service SCIM peuvent authentifier les demandes d’Azure Active Directory à l’aide du package Microsoft.Owin.Security.ActiveDirectory en suivant ces étapes : 

1. Dans un fournisseur, implémentez la propriété Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior lui faisant retourner une méthode à appeler à chaque démarrage du service : 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Ajoutez le code suivant à cette méthode pour que toute demande à un des points de terminaison du service authentifiés comme portant un jeton émis par Azure Active Directory pour un client spécifié puisse accéder au service web Azure AD Graph : 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Gestion d’approvisionnement et annulation d’approvisionnement des utilisateurs

1. Azure Active Directory interroge le service pour trouver un utilisateur avec une valeur d’attribut externalId correspondant à la valeur d’attribut mailNickname d’un utilisateur dans Azure AD. La requête est exprimée dans le protocole HTTP (Hypertext Transfer) comme dans cet exemple, jyoung étant un exemple de mailNickname d’un utilisateur dans Azure Active Directory.

    >[!NOTE]
    > Il s’agit d’un exemple uniquement. Pas tous les utilisateurs ont un attribut mailNickname, et la valeur de qu'un utilisateur a peut-être pas unique dans le répertoire. En outre, l’attribut utilisé pour la correspondance (c'est-à-dire dans ce cas externalId) est configurable dans le [des mappages d’attributs Azure AD](customize-application-attributes.md).

   ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ````
   Si le service a été créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel à la méthode de requête de fournisseur de service.  Voici la signature de cette méthode : 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ````
   Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 
   ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user doesn't return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

   ````
    Autorisation de HTTP/1.1 https://.../scim/Users POST : Porteur...  Type de contenu : application/scim + json {« schémas » : [« urn : ietf:params:scim:schemas:core:2.0:User », « urn : ietf:params:scim:schemas:extension:enterprise:2.0User »], « externalId » : « jyoung », « userName » : « jyoung », « actif » : true, les « adresses » : null,    « displayName » : « Joy Young », « e-mails » : [{« type » : « travail », « value » : «jyoung@Contoso.com», « principal » : true}], « meta » : {« resourceType » : « Utilisateur »}, « name » : {« familyName » : « Jeune », « givenName » : « Joie »}, « phoneNumbers » : null, « preferredLa nguage » : la valeur null, « title » : null, « department » : null, « manager » : null}
   ````
   The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
   ````
    System.Threading.Tasks.Tasks est défini dans mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource est défini dans / / Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(    Microsoft.SystemForCrossDomainIdentityManagement.Resource resource,    string correlationIdentifier);
   ````
   In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
   ````
    OBTENIR l’autorisation de HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 : Porteur...
   ````
   In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
   ````
    System.Threading.Tasks.Tasks est défini dans mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource et / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / sont définis dans Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > récupérer (paramètres de Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters, chaîne correlationIdentifier) ;

    interface publique Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters :   
        IRetrievalParameters {Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get ;}} interface publique Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {identificateur de chaîne {get ; set ;} chaîne Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get ; set ;}}
   ````
   In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

   If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Here, the value of the index x can be 0 and the value of the index y can be 1, or the value of x can be 1 and the value of y can be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
   ````
    Autorisation de correctif ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1 : Porteur...  Type de contenu : application/scim + json {« schémas » : [« urn : ietf:params:scim:api:messages:2.0:PatchOp »], « Opérations » : [{« op » : « Ajouter », « path » : « manager », « value » : [{« $ref » : « http://.../scim/Users/2819c223-7f76-453a-919d-413861904646 », « value » : « 2819c223-7f76-453a-919d-413861904646 »}]}]}
   ````
   The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
   ````
    System.Threading.Tasks.Tasks et / / System.Collections.Generic.IReadOnlyCollection<T> / / sont définis dans mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath et / / Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / / sont Microsoft.SystemForCrossDomainIdentityManagement.Protocol définis dans toutes les. 

    System.Threading.Tasks.Task Update(    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch,    string correlationIdentifier);

    interface publique Microsoft.SystemForCrossDomainIdentityManagement.IPatch Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {{get ; set ;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get ; set ;}        
    }

    classe publique PatchRequest2 :    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {public System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > opérations {get ;}


   Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service. La valeur des propriétés de l’objet fourni en tant que valeur d’argument des paramètres est la suivante : 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: « ID »
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): « ID »
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Ici, la valeur x de l’index peut être 0 et la valeur y de l’index peut être 1, ou la valeur de x peut comporter entre 1 et la valeur de y peut être 0, selon l’ordre des expressions de paramètre de requête de filtre.   

1. Voici un exemple de requête d’Azure Active Directory à un service SCIM pour mettre à jour un utilisateur : 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Les bibliothèques Microsoft Common Language Infrastructure pour l’implémentation de services SCIM convertissent la requête en appel à la méthode de mise à jour du fournisseur du service. Voici la signature de la méthode de mise à jour : 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    Dans le cas d’une demande de mise à jour d’un utilisateur, l’objet fourni comme valeur d’argument de correctif a les valeurs de propriété suivantes : 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Pour annuler l’approvisionnement d’un utilisateur à partir d’un magasin d’identités avec un service SCIM frontal, Azure AD envoie une demande similaire à celle-ci : 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode Delete du fournisseur de service.   Cette méthode a sa signature : 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   L’objet fourni comme valeur d’argument resourceIdentifier a ces valeurs de propriété dans le cas d’une demande d’annulation d’approvisionnement d’un utilisateur : 

1. Pour annuler l’approvisionnement d’un utilisateur à partir d’un magasin d’identités avec un service SCIM frontal, Azure AD envoie une demande similaire à celle-ci : 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Si le service a été créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode Delete du fournisseur de service.   Cette méthode a sa signature : 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   L’objet fourni comme valeur d’argument resourceIdentifier a ces valeurs de propriété dans le cas d’une demande d’annulation d’approvisionnement d’un utilisateur : 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Référence du schéma utilisateur et de groupe
Azure Active Directory peut configurer deux types de ressources pour les services web SCIM.  Ces types de ressources sont des utilisateurs et des groupes.  

Ressources de l’utilisateur sont identifiées par l’identificateur de schéma `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, qui est inclus dans cette spécification de protocole : https://tools.ietf.org/html/rfc7643.  Le mappage par défaut des attributs d’utilisateurs dans Azure Active Directory pour les attributs de ressources de l’utilisateur est fourni dans le tableau 1.  

Les ressources du groupe sont identifiées par l’identificateur de schéma, `urn:ietf:params:scim:schemas:core:2.0:Group`. Tableau 2 illustre le mappage par défaut des attributs de groupes dans Azure Active Directory sur les attributs du groupe de ressources.  

### <a name="table-1-default-user-attribute-mapping"></a>Tableau 1 : Mappage d’attributs utilisateur par défaut

| Utilisateur Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickName |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tableau 2 : Mappage d’attribut de groupe par défaut

| Groupe Azure Active Directory | urn : ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickName |displayName |
| membres |membres |
| objectId |ID |
| proxyAddresses |emails[type eq "other"].Value |


## <a name="related-articles"></a>Articles connexes
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](customize-application-attributes.md)
* [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifications d’approvisionnement de comptes](user-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
