---
title: Créer un point de terminaison SCIM pour l’approvisionnement des utilisateurs dans des applications à partir d’Azure Active Directory
description: Apprenez à développer un point de terminaison SCIM, à intégrer votre API SCIM à Azure AD et à automatiser le provisionnement des utilisateurs et des groupes dans vos applications cloud avec Azure Active Directory.
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
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689332"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutoriel : Développer un exemple de point de terminaison SCIM

Personne ne souhaite créer un nouveau point de terminaison à partir de zéro. Nous avons donc créé du [code de référence](https://aka.ms/scimreferencecode) pour vous permettre de bien démarrer avec [SCIM (System for Cross-domain Identity Management)](https://aka.ms/scimoverview). Vous pouvez rendre votre point de terminaison SCIM opérationnel sans code en seulement cinq minutes.

Ce tutoriel décrit comment déployer le code de référence SCIM dans Azure et le tester à l’aide de Postman ou en l’intégrant au client Azure Active Directory (Azure AD) SCIM. Ce tutoriel s’adresse aux développeurs qui cherchent à prendre en main SCIM ou à toute personne souhaitant tester un point de terminaison SCIM.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Déployer votre point de terminaison SCIM dans Azure.
> * Tester votre point de terminaison SCIM.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Déployer votre point de terminaison SCIM dans Azure

Les étapes fournies ici permettent de déployer le point de terminaison SCIM sur un service à l’aide de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) et [Azure App Service](../../app-service/index.yml). Le code de référence SCIM peut également être exécuté localement, hébergé par un serveur local ou déployé sur un autre service externe.

