---
title: L’authentification et l’autorisation d’ancres Spatial Azure | Microsoft Docs
description: Découvrez les différentes façons qu'une application ou un service peut s’authentifier d’ancres spatiale d’Azure et les niveaux de contrôle que vous avez pour réguler l’accès à Azure les ancres spatiale.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: e641025d49dd42125aa692925c0697235489b1db
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307151"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>L’authentification et l’autorisation d’ancres Spatial Azure

Dans cette section, nous aborderons les différentes façons de que vous authentifier auprès d’Azure des ancres spatiale à partir de votre application ou service web et les méthodes dans lequel vous pouvez utiliser le contrôle d’accès en fonction du rôle dans Azure Directory (Azure AD) pour contrôler l’accès à vos comptes ancres spatiale.  

## <a name="overview"></a>Vue d'ensemble

![Une vue d’ensemble d’authentification à Azure les ancres Spatial](./media/spatial-anchors-authentication-overview.png)

Pour accéder à un compte Azure les ancres Spatial donné, les clients doivent d’abord obtenir un jeton d’accès à partir d’Azure mixte réalité Service STS (Security Token). Jetons obtenus à partir de STS live pendant 24 heures et contiennent des informations pour les services ancres Spatial pour les décisions d’autorisation sur le compte et vous assurer que seules les entités autorisées peuvent accéder à ce compte. 

Jetons d’accès peuvent être obtenus dans exchange à partir de deux clés de compte ou les jetons émis par AD Azure. 

Clés de compte de pouvoir commencer rapidement sur l’utilisation du service Azure les ancres spatiales ; Toutefois, avant de déployer votre application en production, il est recommandé que vous mettez à jour votre application pour utiliser l’authentification basée sur Active Directory Azure. 

Les jetons d’authentification Azure AD peuvent être obtenus de deux façons :

- Si vous générez une application d’entreprise, et que votre société utilise Azure AD en tant que son système d’identité, vous pouvez utiliser Azure basé sur utilisateur authentification AD dans votre application, puis accorder l’accès à vos comptes ancres spatial à l’aide de vos groupes de sécurité Azure AD existants, ou directement aux utilisateurs de votre organisation. 
- Sinon, il est recommandé d’obtenir des jetons Azure AD à partir d’un service web prenant en charge de votre application. À l’aide d’un service web de prise en charge d’est la méthode d’authentification recommandée pour les applications de production, car elle évite d’incorporer les informations d’identification pour accéder à Azure les ancres spatiale dans votre application cliente. 

## <a name="account-keys"></a>Clés de compte

À l’aide de clés de compte pour l’accès à votre compte Azure les ancres spatiale est la façon la plus simple pour commencer. Vous trouverez vos clés de compte sur le portail Azure. Accédez à votre compte, puis sélectionnez l’onglet « Clés ».

