---
title: Inscription d’une application API web protégée | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web protégée et les informations dont vous avez besoin pour inscrire l’application.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86c36661dd8b5d43417ca1d845eef5cdb146f1e9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962523"
---
# <a name="protected-web-api-app-registration"></a>API web protégée : Inscription d'application

Cet article explique les spécificités de l’inscription d’application pour une API web protégée.

Consultez [Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft](quickstart-register-app.md) pour découvrir les étapes courantes de l’inscription d’une application.

## <a name="accepted-token-version"></a>Version de jeton acceptée

Le point de terminaison de la plateforme d’identités Microsoft peut émettre deux types de jetons : des jetons v1.0 et des jetons v2.0. Pour plus d’informations sur ces jetons, consultez [Jetons d’accès](access-tokens.md). La version de jeton acceptée varie selon les **types de comptes pris en charge** que vous avez choisis lorsque vous avez créé votre application :

- Si la valeur de **Types de comptes pris en charge** est **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** , la version de jeton acceptée est v2.0.
- Sinon, la version de jeton acceptée est v1.0.

Après avoir créé l’application, vous pouvez déterminer ou changer la version de jeton acceptée en procédant comme suit :

1. Dans le portail Azure, sélectionnez votre application puis le **manifeste** pour votre application.
2. Dans le manifeste, recherchez **« accessTokenAcceptedVersion »** . Notez que sa valeur est **2**. Cette propriété indique à Azure Active Directory (Azure AD) que l’API web accepte les jetons v2.0. Si la valeur est **null**, la version de jeton acceptée est v1.0.
3. Si vous avez modifié la version de jeton, sélectionnez **Enregistrer**.

> [!NOTE]
> L’API web spécifie la version de jeton (v1.0 ou v2.0) qu’elle accepte. Quand des clients demandent un jeton pour votre API web à partir du point de terminaison de la plateforme d’identités Microsoft (v2.0), ils obtiennent un jeton qui indique la version qui est acceptée par l’API web.

## <a name="no-redirect-uri"></a>Aucun URI de redirection

Les API web n’ont pas besoin d’inscrire d’URI de redirection, car aucun utilisateur n’est connecté de manière interactive.

## <a name="expose-an-api"></a>Exposer une API

Un autre paramètre spécifique des API web est l’API exposée et les étendues exposées.

### <a name="resource-uri-and-scopes"></a>URI de ressource et étendues

Les étendues sont généralement de la forme `resourceURI/scopeName`. Pour Microsoft Graph, les étendues ont des raccourcis tels que `User.Read`. Cette chaîne est un raccourci pour `https://graph.microsoft.com/user.read`.

Lors de l’inscription de l’application, vous devez définir les paramètres suivants :

- L’URI de ressource. Par défaut, le portail d’inscription des applications vous recommande d’utiliser `api://{clientId}`. Cet URI de ressource est unique, mais il n’est pas lisible. Vous pouvez le changer, mais assurez-vous que la nouvelle valeur est unique.
- Une ou plusieurs *étendues*. (Pour les applications clientes, elles apparaîtront en tant qu’*autorisations déléguées* pour votre API web.)
- Un ou plusieurs *rôles d’application*. (Pour les applications clientes, elles apparaîtront en tant qu’*autorisations d’application* pour votre API web.)

Les étendues sont également affichées sur l’écran de consentement qui est présenté aux utilisateurs finaux de votre application. Par conséquent, vous devez fournir les chaînes correspondantes qui décrivent l’étendue :

- Comme vue par l’utilisateur final.
- Comme vue par l’administrateur de locataire, qui peut accorder un consentement d’administrateur.

### <a name="exposing-delegated-permissions-scopes"></a>Exposition d’autorisations déléguées (étendues)

1. Sélectionnez la section **Exposer une API** dans l’inscription de l’application.
1. sélectionner **Ajouter une étendue**.
1. Si vous y êtes invité, acceptez l’URI d’ID d’application proposé (`api://{clientId}`) en sélectionnant **Enregistrer et continuer**.
1. Entrez ces paramètres :
      - Pour **Nom de l’étendue**, utilisez **access_as_user**.
      - Pour **Qui peut donner son consentement**, assurez-vous que l’option **Administrateurs et utilisateurs** est sélectionnée.
      - Dans **Nom d'affichage du consentement administrateur**, entrez **Accéder à TodoListService en tant qu’utilisateur**.
      - Dans **Description du consentement de l'administrateur**, entrez **Accède à l’API web TodoListService en tant qu’utilisateur**.
      - Dans **Nom d'affichage du consentement de l’administrateur**, entrez **Accéder à TodoListService en tant qu’utilisateur**.
      - Dans **Description du consentement de l'utilisateur**, entrez **Accède à l’API web TodoListService en tant qu’utilisateur**.
      - Conservez **État** défini sur **Activé**.
      - Sélectionnez **Ajouter une étendue**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Si votre API web est appelée par une application démon

