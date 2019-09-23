---
title: Démarrage rapide pour les applications web Python de la plateforme d’identités Microsoft | Azure
description: Découvrir comment implémenter la connexion Microsoft sur une application web Python à l’aide d’OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 682582c8c695550f7dfdfcc079e1d0bf04828180
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997223"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Dans ce guide de démarrage rapide, vous allez apprendre à intégrer une application web Python à la plateforme d’identités Microsoft. Votre application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph.

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avez besoin des éléments suivants :

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) or [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https:/pythonhosted.org/Flask-Session/), [requests](https://2.python-requests.org/en/master/)
- [Python MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Un locataire Azure Active Directory (Azure AD). Pour savoir comment obtenir un locataire Azure AD, consultez [Obtention d’un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
>
> Vous avez deux options pour démarrer votre application du guide de démarrage rapide : rapide (Option 1) et manuelle (Option 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>      - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `python-webapp`.
>      - Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
>      - Sous la section **URI de redirection**, dans la liste déroulante, sélectionnez la plateforme **Web**, puis définissez la valeur sur `http://localhost:5000/getAToken`.
>      - Sélectionnez **Inscription**. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
> 1. Dans le menu de gauche, choisissez **Certificats et secrets**, puis cliquez sur **Nouveau secret client** dans la section **Secrets client** :
>
>      - Tapez une description de la clé (pour le secret d’application de l’instance).
>      - Sélectionnez la durée de clé **Dans 1 an**.
>      - Quand vous cliquez sur **Ajouter**, la valeur de la clé s’affiche.
>      - Copiez la valeur de la clé. Vous en aurez besoin ultérieurement.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
>
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez :
>
> 1. Ajoutez une URL de réponse sous la forme `http://localhost:5000/getAToken`.
> 1. Créer un secret client.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-your-project"></a>Étape 2 : Télécharger votre projet

[Télécharger l'exemple de code](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Étape 3 : Configurer l’application

1. Extrayez le fichier zip dans un dossier local proche du dossier racine (par exemple, **C:\Azure-Samples**)
1. Si vous utilisez un environnement de développement intégré, ouvrez l’exemple dans votre IDE habituel (facultatif).
1. Ouvrez le fichier **app_config.py** qui se trouve dans le dossier racine, puis remplacez son contenu par l’extrait de code suivant :

```python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

> [!div renderon="docs"]
> Où :
>
> - `Enter_the_Application_Id_here` - est l’ID de l’application pour l’application que vous avez inscrite.
> - `Enter_the_Tenant_Info_Here` - correspond à l’une des options ci-dessous :
>   - Si votre application prend en charge **Mon organisation uniquement**, remplacez cette valeur par l’**ID de locataire** ou le **nom du locataire** (par exemple, contoso.onmicrosoft.com)
>   - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`
>   - Si votre application prend en charge **tous les utilisateurs de compte Microsoft**, remplacez cette valeur par `common`
> - `Enter_the_Client_Secret_Here` - correspond au **secret client** que vous avez créé dans **Certificats et secrets** pour l’application que vous avez inscrite.

#### <a name="step-4-run-the-code-sample"></a>Étape 4 : Exécuter l’exemple de code

- Vous devez installer la bibliothèque Python MSAL, le framework Flask, des Flask-Sessions pour la gestion des sessions côté serveur et la bibliothèque Requests avec PIP, comme suit :

```Shell
pip install msal
pip install flask
pip install Flask-Session
pip install requests
```

- Si la variable d’environnement pour Flask est déjà définie : Exécutez app.py à partir de l’interpréteur de commandes ou de la ligne de commande :

```Shell
python app.py
```

- Si la variable d’environnement pour Flask n’est pas définie :

    1. Tapez les commandes suivantes sur l’interpréteur de commandes ou la ligne de commande en accédant au répertoire du projet :

```Shell
export FLASK_APP=app.py
export FLASK_DEBUG=1
flask run
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
