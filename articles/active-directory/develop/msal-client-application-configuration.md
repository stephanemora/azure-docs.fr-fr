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
ms.openlocfilehash: 4d731a8153dc6a70382c0d87cc20d8c961d9fe24
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546009"
---
# <a name="application-configuration-options"></a>Options de configuration d’application

Dans votre code, vous initialisez un nouveau client public ou confidentiel (ou l’agent utilisateur pour MSAL.js) l’application pour authentifier et acquérir des jetons.  Il existe un nombre différent d’options de configuration qui peuvent être définies lors de l’initialisation de l’application cliente dans la bibliothèque MSAL. Ces options peuvent être divisées en deux groupes :

- Options d’inscription, y compris :
    - [Autorité](#authority) (composées du fournisseur d’identité [instance](#cloud-instance) et connectez-vous [audience](#application-audience) pour l’application et, éventuellement, l’ID de locataire).
    - [client ID](#client-id)
    - [URI de redirection](#redirect-uri)
    - [clé secrète client](#client-secret) (pour les applications de client confidentiel).
- [Options de journalisation](#logging), y compris le niveau de journalisation, de données personnelles et le nom du composant à l’aide de la bibliothèque.

## <a name="authority"></a>Autorité
L’autorité est une URL qui indique un répertoire MSAL peut demander des jetons à partir de. Les autorités habituelles sont :

- https://login.microsoftonline.com/&lt; locataire&gt;/, où &lt;locataire&gt; est l’ID de client du locataire Azure AD ou un domaine associé à ce client Azure AD.  Utilisé uniquement pour connecter les utilisateurs d’une organisation spécifique.
- https://login.microsoftonline.com/common/. Utilisé pour connecter des utilisateurs avec le travail et les comptes scolaires ou un compte personnel Microsoft.
- https://login.microsoftonline.com/organizations/. Utilisé pour connecter les utilisateurs avec des comptes professionnels et scolaires.
- https://login.microsoftonline.com/consumers/. Utilisé pour connecter des utilisateurs avec leur compte Microsoft personnel uniquement (actif).

Le paramètre d’autorité doit être cohérent avec ce qui est déclaré dans le portail d’inscription des applications.

L’URL de l’autorité est composé de l’instance et l’audience.

L’autorité peut être :
- une autorité de Cloud Azure Active directory
- une autorité Azure AD B2C. Consultez [B2C spécificités](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- une autorité d’ADFS. Consultez [ADFS prend en charge](https://aka.ms/msal-net-adfs-support).

Autorités de cloud Azure AD ont deux parties :
- le fournisseur d’identité **instance**
- l’authentification dans **audience** pour l’application

L’instance et l’audience peuvent être concaténées et fournis en tant que l’URL de l’autorité. Dans les versions de MSAL.NET avant MSAL 3.x, que vous deviez composer l’autorité vous-même selon le cloud que vous vouliez cible et l’audience de connexion.  Le diagramme suivant montre comment l’URL de l’autorité est composé.

![Autorité](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance de cloud
Le **instance** est utilisée pour spécifier si votre application se connecte les utilisateurs dans le cloud public Microsoft Azure ou dans des clouds nationaux. À l’aide de MSAL dans votre code, l’instance de cloud Azure peut être définie à l’aide d’une énumération ou en passant l’URL pour le [instance cloud nationale](authentication-national-cloud.md#azure-ad-authentication-endpoints) comme le `Instance` membre (si vous le connaissez).

MSAL.NET lève une exception explicite si les deux `Instance` et `AzureCloudInstance` sont spécifiés. 

Si vous ne spécifiez pas une instance, votre application ciblera l’instance de cloud public Azure (l’instance de l’URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Audience de l’application

L’audience de connexion varie selon les besoins de votre application :
- Si vous êtes une ligne de développeur métier (LOB), vous allez probablement générer une application à client unique, qui sera utilisée uniquement dans votre organisation. Dans ce cas, vous devez spécifier les éléments cette organisation, soit par son ID de locataire (ID de votre Azure Active Directory) ou un nom de domaine associé à cette Azure Active Directory.
- Si vous êtes un éditeur de logiciels, vous pouvez connecter des utilisateurs avec leur travail et comptes scolaires dans n’importe quelle organisation, ou certaines organisations (une application multi-locataire), mais vous souhaiterez peut-être également que les utilisateurs à se connecter avec leur compte personnel Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Comment spécifier l’audience dans votre code/configuration
À l’aide de MSAL dans votre code, vous spécifiez le public à l’aide de :
- soit l’Azure AD autorité audience l’énumération. 
- ou l’ID de locataire, ce qui peut être :
  - un GUID (ID de votre Azure Active Directory), pour les applications à locataire unique
  - un nom de domaine associé à votre Azure Active Directory (également pour les applications à locataire unique)
- ou l’un de ces espaces réservés comme un ID de locataire à la place de l’énumération d’audience d’autorité Azure AD :
    - `organizations` pour une application à client multiples
    - `consumers` Pour connecter les utilisateurs uniquement avec leurs comptes personnels
    - `common` Pour connecter les utilisateurs avec leur travail et un compte scolaire ou un compte personnel Microsoft

MSAL lève une exception explicite si vous spécifiez audience d’autorité Azure AD et l’ID client. 

Si vous ne spécifiez pas une audience votre application ciblera Azure AD et les comptes Microsoft personnels comme une audience (c'est-à-dire `common`).

### <a name="effective-audience"></a>Audience efficace
L’audience efficace pour votre application sera la valeur minimale (s’il existe une intersection) de l’audience, que vous définissez dans votre application et l’audience spécifié dans l’inscription de l’application. En effet, l’expérience de l’inscription d’application ([inscription d’application](https://aka.ms/appregistrations)) vous permet de spécifier l’audience (types de compte pris en charge) pour l’application. Consultez [Démarrage rapide : Inscrire une application avec la plateforme Microsoft identity](quickstart-register-app.md) pour plus d’informations.

Actuellement, la seule façon d’obtenir une application de connecter les utilisateurs avec uniquement des comptes personnels Microsoft consiste à définir :
- la valeur de l’audience de l’inscription d’application « Et les comptes scolaires comptes professionnels et personnels » et,
- et définissez le public dans votre code / configuration `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID `= « consommateurs »)

## <a name="client-id"></a>ID client
L’ID d’application unique (client) affecté à votre application par Azure AD lors de l’inscription de l’application.

## <a name="redirect-uri"></a>URI de redirection
L’URI de redirection est l’URI où le fournisseur d’identité envoie les jetons de sécurité précédent. 

### <a name="redirect-uri-for-public-client-applications"></a>URI de redirection pour les applications de client public
Client public aux développeurs d’applications à l’aide de MSAL :
- Vous n’avez pas besoin de transmettre le ``RedirectUri`` comme elle est calculée automatiquement par MSAL. Cette redirection URI est défini sur les valeurs suivantes selon la plateforme :

- ``urn:ietf:wg:oauth:2.0:oob`` pour toutes les plateformes Windows
- ``msal{ClientId}://auth`` pour Xamarin Android et iOS

Toutefois, la redirection URI doit être configuré dans le [inscription d’application](https://aka.ms/appregistrations).

![URI de redirection dans le portail](media/msal-client-application-configuration/redirect-uri.png)

Il est possible de remplacer l’URI de redirection à l’aide de la `RedirectUri` propriété, par exemple si vous utilisez des courtiers. Voici quelques exemples d’URI de redirection dans ce cas :

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Pour plus d’informations, consultez les spécificités Android et [spécificités d’iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>URI de redirection pour les applications de client confidentiel
Pour les applications web, l’URI de redirection (ou le URI de réponse), est l’URI à laquelle Azure AD contactera dans l’application avec le jeton. Cela peut être l’URL de l’application Web / API Web si la confidentialité est un d’eux.  Cet URI de redirection doit être enregistré dans l’inscription d’application. Ceci est particulièrement important lorsque vous déployez une application que vous avez initialement testées localement. Vous devez ensuite ajouter l’URL de réponse de l’application déployée dans le portail d’inscription des applications.

Pour les applications de démon, vous n’avez pas besoin de spécifier un URI de redirection.

## <a name="client-secret"></a>Clé secrète client
La clé secrète client pour l’application de client confidentiel. Ce secret (mot de passe application) est fourni par le portail d’inscription des applications, ou fourni à Azure AD lors de l’inscription de l’application avec PowerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Journalisation
Les autres options activent la journalisation et résolution des problèmes. Pour plus d’informations, consultez le [journalisation](msal-logging.md) page pour plus d’informations sur la façon de les utiliser.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [instanciation d’applications clientes à l’aide de MSAL.NET](msal-net-initializing-client-applications.md).

En savoir plus sur [instanciation des applications clientes utilisant MSAL.js](msal-js-initializing-client-applications.md).
