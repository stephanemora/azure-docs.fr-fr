---
title: Codes d’erreur de conversion de modèle
description: Codes d’erreur de conversion de modèle pour le service Azure Object Anchors.
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202668"
---
# <a name="model-conversion-error-codes"></a>Codes d’erreur de conversion de modèle

Pour les modes courants d’échec de conversion de modèle, l’objet `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties` obtenu à partir du champ `Value` dans `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation` contient un champ ErrorCode de type `ConversionErrorCode`. Ce type énumère ces modes courants d’échec pour localiser les messages d’erreur, récupérer après un échec et donner des conseils à l’utilisateur sur la façon de corriger l’erreur.

| Code d'erreur                    | Description                       |  Limitation des risques                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | La ressource au niveau de l’URI fournie lors du démarrage du travail de conversion est introuvable. | Lors du déclenchement d’un travail de conversion de ressource, fournissez un URI de chargement obtenu auprès du service dans lequel la ressource à convertir a été chargée. |
| INVALID_JOB_ID | L’ID fourni pour le travail de conversion de ressource à créer a été défini sur le GUID par défaut ne comportant que des zéros. | Si un GUID est spécifié lors de la création d’un travail de conversion de ressource, vérifiez qu’il ne s’agit pas du GUID par défaut ne comportant que des zéros. |
| INVALID_GRAVITY | Le vecteur de gravité fourni lors de la création du travail de conversion de ressource était un vecteur entièrement mis à zéro. | Lors du démarrage d’une conversion de ressource, fournissez le vecteur de gravité qui correspond à la ressource chargée. |
| INVALID_SCALE | Le facteur d’échelle fourni n’était pas une valeur positive non nulle. | Lors du démarrage d’une conversion de ressource, fournissez la valeur scalaire qui correspond à l’échelle de l’unité de mesure (par rapport aux compteurs) de la ressource chargée. |
| ASSET_SIZE_TOO_LARGE | Le fichier .PLY intermédiaire généré à partir de la ressource ou son équivalent sérialisé était trop volumineux. | Reportez-vous aux instructions relatives à la taille des ressources avant de soumettre une ressource à la conversion pour en garantir la conformité : aka.ms/aoa/faq. |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | Les dimensions de la ressource ont dépassé la limite physique autorisée. L’échelle de la ressource a peut-être été définie incorrectement lors de la création d’un travail. | Reportez-vous aux instructions relatives à la taille des ressources avant de soumettre une ressource à la conversion pour en garantir la conformité et vérifiez que l’échelle fournie correspond à la ressource chargée : aka.ms/aoa/faq. |
| ZERO_FACES | Le fichier .PLY intermédiaire généré à partir de la ressource a été identifié comme n’ayant pas de face, ce qui le rend non valide pour une conversion. | Vérifiez que la ressource est un maillage valide. |
| INVALID_FACE_VERTICES | Le fichier .PLY intermédiaire généré à partir de la ressource contient des faces qui font référence à des vertex inexistants. | Vérifiez que le fichier de la ressource est construit de façon valide. |
| ZERO_TRAJECTORIES_GENERATED | Les trajectoires de caméra générées à partir de la ressource chargée étaient vides. | Reportez-vous aux instructions relatives aux ressources avant de soumettre une ressource à la conversion pour en garantir la conformité : aka.ms/aoa/faq. |
| TOO_MANY_RIG_POSES | Le nombre de poses de plateforme dans le fichier .PLY intermédiaire a dépassé les limites du service. | Reportez-vous aux instructions relatives à la taille des ressources avant de soumettre une ressource à la conversion pour en garantir la conformité : aka.ms/aoa/faq. |
| SERVICE_ERROR | Une erreur de service inconnue s’est produite. | Contactez un membre de l’équipe du service Object Anchors si le problème persiste : https://github.com/Azure/azure-object-anchors/issues |
| ASSET_CANNOT_BE_CONVERTED | La ressource fournie est endommagée, malformée ou impossible à convertir dans son format. | Vérifiez que la ressource est un fichier du type spécifié et construit de façon valide, puis reportez-vous aux instructions relatives à la taille des ressources avant de soumettre une ressource à la conversion pour en garantir la conformité : aka.ms/aoa/faq. |

Toutes les erreurs qui se produisent en dehors de réels travaux de conversion de ressource sont levées en tant qu’exceptions. Plus particulièrement, une `Azure.RequestFailedException` peut être levée pour les appels de service qui reçoivent un code de réponse HTTP incorrect (4xx ou 5xx) ou inattendu. Pour plus d’informations sur ces exceptions, examinez les champs `Status`, `ErrorCode` ou `Message` de l’exception.

| Exception                  | Cause                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>Se produit lors de l’utilisation d’un ID de compte non valide ou nul pour construire une requête avec ObjectAnchorsConversionClient.</li><li>Se produit lors de la tentative d’initialisation d’ObjectAnchorsConversionClient à l’aide d’un domaine de compte à espacement non valide.</li><li>Se produit lorsqu’une version de service non prise en charge est fournie à ObjectAnchorsConversionClient par le biais d’ObjectAnchorsConversionClientOptions.</li></ul> |
| ArgumentNullException | <ul><li>Se produit lors de la tentative d’initialisation d’ObjectAnchorsConversionClient à l’aide d’un domaine de compte Null non valide.</li><li>Se produit lors de la tentative d’initialisation d’ObjectAnchorsConversionClient à l’aide d’informations d’identification Null non valides.</li></ul> |
| RequestFailedException | <ul><li>Se produit pour tous les autres problèmes qui résultent d’un code d’état HTTP incorrect (sans rapport avec l’état d’un travail qui sera/est/a été exécuté), tel qu’un compte introuvable, un URI de chargement non valide détecté par le serveur frontal, une erreur de service du serveur frontal, etc.</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>Se produit lors d’une tentative d’envoi d’un travail avec une ressource dont l’extension ou le type de fichier spécifié n’est pas pris en charge par le service Azure Object Anchors Conversion.</li></ul> |