Dans cette section, vous allez apprendre à inscrire votre API web protégée afin qu’elle puisse être appelée en toute sécurité par les applications de démon.

- Vous devez exposer les *autorisations d’application*. Vous allez déclarer uniquement les autorisations d’application, car les applications de démon n’interagissent pas avec les utilisateurs, si bien que les autorisations déléguées n’auraient aucun sens.
- Les administrateurs de locataires peuvent exiger d’Azure Active Directory (Azure AD) qu’il émette des jetons pour votre API web uniquement aux applications qui se sont inscrites pour accéder aux autorisations d’application de l’une des API web.

#### <a name="exposing-application-permissions-app-roles"></a>Exposition des autorisations d’application (rôles d’application)

Pour exposer les autorisations d’application, vous devez modifier le manifeste.

1. Dans l’inscription d’application pour votre application, sélectionnez **Manifeste**.
1. Modifiez le manifeste en recherchant le paramètre `appRoles` et en ajoutant un ou plusieurs rôles d'application. La définition de rôle est fournie dans l’exemple de bloc JSON suivant. Laissez le paramètre `allowedMemberTypes` défini sur `"Application"` uniquement. Assurez-vous que le paramètre `id` est un GUID unique et que `displayName` et `value` ne contiennent pas d’espaces.
1. Enregistrez le manifeste.

L’exemple suivant affiche le contenu de `appRoles`. (`id` peut être n’importe quel GUID unique.)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>S’assurer qu’Azure AD émet des jetons pour votre API web uniquement aux clients autorisés

L’API web recherche le rôle d’application. (C’est la méthode disponible au développeur pour exposer les autorisations d’application.) Toutefois, vous pouvez également configurer Azure AD pour émettre un jeton pour votre API web uniquement aux applications qui sont approuvées par l’administrateur de locataires pour accéder à votre API. Pour ajouter cette sécurité renforcée :

1. Dans la page **Présentation** de l’application pour l’inscription de votre application, sélectionnez le lien avec le nom de votre application sous **Application managée dans l’annuaire local**. Le titre de ce champ peut être tronqué. Vous pouvez, par exemple, voir **Application managée dans ...**

   > [!NOTE]
   >
   > Lorsque vous sélectionnez ce lien, vous accédez à la page **Présentation de l’application d’entreprise** associée au principal de service pour votre application dans le locataire où vous l’avez créée. Vous pouvez revenir à la page d’inscription d’application à l’aide du bouton Précédent de votre navigateur.

1. Sélectionnez la page **Propriétés** dans la section **Gérer** des pages d’application d’entreprise.
1. Si vous souhaitez qu’Azure AD autorise l’accès à votre API web à partir de certains clients seulement, définissez **Assignation requise de utilisateur ?** sur **Oui**.

   > [!IMPORTANT]
   >
   > Si vous définissez **Assignation requise de utilisateur ?** sur **Oui**, Azure AD vérifie les attributions de rôles d’application des clients lorsqu’ils demandent un jeton d’accès pour l’API web. Si le client n’est affecté à aucun rôle d’application, Azure AD renvoie l’erreur `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Si vous maintenez l’option **Assignation requise de utilisateur ?** définie sur **Non**, *Azure AD ne vérifiera pas les attributions de rôles d’application lorsqu’un client demandera un jeton d’accès pour votre API web*. N’importe quel client démon (autrement dit, tous les clients utilisant le flux d’informations d’identification de client) sera en mesure d’obtenir un jeton d’accès pour l’API en spécifiant simplement son public. N’importe quelle application sera en mesure d’accéder à l’API sans avoir à demander d’autorisations pour cela. Toutefois, votre API web peut toujours, comme cela est expliqué dans la section précédente, vérifier que l’application possède le rôle approprié (qui est autorisé par l’administrateur de locataire). L’API effectue cette vérification en validant que le jeton d’accès a une revendication de rôles et que la valeur de cette revendication est correcte. (Dans notre cas, cette valeur est `access_as_application`.)

1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-protected-web-api-app-configuration.md)
