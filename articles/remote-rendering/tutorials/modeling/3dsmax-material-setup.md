---
title: Configurer des matériaux PBR dans 3DSMax
description: Explique comment configurer des matériaux au rendu physique réaliste (matériaux PBR) dans 3DSMax et comment les exporter au format FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857550"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Tutoriel : Configurer des matériaux PBR dans 3D Studio Max

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
>
> * Affecter des matériaux dotés d’un éclairage avancé à des objets dans la scène.
> * Gérer l’instanciation des objets et des matériaux.
> * Exporter une scène au format FBX et sélectionner les options importantes.

La création de [matériaux au rendu physique réaliste (matériaux PBR)](../../overview/features/pbr-materials.md) dans 3D Studio Max (3DSMax) est une tâche relativement simple. Elle est semblable en de nombreux points à la configuration du rendu PBR dans d’autres applications de création de contenu comme Maya. Ce tutoriel explique les étapes de base de la configuration du nuanceur PBR et de l’exportation au format FBX dans le cadre de projets Azure Remote Rendering.

L’exemple de scène de ce tutoriel contient plusieurs boîtes polygonales. Différents matériaux leur sont attribués, tels que du bois, du métal, du métal peint, du plastique et du caoutchouc. Globalement, chaque matériau comporte la totalité ou la plupart des textures suivantes :

* **Albedo**, qui est la table des couleurs du matériau, également appelée **Diffuse** ou **BaseColor**.
* **Metalness** (Aspect métallique), qui détermine si un matériau est métallique et quelles parties sont métalliques. 
* **Roughness** (Rugosité), qui détermine dans quelle mesure une surface est rugueuse ou lisse.
Cela rend également les réflexions et les surbrillances de la surface plus ou moins nettes ou floues.
* **Normal**, qui ajoute des détails à une surface sans imposer l’ajout de polygones. Des exemples de tels détails sont des piqûres et des bosses sur une surface métallique ou un grain de bois.
* **Ambient Occlusion** (Occlusion ambiante), qui ajoute un léger ombrage et des ombres de contact à un modèle. Il s’agit d’une carte de nuances de gris qui détermine quelles zones sont totalement éclairées (blanches) ou ombrées (noires).

## <a name="prepare-the-scene"></a>Préparer la scène
Dans **3D Studio Max**, le processus de configuration d’un matériau PBR est le suivant.

Dans un premier temps, comme vous le verrez dans l’exemple de scène, nous avons créé plusieurs objets de boîte, chacun représentant un type de matériau différent :

>[!TIP]
>Avant de commencer à créer des ressources pour ARR, il est important de noter que l’unité de mesure que le logiciel utilise est le **mètre**.  
>Il est donc conseillé de choisir les **mètres** comme **unités système** de votre scène. De la même façon, il est conseillé pour l’exportation de définir les unités sur les mètres dans les paramètres d’exportation au format FBX.

L’illustration suivante montre les étapes permettant de définir les unités système sur des mètres dans 3D Studio Max. Dans le menu principal, accédez à **Customize** (Personnaliser) > **Units Setup** (Configuration des unités) > **System Units Setup** (Configuration des unités système) et, dans la liste déroulante **System Units Scale** (Échelle des unités système), sélectionnez **Meters** (Mètres). 
![Unités système](media/3dsmax/system-units.jpg)

Une fois les unités système définies sur mètres, nous pouvons commencer à créer nos modèles. Dans notre exemple de scène, nous créons plusieurs objets de boîte, chacun représentant un type de matériau différent (par exemple, métal, caoutchouc, plastique, etc.). 

>[!TIP]
>Une bonne pratique, lors de la création de ressources, consiste à les nommer de façon pertinente au fur et à mesure. Cela vous aidera à les rechercher plus tard si la scène comporte un grand nombre d’objets.

