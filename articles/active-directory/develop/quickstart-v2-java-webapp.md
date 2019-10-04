---
title: Démarrage rapide pour les applications web Java de la plateforme d’identités Microsoft | Azure
description: Découvrez comment implémenter la connexion Microsoft sur une application web Java à l’aide d’OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/18/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 82a5054a98a5b77cf996be1fddd6502b8f3146bc
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120514"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web Java

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Dans ce guide de démarrage rapide, vous allez apprendre à intégrer une application web Java à la plateforme d’identités Microsoft. Votre application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph. 

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avez besoin des éléments suivants :
- [Kit de développement Java (JDK)](https://openjdk.java.net/) version 8 ou ultérieure, et [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous avez deux options pour démarrer votre application du guide de démarrage rapide : express (Option 1) ou manuel (Option 2)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Étape 1 : Télécharger l’exemple de code
> 
> - [Télécharger l'exemple de code](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Étape 2 : Ouvrir application.properties
>
> 1. Extrayez le fichier zip dans un dossier local.
> 1. (Facultatif) Si vous utilisez un environnement de développement intégré, ouvrez l’exemple dans votre IDE habituel.
> 1. Ouvrez le fichier *application.properties*. Vous ajouterez des valeurs pour `aad.clientId`, `aad.authority` et `aad.secretKey` quand vous inscrirez votre application à l’étape suivante.


> #### <a name="step-3-register-your-application"></a>Étape 3 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>    - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `java-webapp`.
>    - Laissez l’**URI de redirection** vide pour l’instant et sélectionnez **Inscrire**.
> 1. Recherchez la valeur **ID d’application (client)** correspondant à l’application. Mettez à jour la valeur pour `Enter_the_Application_Id_here` dans le fichier *application.properties*.
> 1. Recherchez la valeur **ID de l’annuaire (locataire)** correspondant à l’application. Mettez à jour la valeur pour `Enter_the_Tenant_Info_Here` dans le fichier *application.properties*. 
> 1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
>    - Dans **URI de redirection**, ajoutez `http://localhost:8080/msal4jsamples/secure/aad` et `https://localhost:8080/msal4jsamples/graph/users`.
>    - Sélectionnez **Enregistrer**.
> 1. Dans le menu de gauche, choisissez **Certificats et secrets**, puis cliquez sur **Nouveau secret client** dans la section **Secrets client** :
>     
>    - Tapez une description de la clé (pour le secret d’application de l’instance).
>    - Sélectionnez la durée de clé **Dans 1 an**.
>    - Quand vous cliquez sur **Ajouter**, la valeur de la clé s’affiche. 
>    - Copiez la valeur de la clé. Ouvrez le fichier *application.properties* que vous avez téléchargé précédemment et mettez à jour la valeur `Enter_the_Client_Secret_Here` avec la valeur de la clé. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez :
> 1. Ajouter des URL de réponse comme `http://localhost:8080/msal4jsamples/secure/aad` et `https://localhost:8080/msal4jsamples/graph/users`.
> 1. Créer un secret client.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.
> 
> #### <a name="step-2-download-the-code-sample"></a>Étape 2 : Télécharger l’exemple de code
> 
> - [Télécharger l'exemple de code](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Étape 3 : Configurer l’exemple de code 
> 
> 1. Extrayez le fichier zip dans un dossier local.
> 1. Si vous utilisez un environnement de développement intégré, ouvrez l’exemple dans votre IDE habituel (facultatif).
> 1. Ouvrez le fichier **application.properties**, qui se trouve dans *src/main/resources/* .
> 1. Mettez à jour les propriétés de l’application.
>   1. Recherchez `aad.clientId` et mettez à jour la valeur de `Enter_the_Application_Id_here` avec la valeur de l’**ID d’application (client)** de l’application que vous avez inscrite. 
>   1. Recherchez `aad.authority` et mettez à jour la valeur `Enter_the_Tenant_Name_Here` avec la valeur **ID de l’annuaire (locataire)** pour l’application que vous avez inscrite.
>   1. Recherchez `aad.secretKey` et mettez à jour la valeur `Enter_the_Client_Secret_Here` avec le **Secret client** que vous avez créé dans **Certificats et secrets** pour l’application que vous avez inscrite.

#### <a name="step-4-run-the-code-sample"></a>Étape 4 : Exécuter l’exemple de code
1. Exécutez l’exemple de code, puis ouvrez un navigateur et accédez à *http://localhost:8080* .
1. La première page comporte un bouton **Connexion**. Cliquez sur le bouton **Connexion** pour être redirigé vers Azure Active Directory. L’utilisateur est alors invité à entrer ses informations d’identification.  
1. Une fois authentifié sur Azure Active Directory, il est redirigé vers *http://localhost:8080/msal4jsamples/secure/aad* . L’utilisateur est officiellement connecté à l’application, et la page affiche des informations sur le compte connecté. Cette page contient également des boutons pour : 
    - *Se déconnecter* : déconnecte l’utilisateur actuel de l’application et le redirige vers la page d’accueil.
    - *Afficher les utilisateurs* : acquiert un jeton pour Microsoft Graph, puis appelle Microsoft Graph avec le jeton joint à la requête pour obtenir tous les utilisateurs du locataire.


## <a name="more-information"></a>Plus d’informations

### <a name="getting-msal"></a>Obtention de MSAL
MSAL4J est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Vous pouvez ajouter MSAL4J à votre application en utilisant Maven ou Gradle pour gérer vos dépendances. Pour cela, apportez les modifications suivantes au fichier pom.xml ou build.gradle dans votre application. 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Initialisation de MSAL
Vous pouvez ajouter la référence à MSAL4J en ajoutant le code suivant au début du fichier où vous allez utiliser MSAL4J : 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autorisations et le consentement :

> [!div class="nextstepaction"]
> [Autorisations et consentement](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Pour en savoir plus sur le flux d’authentification applicable à ce scénario, consultez cet article sur le flux du code d’autorisation OAuth 2.0 :

> [!div class="nextstepaction"]
> [Flux du code d’autorisation OAuth](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
