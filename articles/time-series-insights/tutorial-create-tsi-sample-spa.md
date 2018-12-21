---
title: Créer une application web monopage Azure Time Series Insights | Microsoft Docs
description: Découvrez comment créer une application web monopage qui interroge et restitue les données à partir d’un environnement STI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: fccd509d4f16cee86d30feb0e838f1493cae4e0b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275837"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Didacticiel : Créer une application web monopage Azure Time Series Insights

Ce tutoriel vous guide tout au long du processus de création de votre propre application web monopage (SPA) pour accéder aux données TSI, modélisées d’après l’[exemple d’application Time Series Insights (TSI)](https://insights.timeseries.azure.com/clientsample). Ce tutoriel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * La conception de l’application
> * Inscription de votre application auprès d’Azure Active Directory (AD)
> * Comment générer, publier et tester votre application web 

## <a name="prerequisites"></a>Prérequis

Inscrivez-vous à un [abonnement Azure gratuit](https://azure.microsoft.com/free/), si vous n’en avez pas déjà un. 

Vous devrez également installer Visual Studio, si vous ne l’avez pas déjà fait. Pour ce tutoriel, vous pouvez [télécharger/installer la version gratuite de la communauté, ou une version d’essai gratuite](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Vue d’ensemble de la conception de l’application

Comme indiqué, l’exemple d’application TSI fournit la base pour la conception et le code utilisé dans ce tutoriel. Le code inclut l’utilisation de la bibliothèque JavaScript cliente TSI. La bibliothèque cliente TSI fournit une abstraction pour les deux catégories d’API principales :

- **Méthodes de wrapper pour appeler les API de requête TSI** : API REST qui vous permettent d’exécuter des requêtes pour obtenir des données TSI à l’aide d’expressions reposant sur JSON. Les méthodes sont organisées sous l’espace de noms `TsiClient.server` de la bibliothèque.
- **Méthodes pour créer et remplir plusieurs types de contrôles graphiques** : Méthodes utilisées pour visualiser les données TSI dans une page web. Les méthodes sont organisées sous l’espace de noms `TsiClient.ux` de la bibliothèque.

Ce tutoriel utilise également les données provenant de l’environnement TSI de l’exemple d’application. Pour plus d’informations sur la structure de l’exemple d’application TSI et son utilisation de la bibliothèque cliente TSI, reportez-vous au tutoriel [Explorer la bibliothèque cliente JavaScript de Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Inscrire l’application auprès d’Azure AD 

Avant de générer l’application, vous devez l’inscrire auprès d’Azure AD. L’inscription fournit la configuration de l’identité pour une application, ce qui lui permet de prendre en charge la console OAuth pour l’authentification unique. OAuth requiert que les applications monopage utilisent l’octroi d’autorisation « implicite », que vous mettrez à jour dans le manifeste d’application. Un manifeste d’application est une représentation JSON de la configuration de l’identité d’une application. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte d’abonnement Azure.  
1. Sélectionnez la ressource **Azure Active Directory** dans le volet gauche, puis **Inscriptions des applications**, puis **Inscription d’une nouvelle application** :  
   
   ![Inscription de l’application Azure AD via le Portail Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. Sur la page **Créer**, renseignez les paramètres requis :
   
   Paramètre|Description
   ---|---
   **Nom** | Fournissez un nom d’inscription explicite.  
   **Type d’application** | Étant donné que vous créez une application web SPA, conservez « application web/API ».
   **URL d’authentification** | Saisissez l’URL de la page d’accueil/de connexion de l’application. Étant donné que l’application sera hébergée dans Azure App Service (ultérieurement), vous devez utiliser une URL dans le domaine « https://azurewebsites.net ». Dans cet exemple, le nom est basé sur le nom de l’inscription.

   Lorsque vous avez terminé, cliquez sur **Créer** pour créer la nouvelle inscription de l’application.

   ![Inscription de l’application Azure AD via le Portail Azure : création](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Les applications de ressource fournissent les API REST pour l’utilisation par d’autres applications et sont également inscrites auprès d’Azure AD. Les API fournissent un accès granulaire/sécurisé pour les applications clientes en exposant les « étendues ». Étant donné que votre application appelle l’API « Azure Time Series Insights », vous devez spécifier les API et l’étendue, pour lesquelles des autorisations seront demandées/accordées lors de l’exécution. Sélectionnez **Paramètres**, puis **Autorisations requises**, puis **+ Ajouter** :

   ![Ajout d’autorisations Azure AD sur le portail Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. À partir de la page **Ajouter un accès d’API** , cliquez sur **1 Sélectionner une API** pour spécifier l’API TSI. Sur la page **Sélectionner une API**, saisissez « azure time » dans le champ de recherche. Puis, sélectionnez l’API « Azure Time Series Insights » dans la liste des résultats, puis cliquez sur **Sélectionner** : 

   ![Ajout d’autorisations Azure AD sur le portail Azure - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. À présent, vous spécifiez une étendue sur l’API. Toujours dans la page **Ajouter un accès d’API**, cliquez sur **2 Sélectionner des autorisations**. Sur la page **Activer l’accès**, sélectionnez l’étendue « Access Azure Time Series Insights service » (Accéder au service Azure Time Series Insights). Cliquez sur **Sélectionner**, ce qui renverra la page **Ajouter un accès d’API**, où vous cliquerez sur **Terminé** :

   ![Ajout d’autorisations Azure AD sur le portail Azure - Étendue](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Lorsque vous revenez à la page **Autorisations requises**, notez que l’API « Azure Time Series Insights » est désormais répertoriée. Vous devez également donner un consentement préalable à l’autorisation d’accès de l’application à l’API et à l’étendue, pour tous les utilisateurs. Cliquez sur le bouton **Accorder des autorisations** en haut, puis sélectionnez **Oui** :

   ![Autorisations requises Azure AD sur le portail Azure - Consentement](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Comme mentionné précédemment, vous devez également mettre à jour le manifeste d’application. Cliquez sur le nom de l’application dans la barre de navigation pour revenir à la page **Application inscrite**. Sélectionnez **Manifeste**, modifiez la propriété `oauth2AllowImplicitFlow` par `true`, puis cliquez sur **Enregistrer** :

   ![Mettre à jour le manifeste Azure AD sur le portail Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Enfin, cliquez sur la barre de navigation pour revenir de nouveau à la page **Application inscrite**, puis copiez les propriétés de l’URL de la **Page d’accueil** et de l’**ID de l’application** de votre application. Vous allez utiliser ces propriétés dans une étape ultérieure :

   ![Propriétés Azure AD du portail Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Générer et publier l’application web

1. Créez un répertoire pour stocker vos fichiers de projet d’application. Puis accédez à chacune des URL suivantes, cliquez avec le bouton droit de la souris sur le lien « Brut » dans l’angle supérieur droit de la page, puis « Enregistrer en tant que » dans votre répertoire de projet :

   > [!NOTE]
   > Selon le navigateur, vous devrez peut-être corriger l’extension de fichier (en HTML ou CSS) avant d’enregistrer le fichier.

   - **index.html** HTML et JavaScript pour la page https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css** : Feuille de style CSS : https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Démarrez et connectez-vous à Visual Studio, pour créer un projet d’application web. Sur le **Fichier** menu, sélectionnez les options **Ouvrir**, **Site web**. Sur la boîte de dialogue **Ouvrir le site Web**, sélectionnez le répertoire de travail où vous avez stocké les fichiers HTML et CSS, puis cliquez sur **Ouvrir** :

   ![VS - ouverture de fichiers site web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Ouvrez l’**Explorateur de solutions** à partir du menu **Affichage** de Visual Studio. Vous devriez voir votre nouvelle solution, contenant un projet de site web (icône globe), qui contient les fichiers HTML et CSS :

   ![VS - Nouvelle solution de l’Explorateur de solutions](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Avant de pouvoir publier l’application, vous devez mettre à jour des parties du code JavaScript dans **index.html** : 

   a. Tout d’abord, modifiez les chemins d’accès pour le code JavaScript et les fichiers de feuille de style de référence dans l’élément `<head>`. Ouvrez le fichier **index.html** dans votre solution Visual Studio et recherchez les lignes suivantes du code JavaScript. Supprimez les marques de commentaire dans les trois lignes sous « PROD RESOURCE LINKS » (LIENS DES RESSOURCES DE PRODUCTION) et commentez les trois lignes sous « DEV RESOURCE LINKS » (LIENS DES RESSOURCES DE DÉVELOPPEMENT) :
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Votre code modifié doit ressembler à l’exemple suivant :

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Ensuite, modifiez la logique de jeton d’accès pour utiliser votre nouvelle inscription d’application Azure AD. Modifiez les variables suivantes respectivement `clientID` et `postLogoutRedirectUri`, pour utiliser l’ID d’application et l’URL de la Page d’accueil copiées à l’étape 9 de la section [Inscrire l’application auprès d’Azure AD](#register-the-application-with-azure-ad) :

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Votre code modifié doit ressembler à l’exemple suivant :

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Enregistrez **index.html** une fois les modifications terminées.

1. Maintenant publiez l’application web dans votre abonnement Azure en tant qu’Azure App Service :  

   > [!NOTE]
   > Plusieurs champs des boîtes de dialogue suivantes sont remplis avec les données issues de votre abonnement Azure. Par conséquent, le chargement complet de chaque boîte de dialogue peut prendre quelques secondes, avant de pouvoir continuer.  

   a. Cliquez avec le bouton droit sur le nœud du projet de site web dans l’**Explorateur de solutions** et sélectionnez **Publier l’application web** :  

      ![VS - Publication de l’application web Explorateur de solutions](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Sélectionnez **Microsoft Azure App Service** pour créer une cible de publication :  

      ![VS - Profil de publication](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Sélectionnez l’abonnement que vous souhaitez utiliser pour la publication de l’application, puis cliquez sur « Nouveau » : 

      ![VS - Profil de publication - App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Patientez quelques secondes sur la boîte de dialogue **Créer un App Service** pour charger tous les champs, puis modifiez les champs suivants :
   
      Champ | Description
      ---|---
      **Nom de l’application** | Modifiez par le nom d’inscription d’application Azure AD que vous avez utilisé à l’étape 3 dans [Inscrire l’application auprès d’Azure AD](#register-the-application-with-azure-ad). 
      **Groupe de ressources** | À l’aide du bouton **Nouveau...**, modifiez pour faire correspondre le champ **Nom de l’application**.
      **Plan App Service** | À l’aide du bouton **Nouveau...**, modifiez pour faire correspondre le champ **Nom de l’application**.

      Lorsque vous avez terminé, cliquez sur **Créer**. Le bouton **Exporter** dans l’angle inférieur gauche est remplacé par « Déploiement : » pendant quelques secondes, pendant la création des ressources Azure. Une fois que les ressources sont créés, vous serez renvoyé vers la boîte de dialogue précédente. 

      ![VS - Profil de publication - Ajouter un nouvel App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Lorsque vous revenez à la boîte de dialogue **Publier**, assurez-vous que **Méthode de publication** est défini sur « Web deploy ». Modifiez également l’**URL de Destination** pour utiliser `https` au lieu de `http`, afin de correspondre avec l’inscription d’application Azure AD. Puis cliquez sur « Publier » pour déployer l’application web :  

      ![VS - Publication de l’application web - Publication du App Service](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Vous devriez voir un journal de publication réussie dans la fenêtre **Sortie** de Visual Studio. Une fois le déploiement terminé, Visual Studio ouvrira également l’application web dans un onglet de navigateur, demandant l’ouverture de session. Après la connexion réussie, tous les contrôles TSI seront remplis avec des données :  

      [![VS - Publication de l’application web - Publication de la sortie du journal](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![application monopage TSI - Connexion](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Résolution de problèmes  

Code d’erreur/condition | Description
---------------------| -----------
*AADSTS50011 : Aucune adresse de réponse n’est inscrite pour l’application.* | Il manque la propriété « URL de réponse » dans l’inscription Azure AD. Accédez à la page **Paramètres** / **URL de réponse** pour votre inscription d’application Azure AD. Vérifiez que l’URL **de connexion** spécifiée à l’étape 3 de [Inscrire l’application auprès d’Azure AD](#register-the-application-with-azure-ad) est présente. 
*AADSTS50011 : L’URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l’application : « <Application ID GUID> ».* | Le `postLogoutRedirectUri` spécifié à l’étape 4.b de [Générer et publier l’application web](#build-and-publish-the-web-application), doit correspondre à la valeur spécifiée sous la propriété **Paramètres** / **URL de réponse** dans votre inscription d’application Azure AD. Veillez à modifier également l’**URL de destination** pour utiliser `https`, conformément à l’étape 5.e de la section [Générer et publier l’application web](#build-and-publish-the-web-application).
L’application web charge, mais comporte une page de connexion textuelle non mise en forme et un arrière-plan blanc. | Vérifiez que les chemins d’accès indiqués dans l’étape 4.a de la section [Générer et publier l’application web](#build-and-publish-the-web-application) sont corrects. Si l’application web ne trouve pas les fichiers .css, la page ne sera pas mise en forme correctement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce tutoriel crée plusieurs services Azure en cours d’exécution. Si vous ne prévoyez pas de terminer cette série de tutoriels, nous vous recommandons de supprimer toutes les ressources pour éviter des coûts inutiles. 

Dans le menu gauche du portail Azure :

1. Cliquez sur l’icône **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour l’environnement TSI. En haut de la page, cliquez sur **Supprimer le groupe de ressources**, saisissez le nom du groupe de ressources, puis cliquez sur **Supprimer**. 
1. Cliquez sur l’icône **Groupes de ressources**, puis sélectionnez le groupe de ressources qui a été créé par l’accélérateur de la solution de simulation d’appareil. En haut de la page, cliquez sur **Supprimer le groupe de ressources**, saisissez le nom du groupe de ressources, puis cliquez sur **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * La conception de l’application
> * Inscription de votre application auprès d’Azure Active Directory (AD)
> * Comment générer, publier et tester votre application web 

Ce tutoriel s’intègre à Azure AD en utilisant l’identité de l’utilisateur connecté pour obtenir un jeton d’accès. Pour savoir comment accéder à l’API TSI à l’aide de l’identité d’une application de service ou démon, consultez :

> [!div class="nextstepaction"]
> [Authentification et autorisation pour l’API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