![Une vue d’ensemble d’authentification à Azure les ancres Spatial](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Deux clés sont rendus disponibles, qui sont toutes deux simultanément valides pour l’accès au compte ancres spatiale. Il est recommandé que vous mettre régulièrement à jour la clé que vous utilisez pour accéder au compte ; possédant deux séparer l’activation de clés valide ces mises à jour sans temps d’arrêt ; Vous devez uniquement mettre à jour également la clé primaire et la clé secondaire. 

Le Kit de développement a une prise en charge intégrée pour l’authentification avec des clés de compte ; Vous devez simplement définir la propriété AccountKey sur votre objet cloudSession. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

Une fois cette opération effectuée, le SDK gère l’échange de la clé de compte pour un jeton d’accès et la mise en cache de jetons pour votre application nécessaire. 

> [!WARNING] 
> Utilisation de clés de compte est recommandée pour l’intégration rapide, mais au cours de développement/prototypage. Il est fortement recommandé de ne pas livrer votre application en production à l’aide d’une clé de compte incorporé dans celui-ci et utiliser à la place le Azure basée sur le service ou utilisateur l’authentification AD approches répertoriée suivant.

## <a name="azure-ad-user-authentication"></a>Authentification utilisateur Azure AD

Pour les applications ciblant des utilisateurs Azure Active Directory, l’approche recommandée consiste à utiliser un jeton Azure AD pour l’utilisateur, vous pouvez obtenir à l’aide de la bibliothèque ADAL comme décrit dans la documentation suivante : https://docs.microsoft.com/azure/active-directory/develop/v1-overview; vous devez suivre les étapes répertoriées sous « Démarrages rapides », qui incluent :

1. Configuration dans le portail Azure
    1.  Inscrire votre application dans Azure AD comme **application Native**. Dans le cadre de l’inscription, vous devez déterminer si votre application doit être l’architecture mutualisée ou non et fournir la redirection d’URL autorisées pour votre application.  
    2.  Accorder à votre application ou les utilisateurs d’accéder à votre ressource : 
        1.  Accédez à votre ressource ancres spatiales dans le portail Azure
        2.  Basculez vers le **contrôle d’accès (IAM)** onglet
        3.  Appuyez sur **ajouter une attribution de rôle**
            1.  [Sélectionner un rôle](#role-based-access-control)
            2.  Dans le **sélectionnez** , entrez le nom de l’utilisateur (s), ou les groupes, et/ou applications auquel vous souhaitez attribuer l’accès. 
            3.  Appuyez sur **enregistrer**.
2. Dans votre code :
    1.  Veillez à utiliser le **ID d’application** et **l’Uri de redirection** de votre propre application Azure AD en tant que le **ID client** et **RedirectUri** paramètres dans la bibliothèque ADAL
    2.  Définir les informations de locataire :
        1.  Si votre application prend en charge **mon organisation uniquement**, remplacez cette valeur avec votre **ID de locataire** ou **nom_client** (par exemple, contoso.microsoft.com)
        2.  Si votre application prend en charge **comptes dans n’importe quel répertoire organisation**, remplacez cette valeur avec **organisations**
        3.  Si votre application prend en charge **utilisateurs de compte Microsoft tous les**, remplacez cette valeur avec **courantes**
    3.  Sur votre demande de jeton, définissez le **ressource** à « https://sts.mixedreality.azure.com». Cette « ressource » indique à Azure AD que votre application demande un jeton pour le service Azure les ancres spatiale.  

Avec cela, votre application doit être en mesure d’obtenir à partir de la bibliothèque ADAL un jeton Azure AD ; Vous pouvez définir ce jeton Azure AD en tant que le **authenticationToken** sur votre objet de configuration de session de cloud. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Authentification de service Azure AD

L’option recommandée pour déployer des applications en tirant parti des ancres Spatial Azure en production consiste à tirer parti d’un service principal qui est les demandes d’authentification du service broker. Le schéma général doit être comme décrit dans ce diagramme :

![Une vue d’ensemble d’authentification à Azure les ancres Spatial](./media/spatial-anchors-aad-authentication.png)

Ici, il est supposé que votre application utilise son propre mécanisme (par exemple : Compte Microsoft, PlayFab, Facebook, Google, ID, nom d’utilisateur/mot de passe personnalisé, etc..) pour s’authentifier auprès de son service principal. Une fois que vos utilisateurs sont authentifiés à votre service principal, service capable de récupérer un jeton Azure AD, échanger contre un jeton d’accès d’ancrage Spatial Azure et renvoyez-le à votre application cliente.

Le jeton d’accès Azure AD est récupéré à l’aide de la bibliothèque ADAL comme décrit dans la documentation suivante : https://docs.microsoft.com/azure/active-directory/develop/v1-overview; vous devez suivre les étapes répertoriées sous « Démarrages rapides », qui incluent :

1.  Configuration dans le portail Azure :
    1.  Inscrire votre application dans Azure AD :
        1.  Dans le portail Azure, accédez à **Azure Active Directory**, puis sélectionnez **inscriptions d’application**
        2.  Sélectionnez **nouvelle inscription d’application**
        3.  Entrez le nom de votre application, sélectionnez **application Web / API** comme type d’application, puis entrez l’URL d’authentification pour votre service. Puis appuyez sur **créer**.
        4.  Sur l’application, appuyez sur **paramètres**, puis sélectionnez le **clés** onglet. Entrez le nom de votre clé, sélectionnez une durée et appuyez sur **enregistrer**. Veillez à enregistrer la valeur de clé qui s’affiche à ce stade, car vous en aurez besoin pour l’inclure dans le code de votre service web.
    2.  Accorder l’accès à vos ressources de votre application et/ou les utilisateurs :
        1.  Accédez à votre ressource ancres spatiales dans le portail Azure
        2.  Basculez vers le **contrôle d’accès (IAM)** onglet
        3.  Appuyez sur **ajouter une attribution de rôle**
        1.  [Sélectionner un rôle](#role-based-access-control)
        2.  Dans le **sélectionnez** champ, entrez le nom de l’ou les applications que vous avez créé et pour lequel vous souhaitez affecter l’accès. Si vous souhaitez que les utilisateurs de votre application d’avoir différents rôles sur le compte ancres spatiale, vous devez inscrire plusieurs applications dans Azure AD et l’attribuer à chacun un rôle distinct. Puis implémenter votre logique d’autorisation pour utiliser le rôle approprié pour vos utilisateurs.  
    3.  Appuyez sur **enregistrer**.
2.  Dans votre code (Remarque : vous pouvez utiliser l’exemple de service fourni sur GitHub) :
    1.  Veillez à utiliser l’ID d’application, la clé secrète d’application et l’Uri de redirection de votre propre application Azure AD en tant que l’ID client, secret et paramètres d’URI de redirection dans la bibliothèque ADAL
    2.  Définir l’ID de locataire sur votre propre ID de locataire AAAzure ajouter dans le paramètre d’autorité dans la bibliothèque ADAL
    3.  Sur votre demande de jeton, définissez le **ressource** à « https://sts.mixedreality.azure.com» 

Avec cela, votre service principal peut récupérer un jeton Azure AD. Il peut ensuite l’échanger contre un jeton de MR qui est retournée au client. À l’aide d’un jeton Azure AD pour récupérer un jeton MR s’effectue via un appel REST. Voici un exemple d’appel :

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Où l’en-tête Authorization est mis en forme comme suit : `Bearer <accoundId>:<accountKey>`

Et la réponse contient le jeton MR en texte brut.
 
Ce jeton MR est ensuite renvoyé au client. Votre application cliente peut ensuite définie comme son jeton d’accès dans la configuration de session de cloud.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour vous aider à contrôler le niveau d’accès accordé aux applications, services ou les utilisateurs Azure AD de votre service, les rôles suivants ont été créés pour vous permettent d’affecter en fonction des besoins par rapport à vos comptes ancres Spatial Azure :

- **Propriétaire du compte ancres spatial**: les applications ou les utilisateurs qui ont ce rôle sont en mesure de créer des ancres spatiales, interroger pour eux et les supprimer. Lorsque vous vous authentifiez à votre compte à l’aide de clés de compte, le **Spatial propriétaire du compte ancres** rôle est attribué au principal authentifié. 
- **Collaborateur de compte ancres spatial**: applications ou les utilisateurs qui ont ce rôle sont en mesure de créer des ancres spatiales, la requête pour eux, mais pas les supprimer. 
- **Lecteur de compte ancres spatial**: applications ou les utilisateurs qui ont ce rôle sont seulement capables d’interroger d’ancrage spatiale, mais ne peut pas créer de nouveaux, supprimer des packages existants ou mettre à jour des métadonnées sur les points d’ancrage spatiales. Cela est généralement utilisé pour les applications où certains utilisateurs organiser l’environnement, tandis que d’autres utilisateurs peuvent rappeler uniquement ancres précédemment placés dans cet environnement.

## <a name="next-steps"></a>Étapes suivantes

Créer votre première application avec ancres spatiale d’Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
