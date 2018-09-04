---
title: Service Vision par ordinateur pour Microsoft Cognitive Services | Microsoft Docs
description: Utilisez des algorithmes avancés dans le service Vision par ordinateur pour traiter des images et renvoyer des informations dans Microsoft Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: v-deken
ms.openlocfilehash: ec3ffa8599192ecd9a4092b026a8d249f50c64c9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248621"
---
# <a name="what-is-computer-vision"></a>Qu’est-ce que le service Vision par ordinateur ?

Le service Vision par ordinateur basé sur le cloud offre aux développeurs un accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations. Ce service prend en charge les formats d’image courants, tels que JPEG et PNG. Pour analyser une image, vous pouvez télécharger une image ou spécifier une URL d’image. Les algorithmes du service Vision par ordinateur peuvent analyser le contenu d’une image de différentes manières, selon les composants visuels qui vous intéressent. Par exemple, le service Vision par ordinateur peut déterminer si une image contient un contenu pour adultes ou choquant, ou rechercher tous les visages dans une image.

Vous pouvez utiliser le service Vision par ordinateur dans votre application, en utilisant nos [bibliothèques clientes](quickstarts-sdk/csharp-analyze-sdk.md) pour appeler le service, ou en appelant directement [l’API REST](vision-api-how-to-topics/howtocallvisionapi.md), pour :