1. Accédez au [code de référence](https://github.com/AzureAD/SCIMReferenceCode) à partir de GitHub et sélectionnez **Clone or download** (Cloner ou télécharger).

1. Sélectionnez **Ouvrir dans le bureau** ou copiez le lien, ouvrez Visual Studio, puis sélectionnez **Cloner ou extraire le code** pour entrer le lien copié et effectuer une copie locale.

1. Dans Visual Studio, veillez à vous connecter au compte qui a accès à vos ressources d’hébergement.

1. Dans l’Explorateur de solutions, ouvrez *Microsoft.SCIM.sln* et cliquez avec le bouton droit sur le fichier *Microsoft.SCIM.WebHostSample*. Sélectionnez **Publier**.

    ![Capture d’écran montrant l’exemple de fichier.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Pour exécuter cette solution localement, double-cliquez sur le projet et sélectionnez **IIS Express** pour lancer le projet en tant que page web avec une URL d’hôte local.

1. Sélectionnez **Créer un profil** et assurez-vous que les options **App Service** et **Créer** sont sélectionnées.

    ![Capture d’écran montrant la fenêtre Publier.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Parcourez les options de la boîte de dialogue et renommez l’application avec le nom de votre choix. Ce nom est utilisé à la fois dans l’application et dans l’URL du point de terminaison SCIM.

    ![Capture d’écran montrant la création d’un App Service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Sélectionnez le groupe de ressources à utiliser et sélectionnez **Publier**.

    ![Capture d’écran montrant la publication d’un nouvel App Service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Accédez à l’application dans **Azure App Service** > **Configuration** et sélectionnez **Nouveau paramètre d’application** pour ajouter le paramètre *Token__TokenIssuer* avec la valeur `https://sts.windows.net/<tenant_id>/`. Remplacez `<tenant_id>` par l’ID de votre locataire Azure AD. Si vous souhaitez tester le point de terminaison SCIM à l’aide de [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), ajoutez un paramètre *ASPNETCORE_ENVIRONMENT* avec la valeur `Development`.

   ![Capture d’écran montrant la fenêtre Paramètres de l’application.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Lorsque vous testez votre point de terminaison avec une application d’entreprise dans le [portail Azure](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client), vous avez deux options. Vous pouvez conserver l’environnement dans `Development` et fournir le jeton de test à partir du point de terminaison `/scim/token`, ou vous pouvez remplacer l’environnement par `Production` et laisser le champ de jeton vide.

Et voilà ! Votre point de terminaison SCIM est désormais publié et vous pouvez utiliser l’URL Azure App Service pour tester le point de terminaison SCIM.

## <a name="test-your-scim-endpoint"></a>Tester votre point de terminaison SCIM

Les demandes adressées à un point de terminaison SCIM nécessitent une autorisation. La norme SCIM propose plusieurs options d’authentification et d’autorisation, notamment les cookies, l’authentification de base, l’authentification du client TLS ou l’une des méthodes énumérées dans le [document RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Veillez à éviter les méthodes non sécurisées, comme le nom d’utilisateur et le mot de passe, au profit d’une méthode plus sûre, comme OAuth. Azure AD prend en charge les jetons du porteur à longue durée de vie (pour les applications de la galerie et les autres) et l’octroi d’autorisation OAuth (pour les applications de la galerie).

> [!NOTE]
> Les méthodes d’autorisation fournies dans le référentiel sont à des fins de test uniquement. Quand vous effectuez l’intégration à Azure AD, vous pouvez réviser les conseils en matière d’autorisation. Consultez [Planifier le provisionnement d’un point de terminaison SCIM](use-scim-to-provision-users-and-groups.md).

L’environnement de développement active des fonctionnalités potentiellement dangereuses pour la production, comme le code de référence pour contrôler le comportement de la validation du jeton de sécurité. Le code de validation du jeton utilise un jeton de sécurité auto-signé et la clé de signature est stockée dans le fichier de configuration. Consultez le paramètre **Token:IssuerSigningKey** dans le fichier *appsettings.Development.json*.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Lorsque vous envoyez une requête **GET** au point de terminaison `/scim/token`, un jeton est émis à l’aide de la clé configurée. Ce jeton peut être utilisé en tant que jeton du porteur pour une autorisation ultérieure.

Le code de validation du jeton par défaut est configuré pour utiliser un jeton Azure AD et requiert que le locataire émetteur soit configuré à l’aide du paramètre **Token:TokenIssuer** dans le fichier *appsettings.json*.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Utiliser Postman pour tester les points de terminaison

Après avoir déployé le point de terminaison SCIM, vous pouvez le tester pour vous assurer qu’il est conforme à SCIM RFC. Cet exemple fournit un ensemble de tests dans Postman pour valider les opérations CRUD (création, lecture, mise à jour et suppression) sur les utilisateurs et les groupes, le filtrage, les mises à jour de l’appartenance à un groupe et la désactivation des utilisateurs.

Les points de terminaison se trouvent dans le répertoire `{host}/scim/` et vous pouvez utiliser des requêtes HTTP standard pour interagir avec eux. Pour modifier l’itinéraire `/scim/`, consultez *ControllerConstant.cs* dans **AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers**.

> [!NOTE]
> Vous ne pouvez utiliser que des points de terminaison HTTP pour les tests locaux. Le service de provisionnement Azure AD requiert que votre point de terminaison prenne en charge le protocole HTTPS.

1. Téléchargez [Postman](https://www.getpostman.com/downloads/) et démarrez l’application.
1. Copiez et collez ce lien dans Postman pour importer la collection de tests : `https://aka.ms/ProvisioningPostman`.

    ![Capture d’écran montrant l’importation de la collection de tests dans Postman.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Créez un environnement de test qui contient les variables suivantes :

   |Environnement|Variable|Valeur|
   |-|-|-|
   |Exécuter le projet localement à l’aide d’IIS Express|||
   ||**Serveur**|`localhost`|
   ||**Port**|`:44359` *(n’oubliez pas **`:`** )*|
   ||**Api**|`scim`|
   |Exécuter le projet localement à l’aide de Kestrel|||
   ||**Serveur**|`localhost`|
   ||**Port**|`:5001` *(n’oubliez pas **`:`** )*|
   ||**Api**|`scim`|
   |Héberger le point de terminaison dans Azure|||
   ||**Serveur**|*(entrez votre URL SCIM)*|
   ||**Port**|*(laissez vide)*|
   ||**Api**|`scim`|

1. Utilisez **Get Key** de la collection Postman pour envoyer une requête **GET** au point de terminaison de jeton et récupérer un jeton de sécurité à stocker dans la variable **token** pour les requêtes suivantes.

   ![Capture d’écran montrant le dossier Get Key de Postman.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Pour sécuriser un point de terminaison SCIM, vous avez besoin d’un jeton de sécurité avant de vous connecter. Le tutoriel utilise le point de terminaison `{host}/scim/token` pour générer un jeton auto-signé.

Et voilà ! Vous pouvez maintenant exécuter la collection **Postman** pour tester la fonctionnalité du point de terminaison SCIM.

## <a name="next-steps"></a>Étapes suivantes

Pour développer un point de terminaison de groupe et d’utilisateur conforme à SCIM avec interopérabilité pour un client, consultez [Implémentation du client SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutoriel : Développer et planifier l’approvisionnement pour un point de terminaison SCIM](use-scim-to-provision-users-and-groups.md)
> [Tutoriel : Configurer l’approvisionnement pour une application de galerie](configure-automatic-user-provisioning-portal.md)