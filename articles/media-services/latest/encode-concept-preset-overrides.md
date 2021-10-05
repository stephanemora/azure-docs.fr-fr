---
title: Utilisation de remplacements de présélections pour modifier les paramètres de transformation lors de l’envoi de travaux
description: Cet article explique comment utiliser les remplacements de présélections pour ajuster les paramètres de transformation sur une instance par travail.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700620"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>Utilisation de remplacements de présélections pour contrôler les paramètres par travail

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>Présentation des transformations et travaux

Pour encoder avec Media Services v3, vous devez créer une [transformation](/rest/api/media/transforms) et un [travail](/rest/api/media/jobs). La transformation définit une recette à appliquer pour vos paramètres et sorties d’encodage ; le travail est une instance de la recette. Pour plus d’informations, consultez [Transformations et travaux](transform-jobs-concept.md).

Lors de l’encodage ou de l’utilisation de l’analytique avec Media Services, vous pouvez définir des présélections personnalisées dans une transformation pour définir les paramètres à utiliser. Il est parfois nécessaire de remplacer les paramètres sur une transformation par travail pour éviter d’avoir à créer une transformation personnalisée pour chaque scénario. Pour remplacer un paramètre de votre présélection de transformation, vous pouvez utiliser la propriété de remplacement de présélection de la [ressource de sortie du travail](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) avant de soumettre le travail à la transformation.

## <a name="preset-overrides"></a>Remplacements de présélections

Les remplacements de présélections vous permettent de transmettre une présélection personnalisée qui remplacera les paramètres fournis à un objet de transformation après sa création.  Cette propriété est disponible sur la [ressource de sortie du travail](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) lors de l’envoi d’un nouveau travail à une transformation.

Cela peut être utile lorsque vous devez remplacer certaines propriétés de vos transformations définies personnalisées ou une propriété sur une présélection intégrée. Par exemple, envisagez le scénario dans lequel vous avez créé une transformation personnalisée qui utilise la [présélection intégrée d’analyseur audio](/rest/api/media/transforms/create-or-update#audioanalyzerpreset), mais où vous configurez initialement cette présélection pour qu’elle utilise le paramètre de langue audio « en-us » pour l’anglais.  Cela entraînerait une transformation où chaque travail soumis serait envoyé au moteur de transcription de reconnaissance vocale comme étant en anglais (États-Unis) uniquement. Chaque travail soumis à cette transformation serait verrouillé sur le paramètre de langue « en-us ». Vous pouvez contourner ce scénario en faisant en sorte qu’une transformation soit définie pour chaque langue, mais cela serait bien plus difficile à gérer et vous pourriez atteindre les limitations de quota de transformation de votre compte.
Pour mieux appréhender ce scénario, vous utilisez un remplacement de présélection sur la ressource de sortie du travail avant de soumettre le travail à la transformation.  Vous pouvez ensuite définir une seule transformation « Transcription audio » et transmettre les paramètres de langue requis par travail.

Le remplacement de présélection vous permet de transmettre une nouvelle définition de présélection personnalisée avec chaque travail soumis à la transformation. Cette propriété est disponible sur l’entité de [sortie du travail](/dotnet/api/microsoft.azure.management.media.models.joboutput) dans toutes les versions du SDK basées sur la version 2021-06-01 de l’API.

Pour référence, consultez la propriété [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) sur l’entité de sortie du travail dans la documentation REST.

> [!NOTE]
> Vous pouvez uniquement utiliser les remplacements de présélections pour remplacer les paramètres d’une présélection définie dans la transformation.  Vous ne pouvez pas passer d’une présélection spécifique à un autre type. Par exemple, si vous tentez de remplacer une transformation créée avec la présélection d’encodage de contenu intégrée pour utiliser une autre présélection comme l’analyseur audio, un message d’erreur s’affiche.


## <a name="example-of-preset-override-in-net"></a>Exemple de remplacement de présélection dans .NET

Un exemple complet utilisant le SDK .NET pour Media Services illustrant l’utilisation du remplacement de présélection avec une transformation de base de l’analyseur audio est disponible dans GitHub.
Pour plus d’informations sur l’utilisation de la propriété de remplacement de présélection de la sortie du travail, consultez l’exemple [Analyse d’un fichier multimédia avec une présélection d’analyseur audio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer).

## <a name="sample-code-of-preset-override-in-net"></a>Exemple de code d’un remplacement de présélection dans .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Charger, encoder et diffuser en continu à l’aide de Media Services](stream-files-tutorial-with-api.md).
* [Encoder à partir d’une URL HTTPS à l’aide de préréglages intégrés](job-input-from-http-how-to.md).
* [Encoder un fichier local à l’aide de préréglages intégrés](job-input-from-local-file-how-to.md).
* [Créer un préréglage intégré pour les besoins de votre scénario ou votre appareil](transform-custom-presets-how-to.md).
