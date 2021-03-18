---
title: Protection du back-end d’une application SPA dans la Gestion des API Azure avec Azure Active Directory B2C
description: Protégez une API avec OAuth 2.0 en appelant Azure Active Directory B2C, la Gestion des API Azure et EasyAuth à partir d’une application SPA JavaScript à l’aide du flux d’authentification SPA compatible PKCE.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449286"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Protéger le back-end d’une application monopage avec OAuth 2.0 à l’aide d’Azure Active Directory B2C et de Gestion des API Azure

Ce scénario montre comment configurer votre instance de Gestion des API Azure pour protéger une API.
Nous allons utiliser le flux SPA Azure AD B2C (code d’authentification + PKCE) pour acquérir un jeton, en parallèle de la Gestion des API pour sécuriser un back-end Azure Functions avec EasyAuth.

## <a name="aims"></a>Objectifs

Nous allons voir comment le service Gestion des API peut être utilisé dans un scénario simplifié avec Azure Functions et Azure AD B2C. Vous allez créer une application JavaScript (JS) appelant une API, qui connecte les utilisateurs avec Azure AD B2C. Vous utiliserez ensuite les fonctionnalités de stratégie validate-jwt, CORS et Limitation du débit par clé de la Gestion des API pour protéger l’API back-end.

Pour une défense en profondeur, vous aurez ensuite recours à EasyAuth pour valider à nouveau le jeton à l’intérieur de l’API back-end et faire en sorte que la Gestion des API soit le seul service qui puisse appeler le back-end Azure Functions.

## <a name="what-will-you-learn"></a>Ce que vous allez apprendre

> [!div class="checklist"]
> * Configuration d’une application monopage et d’une API back-end dans Azure Active Directory B2C
> * Création d’une API back-end Azure Functions
> * Importation d’une API Azure Functions dans la Gestion des API Azure
> * Sécurisation de l’API dans la Gestion des API Azure
> * Appel des points de terminaison d’autorisation Azure Active Directory B2C au moyen des bibliothèques de la Plateforme d’identités Microsoft (MSAL.js)
> * Stockage d’une application monopage JS HTML/Vanilla et traitement de celle-ci à partir d’un point de terminaison de Stockage Blob Azure

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez avoir :

* Un compte de stockage universel v2 (StorageV2) Azure pour héberger l’application monopage JS front-end
* Une instance Gestion des API Azure (tous les niveaux fonctionnent, y compris « Consommation », mais certaines fonctionnalités applicables au scénario complet ne sont pas disponibles dans ce niveau, à savoir la Limitation du débit par clé et l’Adresse IP virtuelle dédiée ; ces restrictions sont indiquées ci-dessous dans l’article, le cas échéant)
* Une application de fonction Azure vide (exécutant le runtime .NET Core v3.1, sur un plan Consommation) pour héberger l’API appelée
* Un locataire Azure AD B2C lié à un abonnement

Bien que dans la pratique vous utiliseriez des ressources de la même région dans les charges de travail de production, pour cet article de procédures, la région de déploiement n’a pas d’importance.

## <a name="overview"></a>Vue d’ensemble

