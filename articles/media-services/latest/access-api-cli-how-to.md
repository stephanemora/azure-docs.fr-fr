---
title: Accéder à l’API Azure Media Services - Azure CLI | Microsoft Docs
description: Suivez les étapes de cet article de procédure pour accéder à l’API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896150"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accéder à l’API Azure Media Services avec Azure CLI
 
Pour utiliser l’authentification du principal du service Azure AD afin de vous connecter à l’API Azure Media Services, votre application doit demander un jeton Azure AD qui possède les paramètres suivants :

* Point de terminaison de locataire Azure AD
* URI de ressource Media Services
* URI de ressource pour REST Media Services
* Valeurs de l’application Azure AD : ID client et clé secrète client

Pour une explication détaillée, consultez [Accès aux API Media Services v3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Cet article vous montre comment utiliser Azure CLI pour créer une application et un principal de service Azure AD afin d’obtenir les valeurs nécessaires pour accéder aux ressources Azure Media Services.

## <a name="prerequisites"></a>Prérequis 

[Créer un compte Media Services](create-account-cli-how-to.md).

Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Voir aussi

- [Mettre à l’échelle des unités réservées de média - CLI](media-reserved-units-cli-how-to.md)
- [Créer un compte Media Services - CLI](create-account-cli-how-to.md) 
- [Réinitialiser les informations d’identification de compte - CLI](cli-reset-account-credentials.md)
- [Créer des actifs multimédias - CLI](cli-create-asset.md)
- [Charger un fichier - CLI](cli-upload-file-asset.md)
- [Créer des transformations - CLI](cli-create-transform.md)
- [Encoder avec une transformation personnalisée - CLI](custom-preset-cli-howto.md)
- [Créer des travaux - CLI](cli-create-jobs.md)
- [Créer une grille d’événement - CLI](job-state-events-cli-how-to.md)
- [Publier un actif multimédia - CLI](cli-publish-asset.md)
- [Filtrer - CLI](filters-dynamic-manifest-cli-howto.md)
- [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Étapes suivantes

Le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver dans l’état En cours d’exécution. La commande CLI suivante démarre votre point de terminaison de streaming par défaut :

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

