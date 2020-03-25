---
title: 'Exemple de script Azure CLI : Création d’une transformation | Microsoft Docs'
description: Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »). Le script Azure CLI dans cet article montre comment créer une transformation.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74888596"
---
# <a name="cli-example-create-a-transform"></a>Exemple CLI : créer une transformation

Le script Azure CLI dans cet article montre comment créer une transformation. Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »). Vous devez toujours vérifier si une transformation portant le nom de votre choix et « recette » existe déjà. Dans ce cas, vous devez la réutiliser.

## <a name="prerequisites"></a>Conditions préalables requises 

[Créer un compte Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Vous pouvez uniquement spécifier le chemin d’un fichier JSON prédéfini Standard Encoder personnalisé pour [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Pour plus d’informations, consultez l’exemple [Encoder avec une transformation personnalisée](custom-preset-cli-howto.md).
>
> Vous ne pouvez pas passer un nom de fichier si vous utilisez [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Étapes suivantes

[az ams transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
