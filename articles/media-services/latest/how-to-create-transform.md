---
title: Exemple de script Azure CLI – Créer une transformation
description: Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »). Le script Azure CLI dans cet article montre comment créer une transformation.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4623610960d8f21a2dab3293c7499a2112416254
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101718911"
---
# <a name="create-a-transform"></a>Créer une transformation

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Le script Azure CLI dans cet article montre comment créer une transformation. Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »). Vous devez toujours vérifier si une transformation portant le nom de votre choix et « recette » existe déjà. Dans ce cas, vous devez la réutiliser.

## <a name="prerequisites"></a>Prérequis

[Créer un compte Media Services](./create-account-howto.md).

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

> [!NOTE]
> Vous pouvez uniquement spécifier le chemin d’un fichier JSON prédéfini Standard Encoder personnalisé pour [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Pour plus d’informations, consultez l’exemple [Encoder avec une transformation personnalisée](custom-preset-cli-howto.md).
>
> Vous ne pouvez pas passer un nom de fichier si vous utilisez [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