Voici une illustration des composants utilisés et du flux qui les relie une fois ce processus terminé.
![Composants utilisés et flux](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Composants utilisés et flux")

Voici un petit aperçu de la procédure :

1. Créer les composants Azure AD B2C appelants (front-end, Gestion des API) et les applications API avec des étendues, et accorder l’accès à l’API
1. Créer les stratégies d’inscription et de connexion pour permettre aux utilisateurs de se connecter avec Azure AD B2C 
1. Configurer Gestion des API avec les nouveaux ID clients et clés Azure AD B2C pour activer l’autorisation utilisateur OAuth2 dans la console de développement
1. Générer l’API de fonction
1. Configurer l’API de fonction pour activer EasyAuth avec les nouveaux ID clients et clés Azure AD B2C, et verrouiller sur l’adresse IP virtuelle APIM
1. Générer la définition d’API dans Gestion des API
1. Configurer Oauth2 pour la configuration de l’API Gestion des API
1. Configurer la stratégie **CORS** et ajouter une stratégie **validate-jwt** afin de valider le jeton OAuth pour chaque requête entrante
1. Générer l’application appelante pour consommer l’API
1. Charger l’exemple d’application monopage JS
1. Configurer l’exemple d’application cliente JS avec les nouveaux ID clients et clés Azure AD B2C 
1. Tester l’application cliente

   > [!TIP]
   > Nous allons capturer un certain nombre d’informations, de clés, etc., au fil de ce document. Il peut être utile d’ouvrir un éditeur de texte pour stocker temporairement les éléments de configuration suivants.  
   >
   > ID CLIENT DU BACK-END B2C :  
   > CLÉ SECRÈTE CLIENT DU BACK-END B2C :  
   > URI D’ÉTENDUE DE L’API BACK-END B2C :  
   > ID CLIENT DU FRONT-END B2C :  
   > URI DU POINT DE TERMINAISON DU FLUX D’UTILISATEUR B2C :  
   > POINT DE TERMINAISON OPENID RECONNU B2C :   
   > NOM DE LA STRATÉGIE B2C : Frontendapp_signupandsignin
   > URL DE LA FONCTION :  
   > URL DE BASE DE L’API APIM :
   > URL DU POINT DE TERMINAISON PRINCIPAL DE STOCKAGE :  

## <a name="configure-the-backend-application"></a>Configuration de l’application back-end

Ouvrez le panneau Azure AD B2C dans le portail et effectuez les étapes ci-dessous.

1. Sélectionnez l’onglet **Inscriptions d’applications**.
1. Cliquez sur le bouton « Nouvelle inscription ».
1. Choisissez « Web » dans la zone de sélection de l’URI de redirection.
1. Maintenant, définissez le Nom d’affichage : choisissez un nom unique correspondant au service créé. Dans cet exemple, nous allons utiliser le nom « Application back-end ».
1. Utilisez des espaces réservés pour les URL de réponse, par exemple « https://jwt.ms » (un site de décodage de jetons appartenant à Microsoft). Nous mettrons à jour ces URL ultérieurement.
1. Veillez à sélectionner l’option « Comptes de n’importe quel fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateur) ».
1. Dans le cadre de cet exemple, décochez la case « Accorder le consentement administrateur », car nous n’aurons pas besoin des autorisations offline_access.
1. Cliquez sur « Inscrire ».
1. Enregistrez l’ID client de l’application back-end, indiqué sous « ID (client) de l’application », pour plus tard.
1. Sélectionnez l’onglet *Certificats et secrets* (sous Gérer), puis cliquez sur « Nouvelle clé secrète client » pour générer une clé d’authentification (acceptez les paramètres par défaut et cliquez sur « Ajouter »).
1. Lorsque vous cliquez sur « Ajouter », copiez la clé (sous « valeur ») dans un emplacement sécurisé pour pouvoir l’utiliser plus tard comme « Clé secrète client back-end ». Cette boîte de dialogue constitue la SEULE possibilité que vous aurez de copier cette clé.
1. Sélectionnez maintenant l’onglet *Exposer une API* (sous Gérer).
1. Il vous est demandé de définir l’URI AppID, de sélectionner la valeur par défaut et de l’enregistrer.
1. Créez et nommez l’étendue « Hello » pour votre API de fonction. Vous pouvez utiliser l’expression « Hello » pour toutes les options à entrer, en enregistrant l’URI de valeur d’étendue complète rempli. Ensuite, cliquez sur « Ajouter une étendue ».
1. Revenez à la racine du panneau Azure AD B2C en sélectionnant « Azure AD B2C » dans la barre de navigation en haut à gauche du portail.

   > [!NOTE]
   > Les étendues Azure AD B2C sont en fait des autorisations au sein de votre API auxquelles d’autres applications peuvent demander l’accès par le biais du panneau d’accès d’API à partir de leurs applications. Concrètement, vous venez de créer des autorisations d’application pour votre API appelée.

## <a name="configure-the-frontend-application"></a>Configuration de l’application front-end