- [Analyser des images pour obtenir des informations](#analyzing-images-for-insight)
- [Extraire du texte à partir des images](#extracting-text-from-images)
- [Modérer du contenu dans les images](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Analyser des images pour obtenir des informations

Vous pouvez analyser des images à l’aide du service Vision par ordinateur pour obtenir des informations détaillées sur les composants et les caractéristiques visuels de vos images. Vous pouvez télécharger le contenu d’une image pour analyser les images locales, ou vous pouvez spécifier l’URL d’une image pour analyser des images à distance.

Le service Vision par ordinateur peut exécuter les actions suivantes lors de l’analyse d’une image :

| Action | Description |
| ------ | ----------- |
|**[Identifier les composants visuels à l’aide de balises](quickstarts/csharp-analyze.md)**|Identifier les composants visuels d’une image à l’aide de balises basées sur plus de 2 000 objets, êtres vivants, scènes et actions reconnaissables. Lorsque les balises sont ambigües ou inhabituelles, la réponse du service fournit des « indications » pour expliquer la signification de la balise dans le contexte des paramètres connus. Le balisage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc.|
|**[Catégoriser une image](quickstarts/csharp-analyze.md)**|Identifier et catégoriser une image dans son ensemble en utilisant une [taxonomie des catégories](Category-Taxonomy.md) comprenant des hiérarchies héréditaires parent/enfant. Les catégories peuvent être utilisées seules ou avec nos nouveaux modèles de balisage.<br/>Actuellement, l’anglais est la seule langue prise en charge pour le balisage et la catégorisation des images.|
|**[Décrire une image](quickstarts/csharp-analyze.md)**|Générer une description de l’intégralité d’une image dans un langage lisible utilisant des phrases complètes. Les algorithmes du service Vision par ordinateur génèrent différentes descriptions selon les objets identifiés dans l’image. Chacune des descriptions est évaluée, et un score de confiance est généré. Une liste est ensuite renvoyée, classée du score de confiance plus élevé au plus bas.<br/>Vous pouvez consulter un exemple de robot utilisant cette technologie pour générer des légendes pour les images [sur GitHub](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).|
|**[Détecter les visages](quickstarts/csharp-analyze.md)** |Détecter les visages dans une image et fournir des informations sur chaque visage détecté. Le service Vision par ordinateur indique les coordonnées, le rectangle, le sexe et l’âge pour chaque visage détecté.<br/>Il fournit un sous-ensemble de fonctionnalités proposées par le service [Visage](/azure/cognitive-services/face/). Vous pouvez également utiliser le service Visage pour effectuer une analyse plus détaillée, comme l’identification faciale et la détection de la pose.|
|**[Détecter les types d’images](quickstarts/csharp-analyze.md)**|Détecter les caractéristiques relatives à une image, par exemple si une image est un dessin au trait ou s’il pourrait s’agir d’une image clipart.|
|**[Détecter le contenu spécifique à un domaine](quickstarts/python-domain.md)**|Utiliser des modèles de domaine pour détecter et identifier le contenu spécifique à un domaine dans une image, notamment pour reconnaître des célébrités ou des éléments géographiques. Par exemple, si une image contient des célébrités, le service Vision par ordinateur peut utiliser un modèle de domaine pour célébrités intégré afin de déterminer si les personnes détectées dans l’image correspondent à des célébrités connues.|
|**[Détecter le jeu de couleurs](quickstarts/csharp-analyze.md)**|Analyser l’utilisation des couleurs dans une image. Le service Vision par ordinateur peut déterminer si une image est en noir et blanc ou en couleur. Pour les images en couleur, il peut également identifier les couleurs dominantes et d’accentuation.|
|**[Générer une miniature](quickstarts/csharp-thumb.md)**|Analyser le contenu d’une image pour en générer une miniature. Le service Vision par ordinateur commence par générer une miniature de haute qualité, puis analyse les objets contenus dans l’image pour déterminer la *zone d’intérêt*. Il rogne ensuite l’image pour conserver uniquement la zone d’intérêt. La miniature générée peut être présentée à l’aide de proportions différentes de celles de l’image d’origine selon les besoins de chacun.|

## <a name="extracting-text-from-images"></a>Extraire du texte à partir des images

Vous pouvez utiliser le service Vision par ordinateur pour [extraire du texte via la reconnaissance optique de caractères (OCR)](quickstarts/csharp-print-text.md) à partir d’une image sous forme de flux de caractères lisibles par ordinateur. Le cas échéant, la fonction OCR corrige la rotation du texte reconnu, en degrés, autour de l’axe horizontal de l’image et fournit les coordonnées du cadre de chaque mot. La fonction OCR prend en charge 25 langues et détecte automatiquement la langue du texte extrait.

Vous pouvez également [reconnaître le texte imprimé et le texte manuscrit](quickstarts/csharp-hand-text.md) à partir d’une image. Le service Vision par ordinateur peut détecter et extraire à la fois le texte imprimé et le texte manuscrit à partir d’images d’objets divers avec différents fonds et surfaces, tels que des reçus, des affiches, des cartes de visite, des courriers ou des tableaux blancs. Pour l’instant, la fonction de reconnaissance du texte manuscrit et imprimé est en préversion et prend uniquement en charge la langue Anglais.  

## <a name="moderating-content-in-images"></a>Modérer du contenu dans les images

Vous pouvez utiliser le service Vision par ordinateur pour [détecter du contenu pour adultes et choquant](quickstarts/csharp-analyze.md) dans une image, évaluer la probabilité que l’image contienne du contenu pour adultes ou choquant, et générer un score de confiance pour ces deux types de contenu. Le filtre pour la détection de contenu choquant et réservé aux adultes peut être défini sur une échelle variable afin de répondre à vos besoins spécifiques.

## <a name="image-requirements"></a>Exigences des images

Le service Vision par ordinateur peut analyser des images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG, GIF ou BMP
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être supérieures à 50 x 50 pixels  
  Pour la reconnaissance optique de caractères (OCR), les dimensions de l’image doivent être comprises entre 40 x 40 pixels et 3200 x 3200 pixels, et la taille de l’image ne peut pas être supérieure à 10 mégapixels.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser le service Vision par ordinateur en consultant l’un de nos guides de démarrage rapide :

- [Analyser une image](/quickstarts-sdk/csharp-analyze-sdk.md)
- [Extraire le texte manuscrit](/quickstarts-sdk/csharp-hand-text-sdk.md)
- [Générer une miniature](/quickstarts-sdk/csharp-thumb-sdk.md)
