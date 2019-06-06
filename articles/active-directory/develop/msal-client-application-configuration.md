---
title: Configuration de l’application cliente (Microsoft Authentication Library) | Azure
description: En savoir plus sur les options de configuration de client public et de client confidentiel applications dans la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430813"
---
# <a name="application-configuration-options"></a>Options de configuration d’application

Dans votre code, vous initialisez un nouveau client public ou confidentiel (ou l’agent utilisateur pour MSAL.js) l’application pour authentifier et acquérir des jetons. Vous pouvez définir un nombre d’options de configuration lorsque vous initialisez l’application cliente dans la bibliothèque d’authentification Microsoft (MSAL). Ces options se répartissent en deux groupes :

- Options d’inscription, y compris :
    - [Autorité](#authority) (composées du fournisseur d’identité [instance](#cloud-instance) et connectez-vous [audience](#application-audience) pour l’application et, éventuellement, l’ID de locataire).
    - [ID de client](#client-id).
    - [URI de redirection](#redirect-uri).
    - [clé secrète client](#client-secret) (pour les applications de client confidentiel).
- [Options de journalisation](#logging), y compris le niveau de journalisation, de données personnelles et le nom du composant à l’aide de la bibliothèque.

## <a name="authority"></a>Authority
L’autorité est une URL qui indique un répertoire MSAL peut demander des jetons à partir de. Les autorités courantes sont :

- https://login.microsoftonline.com/&lt; locataire&gt;/, où &lt;locataire&gt; est l’ID de client du locataire Azure Active Directory (Azure AD) ou d’un domaine associé à ce client Azure AD. Utilisé uniquement pour connecter les utilisateurs d’une organisation spécifique.
- https://login.microsoftonline.com/common/. Utilisé pour connecter des utilisateurs avec le travail et les comptes scolaires ou les comptes Microsoft personnels.
- https://login.microsoftonline.com/organizations/. Utilisé pour connecter les utilisateurs avec des comptes professionnels et scolaires.
- https://login.microsoftonline.com/consumers/. Utilisé pour connecter les utilisateurs avec uniquement des comptes personnels Microsoft (anciennement appelé comptes Windows Live ID).

Le paramètre d’autorité doit être cohérent avec ce qui est déclaré dans le portail d’inscription des applications.

L’URL de l’autorité est composé de l’instance et l’audience.

L’autorité peut être :
- Une autorité de cloud Azure AD.
- une autorité Azure AD B2C. Consultez [B2C spécificités](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Une autorité Active Directory Federation Services (ADFS). Consultez [ADFS prend en charge](https://aka.ms/msal-net-adfs-support).

Autorités de cloud Azure AD ont deux parties :
- le fournisseur d’identité *instance*
- L’authentification dans *audience* pour l’application

L’instance et l’audience peuvent être concaténées et fournis en tant que l’URL de l’autorité. Dans les versions de MSAL.NET antérieure à 3 de la bibliothèque MSAL. *x*, vous deviez composer l’autorité vous-même, basé sur le cloud, vous souhaitez cibler et l’audience de connexion.  Ce diagramme illustre comment l’URL de l’autorité se compose :

![Comment l’URL de l’autorité est composé](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance de cloud
Le *instance* est utilisée pour spécifier si votre application se connecte les utilisateurs dans le cloud public Azure ou dans des clouds nationaux. À l’aide de MSAL dans votre code, vous pouvez définir l’instance de cloud Azure à l’aide d’une énumération ou en passant l’URL pour le [instance cloud nationale](authentication-national-cloud.md#azure-ad-authentication-endpoints) comme le `Instance` membre (si vous le connaissez).

MSAL.NET lève une exception explicite si les deux `Instance` et `AzureCloudInstance` sont spécifiés.

Si vous ne spécifiez pas une instance, votre application ciblera l’instance de cloud public Azure (l’instance de l’URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Audience de l’application

L’audience de connexion varie selon les besoins de votre application :
- Si vous êtes une ligne de développeur métier (LOB), vous allez probablement générer une application à locataire unique qui sera utilisée uniquement dans votre organisation. Dans ce cas, vous devez spécifier l’organisation, soit par son ID de locataire (ID de votre instance Azure AD) ou par un nom de domaine associé à l’instance Azure AD.
- Si vous êtes un ISV, vous souhaiterez connecter des utilisateurs avec leurs comptes professionnels et scolaires dans n’importe quelle organisation ou dans certaines organisations (application mutualisée). Mais vous pouvez également que les utilisateurs à se connecter avec leurs comptes Microsoft personnels.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Comment spécifier l’audience dans votre code/configuration
À l’aide de MSAL dans votre code, vous spécifiez l’audience en utilisant l’une des valeurs suivantes :
- L’énumération d’audience d’autorité Azure AD
- L’ID de locataire, qui peut être :
  - Un GUID (ID de votre instance Azure AD), pour les applications à locataire unique
  - Un nom de domaine associé à votre instance Azure AD (également pour les applications à locataire unique)
- Un de ces espaces réservés comme un ID de locataire à la place de l’énumération d’audience d’autorité Azure AD :
    - `organizations` pour une application multi-locataire
    - `consumers` Pour connecter les utilisateurs uniquement avec leurs comptes personnels
    - `common` Pour connecter les utilisateurs avec leur travail et comptes scolaires ou leurs comptes Microsoft personnels

MSAL lève une exception explicite si vous spécifiez l’audience d’autorité Azure AD et l’ID client.

Si vous ne spécifiez pas un public, votre application ciblera Azure AD et les comptes Microsoft personnels comme une audience. (Autrement dit, il se comportera comme si `common` ont été spécifiées.)

### <a name="effective-audience"></a>Audience efficace
L’audience efficace pour votre application sera la valeur minimale (s’il existe une intersection) de l’audience, que vous définissez dans votre application et le public qui est spécifié dans l’inscription d’application. En fait, le [inscriptions](https://aka.ms/appregistrations) expérience vous permet de spécifier l’audience (les types de compte pris en charge) pour l’application. Pour plus d’informations, consultez [Démarrage rapide : Inscrire une application avec la plateforme Microsoft identity](quickstart-register-app.md).

Actuellement, la seule façon d’obtenir une application de connecter les utilisateurs avec uniquement des comptes personnels Microsoft consiste à configurer ces deux paramètres :
- La valeur est l’audience de l’inscription d’application `Work and school accounts and personal accounts`.
- Définir l’audience dans votre code/configuration `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID` = « consommateurs »).

## <a name="client-id"></a>ID client
L’ID de client est l’ID d’application unique (client) affecté à votre application par Azure AD lors de l’inscription de l’application.

## <a name="redirect-uri"></a>URI de redirection
La redirection URI est l’URI du fournisseur d’identité sera renvoyer les jetons de sécurité à.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirection pour les applications de client public
Si vous êtes un développeur d’application de client public qui est à l’aide de MSAL :
- Vous n’avez pas besoin de passer `RedirectUri` , car elle est calculée automatiquement par MSAL. Cette redirection URI est défini sur une des valeurs suivantes, selon la plateforme :
   - `urn:ietf:wg:oauth:2.0:oob` pour toutes les plateformes Windows
   - `msal{ClientId}://auth` pour Xamarin Android et iOS

- Vous n’avez pas besoin de configurer la redirection URI dans [inscriptions](https://aka.ms/appregistrations):

   ![URI de redirection dans les inscriptions d’application](media/msal-client-application-configuration/redirect-uri.png)

Vous pouvez remplacer l’URI de redirection à l’aide de la `RedirectUri` propriété (par exemple, si vous utilisez des courtiers). Voici quelques exemples d’URI de redirection pour ce scénario :

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Pour plus d’informations, consultez le [documentation pour Android et iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirection pour les applications de client confidentiel
Pour les applications web, l’URI de redirection (ou URI de réponse) est l’URI qu’Azure AD doit utiliser pour envoyer le jeton à l’application. Cela peut être l’URL de l’API web app/Web si l’application confidentielle est un d’eux. La redirection URI doit être inscrite dans l’inscription de l’application. Cette inscription est particulièrement importante lorsque vous déployez une application que vous avez initialement testées localement. Vous devez ensuite ajouter l’URL de réponse de l’application déployée dans le portail d’inscription des applications.

Pour les applications de démon, vous n’avez pas besoin de spécifier un URI de redirection.

## <a name="client-secret"></a>Clé secrète client
Cette option spécifie la clé secrète client pour l’application de client confidentiel. Ce secret (mot de passe) est fourni par le portail de l’inscription d’application ou fourni pour Azure AD lors de l’inscription de l’application avec PowerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Journalisation
Les autres options de configuration activent la journalisation et résolution des problèmes. Consultez le [journalisation](msal-logging.md) article pour plus d’informations sur la façon de les utiliser.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [instanciation des applications clientes à l’aide de MSAL.NET](msal-net-initializing-client-applications.md).

En savoir plus sur [instanciation des applications clientes en utilisant MSAL.js](msal-js-initializing-client-applications.md).
