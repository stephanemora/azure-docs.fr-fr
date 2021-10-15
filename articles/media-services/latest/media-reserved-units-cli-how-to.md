---
title: Mettre à l’échelle des unités réservées Multimédia - CLI
description: Cette rubrique explique comment utiliser la CLI pour la mise à l’échelle du traitement multimédia avec Azure Media Services.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 2525417516691fa4ffb6d681fc23b394f4588649
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129364097"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>Mise à l'échelle des unités réservées Multimédia (hérité)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment procéder à la mise à l'échelle des unités réservées Multimédia (MRU) afin d'accélérer l'encodage.

> [!WARNING]
> Cette commande ne fonctionne plus pour les comptes Media Services créés avec la version 2020-05-01 (ou ultérieure) de l’API. Pour ces comptes, les unités réservées Multimédia ne sont plus nécessaires, car le système effectue automatiquement un scale-up ou un scale-down en fonction de la charge. Si vous ne voyez pas l’option permettant de gérer les unités réservées Multimédia dans le portail Azure, cela signifie que vous utilisez un compte créé avec l’API 2020-05-01 ou une version ultérieure.
> L’objectif de cet article est de documenter le processus hérité d’utilisation des MRU

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

 Bien qu’il y ait eu précédemment des frais pour les unités réservées Multimédia, à compter du 17 avril, 2021 il n’y a plus de frais pour les comptes qui ont une configuration pour les unités réservées Multimédia.

## <a name="see-also"></a>Voir aussi

* [Migrer de Media Services v2 vers v3](migrate-v-2-v-3-migration-introduction.md)
