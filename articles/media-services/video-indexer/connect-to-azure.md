---
title: Créer un compte Video Indexer dans le Portail Azure
titlesuffix: Azure Media Services
description: Cet article explique comment créer un compte Video Indexer dans le Portail Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 49e05047d58cc5b6bb5e0099c24a131a26dc8af1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291811"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Créer un compte Video Indexer connecté à Azure

Lorsque vous créez un compte Video Indexer, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limités par le quota). Avec l’essai gratuit, Video Indexer fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec l’option payante, vous créez un compte Video Indexer connecté à votre abonnement Azure et un compte Azure Media Services. Vous payez pour les minutes indexées, ainsi que pour les frais liés au compte média. 

Cet article explique comment créer un compte Video Indexer connecté à un abonnement Azure et un compte Azure Media Services. 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. 

    Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un essai gratuit Azure](https://azure.microsoft.com/free/).

* Un domaine Azure Active Directory (AD). 

    Si vous n’avez pas de domaine Azure AD, créez-en un avec votre abonnement Azure. Pour plus d’informations, consultez la rubrique [Gestion des noms de domaine personnalisés dans Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md).

* Un utilisateur et un membre dans votre domaine Azure AD. Vous allez utiliser ce membre lors de la connexion de votre compte Video Indexer à Azure.

    Cet utilisateur doit satisfaire aux critères suivants :

    * Être un utilisateur Azure AD avec un compte professionnel ou scolaire, pas un compte personnel, tel que outlook.com, live.com ou hotmail.com.
        
        ![Tous les utilisateurs ADD](./media/create-account/all-aad-users.png)

    *  Être un membre de votre abonnement Azure avec un rôle de propriétaire ou les rôles de contributeur et d’administrateur des accès utilisateur. Un utilisateur peut être ajouté à deux reprises, avec deux rôles. Une fois comme contributeur et une autre fois comme administrateur des accès utilisateur.

        ![Contrôle d’accès](./media/create-account/access-control-iam.png)

* Inscrivez le fournisseur de ressources EventGrid à l’aide du portail Azure.

    Dans le [Portail Microsoft Azure](https://portal.azure.com/), accédez à **Abonnements** > [abonnement] > **ResourceProviders** > **Microsoft.EventGrid**. S’il n’a pas l’état « Inscrit », cliquez sur **Inscrire**. Quelques minutes sont nécessaires pour effectuer l’inscription. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Connexion à Azure

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.

2. Cliquez sur le bouton **Se connecter à Azure** :

    ![Connexion à Azure](./media/create-account/connect-to-azure.png)

3. Lorsque la liste des abonnements s’affiche, sélectionnez l’abonnement que vous souhaitez utiliser. 

    ![Connexion de Video Indexer à Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Sélectionnez une région Azure parmi les lieux pris en charge : USA Ouest 2, Europe Nord et Asie-Pacifique.
5. Sous **Compte Azure Media Services**, choisissez une des options suivantes :

    * Pour créer un compte Media Services, sélectionnez **Créer un groupe de ressources**. Indiquez le nom de votre groupe de ressources.

        Azure crée ce compte dans votre abonnement, y compris un compte de stockage Azure. Votre nouveau compte Media Services a une configuration initiale par défaut avec un point de terminaison de streaming et 10 unités réservées S3.
    * Pour utiliser un compte Media Services existant, sélectionnez **Utiliser une ressource existante**. Dans la liste des comptes, sélectionnez votre compte.

        Votre compte Media Services doit avoir la même région que votre compte Video Indexer. Pour réduire la durée d’indexation et améliorer le débit, ajustez le type et le nombre d’unités réservées sur **10 unités réservées S3** dans votre compte Media Services.
    * Pour configurer manuellement votre connexion, cliquez sur **Basculer vers la configuration manuelle**. 
    
        Vous pouvez configurer manuellement votre connexion si l’option automatique échoue pour une raison quelconque, si vos installation et configuration sont différentes des scénarios courants ou si vous voulez avoir une visibilité et un contrôle complets sur les paramètres. 
        
        Dans **Connecter Video Indexer à un abonnement Azure**, indiquez les informations suivantes.

        |Paramètre|Description|
        |---|---|
        |Région du compte Video Indexer|Nom de la région du compte Video Indexer. Pour améliorer les performances et réduire les coûts, il est fortement recommandé de spécifier le nom de la région où se trouvent la ressource Azure Media Services et le compte de stockage Azure. |
        |Locataire Azure Active Directory (AAD)|Nom du locataire Azure AD, par exemple contoso.onmicrosoft.com. Les informations relatives au locataire peuvent être récupérées à partir du portail Azure. Placez votre curseur sur le nom de l’utilisateur connecté, en haut à droite.|
        |Identifiant d’abonnement|Abonnement Azure sous lequel cette connexion doit être créée. L’ID d’abonnement peut être récupéré à partir du portail Azure. Cliquez sur **Tous les services** dans le panneau gauche, puis recherchez « abonnements ». Sélectionnez **Abonnements** et choisissez l’ID voulu dans la liste de vos abonnements.|
        |Nom du groupe de ressources Azure Media Services|Nom du groupe de ressources dans lequel le compte Media Services figure.|
        |Nom de la ressource de service multimédia|Nom de la ressource Azure Media Services.|
        |ID de l'application|ID d’application Azure AD avec des autorisations pour le compte Media Services spécifié. Pour plus d’informations, consultez [Utiliser une authentification de principal de service](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Clé de l’application|Pour plus d’informations, consultez [Utiliser une authentification de principal de service](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. Lorsque vous avez terminé, choisissez **Connecter**. Cette opération peut prendre quelques minutes. 

    Une fois que vous êtes connecté à Azure, votre nouveau compte Video Indexer apparaît dans la liste des comptes :

    ![Nouveau compte](./media/create-account/new-account.png)

7. Accédez à votre nouveau compte : 

    ![Compte Video Indexer](./media/create-account/vi-account.png)

## <a name="considerations"></a>Considérations

Tenez compte des considérations suivantes pour Azure Media Services :

* Si vous vous êtes connecté à un nouveau compte Media Services, vous voyez un nouveau groupe de ressources, un nouveau compte Media Services et un nouveau compte de stockage dans votre abonnement Azure.
* Si vous vous êtes connecté à un compte Media Services, Video Indexer définit 10 **unités réservées** S3 :

    ![Unités réservées Media Services](./media/create-account/ams-reserved-units.png)

* Si vous vous êtes connecté à un compte Media Services existant, Video Indexer ne modifie pas la configuration existante pour les **unités réservées** multimédia.

    Vous devrez peut-être ajuster le type et le nombre d’**unités réservées** multimédia en fonction de la charge prévue. N’oubliez pas que si votre charge est élevée et si vous n’avez pas suffisamment unités ou manquez de vitesse, le traitement de vidéos peut échouer en raison d’une expiration de délai.

* Si vous vous êtes connecté à un nouveau compte Media Services, Video Indexer démarre automatiquement son **point de terminaison de streaming** par défaut :

    ![Point de terminaison de diffusion en continu Media Services](./media/create-account/ams-streaming-endpoint.png)

* Si vous vous êtes connecté à un compte Media Services existant, Video Indexer ne modifie pas la configuration du point de terminaison de streaming par défaut. Si aucun **point de terminaison de streaming** n’est en cours d’exécution, vous ne serez pas en mesure de regarder les vidéos à partir de ce compte Media Services, ni dans Video Indexer.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez interagir par programmation avec votre compte d’évaluation et/ou avec les comptes Video Indexer connectés à Azure en suivant les instructions de la section [Utiliser des API](video-indexer-use-apis.md).

Vous devez vous servir du même utilisateur Azure AD que lors de la connexion à Azure.


