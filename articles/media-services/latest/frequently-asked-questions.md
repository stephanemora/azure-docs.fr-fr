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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e543f6e69632da67900e3b49a9c77e125df5f852
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316913"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Forum Aux Questions (FAQ) Azure Media Services v3

Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services (AMS) v3.

## <a name="v3-apis"></a>API v3

### <a name="how-do-i-configure-media-reserved-units"></a>Comment configurer des unités réservées Multimédia ?

Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3. Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).

Pour plus de détails, voir [Mise à l’échelle du traitement multimédia avec l’interface CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Quelle est la méthode recommandée pour traiter les vidéos ?

Utilisez des [transformations](https://docs.microsoft.com/rest/api/media/transforms) pour configurer des tâches courantes de codage ou d'analyse des vidéos. Chaque **transformation** décrit une recette, ou un flux de travail de tâches pour le traitement de vos fichiers vidéo ou audio. Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la demande réelle à Media Services pour appliquer le **transformer** à un contenu vidéo ou audio d’entrée donné. Lorsque la transformation est créée, vous pouvez envoyer des travaux à l’aide des API Media Services ou de l’un des kits de développement logiciel (SDK) publiés. Pour plus d’informations, consultez [Transformations et travaux](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Comment fonctionne la pagination ?

Lors de l’utilisation de la pagination, vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière. Pour voir des détails et des exemples, consultez [Filtrage, tri, pagination](entities-overview.md).

## <a name="live-streaming"></a>Vidéo en flux continu 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Comment insérer des pauses/séquences d’images et de vidéos pendant le flux de données en direct ?

Le Live Encoding Media Services v3 ne prend pas encore en charge l’insertion de séquences de vidéos ou d’images pendant le flux de données en direct. 

Vous pouvez utiliser un [encodeur live local](recommended-on-premises-live-encoders.md) pour changer la vidéo source. De nombreuses applications offrent la possibilité de changer les sources, parmi lesquelles Telestream Wirecast, Switcher Studio (sur iOS), OBS Studio (application gratuite).

## <a name="content-protection"></a>Protection du contenu

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Comment et où obtenir un jeton JWT avant de l’utiliser pour demander une licence ou une clé ?

1. Pour un environnement de production, vous devez disposer d’un service d’émission de jeton de sécurité (STS) (service web) qui émet un jeton JWT en réponse à une requête HTTPS. Dans le cas d’un environnement de test, vous pouvez utiliser le code figurant dans la méthode **GetTokenAsync** définie dans [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Après l’authentification d’un utilisateur, le lecteur doit adresser une requête au service STS afin d’obtenir ce jeton et l’attribuer comme valeur de jeton. Vous pouvez utiliser [l’API Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Pour obtenir un exemple d’exécution de service STS, avec une clé symétrique ou asymétrique, accédez à l’adresse [https://aka.ms/jwt](https://aka.ms/jwt). 
* Pour découvrir un exemple de lecteur basé sur le Lecteur multimédia Azure utilisant ce type de jeton JWT, accédez à l’adresse [https://aka.ms/amtest](https://aka.ms/amtest) (développez le lien « player_settings » pour voir l’entrée de jeton).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Comment autoriser les requêtes de diffusion en continu de vidéos avec chiffrement AES ?

L’approche correcte consiste à tirer profit du service STS :

Dans STS, en fonction du profil utilisateur, ajoutez différentes revendications (par exemple, « Utilisateur Premium », « Utilisateur de la version de base », « Utilisateur de la version d’évaluation gratuite »). La définition de différentes revendications dans un jeton JWT permet à l’utilisateur de visualiser des contenus distincts. Bien entendu, dans le cas d’un contenu/élément multimédia différent, l’élément ContentKeyPolicyRestriction disposera des RequiredClaims correspondants.

Utilisez Azure Media Services API pour la configuration/clé de licence de remise et de chiffrement de vos ressources (comme indiqué dans [cet exemple](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Pour plus d'informations, consultez les pages suivantes :

- [Vue d’ensemble de la protection du contenu](content-protection-overview.md)
- [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Media Services v2 et v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Puis-je utiliser le portail Azure pour gérer des ressources v3 ?

Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez le [API REST](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), ou l’un des prises en charge [kits de développement logiciel](developers-guide.md).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe-t-il un concept de fichier d’élément multimédia dans v3 ?

Les fichiers d’élément multimédia ont été supprimés de l’API AMS afin de séparer Media Services de la dépendance du SDK de Stockage. C’est maintenant Stockage, et non plus Media Services, qui conserve les informations appartenant à Stockage. 

Pour plus d’informations, consultez [Migrer vers Azure Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Où le chiffrement du stockage côté client se fait-il ?

Il est désormais recommandé d’utiliser le chiffrement de stockage côté serveur (qui est activé par défaut). Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Media Services v3](media-services-overview.md)
