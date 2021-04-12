---
title: Mettre à l’échelle des unités réservées Multimédia - CLI
description: Cette rubrique explique comment utiliser la CLI pour la mise à l’échelle du traitement multimédia avec Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121674"
---
# <a name="how-to-scale-media-reserved-units"></a>Mise à l'échelle des unités réservées Multimédia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment procéder à la mise à l'échelle des unités réservées Multimédia (MRU) afin d'accélérer l'encodage.

> [!WARNING]
> Cette commande ne fonctionne plus pour les comptes Media Services créés avec la version 2020-05-01 ou ultérieure de l’API. Pour ces comptes, les unités réservées Multimédia ne sont plus nécessaires, car le système effectue automatiquement un scale-up ou un scale-down en fonction de la charge. Si vous ne voyez pas l’option permettant de gérer les unités réservées Multimédia dans le portail Azure, cela signifie que vous utilisez un compte créé avec l’API 2020-05-01 ou une version ultérieure.

## <a name="prerequisites"></a>Prérequis

[Créer un compte Media Services](./account-create-how-to.md).

Familiarisez-vous avec les [unités réservées Multimédia](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Mettre à l’échelle les unités réservées au multimédia avec l’interface CLI

Exécutez la commande `mru`.

La commande [az ams account mru](/cli/azure/ams/account/mru) suivante définit les unités réservées au multimédia sur le compte « amsaccount » à l’aide des paramètres **count** et **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Facturation

Vous êtes facturé en fonction du nombre de minutes pendant lesquelles les unités réservées Multimédia sont approvisionnées dans votre compte. Ceci est indépendant d'éventuels travaux en cours d'exécution dans votre compte. Pour plus d’informations, consultez la section FAQ de la page [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Étape suivante

[Analyser des vidéos](analyze-videos-tutorial.md)

## <a name="see-also"></a>Voir aussi

* [Quotas et limites](limits-quotas-constraints-reference.md)