1. Sélectionnez l’onglet **Inscriptions d’applications**.
1. Cliquez sur le bouton « Nouvelle inscription ».
1. Choisissez « Application monopage (SPA) » dans la zone de sélection de l’URI de redirection.
1. Maintenant, définissez le Nom d’affichage et l’URI AppID : choisissez un nom unique correspondant à l’application front-end qui utilisera cette inscription d’application AAD B2C. Dans cet exemple, vous pouvez utiliser « Application front-end ».
1. Comme pour la première inscription d’application, laissez la sélection par défaut des types de comptes pris en charge (authentification des utilisateurs avec des flux d’utilisateurs).
1. Utilisez des espaces réservés pour les URL de réponse, par exemple « https://jwt.ms » (un site de décodage de jetons appartenant à Microsoft). Nous mettrons à jour ces URL ultérieurement.
1. Laissez la case Accorder le consentement Administrateur cochée.
1. Cliquez sur « Inscrire ».
1. Enregistrez l’ID client de l’application front-end, indiqué sous « ID (client) de l’application », pour plus tard.
1. Basculer sur l’onglet *Autorisations de l’API*.
1. Accordez l’accès à l’application back-end. Pour cela, cliquez sur « Ajouter une autorisation », puis sur « Mes API », sélectionnez « Application back-end », puis « Autorisations », sélectionnez l’étendue que vous avez créée dans la section précédente et enfin cliquez sur « Ajouter des autorisations ».
1. Cliquez sur « Accorder le consentement administrateur pour {locataire} », puis cliquez sur « Oui » dans la boîte de dialogue contextuelle. Cette fenêtre contextuelle autorise l’« Application front-end » à utiliser l’autorisation « Hello » définie dans l’« Application back-end » créée précédemment.
1. Toutes les autorisations devraient maintenant s’afficher pour l’application sous la forme d’une coche verte dans la colonne État.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Création d’un flux d’utilisateur « Inscription et connexion »

1. Revenez à la racine du panneau B2C en sélectionnant Azure AD B2C dans la barre de navigation.
1. Basculez vers l’onglet « Flux d’utilisateurs » (sous Stratégies).
1. Cliquez sur « Nouveau flux d’utilisateur ».
1. Choisissez le type de flux d’utilisateur « Inscription et connexion », puis sélectionnez « Recommandé », puis « Créer ».
1. Donnez un nom à la stratégie et prenez-en note pour une utilisation ultérieure. Dans le cadre de cet exemple, vous pouvez utiliser « Frontendapp_signupandsignin ». Notez que le préfixe « B2C_1_ » est ajouté, ce qui donne « B2C_1_Frontendapp_signupandsignin ».
1. Sous « Fournisseurs d’identité » et « Comptes locaux », cochez la case « Inscription par e-mail » (ou « Inscription de l’ID d’utilisateur » en fonction de la configuration de votre client B2C), puis cliquez sur OK. Nous allons en effet enregistrer des comptes B2C locaux, et non les différer vers un autre fournisseur d’identité (par exemple un fournisseur d’identité sociale) pour utiliser le compte de réseau social existant d’un utilisateur.
1. Laissez les paramètres par défaut pour l’authentification MFA et l’accès conditionnel.
1. Sous « Attributs utilisateur et revendications », cliquez sur « Afficher plus... », puis choisissez les options de revendication que vous souhaitez que vos utilisateurs entrent et qui doivent être retournées dans le jeton. Cochez au moins « Nom d’affichage » et « Adresse e-mail » (au singulier) à collecter, avec « Nom d’affichage » et « Adresses e-mail » (au pluriel) à retourner, puis cliquez sur « OK » et sur « Créer ».
1. Cliquez sur le flux d’utilisateur que vous avez créé dans la liste, puis sur le bouton « Exécuter le flux d’utilisateur ».
1. Le panneau Exécuter le flux d’utilisateur s’ouvre. Sélectionnez l’application front-end, copiez le point de terminaison du flux d’utilisateur et enregistrez-le pour plus tard.
1. Copiez et stockez le lien situé en haut, en l’enregistrant comme « Point de terminaison de configuration OpenID reconnu » pour plus tard.

   > [!NOTE]
   > Les stratégies B2C vous permettent d’exposer les points de terminaison de connexion Azure AD B2C pour pouvoir capturer différents composants de données et connecter des utilisateurs de différentes manières.
   > 
   > Dans ce cas, nous avons configuré un flux d’inscription ou de connexion (stratégie). A également été exposé un point de terminaison de configuration reconnu. Dans les deux cas, notre stratégie créée a été identifiée dans l’URL par le paramètre de chaîne de requête « p = ».  
   >
   > Vous disposez au bout du compte d’une Plateforme d’identités entreprise-client fonctionnelle qui connecte les utilisateurs à plusieurs applications.

