---
title: Configuration de matériaux PBR dans Maya
description: Explique comment configurer des matériaux au rendu physique réaliste (les matériaux PBR) dans Maya et comment les exporter au format FBX
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977379"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Tutoriel : Configuration de matériaux PBR dans Maya

## <a name="overview"></a>Vue d’ensemble
Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Affecter des matériaux qui créent un modèle d’éclairage précis des objets dans la scène.
> * Gérer l’instanciation des objets et des matériaux.
> * Exporter une scène au format FBX et sélectionner les options importantes.

La création de [matériaux au rendu physique réaliste (matériaux PBR)](../../overview/features/pbr-materials.md) dans `Maya` est une tâche relativement simple, qui est semblable en de nombreux points à la configuration PBR dans d’autres applications de création de contenu comme `3DS Max`. Le tutoriel ci-dessous explique les étapes de base de la configuration du nuanceur PBR et de l’exportation au format FBX dans le cadre de projets ARR. 

L’exemple de scène utilisé dans ce tutoriel contient divers objets `Polygon Box` auxquels ont été affectés différents matériaux (bois, métal, métal peint, plastique et caoutchouc). Globalement, chaque matériau comporte la totalité ou la plupart des textures suivantes 

* `Albedo`, qui est la table des couleurs des matériaux, également appelée `Diffuse` ou `BaseColor`
* `Metalness`, qui détermine si un matériau est métallique et quelles parties de ce matériau sont métalliques. 
* `Roughness`, qui détermine dans quelle mesure la surface est rugueuse ou lisse, ce qui rend les réflexions et les surbrillances sur une surface plus ou moins nettes ou floues.
* `Normals`, qui ajoute des détails à une surface, par exemple des piqûres et des bosses sur une surface métallique ou un grain de bois, sans avoir à ajouter de polygones.
* `Ambient Occlusion`, qui ajoute un léger ombrage et des ombres de contact à un modèle. Il s’agit d’une carte de nuances de gris qui détermine quelles zones sont entièrement éclairées (blanc) ou ombrées (noir). 

## <a name="prerequisites"></a>Prérequis
* `Autodesk Maya 2017` ou version ultérieure

## <a name="setting-up-materials-in-the-scene"></a>Configuration de matériaux dans la scène
Dans Maya, le processus de configuration d’un matériau PBR est le suivant :

Dans un premier temps, comme vous le verrez dans l’exemple de scène, nous avons créé plusieurs objets Box, chacun représentant un type de matériau différent. Vous remarquerez dans l’image ci-dessous que chacun de ces objets a un nom approprié qui lui est propre 

> Avant de commencer à créer des ressources pour Azure Remote Rendering (ARR), il est important de noter que l’unité de mesure est le mètre et que l’axe Y correspond à la direction vers le haut. Il est donc conseillé de choisir les mètres comme unités de votre scène dans Maya. De la même façon, il est conseillé pour l’exportation de définir les unités sur les mètres dans les paramètres d’exportation au format FBX. 

> [!TIP]
Une bonne pratique est de nommer vos ressources de modèle d’une manière appropriée, généralement en mentionnant la partie ou le type de matériau en question. Choisir des noms pertinents facilitera la navigation dans les scènes contenant beaucoup d’objets.

