---
title: Obtenir les intervenants actifs
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les SDK Azure Communication Services pour rendre les intervenants actifs dans un appel.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700521"
---
# <a name="get-active-speakers-within-a-call"></a>Obtenir les intervenants actifs dans un appel

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Pendant un appel actif, vous pouvez obtenir une liste des intervenants actifs afin de les rendre ou de les afficher différemment. Voici comment procéder.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : Suivez le guide de démarrage rapide pour [ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir comment gérer la vidéo](./manage-video.md)
- [Découvrir comment gérer les appels](./manage-calls.md)
- [Découvrez comment enregistrer des appels](./record-calls.md)