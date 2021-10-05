---
title: Transférer les appels
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les kits de développement logiciel (SDK) Azure Communication Services pour transférer les appels.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e68c81a88c972cc05ffafea0d94e82c84de4268f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700631"
---
# <a name="transfer-calls"></a>Transférer les appels

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Pendant un appel actif, vous souhaiterez peut-être transférer l’appel vers une autre personne ou un autre numéro. Voyons comment procéder : 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : suivez le guide de démarrage rapide permettant d’[ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

[!INCLUDE [Transfer calls Client-side JavaScript](./includes/transfer-calls/transfer-calls-web.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment gérer les appels](./manage-calls.md)
- [Découvrez comment gérer la vidéo](./manage-video.md)
- [Découvrez comment enregistrer les appels](./record-calls.md)
- [Découvrez comment transcrire les appels](./call-transcription.md)