---
title: Présentation de l’API Vision par ordinateur
titlesuffix: Azure Cognitive Services
description: L’API Vision par ordinateur offre aux développeurs un accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: acd6d41e8b6d1fb834697ec3d026419ee6b69ec9
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582352"
---
# <a name="what-is-computer-vision-api-version-10"></a>Qu’est-ce que l’API Vision par ordinateur Version 1.0 ?

> [!IMPORTANT]
> Une nouvelle version de l’API Vision par ordinateur est maintenant disponible, consultez les documents suivants :
>- [Vue d'ensemble](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [API Vision par ordinateur Version 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

L’API Vision par ordinateur basée sur le cloud offre aux développeurs un accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations. En téléchargeant une image ou en spécifiant l’URL d’une image, les algorithmes Vision par ordinateur Microsoft peuvent analyser du contenu visuel de différentes façons selon les entrées et les choix de l’utilisateur. Avec l’API Vision par ordinateur, les utilisateurs peuvent analyser les images afin de :
* [Baliser des images en fonction du contenu.](#Tagging)
* [Classer des images.](#Categorizing)
* [Identifier le type et la qualité des images.](#Identifying)
* [Détecter des visages et renvoyer leurs coordonnées. ](#Faces)
* [Reconnaître le contenu spécifique à un domaine.](#Domain-Specific)
* [Générer des descriptions de contenu.](#Descriptions)
* [Utiliser la reconnaissance optique des caractères afin d’identifier le texte imprimé trouvé dans les images.](#OCR)
* [Reconnaître le texte manuscrit.](#RecognizeText)
* [Distinguer des jeux de couleurs.](#Color)
* [Signaler le contenu réservé aux adultes.](#Adult)
* [Rogner des photos à utiliser sous forme de miniatures.](#Thumbnails)

## <a name="requirements"></a>Configuration requise
* Méthodes d’entrée prises en charge : image Raw binaire sous forme de flux application/octet ou d’URL d’image.
* Formats d’image pris en charge : JPEG, PNG, GIF, BMP.
* Taille du fichier image : inférieure à 4 Mo.
* Dimensions de l’image : plus de 50 x 50 pixels.

## <a name="tagging-images"></a>Attribuer des balises aux images
L’API Vision par ordinateur retourne des balises basées sur des milliers d’objets, d’êtres vivants, de scènes et d’actions reconnaissables. Lorsque les balises sont ambigües ou inhabituelles, la réponse de l’API fournit des « conseils » pour expliquer la signification de la balise dans le contexte des paramètres connus. Les balises ne sont pas organisées sous forme de taxonomie et aucune hiérarchie d’héritage n’existe. La collection de balises de contenu constitue la « description » essentielle d’une image, qui est affichée sous forme de texte lisible par l’homme (phrases complètes). Notez que, à ce stade, seul l’anglais est pris en charge pour la description d’images.

Après le chargement d’une image ou d’une URL d’image, les algorithmes de l’API Vision par ordinateur génèrent des balises basées sur les objets, les êtres vivants et les actions identifiées dans l’image. Le balisage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc.

### <a name="example"></a>Exemples
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Classement des Images
Outre le balisage et les descriptions, l’API Vision par ordinateur renvoie les catégories basées sur la taxonomie définies dans les versions précédentes. Ces catégories sont classées au sein d’une taxonomie comprenant des hiérarchies héréditaire parent/enfant. Toutes les catégories sont en anglais. Elles peuvent être utilisées seules ou avec les nouveaux modèles.

### <a name="the-86-category-concept"></a>Concept des 86 catégories
Selon une liste de 86 concepts (voir diagramme ci-dessous), les fonctionnalités visuelles d’une image peuvent être classées, qu’elles soient générales ou spécifiques. Pour consulter la taxonomie complète au format texte, consultez [Taxonomie des catégories](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analyser les catégories](./Images/analyze_categories.png)

Image                                                  | response
------------------------------------------------------ | ----------------
![Toit femme](./Images/woman_roof.png)                 | people
![Photo de famille](./Images/family_photo.png)             | people_crowd
![Chien mignon](./Images/cute_dog.png)                     | animal_dog
![Montagne extérieur](./Images/mountain_vista.png)       | outdoor_mountain
![Analyse Vision aliment pain](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identifier les types d’images
Plusieurs possibilités s’offrent à vous pour classer des images. L’API Vision par ordinateur peut définir un indicateur booléen pour spécifier si une image est en noir et blanc ou en couleur. Elle peut également définir un indicateur pour spécifier si une image est un dessin au trait ou non. Elle peut également indiquer si une image est une image clipart ou non et indiquer sa qualité sur une échelle de 0 à 3.

### <a name="clip-art-type"></a>Type d’image clipart
Détecte si une image est une image clipart ou non.  

Valeur | Signification
----- | --------------
0     | Non-clip-art
1     | ambiguous
2     | normal-clip-art
3     | good-clip-art

Image|response
----|----
![Analyse Vision clipart fromage](./Images/cheese_clipart.png)|3 good-clip-art
![Analyse Vision jardin maison](./Images/house_yard.png)|0 Non-clip-art

### <a name="line-drawing-type"></a>Type d’image dessin au trait
Détecte si une image est un dessin au trait ou non.

Image|response
----|----
![Analyse Vision dessin lion](./Images/lion_drawing.png)|True
![Analyse Vision fleur](./Images/flower.png)|False

### <a name="faces"></a>Visages
Détecte les visages au sein d’une image et génère les coordonnées du visage, le rectangle du visage, le sexe et âge. Ces fonctionnalités visuelles constituent un sous-ensemble des métadonnées générées pour le visage. Pour obtenir des métadonnées plus complètes sur les visages (identification faciale, détection de la pose, etc.), utilisez l’API Visage.  

Image|response
----|----
![Analyse Vision femme toit visage](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Analyse Vision mère fille visage](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Analyse Vision photo de famille visage](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Contenu spécifique à un domaine

Outre le balisage et la catégorisation très précise, l’API Vision par ordinateur prend également en charge les informations spécialisées (ou spécifiques à un domaine). Les informations spécialisées peuvent être implémentées en tant que méthode autonome ou à l’aide de la catégorisation précise. Elles permettent d’affiner la taxonomie des 86 catégories via l’ajout de modèles spécifiques aux domaines.

À l’heure actuelle, la seule information spécialisée prise en charge porte sur la reconnaissance de célébrités et la reconnaissance des monuments. Il s’agit d’un affinage spécifique à un domaine pour les catégories de personnes, de groupes de personnes et de monuments dans le monde entier.

Deux options existent pour utiliser les modèles spécifiques à un domaine :

### <a name="option-one---scoped-analysis"></a>Option 1 - analyse étendue
Analysez uniquement un modèle choisi, en lançant un appel HTTP POST. Pour cette option, si vous avez choisi le modèle que vous souhaitez utiliser, spécifiez le nom du modèle pour obtenir uniquement les informations pertinentes pour ce modèle. Par exemple, vous pouvez utiliser cette option pour ne rechercher que la reconnaissance de célébrités. La réponse contient une liste de célébrités susceptibles de correspondre, accompagnées de scores de confiance.

### <a name="option-two---enhanced-analysis"></a>Option 2 - analyse améliorée
Effectuez une analyse pour fournir des informations supplémentaires en lien avec les catégories de la taxonomie des 86 catégories. Cette option peut être utilisée dans les applications où les utilisateurs souhaitent obtenir une analyse d’image générique en plus des détails issus d’un ou de plusieurs modèles spécifique à un domaine. Lorsque cette méthode est appelée, le classifieur de la taxonomie des 86 catégories est appelé en premier. Si aucune des catégories ne correspond à celle des modèles connus ou correspondants, une deuxième session d’appels du classifieur est lancée. Par exemple, si 'details=all' ou « details » incluent 'celebrities', la méthode appelle le classifieur de célébrités après que le classifieur des 86 catégories soit appelé. Le résultat inclut les balises commençant par « people_ ».

## <a name="generating-descriptions"></a>Générer des descriptions 
Les algorithmes de l’API Vision par ordinateur analysent le contenu d’une image. L’analyse constitue la description essentielle qui est affichée sous forme de texte lisible par l’homme (phrases complètes). La description résume les éléments identifiés dans l’image. Les algorithmes de l’API Vision par ordinateur génèrent différentes descriptions selon les objets identifiés dans l’image. Chacune des descriptions est évaluée, et un score de confiance est généré. Une liste est ensuite renvoyée, classée du score de confiance plus élevé au plus bas. Vous pouvez consulter un exemple de robot utilisant cette technologie pour générer des légendes pour les images [ici](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Exemple de génération de description
![Bâtiments B&W](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Percevoir les jeux de couleurs
L’algorithme Vision par ordinateur extrait les couleurs d’une image. Les couleurs sont analysées dans trois contextes différents (premier plan, arrière-plan et dans l’ensemble). Elles sont groupées en 12 couleurs d’accentuation dominantes. Les couleurs d’accentuation sont les suivantes : noir, bleu, marron, gris, vert, orange, rose, violet, rouge, vert foncé, blanc et jaune. Selon les images, qu’elles soient simplement en noir et blanc ou comportant des couleurs d’accentuation, les couleurs peuvent être renvoyées en codes de couleur hexadécimaux.

Image                                                       | Premier plan |Arrière-plan| Couleurs
----------------------------------------------------------- | --------- | ------- | ------
![Montagne extérieur](./Images/mountain_vista.png)            | Noir     | Noir   | Blanc
![Analyse Vision fleur](./Images/flower.png)               | Noir     | Blanc   | Blanc, noir, vert
![Analyse Vision gare ferroviaire](./Images/train_station.png) | Noir     | Noir   | Noir

### <a name="accent-color"></a>Couleur d’accentuation
Il s’agit de la couleur extraite d’une image, visant à représenter la couleur qui attire le plus l’œil des utilisateurs, à l’aide d’un mélange des couleurs dominantes et de la saturation.

Image                                                       | response
----------------------------------------------------------- | ----
![Montagne extérieur](./Images/mountain_vista.png)            | #BC6F0F
![Analyse Vision fleur](./Images/flower.png)               | #CAA501
![Analyse Vision gare ferroviaire](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Noir et blanc
Indicateur booléen qui spécifie si une image est en noir et blanc ou non.

Image                                                      | response
---------------------------------------------------------- | ----
![Analyse Vision bâtiment](./Images/bw_buildings.png)      | True
![Analyse Vision jardin maison](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Signaler le contenu réservé aux adultes
Les différentes catégories visuelles comptent également le groupe pour le contenu suggestif et/ou réservé aux adultes qui permet de détecter des sujets pour adultes et restreint l’affichage des images à caractère sexuel. Le filtre pour la détection de contenu suggestif et réservé aux adultes peut être défini sur une échelle variable pour s’adapter aux préférences de l’utilisateur.

## <a name="optical-character-recognition-ocr"></a>Reconnaissance optique des caractères (OCR)
La technologie d’OCR détecte le contenu textuel dans une image et extrait le texte identifié sous forme de flux de caractères lisibles par ordinateur. Vous pouvez utiliser le résultat pour effectuer des recherches ou l’appliquer à de nombreuses autres applications, comme les dossiers médicaux, la sécurité et le secteur bancaire. Cette technologie détecte automatiquement la langue. Elle permet de gagner du temps, et elle est pratique car elle permet de prendre des photos du texte au lieu de le retranscrire.

L’OCR prend en charge 25 langues. Ces langues sont les suivantes : arabe, chinois simplifié, chinois traditionnel, tchèque, danois, néerlandais, anglais, finnois, français, allemand, grec, hongrois, italien, japonais, coréen, norvégien, polonais, portugais, roumain, russe, serbe (cyrillique et latin), slovaque, espagnol, suédois et turc.

Si nécessaire, l’OCR corrige la rotation du texte reconnu, en degrés, autour de l’axe horizontal de l’image. L’OCR fournit les coordonnées du cadre de chaque mot, comme illustré ci-dessous.

![Vue d’ensemble de la reconnaissance optique des caractères](./Images/vision-overview-ocr.png) Configuration requise pour l’OCR :
- La taille de l’image entrante doit être comprise entre 40 x 40 et 3200 x 3200 pixels.
- L’image ne peut pas dépasser 10 mégapixels.

L’image d’entrée peut être pivotée par multiples de 90 degrés, plus un faible angle de 40 degrés maximum.

La précision de la reconnaissance du texte dépend de la qualité de l’image. Une lecture inexacte peut être due aux situations suivantes :
- Images floues.
- Texte manuscrit ou lié.
- Styles de police artistiques.
- Taille de police trop petite.
- Arrière-plans complexes, ombres ou reflets sur le texte ou distorsion de perspective.
- Lettres majuscules trop grandes ou manquantes au début des mots.
- Texte barré, exposant ou indice.

 Limites : Sur les photos où le texte est dominant, de faux positifs peuvent provenir de mots partiellement reconnus. Sur certaines photos, en particulier les photos sans texte, la précision peut beaucoup varier selon le type d’image.

## <a name="recognize-handwritten-text"></a>Reconnaître le texte manuscrit
Cette technologie vous permet de détecter et d’extraire du texte écrit à la main sur des notes, des lettres, des dissertations, des tableaux blancs, des formulaires, etc. Elle fonctionne avec différents surfaces et fonds, comme du papier blanc, des notes autocollantes jaunes et des tableaux blancs.

La reconnaissance du texte manuscrit vous permet de gagner du temps et augmente votre productivité en vous permettant de prendre des images d’un texte, plutôt que de le retranscrire. Elle permet de numériser les notes. Cette numérisation vous permet d’implémenter une recherche simple et rapide. Elle réduit également l’encombrement papier.

Configuration de l’entrée :
- Formats d’image pris en charge : JPEG, PNG et BMP.
- La taille du fichier image doit être inférieure à 4 Mo.
- Les dimensions de l’image doivent être de 40 x 40 au minimum et de 3200 x 3200 au maximum.

Remarque : cette technologie est actuellement en préversion et n’est disponible que pour du texte écrit en anglais.

## <a name="generating-thumbnails"></a>Générer des miniatures
Une miniature est une petite représentation d’une image en taille réelle. Divers appareils tels que les téléphones, les tablettes et les PC nécessitent l’utilisation de mises en page et de tailles de miniatures différentes pour leur expérience utilisateur. La fonctionnalité de rognage intelligent de l’API Vision par ordinateur permet de résoudre le problème.

Après avoir chargé une image, une miniature de haute qualité est générée et l’algorithme de l’API Vision par ordinateur analyse les objets au sein de l’image. Il rogne ensuite l’image pour conserver uniquement la « zone d’intérêt ». La sortie s’affiche au sein d’un cadre spécifique comme illustré ci-dessous. La miniature générée peut être présentée à l’aide proportions différentes de celles de l’image d’origine pour s’adapter aux besoins de l’utilisateur.

L’algorithme des miniatures fonctionne de la façon suivante :

1. Il supprime les éléments parasites de l’image et reconnaît l’objet principal, la « zone d’intérêt ».
2. Il rogne l’image en fonction de la zone d’intérêt identifiée.
3. Il modifie les proportions afin de s’adapter aux dimensions cibles de la miniature.

![Miniatures](./Images/thumbnail-demo.png)
