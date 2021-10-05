---
title: Présentation du Lecteur multimédia Azure
description: Apprenez-en davantage sur le Lecteur multimédia Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: faf5bc8a96cb3781ed1d48e9d7f0d1fd7cbc386e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679576"
---
# <a name="azure-media-player-overview"></a>Présentation du lecteur multimédia Azure #

Le Lecteur multimédia Azure est un lecteur vidéo web qui peut lire du contenu multimédia à partir de [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/), sur un grand nombre de navigateurs et d’appareils. Lecteur multimédia Azure exploite les normes du secteur, telles que HTML5, Media Source Extensions (MSE) et Encrypted Media Extensions (EME), pour offrir une expérience enrichie de diffusion en continu adaptative.  Lorsque ces normes ne sont pas disponibles sur un périphérique ou dans un navigateur, Azure Media Player utilise Flash et Silverlight comme technologies de secours. Quelle que soit la technologie de lecture utilisée, les développeurs disposent d’une interface JavaScript unifiée pour accéder aux API, ce qui permet de lire directement le contenu fourni par Azure Media Services sur un large choix d’appareils et de navigateurs.

Microsoft Azure Media Services permet la distribution de contenu dans les formats de streaming DASH, Smooth Streaming et HLS pour la lecture. Azure Media Player prend en compte des différents formats et lit automatiquement le lien le mieux adapté aux capacités de la plateforme/du navigateur. Microsoft Azure Media Services assure également le chiffrement dynamique des ressources avec le chiffrement commun (PlayReady ou Widevine) ou le chiffrement d’enveloppe AES 128 bits. Lorsqu’il est configuré de manière appropriée, Azure Media Player permet le déchiffrement du contenu chiffré avec PlayReady et AES 128 bits.  Pour savoir comment configurer le lecteur, consultez la section [Contenu protégé](azure-media-player-protected-content.md).

Si vous avez des problèmes, ou si vous trouvez des bogues, [créez un ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) en utilisant la catégorie Lecture sur le client.

> [!NOTE]
> Notez que Lecteur multimédia Azure ne prend en charge que les flux multimédias provenant d’Azure Media Services.

## <a name="license"></a>Licence ##

Le Lecteur multimédia Azure est concédé sous licence, et soumis aux conditions énoncées dans les termes du contrat de licence logiciel Microsoft du Lecteur multimédia Azure. Consultez le [fichier de licence](/legal/azure-media-player/azure-media-player-license) pour connaître l’intégralité des conditions. Pour plus d’informations, reportez-vous à la [Déclaration de confidentialité](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015, Microsoft Corporation.

## <a name="next-steps"></a>Étapes suivantes ##

- [Démarrage rapide du Lecteur multimédia Azure](azure-media-player-quickstart.md)