## <a name="build-the-function-api"></a>Générer l’API de fonction

1. Revenez à votre locataire Azure AD standard sur le Portail Azure pour pouvoir reconfigurer des éléments de votre abonnement.
1. Accédez au panneau Applications de fonction du Portail Azure, ouvrez votre application de fonction vide, puis cliquez sur « Fonctions » et sur « Ajouter ».
1. Dans le menu volant qui s’affiche, choisissez « Développer sur le portail » sous « Sélectionner un modèle », puis « Déclencheur HTTP » sous Détails du modèle. Nommez-le « hello » avec le niveau d’autorisation « Fonction », puis sélectionnez Ajouter.
1. Basculez vers le panneau Code + test, puis copiez-collez l’exemple de code ci-dessous *sur le code existant* qui s’affiche.
1. Sélectionnez Enregistrer.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > Le code de fonction de script C# que vous venez de coller enregistre simplement une ligne dans les journaux de fonctions et retourne le texte « Hello World » avec des données dynamiques (date et heure).

1. Sélectionnez « Intégration » dans le panneau de gauche, puis cliquez sur le lien HTTP (requête) à l’intérieur de la zone « Déclencheur ».
1. Dans la liste déroulante « Méthodes HTTP sélectionnées », décochez la méthode HTTP POST pour ne laisser que GET sélectionné, puis cliquez sur Enregistrer.
1. Revenez à l’onglet Code + test, cliquez sur « Obtenir l’URL de la fonction », puis copiez l’URL qui s’affiche et enregistrez-la pour plus tard.

   > [!NOTE]
   > Les liaisons que vous venez de créer imposent simplement à Functions de répondre aux requêtes HTTP GET anonymes envoyées à l’URL copiée (`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`). Nous disposons maintenant d’une API HTTPS serverless scalable et capable de retourner une charge utile très simple.
   >
   > Vous pouvez à présent tester l’appel de cette API à partir d’un navigateur web en utilisant votre version de l’URL ci-dessus, que vous venez de copier et d’enregistrer. Vous pouvez également supprimer la partie « ?code=secretkey » des paramètres de chaîne de requête de l’URL, puis effectuer un nouveau test pour prouver qu’Azure Functions retourne une erreur 401.

## <a name="configure-and-secure-the-function-api"></a>Configurer et sécuriser l’API de fonction