![les noms d'objets ;](media/object-names.jpg)

Après avoir créé/obtenu vos textures (selon vos besoins, vous voudrez créer des textures uniques pour un modèle dans des applications de texturing comme `Quixel Suite`, `Photoshop` ou `Substance Suite`, ou bien utiliser des textures de mosaïque génériques d’autres sources), vous pouvez les appliquer à votre modèle comme suit :

* Dans la fenêtre d’affichage (le viewport) de votre scène, sélectionnez le modèle/la géométrie, puis cliquez dessus avec le bouton droit. Dans le menu qui s’affiche, cliquez sur `Assign New Material`
* Dans les options `Assign New Material`, accédez à `Maya`>`Stingray PBS`. Cette action affecte un matériau PBR à votre modèle. 

Dans `Maya 2020`, plusieurs nuanceurs PBR sont disponibles : `Maya Standard Surface`, `Arnold Standard Surface` et `Stingray PBR`. Le `Maya Standard Surface Shader` n’est pas exportable avec `FBX plugin 2020`, alors que le `Arnold Standard Surface Shader` peut être exporté avec des fichiers FBX. Sur la plupart des autres aspects, il est identique au `Maya Standard Surface Shader` et est analogue au `Physical Material` dans `3D Studio Max`.

**`The Stingray PBR Shader`** est compatible avec de nombreuses autres applications, il remplit globalement les exigences d’`ARR` et il est pris en charge depuis `Maya 2017`. De plus, il est pratique que ce type de matériaux visualisés dans le viewport ressemble à ce qui sera visualisé ultérieurement dans ARR.

![Matériau « Stingray »](media/stingray-material.jpg)

Maintenant que vous avez affecté le matériau à votre ressource et que vous l’avez nommé de manière appropriée, vous pouvez affecter vos différentes textures. Dans les images suivantes, vous voyez précisément à quoi correspond chaque type de texture dans le matériau PBR. Avec le matériau `Stingray PBR`, vous pouvez sélectionner les attributs à activer. Par conséquent, avant de pouvoir faire un `plug in` de vos cartes de textures, vous devez activer les attributs appropriés : 

![Configuration des matériaux](media/material-setup.jpg)

> [!TIP]
Une bonne pratique à suivre est de nommer vos matériaux de manière pertinente, en fonction de leur usage et/ou de leur type. Un matériau destiné à être utilisé sur une seule partie peut être nommé spécifiquement pour cette partie, alors qu’un matériau susceptible d’être utilisé plus largement sur différentes zones peut être nommé d’après ses propriétés ou son type.

Affectez vos textures comme ceci :

![Configuration des textures](media/texture-setup.jpg)

Vous avez créé et configuré vos matériaux PBR. Il est temps maintenant de réfléchir à l’[instanciation des objets](../../how-tos/conversion/configure-model-conversion.md#instancing) dans votre scène. L’instanciation d’objets similaires dans votre scène (par exemple, les écrous, les boulons et les rondelles), fondamentalement, tous les objets qui sont identiques, peut réduire considérablement la taille des fichiers. Les instances d’un objet principal peuvent avoir leurs propres transformations, rotation et échelle, ce qui vous permet de les placer où vous le souhaitez dans votre scène. Dans Maya, le processus d’instanciation est simple.

* Dans le menu `Edit`, accédez à `Duplicate Special` et ouvrez `Options` 
* Dans les options `Duplicate Special`, changez l’option `Geometry Type` de `Copy` à `Instance` 
* Cliquez sur `Duplicate Special`

![Instanciation](media/instancing.jpg)

Cette action crée une instance de votre objet qui peut être déplacée, pivotée ou mise à l’échelle indépendamment de son parent et des autres instances de ce parent. 
>Toutefois, sachez que les modifications apportées à une instance en mode composant sont transmises à toutes les instances de votre objet. Donc, si vous utilisez des composants d’objets instanciés, comme des sommets, des faces de polygone, etc., vous devez être sûr de vouloir appliquer les modifications à l’ensemble de ces instances.

Dans l’exemple de scène, chacun des objets Box a été instancié. Nous verrons en quoi cela est utile quand nous exporterons la scène au format FBX.

![Vue d’ensemble de la scène](media/scene-overview.jpg)

>La bonne pratique en matière d’instanciation dans votre scène consiste à créer les objets instanciés au fur et à mesure, car le remplacement ultérieur des « copies » par des objets instanciés est extrêmement difficile. 

## <a name="fbx-export-process"></a>Processus d’exportation au format FBX

Nous pouvons maintenant procéder à l’exportation au format FBX de votre scène ou de ses ressources. En règle générale, quand vous exportez des ressources, il vaut mieux sélectionner comme objets/ressources à exporter de votre scène uniquement ceux que vous comptez réutiliser. En effet, si votre scène contient 100 objets, mais que vous souhaitez utiliser seulement 30 d’entre eux, il n’y a aucun intérêt à exporter la scène entière. Donc, sauf si vous tenez vraiment à exporter l’intégralité de la scène, effectuez votre sélection, puis accédez à :

* `File` > `Export Selection` et, en bas de la boîte de dialogue d’exportation, définissez `Files of Type` sur `FBX Export`. Cette fenêtre présente les paramètres d’exportation au format FBX disponibles. Les principaux paramètres pour l’exportation au format FBX sont entourés en rouge dans l’image ci-dessous.

![Exportation au format FBX](media/FBX-exporting.jpg)

En fonction de vos besoins, par exemple, si vous souhaitez envoyer une ressource à un client sans joindre de multiples fichiers de textures avec la ressource, vous pouvez choisir d’incorporer les textures dans le fichier FBX exporté. En choisissant cette option, vous n’avez qu’un seul fichier à inclure dans le package, mais cela augmente considérablement la taille de cette ressource FBX. Pour activer l’incorporation des textures, cochez l’option `Embed Media` comme illustré ci-dessous.

> [!TIP]
> Notez qu’ici, le nom du fichier reflète la condition d’incorporation. C’est une bonne pratique qui facilite la gestion des ressources. 

Une fois que vous avez terminé la configuration de l’exportation, cliquez sur le bouton « Export Selection » (Exporter la sélection) dans le coin inférieur droit.

![Incorporation de ressource](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusion

En général, ce type de matériaux semble plus réaliste, car il est basé sur les vraies propriétés physiques de la lumière. Il ajoute un effet immersif qui donne l’impression que la scène existe réellement.

## <a name="next-steps"></a>Étapes suivantes

Vous connaissez à présent les fonctionnalités les plus importantes pour configurer des matériaux qui apportent un éclairage précis aux objets dans une scène et pour les exporter dans un format FBX pris en charge par ARR. Dans l’étape suivante, nous allons convertir le fichier FBX et le visualiser dans ARR.

> [!div class="nextstepaction"]
> [Démarrage rapide : Convertir un modèle pour le rendu](../../quickstarts\convert-model.md)