---
title: Comment configurer Postman - Azure Digital Twins | Microsoft Docs
description: Découvrez comment configurer et utiliser Postman pour tester les API Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023308"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Guide pratique pour configurer Postman pour Azure Digital Twins

Cet article décrit comment configurer le client Postman REST pour interagir avec et tester les API de gestion Azure Digital Twins. Il décrit plus précisément :

* Comment configurer une application Azure Active Directory pour qu’elle utilise le flux d’octroi implicite OAuth 2.0.
* Comment utiliser le client Postman REST pour effectuer des requêtes HTTP porteuses de jeton à vos API de gestion.
* Comment utiliser Postman pour effectuer des requêtes POST multipart sur vos API de gestion.

## <a name="postman-summary"></a>Postman en bref

Commencez avec Azure Digital Twins en utilisant un outil client REST comme [Postman](https://www.getpostman.com/) pour préparer votre environnement de test local. Le client Postman vous aide à créer rapidement des requêtes HTTP complexes. Téléchargez la version bureau du client Postman en accédant à l’adresse [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) est un outil de test REST qui trouve les principales fonctionnalités de requête HTTP dans une interface graphique utilisateur utile de type Bureau à base de plug-ins.

Avec le client Postman, les développeurs de solutions peuvent spécifier le type de requête HTTP (*POST*, *GET*, *UPDATE*, *PATCH* et *DELETE*), le point de terminaison d’API à appeler et l’utilisation de SSL. Postman prend aussi en charge l’ajout d’en-têtes, de paramètres, de données de formulaire et de corps de requête HTTP.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurer Azure Active Directory pour utiliser le flux d’octroi implicite OAuth 2.0

1. Suivez les étapes du [guide de démarrage rapide](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) pour créer et configurer une application Azure Active Directory. Vous pouvez aussi réutiliser une inscription d’application existante.

    [![Configurer un nouvel URI de redirection Postman](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. À présent, ajoutez un **URI de redirection** à `https://www.getpostman.com/oauth2/callback`.

1. Cochez la case **Octroi implicite** > **Jetons d’accès** pour permettre l’utilisation du flux d’octroi implicite OAuth 2.0. Sélectionnez **Configurer**, puis **Enregistrer**.

1. Copiez l’**ID de client** de votre application Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Obtenir un jeton OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configurez Postman pour obtenir un jeton Azure Active Directory. Après quoi, adressez une requête HTTP authentifiée à Azure Digital Twins en utilisant le jeton acquis :

1. Vérifiez que votre **URL d’autorisation** est correcte. Elle doit prendre la forme suivante :

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Remplacer par | Exemple |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nom de votre locataire ou organisation. Utilisez le nom convivial plutôt que l'**ID de locataire** alphanumérique de l'inscription de votre application Azure Active Directory. | `microsoft` |

1. Accédez à [www.getpostman.com](https://www.getpostman.com/) pour télécharger l’application.

1. Nous souhaitons effectuer une requête GET. Sélectionnez l’onglet **Autorisation**, OAuth 2.0, puis **Obtenir un nouveau jeton d’accès**.

    | Champ  | Valeur |
    |---------|---------|
    | Type d’autorisation | `Implicit` |
    | URL de rappel | `https://www.getpostman.com/oauth2/callback` |
    | URL d’authentification | Utilisez l’**URL d’autorisation** de l’**étape 1**. |
    | ID client | Utilisez l’**ID d’application** de l’application Azure Active Directory qui a été créée ou réutilisée dans la section précédente. |
    | Étendue | Laisser vide |
    | State | Laisser vide |
    | Authentification du client | `Send as Basic Auth header` |

1. Le client doit maintenant se présenter comme suit :

    [![Exemple de jeton client Postman](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Sélectionnez **Jeton de demande**.
  
1. Faites défiler la page et sélectionnez **Use Token** (Utiliser le jeton).

## <a name="make-a-multipart-post-request"></a>Effectuer une requête POST multipart

Après avoir effectué les étapes précédentes, configurez Postman pour effectuer une requête HTTP POST authentifiée multipart :

1. Sous l’onglet **En-têtes**, ajoutez une clé d’en-tête de requête HTTP **Content-Type** avec la valeur `multipart/mixed`.

   [![Spécifier un type de contenu multipartie/mixte](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Sérialisez les données non textuelles dans des fichiers. Les données JSON seraient enregistrées dans un fichier JSON.
1. Sous l’onglet **Corps**, sélectionnez `form-data`. 
1. Ajoutez chaque fichier en attribuant un nom de **clé** et en sélectionnant `File`.
1. Ensuite, sélectionnez chaque fichier à l’aide du bouton **Choisir un fichier**.

   [![Exemple de corps de formulaire client Postman](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Le client Postman n’exige pas que les segments multipart aient un **Content-Type** ou **Content-Disposition** affecté manuellement.
   > * Vous n’avez pas besoin de spécifier ces en-têtes pour chaque partie.
   > * Vous devez sélectionner `multipart/mixed` ou un autre **Content-Type** approprié pour l’ensemble de la requête.

1. Pour finir, sélectionnez **Envoyer** pour envoyer votre requête HTTP POST multipart. Un code d’état `200` ou `201` indique une demande réussie. Le message de réponse approprié s’affiche dans l’interface client.

1. Validez vos données de requête HTTP POST en appelant le point de terminaison d’API : 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir sur les API de gestion Digital Twins et savoir comment les utiliser, consultez [Utiliser les API de gestion Azure Digital Twins](how-to-navigate-apis.md).

- Utilisez des requêtes multipart pour [ajouter des objets blob à des entités Azure Digital Twins](./how-to-add-blobs.md).

- Pour savoir comment vous authentifier auprès des API de gestion, consultez [S’authentifier auprès des API](./security-authenticating-apis.md).