1. Deux zones supplémentaires de l’application de fonction doivent être configurées (Autorisation et Restrictions de réseau).
1. Nous allons d’abord configurer Authentification/autorisation. Revenez au panneau racine de l’application de fonction grâce à la barre de navigation.
1. Sélectionnez ensuite « Authentification/autorisation » (sous « Paramètres »).
1. Activez la fonctionnalité Authentification App Service.
1. Définissez la liste déroulante Action à effectuer quand une demande n’est pas authentifiée sur « Se connecter avec Azure Active Directory ».
1. Sous « Fournisseurs d’authentification », sélectionnez « Azure Active Directory ».
1. Choisissez « Avancé » dans le commutateur Mode d’administration.
1. Collez l’ID client [Application] de l’application back-end (à partir d’Azure AD B2C) dans la zone « ID client ».
1. Collez le Point de terminaison de configuration OpenID reconnu de la stratégie d’inscription et de connexion vers la zone URL de l’émetteur (nous avions enregistré cette configuration).
1. Cliquez sur « Afficher le secret », puis collez la clé secrète client de l’application back-end dans la zone correspondante.
1. Sélectionnez OK. Vous revenez ainsi au panneau/à l’écran de sélection du fournisseur d’identité.
1. Laissez [Magasin de jetons](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) activé sous Paramètres avancés (par défaut).
1. Cliquez sur « Enregistrer » (en haut à gauche du panneau).

   > [!IMPORTANT]
   > Votre API Function, maintenant déployée, devrait retourner des réponses 401 si le jeton JWT fourni comme en-tête Authorization: Bearer n’est pas le bon, et des données quand une demande valide est présentée.  
   > Vous avez ajouté une sécurité supplémentaire de défense approfondie dans EasyAuth en configurant l’option « Se connecter avec Azure Active Directory » pour gérer les demandes non authentifiées. N’oubliez pas que cela modifiera le comportement des demandes non autorisées entre l’application de fonction principale et l’application monopage frontale, car EasyAuth émettra une redirection 302 vers AAD au lieu d’une réponse 401 Non autorisée. Nous corrigerons cela ultérieurement en utilisant Gestion des API.  
   >
   > Aucune sécurité IP n’est encore appliquée ; si on dispose d’une clé et d’un jeton OAuth2 valides, tout le monde peut appeler cette API depuis n’importe où. Dans l’idéal, nous souhaitons forcer toutes les requêtes à venir par le biais de Gestion des API.  
   > 
   > Si vous utilisez le niveau Consommation APIM, [il n’y a pas d’adresse IP virtuelle Gestion des API Azure dédiée](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) à ajouter sur liste verte avec les fonctions de restrictions d’accès. Dans la référence SKU Standard de la Gestion des API Azure et les références supérieures, [l’adresse IP virtuelle est à locataire unique et pour la durée de vie de la ressource](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Avec le niveau Consommation de la Gestion des API Azure, vous pouvez verrouiller vos appels d’API au moyen de la clé de fonction de secret partagé dans la partie de l’URI que vous avez copiée ci-dessus. Par ailleurs, les étapes 12-17 ci-dessous ne s’appliquent pas au niveau Consommation.

1. Fermez le panneau « Authentification / Autorisation ». 
1. Ouvrez le *panneau Gestion des API du portail*, puis *votre instance*.
1. Enregistrez l’adresse IP virtuelle privée indiquée dans l’onglet Vue d’ensemble.
1. Revenez au panneau *Azure Functions du portail*, puis rouvrez *votre instance*.
1. Sélectionnez « Mise en réseau », puis « Configurer les restrictions d’accès ».
1. Cliquez sur « Ajouter une règle », puis entrez l’adresse IP virtuelle copiée à l’étape 3 ci-dessus au format xx.xx.xx.xx/32.
1. Si vous souhaitez continuer à interagir avec le portail Functions et effectuer les étapes facultatives ci-dessous, vous devez également ajouter ici votre propre plage CIDR ou adresse IP publique.
1. Une fois que la liste contient une entrée d’autorisation, Azure ajoute une règle de refus implicite afin de bloquer toutes les autres adresses.

Vous devez ajouter des blocs d’adresses au format CIDR au panneau de restrictions d’adresses IP. Quand vous devez ajouter une adresse unique telle que l’adresse IP virtuelle de Gestion des API, vous devez l’ajouter au format xx.xx.xx.xx/32.

   > [!NOTE]
   > Désormais, votre API de fonction ne devrait plus pouvoir être appelée à partir d’un emplacement autre que par le biais de Gestion des API, ou votre adresse.

1. Ouvrez le *panneau Gestion des API*, puis ouvrez *votre instance*.
1. Sélectionnez le panneau API (sous API).
1. Dans le volet « Ajouter une nouvelle API », choisissez « Function App », puis « Complète » dans la partie supérieure de la fenêtre contextuelle.
1. Cliquez sur Parcourir, choisissez l’application de fonction dans laquelle vous hébergez l’API, puis cliquez sur Sélectionner. Ensuite, cliquez à nouveau sur Sélectionner.
1. Donnez un nom et une description à l’API pour l’utilisation interne de Gestion des API, et ajoutez-la au produit « unlimited ».
1. Copiez et enregistrez l’« URL de base » de l’API, puis cliquez sur « Créer ».
1. Cliquez sur l’onglet « Paramètres », puis, sous Abonnement, décochez la case « Abonnement requis », car nous allons dans ce cas utiliser le jeton JWT OAuth pour limiter le débit. Notez que, si vous utilisez le niveau Consommation, cela resterait nécessaire dans un environnement de production. 

   > [!TIP]
   > Si vous utilisez le niveau Consommation APIM, le produit Illimité ne sera pas disponible d’emblée. Accédez à « Produits » sous « API », puis appuyez sur « Ajouter ».  
   > Tapez « Illimité » comme nom et description du produit, puis sélectionnez l’API que vous venez d’ajouter à partir de la légende « + » des API en bas à gauche de l’écran. Cochez la case « Publié ». Conservez les autres valeurs par défaut. Enfin, appuyez sur le bouton « Créer ». Le produit « Illimité » a été créé et affecté à votre API. Vous pourrez le personnaliser plus tard.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Configuration et capture des bons paramètres de point de terminaison de stockage

1. Ouvrez le panneau des comptes de stockage dans le portail Azure. 
1. Sélectionnez le compte que vous avez créé et sélectionnez le panneau « Site web statique » dans la section Paramètres (si aucune option « Site web statique » n’est visible, vérifiez que vous avez créé un compte V2).
1. Affectez la valeur « activé » à la fonctionnalité d’hébergement web statique, affectez « index.html » comme nom du document d’index, puis cliquez sur « Enregistrer ».
1. Notez le contenu du « Point de terminaison principal » pour plus tard, car il s’agit de l’emplacement où le site front-end sera hébergé.

   > [!TIP]
   > Vous pouvez utiliser Stockage Blob Azure + la réécriture CDN ou bien Azure App Service pour héberger l’application SPA. Cependant, la fonctionnalité d’hébergement de site web statique du Stockage Blob fournit un conteneur par défaut pour traiter le contenu web statique/HTML/JS/CSS à partir du Stockage Azure et produit automatiquement une page par défaut.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurer les stratégies **CORS** et **validate-jwt**

> Les instructions des sections suivantes doivent être suivies quel que soit le niveau APIM utilisé. L’URL du compte de stockage provient du compte de stockage que vous aurez mis à disposition dans la partie des prérequis en haut de cet article.
1. Basculez vers le panneau Gestion des API du portail, puis ouvrez votre instance.
1. Sélectionnez API, puis « Toutes les API ».
1. Sous « Traitement entrant », cliquez sur le bouton du mode Code « </> » pour afficher l’éditeur de stratégie.
1. Modifiez la section entrante et collez le code XML ci-dessous pour qu’il ressemble à ce qui suit.
1. Remplacez les paramètres suivants dans la stratégie :
1. {PrimaryStorageEndpoint} (« Point de terminaison de stockage principal » copié dans la section précédente), {b2cpolicy-well-known-openid} (« Point de terminaison de configuration OpenID reconnu » copié précédemment) et {backend-api-application-client-id} (application B2C/ID client de **l’API back-end**) par les valeurs correctes enregistrées précédemment.
1. Si vous utilisez le niveau Consommation de la Gestion des API Azure, supprimez la stratégie Limitation du débit par clé, car elle n’est pas disponible avec ce niveau.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > La Gestion des API est maintenant capable de répondre aux demandes cross-origin des applications SPA JavaScript. Elle effectue la limitation, la limitation du débit et la prévalidation du jeton d’authentification JWT transmis AVANT de transférer la requête à l’API de fonction.
   > 
   > Félicitations, vous avez fait en sorte qu’Azure AD B2C, la Gestion des API et Azure Functions fonctionnent en synergie pour publier, sécuriser ET consommer une API !

   > [!TIP]
   > Si vous utilisez le niveau Consommation de la Gestion des API, au lieu de la limitation du débit par le sujet du jeton JWT ou l’adresse IP entrante, vous pouvez effectuer une limitation par quota de débit d’appels (la stratégie Limitation du débit d’appels par clé n’est pas prise en charge actuellement pour le niveau « Consommation »). [Cliquez ici](./api-management-access-restriction-policies.md#LimitCallRate) pour en savoir plus.  
   > Cet exemple étant une application monopage JavaScript, la clé Gestion des API n’est utilisée que pour les appels de limitation de débit et de facturation. L’autorisation et l’authentification proprement dites sont gérées par Azure AD B2C et encapsulées dans le jeton JWT. Celui-ci est validé deux fois, d’abord par la Gestion des API, puis par la fonction Azure back-end.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Chargement de l’exemple d’application SPA JavaScript dans un stockage statique

1. Toujours dans le panneau du compte de stockage, sélectionnez le panneau « Conteneurs » dans la section Service blob, puis cliquez sur le conteneur $web qui apparaît dans le volet droit.
1. Enregistrez le code ci-dessous dans un fichier local sur votre ordinateur sous le nom index.html, puis chargez le fichier index.html dans le conteneur $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Accédez au point de terminaison principal du site web statique que vous avez stocké dans la dernière section.

   > [!NOTE]
   > Félicitations, vous venez de déployer une application monopage JavaScript sur l’hébergement de contenu statique du Stockage Azure.  
   > Étant donné que nous n’avons pas encore configuré l’application JS avec vos informations Azure AD B2C, la page ne fonctionne pas si vous l’ouvrez.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Configuration de l’application SPA JavaScript pour Azure AD B2C

1. Maintenant que nous savons où tout se trouve, nous pouvons configurer l’application SPA avec l’adresse de l’API Gestion des API appropriée et les bons ID client/d’application Azure AD B2C.
1. Revenez au panneau Stockage du Portail Azure. 
1. Sélectionnez « Conteneurs » (sous « Paramètres »). 
1. Sélectionnez le conteneur « $web » dans la liste.
1. Sélectionnez l’objet blob index.html dans la liste. 
1. Cliquez sur « Modifier ». 
1. Mettez à jour les valeurs d’authentification de la section de configuration MSAL pour qu’elles correspondent à votre application *front-end* que vous avez inscrite dans B2C plus tôt. Utilisez les commentaires de code pour obtenir des indications sur les valeurs de configuration.
La valeur *authority* doit être au format https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantname}.onmicrosoft.com}/{signupandsigninpolicyname}. Si vous avez utilisé nos exemples de noms et que votre locataire B2C est appelé « contoso », l’autorité devrait être « https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin ».
1. Définissez les valeurs de l’API pour qu’elles correspondent à votre adresse back-end (l’URL de base de l’API et les valeurs « b2cScopes » ont été enregistrées précédemment pour *l’application back-end*).
1. Cliquez sur Enregistrer.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Définir les URI de redirection pour l’application front-end Azure AD B2C

