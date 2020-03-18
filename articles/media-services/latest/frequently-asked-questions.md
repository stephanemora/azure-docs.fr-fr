---
title: Azure Media Services v3 - Forum aux questions | Microsoft Docs
description: Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: a2619293bf3641cdca370ff528a87ae879460a3b
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086776"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Forum Aux Questions (FAQ) Media Services v3

Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services (AMS) v3.

## <a name="general"></a>Général

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quels sont les rôles Azure pouvant effectuer des actions sur les ressources Azure Media Services ? 

Consultez [Contrôle d’accès en fonction du rôle (RBAC) pour les comptes Media Services](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Comment diffuser en continu vers des appareils Apple iOS ?

Vérifiez que « (format=m3u8-aapl) » est présent à la fin de votre chemin (après la partie « /Manifest » de l’URL) pour indiquer au serveur de streaming d’origine qu’il doit retourner le contenu HLS pour la consommation sur les appareils natifs Apple iOS (pour plus d’informations, consultez l’article relatif à la [distribution de contenu](dynamic-packaging-overview.md)).

### <a name="how-do-i-configure-media-reserved-units"></a>Comment configurer des unités réservées Multimédia ?

Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3. Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).

Pour plus de détails, voir [Mise à l’échelle du traitement multimédia avec l’interface CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Quelle est la méthode recommandée pour traiter les vidéos ?

Utilisez des [transformations](https://docs.microsoft.com/rest/api/media/transforms) pour configurer des tâches courantes de codage ou d'analyse des vidéos. Chaque **transformation** décrit une recette, ou un flux de travail de tâches pour le traitement de vos fichiers vidéo ou audio. Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la demande réelle envoyée à Media Services pour appliquer la **transformation** à un contenu vidéo ou audio d’entrée donné. Lorsque la transformation est créée, vous pouvez envoyer des travaux à l’aide des API Media Services ou de l’un des kits de développement logiciel (SDK) publiés. Pour plus d’informations, consultez [Transformations et travaux](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>J’ai chargé, encodé et publié une vidéo. Pourquoi la vidéo n’est-elle pas lue lorsque j’essaie de la diffuser en continu ?

L’une des raisons les plus courantes est que le point de terminaison de streaming à partir duquel vous essayez de lire n’est pas à l’état En cours d’exécution.

### <a name="how-does-pagination-work"></a>Comment fonctionne la pagination ?

Lors de l’utilisation de la pagination, vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière. Pour voir des détails et des exemples, consultez [Filtrage, tri, pagination](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quelles fonctionnalités ne sont pas encore disponibles dans Azure Media Services v3 ?

Pour plus d’informations, consultez [Différences de fonctionnalités par rapport aux API v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qu'est-ce que le déplacement d’un compte Media Services entre abonnements ?  

Pour en savoir plus, consultez [Déplacement d’un compte Media Services entre abonnements](media-services-account-concept.md).

## <a name="live-streaming"></a>Vidéo en flux continu 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Comment insérer des pauses/séquences d’images et de vidéos pendant le flux de données en direct ?

Le Live Encoding Media Services v3 ne prend pas encore en charge l’insertion de séquences de vidéos ou d’images pendant le flux de données en direct. 

Vous pouvez utiliser un [encodeur live local](recommended-on-premises-live-encoders.md) pour changer la vidéo source. De nombreuses applications offrent la possibilité de changer les sources, parmi lesquelles Telestream Wirecast, Switcher Studio (sur iOS), OBS Studio (application gratuite).

## <a name="content-protection"></a>Protection du contenu

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Dois-je utiliser un chiffrement à clé en clair AES-128 ou un système DRM ?

Les clients se demandent souvent s’ils devraient utiliser un chiffrement AES ou un système DRM. La principale différence entre les deux systèmes est qu’avec le chiffrement AES, la clé de contenu est transmise au client via TLS ; elle est chiffrée en transit, mais sans autre chiffrement (« en clair »). Par conséquent, la clé utilisée pour déchiffrer le contenu peut être lue par le lecteur client et affichée dans la trace réseau du client, en texte en clair. Le chiffrement avec une clé en clair AES-128 convient à des cas d’utilisation dans lesquels l’observateur est fiable (par exemple, le chiffrement de vidéos d’entreprise distribuées dans une société et destinées aux employés).

Par rapport à la clé en clair AES-128, les systèmes DRM comme PlayReady, Widevine et FairPlay fournissent tous un niveau supplémentaire de chiffrement sur la clé utilisée pour déchiffrer le contenu. La clé de contenu est chiffrée selon une clé protégée par le runtime DRM, en plus de tout chiffrement lors du transit fourni par TLS. De plus, le déchiffrement est pris en charge dans un environnement sécurisé au niveau du système d’exploitation, où il est plus difficile pour un utilisateur malintentionné de commettre une attaque. DRM est recommandé dans des cas d’utilisation où l’observateur peut ne pas être fiable et où donc un niveau de sécurité supérieur est requis.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Comment afficher une vidéo uniquement pour les utilisateurs qui disposent d’une autorisation spécifique, sans utiliser Azure AD ?

Vous n’êtes pas obligé d’utiliser un fournisseur de jetons spécifique (tel qu’Azure AD). Vous pouvez créer votre propre fournisseur [JWT](https://jwt.io/) (appelé STS, service d’émission de jeton de sécurité) à l’aide du chiffrement à clé asymétrique. Dans votre STS personnalisé, vous pouvez ajouter des revendications en fonction de votre logique métier.

Assurez-vous que l’émetteur, l’audience et les revendications correspondent exactement entre ce qui se trouve dans JWT et la ContentKeyPolicyRestriction utilisée dans ContentKeyPolicy.

Pour plus d’informations, consultez [Protéger votre contenu à l’aide du chiffrement dynamique de Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Comment et où obtenir un jeton JWT avant de l’utiliser pour demander une licence ou une clé ?

1. Pour un environnement de production, vous devez disposer d’un service d’émission de jeton de sécurité (STS) (service web) qui émet un jeton JWT en réponse à une requête HTTPS. Dans le cas d’un environnement de test, vous pouvez utiliser le code figurant dans la méthode **GetTokenAsync** définie dans [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Après l’authentification d’un utilisateur, le lecteur doit adresser une requête au service STS afin d’obtenir ce jeton et l’attribuer comme valeur de jeton. Vous pouvez utiliser [l’API Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Pour obtenir un exemple d’exécution de service STS, avec une clé symétrique ou asymétrique, accédez à l’adresse [https://aka.ms/jwt](https://aka.ms/jwt). 
* Pour découvrir un exemple de lecteur basé sur le Lecteur multimédia Azure utilisant ce type de jeton JWT, accédez à l’adresse [https://aka.ms/amtest](https://aka.ms/amtest) (développez le lien « player_settings » pour voir l’entrée de jeton).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Comment autoriser les requêtes de diffusion en continu de vidéos avec chiffrement AES ?

L’approche correcte consiste à tirer profit du service STS :

Dans STS, en fonction du profil utilisateur, ajoutez différentes revendications (par exemple, « Utilisateur Premium », « Utilisateur de la version de base », « Utilisateur de la version d’évaluation gratuite »). La définition de différentes revendications dans un jeton JWT permet à l’utilisateur de visualiser des contenus distincts. Bien entendu, dans le cas d’un contenu/élément multimédia différent, l’élément ContentKeyPolicyRestriction disposera des RequiredClaims correspondants.

Utilisez l’API Azure Media Services pour la configuration de la livraison de licences/clés et pour le chiffrement de vos éléments multimédias (comme indiqué dans [cet exemple](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Pour plus d'informations, consultez les pages suivantes :

- [Présentation de la protection du contenu](content-protection-overview.md)
- [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP ou HTTPS ?
L’application de lecteur MVC ASP.NET doit prendre en charge les éléments suivants :

* Authentification des utilisateurs via Azure AD, sous HTTPS.
* Échange de jeton JWT entre le client et Azure AD, sous HTTPS.
* Acquisition de licence DRM par le client, qui doit être sous HTTPS si la distribution de licences est assurée par Azure Media Services. La suite de produits PlayReady n’impose pas le format HTTPS pour la distribution de licences. Si votre serveur de licences PlayReady se trouve en dehors de Media Services, vous pouvez utiliser HTTP ou HTTPS.

Puisque l’application de lecteur ASP.NET utilise le protocole HTTPS en tant que meilleure pratique, Media Player se trouve sur une page sous HTTPS. Le protocole HTTP est cependant privilégié pour la diffusion en continu, ce qui signifie que vous devez tenir compte du problème du contenu mixte.

* Le navigateur ne permet pas un contenu mixte. Mais vous pouvez utiliser des plug-ins tels que Silverlight et OSMF pour Smooth et DASH. Le contenu mixte est un problème de sécurité, en raison de la menace que constitue la possibilité d’injecter un logiciel JavaScript malveillant, qui risque de mettre en danger les données du client. Les navigateurs bloquent cette fonctionnalité par défaut. Le seul moyen de contourner ce problème est d’autoriser tous les domaines (HTTPS ou HTTP) côté serveur (origine). Il ne s’agit probablement pas d’une idée judicieuse.
* Évitez le contenu mixte. L’application de lecteur et Media Player doivent utiliser HTTP ou HTTPS. Si vous lisez du contenu mixte, silverlightSS tech vous oblige à supprimer un avertissement de contenu mixte. flashSS tech traite le contenu mixte sans aucun avertissement.
* Si votre point de terminaison de diffusion a été créé avant août 2014, il ne prend pas en charge HTTPS. Dans ce cas, créez et utilisez un nouveau point de terminaison pour le protocole HTTPS.

### <a name="what-about-live-streaming"></a>Qu’en est-il de la diffusion en continu ?

Vous pouvez utiliser exactement la même conception et la même implémentation pour protéger un service de diffusion en continu dans Media Services, en traitant l’élément multimédia associé à un programme comme un élément multimédia VOD. Pour pouvoir protéger du contenu en direct via la fonctionnalité multi-DRM, appliquez la même procédure d’installation/de traitement à l’élément multimédia, comme s’il s’agissait d’un élément VOD, avant de l’associer à une sortie en direct.

### <a name="what-about-license-servers-outside-media-services"></a>Qu’en est-il des serveurs de licences hors Media Services ?

Souvent, les clients investissent dans une batterie de serveurs qu’ils hébergent dans leur propre centre de données ou chez des fournisseurs de services DRM. Avec la protection de contenu Media Services, vous pouvez fonctionner en mode hybride. Le contenu peut être hébergé et protégé dynamiquement dans Media Services, alors que les licences DRM sont délivrées par des serveurs situés en dehors de Media Services. Dans ce cas, envisagez les modifications suivantes :

* Le service STS doit émettre des jetons acceptables et pouvant être vérifiés par la batterie de serveurs de licence. Par exemple, les serveurs de licences Widevine fournis par Axinom exigent un jeton JWT spécifique contenant un message d’octroi de droit. Par conséquent, vous devez disposer d’un STS pour émettre un jeton JWT. 
* Vous n’avez plus besoin de configurer le service de distribution de licences dans Media Services. Vous devez fournir les URL d’acquisition de la licence (pour PlayReady, Widevine et FairPlay) au moment où vous configurez ContentKeyPolicies.

> [!NOTE]
> Widevine est un service fourni par Google Inc. soumis aux conditions de service et à la politique de confidentialité de Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 et v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Puis-je utiliser le portail Azure pour gérer des ressources v3 ?

Vous pouvez actuellement utiliser le portail [Azure](https://portal.azure.com/) pour :

* Gérer les [événements en direct](live-events-outputs-concept.md) Media Services v3. 
* Consulter (et non gérer) les [actifs multimédias](assets-concept.md) v3. 
* [Obtenir des informations sur l’accès aux API](access-api-portal.md). 

Pour toutes les autres tâches de gestion (par exemple les [transformations et travaux](transforms-jobs-concept.md) et la [protection du contenu](content-protection-overview.md)), utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des [SDK](media-services-apis-overview.md#sdks) pris en charge.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe-t-il un concept de fichier d’élément multimédia dans v3 ?

Les fichiers d’élément multimédia ont été supprimés de l’API AMS afin de séparer Media Services de la dépendance du SDK de Stockage. C’est maintenant Stockage, et non plus Media Services, qui conserve les informations appartenant à Stockage. 

Pour plus d’informations, consultez [Migrer vers Azure Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Où le chiffrement du stockage côté client se fait-il ?

Il est désormais recommandé d’utiliser le chiffrement de stockage côté serveur (qui est activé par défaut). Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Media Services v3](media-services-overview.md)
