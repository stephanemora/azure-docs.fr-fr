---
title: Charger du contenu sur un actif multimédia Azure Media Services avec Azure CLI
description: Le script Azure CLI de cette rubrique montre comment créer un élément multimédia Azure Media Services sur lequel charger du contenu.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585410"
---
# <a name="create-an-asset"></a>Créer une ressource

Cet article explique comment créer un actif multimédia Media Services.  Vous allez utiliser un actif multimédia pour stocker le contenu multimédia destiné à l’encodage et au streaming.  Pour en savoir plus sur les actifs multimédias Media Services, consultez [Actifs multimédias dans Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Créer un compte Media Services](./create-account-howto.md) pour créer le compte Media Services et le groupe de ressources nécessaires à la création d’un actif multimédia.

## <a name="methods"></a>Méthodes

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Interpréteur de commandes CLI](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>Étapes suivantes

[Gérer les actifs multimédias](manage-asset-concept.md)
