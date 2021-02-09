---
title: Créer un point de terminaison SCIM pour l’approvisionnement des utilisateurs dans des applications à partir d’Azure Active Directory
description: Le système SCIM (Cross-domain Identity Management) normalise le provisionnement automatique des utilisateurs. Apprenez à développer un point de terminaison SCIM, à intégrer votre API SCIM à Azure Active Directory et à automatiser l’approvisionnement d’utilisateurs et de groupes dans vos applications cloud avec Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6b7451b0d664995a6b647f7926d856b0db6090d8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256100"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutoriel : Développer un exemple de point de terminaison SCIM

Personne ne souhaitant créer un nouveau point de terminaison à partir de zéro, nous avons créé un [code de référence](https://aka.ms/scimreferencecode) pour vous permettre de démarrer avec [SCIM](https://aka.ms/scimoverview). Ce tutoriel décrit comment déployer le code de référence SCIM dans Azure et le tester à l’aide de Postman ou en l’intégrant au client Azure AD SCIM. Vous pouvez faire fonctionner votre point de terminaison SCIM sans code en cinq minutes seulement. Ce tutoriel s’adresse aux développeurs qui cherchent à prendre en main SCIM ou à d’autres personnes souhaitant tester un point de terminaison SICM. 

Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer votre point de terminaison SCIM dans Azure
> * Tester votre point de terminaison SCIM

## <a name="deploy-your-scim-endpoint-in-azure"></a>Déployer votre point de terminaison SCIM dans Azure

Les étapes fournies ici permettent de déployer le point de terminaison SCIM sur un service à l’aide de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) et [Azure App Service](https://docs.microsoft.com/azure/app-service/). Le code de référence SCIM peut également être exécuté localement, hébergé par un serveur local ou déployé sur un autre service externe. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Ouvrir la solution et la déployer sur Azure App Service

1. Accédez au [code de référence](https://github.com/AzureAD/SCIMReferenceCode) à partir de GitHub et sélectionnez **Clone or download** (Cloner ou télécharger).

1. Choisissez **Ouvrir dans le bureau** ou copiez le lien, ouvrez **Visual Studio**, puis sélectionnez **Cloner ou extraire le code** pour entrer le lien copié et effectuer une copie locale.

1. Dans **Visual Studio**, assurez-vous de vous connecter au compte qui a accès à vos ressources d’hébergement.

1. Dans **Explorateur de solutions**, ouvrez *Microsoft.SCIM.sln* et cliquez avec le bouton droit sur le fichier *Microsoft.SCIM.WebHostSample*. Sélectionnez **Publier**.

    ![publication sur le cloud](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Pour exécuter cette solution localement, double-cliquez sur le projet et sélectionnez **IIS Express** pour lancer le projet en tant que page web avec une URL d’hôte local.

1. Sélectionnez **Créer un profil** et assurez-vous que **App Service** et **Créer nouveau** sont sélectionnés.

    ![publication sur le cloud 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Parcourez les options de la boîte de dialogue et renommez l’application avec le nom de votre choix. Ce nom est utilisé à la fois dans l’application et dans l’URL du point de terminaison SCIM.

    ![publication sur le cloud 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Sélectionnez le groupe de ressources à utiliser et choisissez **Publier**.

1. Accédez à l’application dans **Azure App Service** > **Configuration** et sélectionnez **Nouveau paramètre d’application** pour ajouter le paramètre *Token__TokenIssuer* avec la valeur `https://sts.windows.net/<tenant_id>/`. Remplacez `<tenant_id>` par votre tenant_id Azure AD et, si vous souhaitez tester le point de terminaison SCIM à l’aide de [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), ajoutez également un paramètre *ASPNETCORE_ENVIRONMENT* avec la valeur `Development`. 

   ![paramètres appservice](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Lorsque vous testez votre point de terminaison avec une application d’entreprise dans le portail Azure, choisissez de conserver l’environnement comme `Development` et fournissez le jeton généré à partir du point de terminaison `/scim/token` pour le test ou remplacez l’environnement par `Production` et laissez le champ de jeton vide dans l’application d’entreprise dans le [portail Azure](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Et voilà ! Votre point de terminaison SCIM est désormais publié et vous permet d’utiliser l’URL Azure App Service pour tester le point de terminaison SCIM.

## <a name="test-your-scim-endpoint"></a>Tester votre point de terminaison SCIM

Les demandes adressées à un point de terminaison SCIM nécessitent une autorisation et la norme SCIM laisse plusieurs options d’authentification et d’autorisation, telles que les cookies, l’authentification de base, l’authentification du client TLS ou l’une des méthodes énumérées dans la norme [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Veillez à éviter les méthodes non sécurisées, telles que le nom d’utilisateur/mot de passe, au profit d’une méthode plus sûre comme OAuth. Azure AD prend en charge les jetons du porteur à longue durée de vie (pour les applications de la galerie et celles non galerie) et l’autorisation OAuth (pour les applications publiées dans la galerie d’applications).

> [!NOTE]
> Les méthodes d’autorisation fournies dans le référentiel sont à des fins de test uniquement. Lors de l’intégration à Azure AD, vous pouvez consulter l’aide relative à l’autorisation, voir [Planifier l’approvisionnement d’un point de terminaison SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

L’environnement de développement active des fonctionnalités potentiellement dangereuses pour la production, telles que le code de référence pour contrôler le comportement de la validation du jeton de sécurité. Le code de validation du jeton est configuré pour utiliser un jeton de sécurité auto-signé, et la clé de signature est stockée dans le fichier config. Consultez le paramètre **Token:IssuerSigningKey** dans le fichier *appsettings.Development.json*.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> En envoyant une requête **GET** au point de terminaison `/scim/token`, un jeton est émis à l’aide de la clé configurée et peut être utilisé comme jeton du porteur pour l’autorisation suivante.

Le code de validation de jeton par défaut est configuré pour utiliser un jeton émis par Azure Active Directory et requiert que le locataire émetteur soit configuré à l’aide du paramètre **Token:TokenIssuer** dans le fichier *appsettings.json*.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Utiliser Postman pour tester les points de terminaison

Une fois le point de terminaison SCIM déployé, vous pouvez effectuer un test pour vous assurer qu’il est conforme à la norme RFC SCIM. Cet exemple fournit un ensemble de tests dans **Postman** pour valider les opérations CRUD sur les utilisateurs et les groupes, le filtrage, les mises à jour de l’appartenance à un groupe et la désactivation des utilisateurs.

Les points de terminaison se trouvent dans le répertoire `{host}/scim/` et peuvent être utilisés en interaction avec des requêtes HTTP standard. Pour modifier l’itinéraire `/scim/`, consultez *ControllerConstant.cs* dans **AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers**.

> [!NOTE]
> Vous ne pouvez utiliser des points de terminaison HTTP que pour les tests locaux, car le service d’approvisionnement Azure AD nécessite que votre point de terminaison prenne en charge le protocole HTTPS.

#### <a name="open-postman-and-run-tests"></a>Ouvrir Postman et exécuter des tests

1. Téléchargez [Postman](https://www.getpostman.com/downloads/) et démarrez l’application.
1. Copiez le lien [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) et collez-le dans Postman pour importer la collection de tests.

    ![collection Postman](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Créez un environnement de test avec les variables ci-dessous :

   |Environnement|Variable|Valeur|
   |-|-|-|
   |Exécuter le projet localement à l’aide d’IIS Express|||
   ||**Serveur**|`localhost`|
   ||**Port**|`:44359` *(n’oubliez pas les deux-points «  **:**  »)*|
   ||**Api**|`scim`|
   |Exécuter le projet localement à l’aide de Kestrel|||
   ||**Serveur**|`localhost`|
   ||**Port**|`:5001` *(n’oubliez pas les deux-points «  **:**  »)*|
   ||**Api**|`scim`|
   |Héberger le point de terminaison dans Azure|||
   ||**Serveur**|*(entrez votre URL SCIM)*|
   ||**Port**|*(laissez vide)*|
   ||**Api**|`scim`|

1. Utilisez **Get Key** de la collection Postman pour envoyer une requête **GET** au point de terminaison de jeton et récupérer un jeton de sécurité à stocker dans la variable **jeton** pour les requêtes suivantes. 

   ![postman get key](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Pour sécuriser les points de terminaison SCIM, vous avez besoin d’un jeton de sécurité avant d’établir la connexion, et le tutoriel utilise le point de terminaison `{host}/scim/token` pour générer un jeton auto-signé.

Et voilà ! Vous pouvez maintenant exécuter la collection **Postman** pour tester la fonctionnalité du point de terminaison SCIM.

## <a name="next-steps"></a>Étapes suivantes

Pour développer un point de terminaison de groupe et d’utilisateur conforme à SCIM avec interopérabilité pour un client, consultez l’[implémentation du client SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutoriel : Développer et planifier l’approvisionnement pour un point de terminaison SCIM](use-scim-to-provision-users-and-groups.md)
> [Tutoriel : Configurer l’approvisionnement pour une application de galerie](configure-automatic-user-provisioning-portal.md)