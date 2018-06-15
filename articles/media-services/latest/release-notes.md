---
title: Notes de publication Azure Media Services v3 | Microsoft Docs
description: Pour vous informer des développements les plus récents, cet article détaille les toutes dernières mises à jour concernant Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782638"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Notes de publication Azure Media Services v3 (préversion) 

Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités déconseillées
* Modifications planifiées

## <a name="may-07-2018"></a>07 mai 2018

### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .Net

Les fonctionnalités suivantes sont présentes dans le Kit de développement logiciel (SDK) .Net :

1. **Transformations** et **Travaux** pour encoder ou analyser le contenu du média. Pour obtenir des exemples, consultez [Diffuser des fichiers](stream-files-tutorial-with-api.md) et [Analyser](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** pour la publication et la diffusion en continu de contenu sur les appareils de l’utilisateur final
3. **StreamingPolicies** et **ContentKeyPolicies** pour configurer la distribution de clés et la protection du contenu (DRM) lors de la remise de contenu.
4. **LiveEvents** et **LiveOutputs** pour configurer l’ingestion et l’archivage de contenu en continu en direct.
5. **Éléments multimédias** pour stocker et publier du contenu multimédia dans le stockage Azure. 
6. **StreamingEndpoints** pour configurer et mettre à l’échelle l’empaquetage dynamique, le chiffrement et la diffusion en continu du contenu multimédia en direct et à la demande.

### <a name="known-issues"></a>Problèmes connus

Problème connu :

Lors de l’envoi d’un travail avec une URL HTTPS (JobInputHttp) pointant vers le contenu source, assurez-vous que le serveur HTTP prend en charge la requête « HEAD ». Si ce n’est pas le cas, le travail est rejeté.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d'ensemble](media-services-overview.md)
