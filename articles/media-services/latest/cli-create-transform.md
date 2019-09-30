---
title: 'Exemple de script Azure CLI : Création d’une transformation | Microsoft Docs'
description: Utilisez le script Azure CLI pour créer une transformation.
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
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128793"
---
# <a name="cli-example-create-a-transform"></a>Exemple CLI : Créer une transformation

Le script Azure CLI dans cet article montre comment créer une transformation. Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »). Vous devez toujours vérifier si une transformation portant le nom de votre choix et « recette » existe déjà. Dans ce cas, vous devez la réutiliser.

## <a name="prerequisites"></a>Prérequis 

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