1. Ouvrez le panneau Azure AD B2C, puis accédez à l’inscription d’application pour l’application front-end JavaScript.
1. Cliquez sur « URI de redirection », puis supprimez l’espace réservé « https://jwt.ms » entré précédemment.
1. Ajoutez un nouvel URI pour le point de terminaison (de stockage) principal (sans la barre oblique de fin).

   > [!NOTE]
   > Cette configuration entraînera la réception, par un client de l’application front-end, d’un jeton d’accès avec les revendications appropriées en provenance d’Azure AD B2C.  
   > L’application monopage sera en mesure de l’ajouter en tant que jeton du porteur dans l’en-tête HTTPS de l’appel à l’API back-end.  
   > 
   > La Gestion des API prévalide le jeton, limite le débit des appels au point de terminaison à la fois par le sujet du jeton JWT émis par l’ID Azure (l’utilisateur) et par l’adresse IP de l’appelant (selon le niveau de service de la Gestion des API, cf. remarque ci-dessus), avant de transmettre la demande à l’API de la fonction Azure réceptrice, en ajoutant la clé de sécurité de la fonction.  
   > L’application monopage affichera la réponse dans le navigateur.
   >
   > *Félicitations, vous avez configuré Azure AD B2C, Gestion des API Azure, Azure Functions et l’autorisation Azure App Service pour opérer en parfaite harmonie !*