![rename-objects](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Affecter des matériaux

Avec certains objets créés dans notre scène (dans ce cas, plusieurs cubes), nous pouvons commencer la configuration du rendu PBR :

* Dans la barre d’outils principale, cliquez sur l’icône **Material Editor** (Éditeur de matériaux), comme illustré dans l’image suivante. Vous pouvez également appuyer sur la touche **M** du clavier pour ouvrir l’éditeur. L’éditeur de matériaux a deux modes qui peuvent être sélectionnés dans la liste déroulante **Modes** – le mode **Compact material editor** (Éditeur de matériaux compacts) et le mode **Slate material** (Ardoise). Comme cette scène est relativement simple, nous allons utiliser le **mode compact**.

* Dans l’éditeur de matériaux, vous voyez plusieurs sphères : ces sphères sont nos matériaux. Nous allons affecter l’un de ces matériaux à chaque objet (boîte) dans notre scène. Pour effectuer cette affectation, commencez par sélectionner l’un des objets dans la fenêtre d’affichage principale. Une fois cette sélection effectuée, cliquez sur la première sphère dans la fenêtre de l’éditeur de matériaux. Une fois affecté à un objet, votre matériau sélectionné est mis en évidence comme dans l’image suivante.

* Cliquez sur le bouton **Assign Material to Selection** (Affecter le matériau à la sélection) comme illustré. Le matériau sélectionné a maintenant été affecté à l’objet sélectionné.
![assign-material](media/3dsmax/assign-material.jpg)

Dans l’éditeur de matériaux, vous pouvez sélectionner des types de matériaux parmi une vaste sélection, en fonction de votre cas d’utilisation. En règle générale, le type de matériau est défini sur **Standard** par défaut. Ce matériau est un matériau de base qui ne convient pas à la configuration du rendu PBR. Nous devons donc remplacer ce type de matériau par un matériau PBR. Le matériau **3DSMax** préféré pour les projets Azure Remote Rendering est **Physical Material** (Matériau physique).

* Dans l’éditeur de matériaux, cliquez sur l’onglet **Standard** et, dans le navigateur de matériaux/cartes qui s’ouvre, sélectionnez **Physical Material** (Matériau physique). Cette action permet de convertir le matériau **Standard** affecté en un **matériau physique** PBR.
![physical-material](media/3dsmax/physical-material.jpg)

* Dans l’éditeur de matériaux, vous voyez maintenant les propriétés du matériau physique (voir ci-dessous) et nous pouvons commencer à assigner des textures à la ressource.
![textures-list](media/3dsmax/textures-list.jpg)

Comme vous pouvez le voir sur l’image ci-dessus, il existe un large éventail de cartes et de textures qui peuvent être ajoutées au matériau. Toutefois, dans le cadre de nos objectifs, nous n’utiliserons que cinq emplacements de texture dans le matériau.

>[!TIP]
>Une bonne pratique consiste à nommer vos matériaux de manière pertinente, comme illustré dans l’image ci-dessus.

Nous pouvons maintenant commencer à envisager d’affecter des textures à notre matériau. La façon dont vous générez vos textures peut varier en fonction de vos préférences, voire même en fonction de leur utilisation. Par exemple, vous pouvez vous satisfaire d’utiliser des textures de mosaïque applicables à n’importe quelle ressource ou vous pouvez exiger que certaines pièces spécifiques d’un projet/d’une ressource aient leur propre ensemble personnalisé de textures. Vous pouvez utiliser des textures de mosaïque génériques obtenues en ligne ou les créer vous-même dans des applications telles que **Photoshop**, **Quixel Suite**, **Substance Suite**, etc. 

Avant de commencer à affecter nos textures, nous devons considérer nos coordonnées de texture de ressource (UVW). Bien qu’il soit recommandé, lors de l’application de textures à un modèle, de s’assurer que le modèle a été déplié (les textures ne s’afficheront pas correctement sans un dépliage UV approprié), cela est important dans notre cas, si nous envisageons d’utiliser une carte d’**occlusion ambiante** sur notre modèle. Contrairement au nuanceur **Stingray Shader** dans **Maya**, **Physical Material** (Matériau physique) dans **3DSMax** n’a pas d’emplacement de texture d’**occlusion ambiante** dédié. Par conséquent, nous allons appliquer la carte AO dans un autre emplacement et, pour lui permettre d’être utilisée séparément des autres textures (par exemple, des textures de mosaïque), nous lui affecterons un canal de mappage UVW distinct. 

Nous allons commencer par affecter un modificateur **Unwrap UVW** (Déplier UVW) à notre modèle, comme illustré ci-dessous :

* Dans l’éditeur des propriétés des objets sélectionnés, cliquez sur Modifier List (Liste des modificateurs), faites défiler la liste déroulante correspondante vers le bas et sélectionnez Unwrap UVW (Déplier UVW). Cette action appliquera un modificateur de dépliage UVW à notre ressource.
![unwrap-modifier](media/3dsmax/unwrap-modifier.jpg)

* Le canal de mappage est défini sur 1. C’est dans le canal de mappage 1 que votre dépliage principal sera généralement effectué. Dans le cas présent, l’objet a été déplié sans coordonnées de texture superposées (UV).
![unwrapped-uvw](media/3dsmax/unwrapped-uvw.jpg)

L’étape suivante consiste à créer un deuxième canal de mappage UV.

* Fermez l’éditeur UV s’il est ouvert et, dans la section Canal du menu **Edit UVs** (Modifier les UV), remplacez le numéro de canal par 2. Le canal de mappage 2 est le canal attendu pour les cartes d’occlusion ambiante. 

* Dans la boîte de dialogue **Channel Change Warning** (Avertissement de modification de canal) qui s’ouvre, vous avez la possibilité de déplacer (**Move**) les UV existants du canal 1 vers le nouveau canal 2, ou d’abandonner (**Abandon**) les UV existants, ce qui créera automatiquement un nouveau dépliage UV (**UV Unwrap**). Sélectionnez **Abandon** seulement si vous envisagez de créer un nouveau dépliage UV (**UV Unwrap**) pour la carte d’occlusion ambiante qui diffère des UV du canal de mappage 1 (par exemple, si vous souhaitez utiliser des textures de mosaïque dans le canal 1). Dans le cadre de nos objectifs, nous allons déplacer (**Move**) les UV du canal 1 vers le canal 2, car nous n’avons pas besoin de modifier le nouveau canal UV.

>[!NOTE]
>Même si vous avez copié – **déplacé** – le dépliage UV du canal de mappage 1 vers le canal de mappage 2, vous pouvez apporter toutes les modifications nécessaires aux UV du nouveau canal sans affecter le canal de mappage d’origine.

![channel-change](media/3dsmax/channel-change.jpg)

Une fois le nouveau canal de mappage créé, nous pouvons revenir au matériau physique dans l’éditeur de matériaux et commencer à lui ajouter nos textures. Nous allons d’abord ajouter la carte d’occlusion ambiante (**AO**), car il existe une étape supplémentaire à effectuer pour lui permettre de fonctionner correctement. Une fois que la carte AO est insérée dans notre matériau, nous devons lui demander d’utiliser le canal de mappage 2.

* Comme mentionné précédemment, il n’existe pas d’emplacement dédié pour les cartes AO dans le **matériau physique 3DSMax**. Au lieu de cela, nous appliquerons la carte AO à l’emplacement **Diffuse Roughness** (Rugosité diffuse).

* Dans la liste **Generic Maps** (Cartes génériques) du matériau physique, cliquez sur l’emplacement **No Map** (Aucune carte) de **Diffuse Roughness** (Rugosité diffuse) et chargez votre carte AO.

* Dans les propriétés des textures AO, vous verrez le canal de mappage défini sur **1** par défaut. Remplacez cette valeur par **2**. Cette action termine la procédure d’ajout de votre carte d’occlusion ambiante.

>[!IMPORTANT]
>Il s’agit d’une étape importante, en particulier si vos UV dans le canal 2 sont différents de ceux du canal 1, car l’occlusion ambiante n’effectuera pas un mappage correct si le mauvais canal est sélectionné.

![assign-ao-map](media/3dsmax/assign-ao-map.jpg)

Nous allons maintenant aborder l’affectation de notre carte de normales (normal map) à notre matériau PBR. Cette action diffère quelque peu de **Maya** dans le fait que la carte de normales n’est pas appliquée directement à l’emplacement de la carte de relief (bump map) (il n’y a pas d’emplacement de carte de normales dans le **matériau physique 3DSMax**), mais elle est ajoutée à un modificateur de carte de normales, qui est lui-même inséré dans l’emplacement **normals** (normales).

* Dans la section **Special Maps** (Cartes spéciales) des propriétés de notre matériau physique (dans l’éditeur de matériaux), cliquez sur l’emplacement **No Map** (Aucune carte) de **Bump Map** (Carte de relief). 

* Dans le navigateur de matériaux/cartes, recherchez et cliquez sur **Normal Bump** (Relief normal). Cette action ajoute un modificateur **Normal Bump** (Relief normal) à notre matériau.

* Dans le modificateur **Normal Bump** (Relief normal), cliquez sur **No Map** (Aucune carte) de **Normal**, puis recherchez et chargez votre carte de normales.

* Vérifiez que la méthode est définie sur **Tangente** (elle doit l’être par défaut) et, si nécessaire, activez/désactivez **Flip Green (Y)** (Inverser le vert (Y)).

![normal-bump](media/3dsmax/normal-bump.jpg)
![load-normal-map](media/3dsmax/load-normal-map.jpg)

Une fois notre carte de normales correctement affectée, nous pouvons passer à l’affectation des textures restantes pour terminer la configuration de notre matériau physique. Ce processus est un processus simple sans paramètres spéciaux à prendre en compte. L’illustration suivante montre l’ensemble complet de textures affectées à notre matériau : ![all-textures](media/3dsmax/all-textures.jpg)

Vous avez créé et configuré vos matériaux PBR. Il est temps maintenant de réfléchir à l’instanciation des objets dans votre scène. L’instanciation d’objets similaires dans votre scène (par exemple, les écrous, les boulons et les rondelles), fondamentalement, tous les objets qui sont identiques, peut réduire considérablement la taille des fichiers. Les instances d’un objet principal peuvent avoir leurs propres transformations, rotation et échelle, ce qui vous permet de les placer où vous le souhaitez dans votre scène. Dans **3D Studio Max**, le processus d’**instanciation** est simple.

* Dans la fenêtre d’affichage principale, sélectionnez le ou les objets que vous souhaitez exporter.

* Maintenez **Maj** et faites glisser les ressources vers le haut à l’aide de l’outil de transformation (déplacement) 

* Dans la boîte de dialogue **Clone Options** (Options de clonage) qui s’ouvre, définissez **Object** (Objet) sur **Instance** et cliquez sur **OK**. 
![instance-object](media/3dsmax/instance-object.jpg)

Cette action crée une instance de votre objet qui peut être déplacée, pivotée ou mise à l’échelle indépendamment de son parent et des autres instances de ce parent.

>[!IMPORTANT]
>Toutefois, sachez que les modifications apportées à une instance en mode sous-objet sont transmises à toutes les instances de votre objet. Donc, si vous utilisez des composants d’objets instanciés, comme des sommets, des faces de polygone, etc., vous devez être sûr de vouloir appliquer les modifications à l’ensemble de ces instances. N’oubliez pas que tout objet instancié peut être transformé en objet unique à tout moment. 

>[!TIP]
>Une bonne pratique en matière d’instanciation dans votre scène consiste à créer les objets instanciés au fur et à mesure, car le remplacement ultérieur de **copies** par des objets instanciés est extrêmement difficile. 

Une dernière chose à prendre en compte avant de passer au processus d’exportation est la façon dont vous pouvez empaqueter votre scène/ressource pour le partage. Dans l’idéal, si vous transmettez la ressource à un client ou à un membre d’équipe, vous souhaitez qu’il puisse l’ouvrir et l’afficher avec le moins de difficultés possible. Il est donc important de conserver les chemins des textures des ressources relatifs au fichier de scène. Si les chemins des textures de votre ressource pointent vers un lecteur local ou un chemin/emplacement absolu, elles ne seront pas chargées dans la scène si celle-ci est ouverte sur un autre ordinateur, même si le fichier **.max** se trouve dans le même dossier que les textures. Le fait de rendre les chemins des textures relatifs dans 3D Studio Max résout ce problème de manière relativement simple.

* Dans la barre d’outils principale, accédez à **File** (Fichier) > **Reference** (Reférence) > **Asset Tracking Toggle** (Activer/désactiver le suivi des ressources). 

* Dans le navigateur de suivi des ressources qui s’ouvre, vous voyez la totalité ou la plupart des textures que vous avez appliquées à vos matériaux PBR listées sous la colonne **Maps/Shaders** (Cartes/nuanceurs).

* En regard de celles-ci, dans la colonne **Full Path** (Chemin complet), vous voyez le chemin d’accès à l’emplacement de vos textures, probablement à leur emplacement sur votre ordinateur local.

* Enfin, vous voyez une colonne appelée **Status** (État). Cette colonne indique si une texture donnée a été localisée et appliquée à votre scène ou non, et étiquette cette texture avec l’un des termes suivants **OK**, **Found** (Trouvée) ou **File Missing** (Fichier manquant). Les deux premières étiquettes indiquent que le fichier a été trouvé et chargé, tandis que la dernière signifie de toute évidence que le dispositif de suivi n’a pas réussi à localiser de fichier.
![texture-paths](media/3dsmax/texture-paths.jpg)

Vous remarquerez peut-être que les textures ne sont pas toutes listées dans le traqueur de ressources lorsque vous l’ouvrez pour la première fois. Vous n’avez pas de souci à vous faire, car une ou deux exécutions du processus de recherche de chemin suffisent généralement pour trouver toutes les textures de la scène. Le processus de recherche de chemin est le suivant : 

* Dans la fenêtre du traqueur de ressources, maintenez enfoncée la touche **Maj**+**cliquez** sur la première texture de la liste **Maps/Shaders** (Cartes/nuanceurs) et, tout en maintenant la touche Maj enfoncée, cliquez sur la dernière texture de la liste. Cette action permet de sélectionner toutes les textures de la liste. Les textures sélectionnées sont désormais surlignées en bleu (voir l’image ci-dessus).

* Cliquez avec le bouton droit sur la sélection, puis, dans le menu contextuel qui s’ouvre, sélectionnez **Set Path** (Définir le chemin).

* Dans la boîte de dialogue **Specify Assets Path** (Spécifier le chemin des ressources) qui s’affiche, sélectionnez le chemin local de vos textures affiché et remplacez-le par `.\`, puis cliquez sur **OK**. 

* Après un certain laps de temps (qui varie en fonction du nombre de textures présentes dans votre scène et de la taille de votre scène), le traqueur de ressources doit se résoudre comme suit (voir l’image).
![resolve-textures](media/3dsmax/resolve-textures.jpg)

Remarquez que la colonne **Full Path** (Chemin complet) est maintenant vide. Cela signifie que la scène ne recherche plus les textures appropriées dans un emplacement (absolu) spécifique, mais les trouvera toujours si le fichier .max ou le fichier FBX associé se trouve dans le même dossier que les textures. 

>[!NOTE]
>Il se peut parfois que vous deviez répéter ce processus plusieurs fois pour rechercher et résoudre toutes les textures et tous les chemins. Soyez sans crainte, répétez simplement la procédure jusqu’à ce que toutes les ressources pertinentes soient comptabilisées. Il est possible également que certains fichiers soient introuvables. Dans ce cas, sélectionnez simplement toutes les ressources de la liste et cliquez sur **Remove Missing Paths** (Supprimer les chemins manquants) – Voir l’image ci-dessus.

## <a name="fbx-export"></a>Exportation au format FBX

Une fois le suivi des ressources terminé, nous pouvons maintenant passer à l’exportation au format FBX. Là encore, le processus est simple et peut être effectué de plusieurs manières. 

>[!TIP]
>À moins que vous souhaitiez exporter l’intégralité de votre scène, une bonne pratique consiste à sélectionner uniquement les ressources que vous avez besoin d’exporter. Pour les scènes particulièrement riches en ressources, l’exportation peut prendre beaucoup de temps. Il est donc judicieux d’exporter uniquement ce dont vous avez besoin.
>
>Il est recommandé, si vous avez utilisé des modificateurs tels que **Turbosmooth** ou **Open SubDiv**, etc., de les réduire avant l’exportation, car ils peuvent provoquer des problèmes lors de l’exportation. Enregistrez toujours votre scène avant de continuer ! 

* Dans la scène, sélectionnez les ressources que vous souhaitez exporter et, dans la barre d’outils principale, accédez à **File** (Fichier) > **Export** (Exporter) > **Export Selected** (Exportation sélectionnée).

* Dans la boîte de dialogue **Select File to Export** (Sélectionner le fichier à exporter), tapez ou sélectionnez le nom du fichier de sortie et, dans les options **Save as Type** (Type de fichier), sélectionnez **Autodesk (*.fbx)** . Cette action ouvre le menu d’exportation FBX. 

* N’oubliez pas que si vous avez créé des instances dans votre scène, il est important d’activer **Preserve Instances** (Conserver les instances) dans les paramètres d’exportation FBX. 
![fbx-export](media/3dsmax/fbx-export.jpg)

Souvenez-vous que nous avons déjà mentionné qu’il existait plusieurs façons d’exporter le fichier. Si l’objectif de l’exportation consiste à partager le fichier FBX avec ses fichiers de textures dans un dossier/répertoire, les paramètres affichés dans l’image ci-dessous doivent s’appliquer et fonctionner correctement. Une fois que vous avez sélectionné vos paramètres, cliquez sur **OK**.
![fbx-settings](media/3dsmax/fbx-settings.jpg)

Toutefois, si vous préférez ne pas partager de grands dossiers/répertoires de textures avec le fichier FBX, vous pouvez choisir d’incorporer (**Embed**) les textures dans le fichier FBX. Cela signifie que l’ensemble de la ressource (textures incluses) est ajoutée à un fichier FBX unique. Sachez toutefois qu’en associant votre exportation en une seule ressource, le fichier FBX sera considérablement plus volumineux.

>[!IMPORTANT]
>Si votre fichier FBX résultant dépasse 2,4 Go, la version minimale des paramètres d’exportation FBX (voir ci-dessus) doit être 2016 ou ultérieure. En effet, les versions plus récentes disposent de la prise en charge 64 bits et prennent donc en charge des fichiers plus volumineux.

* Dans les paramètres d’exportation FBX, activez **Embed Media (Incorporer les médias), puis cliquez sur **OK** pour exporter en incluant les textures. 

Lors de l’exportation vers FBX, si vous utilisez le matériau physique, vous verrez probablement apparaître l’avertissement suivant après avoir cliqué sur « OK » dans la boîte de dialogue d’exportation : ![export-warnings](media/3dsmax/export-warnings.jpg)

Cet avertissement informe simplement l’utilisateur que les matériaux exportés peuvent ne pas être compatibles avec d’autres packages logiciels. Comme le matériau physique est compatible avec Azure Remote Rendering, vous n’avez pas à vous en soucier. Cliquez simplement sur **OK** pour terminer le processus et fermer la fenêtre.

## <a name="conclusion"></a>Conclusion

En général, ce type de matériau semble plus réaliste, car il est basé sur les propriétés physiques réelles de la lumière. Il ajoute un effet immersif qui donne un aspect réel à la scène.

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant configurer des matériaux avec un éclairage avancé pour les objets d’une scène. Vous savez également exporter les objets au format FBX pris en charge par Azure Remote Rendering. L’étape suivante consiste à convertir le fichier FBX et à le visualiser dans Azure Remote Rendering.

>[!div class="nextstepaction"]
>[Démarrage rapide : Convertir un modèle pour le rendu](../../quickstarts\convert-model.md)