---
title: Accéder à l’API Azure Media Services - Azure CLI | Microsoft Docs
description: Suivez les étapes de cette procédure pour accéder à l’API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617833"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accéder à l’API Azure Media Services avec Azure CLI
 
Pour utiliser l’authentification de principal du service Azure AD pour se connecter à l’API Azure Media Services, votre application doit demander un jeton Azure AD qui possède les paramètres suivants :

* Point de terminaison de locataire Azure AD
* URI de ressource Media Services
* URI de ressource pour REST Media Services
* Valeurs de l’application Azure AD : ID client et clé secrète client

Pour une explication détaillée, consultez [l’accès à Media Services v3 API](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Cet article vous montre comment utiliser Azure CLI pour créer une application et un principal de service Azure AD afin d’obtenir les valeurs nécessaires pour accéder aux ressources Azure Media Services.

## <a name="prerequisites"></a>Conditions préalables 

[Créer un compte Media Services](create-account-cli-how-to.md).

Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Voir aussi

- [Mettre à l’échelle des unités réservées de média - CLI](media-reserved-units-cli-how-to.md)
- [Créer un compte Media Services - CLI](./scripts/cli-create-account.md) 
- [Réinitialiser les informations d’identification de compte - CLI](./scripts/cli-reset-account-credentials.md)
- [Créer des actifs multimédias - CLI](./scripts/cli-create-asset.md)
- [Charger un fichier - CLI](./scripts/cli-upload-file-asset.md)
- [Créer des transformations - CLI](./scripts/cli-create-transform.md)
- [Créer des travaux - CLI](./scripts/cli-create-jobs.md)
- [Créer une grille d’événement - CLI](./scripts/cli-create-event-grid.md)
- [Publier un actif multimédia - CLI](./scripts/cli-publish-asset.md)
- [Filtrer - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Étapes suivantes

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