Maintenant que nous avons une application simple avec une API sécurisée simple, testons-la.

## <a name="test-the-client-application"></a>Tester l’application cliente

1. Ouvrez l’URL de l’exemple d’application que vous avez notée à partir du compte de stockage créé plus tôt.
1. Cliquez sur « Sign In » dans le coin supérieur droit pour afficher votre profil d’inscription ou de connexion Azure AD B2C.
1. L’application devrait vous accueillir par le nom de votre profil B2C.
1. Cliquez maintenant sur « Appeler l’API ». La page devrait se mettre à jour avec les valeurs retournées à partir de votre API sécurisée.
1. Si vous cliquez à *plusieurs reprises* sur le bouton Appeler l’API avec le niveau Développeur (ou un niveau supérieur) de la Gestion des API, votre solution commence à limiter le débit de l’API. Cette fonctionnalité devrait être signalée dans l’application avec un message approprié.

## <a name="and-were-done"></a>Nous avons terminé.

Les étapes ci-dessus peuvent être adaptées et modifiées pour autoriser de nombreuses utilisations différentes d’Azure AD B2C avec Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Active Directory et OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
* Pour les autres méthodes permettant de sécuriser votre service principal, consultez [Authentification mutuelle des certificats](api-management-howto-mutual-certificates.md).
* [Création d’une instance du service Gestion des API](get-started-create-service-instance.md).
* [Gérer votre première API](import-and-publish.md).
