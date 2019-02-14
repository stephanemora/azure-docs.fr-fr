---
title: Créer un baking acoustique avec Project Acoustics
titlesuffix: Azure Cognitive Services
description: Ce document décrit le processus de soumission d’un baking acoustique à l’aide de l’extension de l’éditeur Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: ce377234cceb22bc2c6979bedd92d800355e75da
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872082"
---
# <a name="bake-acoustics"></a>Créer un baking acoustique

Ce document décrit le processus de soumission d’un baking acoustique à l’aide de l’extension de l’éditeur Unity. Pour plus d’informations sur Acoustics, consultez [Présentation d’Acoustics](what-is-acoustics.md). Pour obtenir un guide de démarrage rapide, consultez [Bien démarrer](getting-started.md).

## <a name="import-the-plugin"></a>Importer le plug-in

Importez le package de plug-in Acoustics dans votre projet. Ensuite, ouvrez l’interface utilisateur d’Acoustics en choisissant **Window > Acoustics** dans le menu Unity :

![Fenêtre d’ouverture d’Acoustics](media/WindowAcoustics.png)

## <a name="principles"></a>Principes

La fenêtre d’outil Acoustics collecte les informations dont le moteur d’acoustique a besoin pour calculer l’acoustique pour votre scène. Un baking nécessite cinq étapes :

1. Créer ou marquer votre maillage de navigation de lecteur
2. Marquer la géométrie d’acoustique
3. Assigner des propriétés de matières acoustiques à la géométrie
4. Passer en revue le placement des sondes
5. Baking

Une fois le baking terminé, consultez [Vue d’ensemble du processus de conception pour Acoustics](design-process.md) afin de découvrir les étapes de conception facultatives post-baking.

## <a name="create-or-mark-a-navigation-mesh"></a>Créer ou marquer un maillage de navigation

Un maillage de navigation sert à placer des points de sonde pour la simulation. Vous pouvez utiliser le [flux de travail de maillage de navigation](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) de Unity ou spécifier votre propre maillage de navigation. Les maillages de navigation créés avec le flux de travail Unity seront détectés par le système acoustique. Pour utiliser vos propres maillages, marquez-les à partir de l’onglet **Objects** comme décrit à l’étape suivante.

## <a name="objects-tab"></a>Onglet Objects

Ouvrez l’onglet **Objects** de la fenêtre **Acoustics**. Utilisez cet onglet pour marquer des objets dans votre scène, ce qui ajoute simplement des composants **AcousticsGeometry** ou **AcousticsNavigation** à l’objet. Vous pouvez également utiliser le [flux de travail de composant Unity standard](https://docs.unity3d.com/Manual/UsingComponents.html) pour marquer ou annuler le marquage d’objets.

Sélectionnez un ou plusieurs objets dans la fenêtre de la scène ou la hiérarchie, et marquez-les ou annulez leur marquage pour **AcousticsGeometry** ou **AcousticsNavigation** comme décrit ci-dessous. Si rien n’est sélectionné, vous pouvez marquer ou annuler le marquage de tous les éléments de la scène en même temps.

Seuls les Mesh Renderers et Terrains peuvent être marqués. Tous les autres types d’objets seront ignorés. Les cases à cocher marquent ou annulent le marquage de tous les objets affectés.

Si vous n’avez rien sélectionné dans votre scène, vous aurez les paramètres suivants :

![Onglet Objects - aucune sélection](media/ObjectsTabNoSelectionDetail.png)

Si vous avez sélectionné quelque chose dans votre scène ou hiérarchie, vous aurez les paramètres suivants :

![Onglet Objects - aucune sélection](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Éléments de l’onglet Objects

Les éléments de l’onglet sont les suivants :

1. Les boutons de sélection d’onglet (onglet **Objects** sélectionné). Utilisez ces boutons pour parcourir les différentes étapes de baking acoustique, de gauche à droite.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Filtres disponibles pour la fenêtre de hiérarchie. Ils vous permettent de filtrer la fenêtre de hiérarchie pour n’afficher que les objets du type spécifié, ce qui facilite leur marquage. Si vous n’avez encore rien marqué pour l’acoustique, la sélection des deux dernières options ne vous montrera rien. Toutefois, elles peuvent être utiles pour rechercher des objets une fois que vous en aurez marqué.
4. Quand aucun objet n’est sélectionné, cette section affiche l’état de tous les objets dans la scène :
    * Total : nombre total d’objets actifs et non masqués dans la scène.
    * Ignored : nombre d’objets qui ne sont pas des Mesh Renderers ou des Terrains.
    * Mesh : nombre d’objets Mesh Renderer dans la scène.
    * Terrain : nombre d’objets Terrain dans la scène.
    * Geometry : nombre d’objets Mesh ou Terrain dans la scène marqués comme « Acoustics Geometry ».
    * Navigation : nombre d’objets Mesh ou Terrain dans la scène marqués comme « Acoustics Navigation ». Ce nombre n’inclut pas les NavMesh de Unity.
5. Indique le nombre total d’objets « marquables » dans la scène, à savoir uniquement les Mesh Renderers et Terrains. Affiche des cases à cocher qui vous permettent de marquer ces objets (c’est-à-dire y ajouter le composant approprié) en tant que géométrie ou navigation pour l’acoustique.
6. Quand rien n’est sélectionné, cette note vous rappelle que vous devez sélectionner des objets pour le marquage si nécessaire. Vous pouvez également cocher une ou deux cases pour marquer tous les objets dans la scène sans rien sélectionner.
7. Quand des objets sont sélectionnés, cette section affiche uniquement l’état des objets sélectionnés.
8. Indique le nombre total d’objets sélectionnés « marquables ». Cocher ou décocher les cases permet de marquer ou d’annuler le marquage des objets sélectionnés.

Si certains objets sont marqués et d’autres non, la case à cocher correspondante affiche une valeur « mixte » :

![Case à cocher à valeur mixte](media/MixedObjectSelectionDetail.png)

Un clic sur la case à cocher force le marquage de tous les objets ; un autre clic annule le marquage de tous les objets.

Les objets peuvent être marqués à la fois pour la géométrie et la navigation.

### <a name="guidelines-for-marking-objects"></a>Instructions relatives au marquage des objets

Veillez à marquer tous les objets comme **Acoustics Geometry** s’ils doivent occulter, renvoyer ou absorber le son. La géométrie d’acoustique peut comprendre des éléments tels que sol, murs, toits, fenêtres et vitres, tapis et meubles volumineux. Vous pouvez très bien inclure de plus petits objets tels que des lampes, des éléments décoratifs ou des appareils d’éclairage, car ils n’augmentent pas sensiblement le coût du baking. Il est important de ne manquer aucun élément majeur tel que le sol ou un plafond. En outre, vous ne devez pas inclure d’éléments qui ne doivent pas affecter l’acoustique, tels que les maillages de collision.

La transformation d’un objet au moment du calcul de sonde (par le biais de l’onglet **Probes**, ci-dessous) est fixe dans les résultats de baking. Le déplacement de tout objet marqué dans la scène exigera de réeffectuer le calcul de la sonde et le baking de la scène.

## <a name="materials-tab"></a>Onglet Materials

Une fois vos objets marqués, cliquez sur le bouton **Materials** pour accéder à l’onglet du même nom.

### <a name="parts-of-the-materials-tab"></a>Éléments de l’onglet Materials

![Détail de l’onglet Materials](media/MaterialsTabDetail.png)

1. Le bouton d’onglet **Materials**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Quand cette case est cochée, seuls les matières utilisées par les objets marqués comme **Acoustics Geometry** sont répertoriées. Sinon, toutes les matières utilisées dans la scène sont répertoriées.
4. Utilisez ces options pour changer l’ordre du menu déroulant qui s’affiche quand vous cliquez sur une liste déroulante dans la colonne Acoustics ci-dessous (6). **Name** trie les matières acoustiques par nom. « Absorptivity » les trie par ordre d’absorption de faible à élevée.
5. La liste des matières utilisées dans la scène, triées par ordre alphabétique. Si la case **Show Marked Only** est cochée (3), seules les matières utilisées par les objets marqués comme **Acoustics Geometry** sont affichées. Si vous cliquez sur une matière ici, tous les objets dans la scène qui utilisent cette matière sont sélectionnés.
6. Montre la matière acoustique à laquelle la matière de la scène a été affectée. Cliquez sur une liste déroulante pour réaffecter une matière de la scène à une autre matière acoustique. Vous pouvez changer l’ordre de tri du menu affiché quand vous cliquez sur un élément ici à l’aide des options **Sort Acoustics By:** ci-dessus (4).
7. Montre le coefficient d’absorption acoustique de la matière sélectionnée dans la colonne précédente. Une valeur nulle signifie que la matière est parfaitement réfléchissante (aucune absorption), alors que la valeur 1 signifie qu’elle est parfaitement absorbante (aucune réflexion). Le coefficient d’absorption ne peut pas être changé, sauf si la  matière sélectionnée est « Custom ».
8. Pour une matière affectée à « Custom », le curseur n’est plus désactivé et vous pouvez choisir le coefficient d’absorption à l’aide du curseur ou en tapant une valeur. Pour plus d’informations sur les propriétés des coefficient matières, consultez [Vue d’ensemble du processus de conception pour Acoustics](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Instructions relatives à l’affectation des matières (ou valeurs d’absorption)

Cet onglet tente de deviner le coefficient d’absorption de vos matières en fonction du nom. Par exemple, si le nom de votre matière de scène est LivingRoom_WoodTable, la matière acoustique initiale qui lui est affectée est « wood ». Les matières pour lesquelles il est impossible de déterminer une matière connue sont affectées comme « Default », dont l’absorption est similaire à celle du béton.

Vous pouvez réaffecter des matières acoustiques à chaque matière de scène. Par exemple, si le son d’une pièce a trop de réverbération, changez la matière acoustique des murs, du sol ou du plafond en leur affectant une matière à l’absorption plus élevée. L’affectation de matière acoustique s’applique à tous les objets qui utilisent cette matière de scène.

## <a name="probes-tab"></a>Onglet Probes

Après avoir affecté les matières, basculez vers l’onglet **Probes**.

### <a name="parts-of-the-probes-tab"></a>Éléments de l’onglet Probes

![Détail de l’onglet Probes](media/ProbesTabDetail.png)

1. Le bouton d’onglet **Probes**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Utilisez ces cases à cocher pour choisir une résolution de simulation grossière ou fine. La simulation grossière est plus rapide, mais a certains inconvénients. Pour plus d’informations, consultez [« Résolution grossière ou fine ? »](#Coarse-vs-Fine-Resolution) ci-dessous.
4. Choisissez l’emplacement où les fichiers de données acoustiques doivent être placés à l’aide de ce champ. Cliquez sur le bouton « ... » pour utiliser un sélecteur de dossier. La valeur par défaut est **Assets/AcousticsData**. Un sous-dossier **Editor** sera également créé sous cet emplacement. Pour plus d’informations sur les fichiers de données, consultez [« Fichiers de données »](#Data-Files) ci-dessous.
5. Les fichiers de données pour cette scène seront nommés en utilisant le préfixe fourni ici. La valeur par défaut est « Acoustics_[nom_scène] ».
6. Une fois que les sondes ont été calculées, les contrôles ci-dessus sont désactivés. Cliquez sur le bouton **Clear** pour effacer les calculs et activer les contrôles afin que vous puissiez recalculer à l’aide de nouveaux paramètres.
7. Cliquez sur le bouton **Calculate...**  pour voxeliser la scène et calculer les emplacements des points de sonde. Cette opération est effectuée localement sur votre ordinateur, et doit être effectuée avant de lancer un baking.

Dans cette version de Project Acoustics, les sondes ne peuvent pas être placées manuellement. Elles doivent être placées à l’aide du processus automatisé fourni sous l’onglet **Probes**.

### <a name="what-the-calculate-button-calculates"></a>Ce que calcule le bouton « Calculate... »

Le bouton **Calculate...** prend toutes les données que vous avez fournies jusqu’à présent (géométrie, navigation, matières et le paramètre Coarse/Fine) et passe par plusieurs étapes :

1. Il prend la géométrie des maillages de scène et calcule un volume de voxel. Le volume de voxel est un volume en trois dimensions qui englobe votre scène entière et est composé de petits « voxels » cubiques. La taille des voxels est déterminée par la fréquence de simulation, qui est définie par le paramètre **Simulation Resolution**. Chaque voxel est marqué comme étant « à l’air libre » ou contenant la géométrie de la scène. Si un voxel contient une géométrie, il est marqué avec le coefficient d’absorption de la matière affectée à cette géométrie.
2. Il utilise ensuite les données de navigation pour calculer des emplacements acoustiquement intéressants où le joueur peut aller. Il tente de trouver un ensemble relativement petit de ces emplacements qui comprend des zones plus petites telles que des portes et des couloirs, puis des pièces, puis des espaces ouverts. Pour les petites scènes il y a généralement moins de 100 emplacements, tandis que les scènes volumineuses peuvent en avoir jusqu’à un millier.
3. Pour chacun des emplacements finaux de l’auditeur qu’il calcule, il détermine différents paramètres tels que le degré d’« ouverture » de l’espace, la taille de la pièce dans laquelle il se trouve, et ainsi de suite.
4. Les résultats de ces calculs sont stockés dans des fichiers à l’emplacement que vous spécifiez (voir [« Fichiers de données »](#Data-Files) ci-dessous).

En fonction de la taille de votre scène et de la rapidité de votre ordinateur, ces calculs peuvent prendre plusieurs minutes.

Une fois ces calculs terminées, vous pouvez prévisualiser les données de voxels et les emplacements des points de sonde pour vérifier que le baking génèrera de bons résultats. Des problèmes comme un maillage de navigation incorrect ou une géométrie manquante/en trop seront en général rapidement visibles dans l’aperçu, ce qui vous permettra de les corriger.

### <a name="scene-rename"></a>Changement de nom de scène

Le nom de la scène sert à connecter la scène aux fichiers stockant les positions des points de sonde et la voxelisation. Si vous renommez la scène après le calcul des points de sonde, les données de position et d’affectation des matières sont perdues et doivent être recalculées.

## <a name="debug-display-through-gizmos"></a>Affichage de débogage à l’aide de Gizmos

Par défaut, les gizmos **Probes** et **Voxels** sont tous deux activés. Ils vous indiqueront les voxels et les emplacements des points de sonde qui ont été calculés. Ils peuvent être activés ou désactivés à l’aide du menu Gizmos :

![Menu Gizmos](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Les voxels sont affichés dans la fenêtre de la scène sous forme de cubes verts autour de la géométrie participante. Les voxels qui contiennent uniquement de l’air ne sont pas affichés. Il existe une grande zone verte autour de votre scène qui indique le volume de voxels complet qui sera utilisé dans la simulation.
Déplacez-vous dans votre scène et vérifiez que tous les éléments de géométrie ont des voxels. La caméra de la scène doit se trouver à moins de cinq mètres de l’objet pour que les voxels soient affichés.

Si vous comparez les voxels créés avec une résolution fine ou grossière, vous constaterez que les voxels grossiers sont deux fois plus volumineux.

![Aperçu des voxels](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Points de sonde

Les points de sonde sont synonymes d’emplacements possibles du joueur (auditeur). Quand vous lancez un baking, l’acoustique d’une source sonore n’importe où dans la scène est calculée pour chacun de ces points de sonde. Si le joueur ne se trouve pas exactement à un emplacement de point de sonde, les données des points de sonde les plus proches de lui sont utilisés pour interpoler les paramètres à cet emplacement.

Par conséquent, il est important de s’assurer qu’il existe des points de sonde partout où le joueur est susceptible de se déplacer dans la scène, et que les petites zones et les embrasures de porte sont correctement représentées. Les points de sonde sont placés par le moteur Project Acoustics en fonction de l’interprétation de la géométrie de votre scène, et ils ne peuvent pas être déplacés ou changés dans cette préversion pour concepteur. Utilisez-les plutôt afin de vérifier l’exactitude de votre marquage de géométrie et des données d’indicateurs de navigation.

![Aperçu des sondes](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Résolution grossière ou fine ?

La seule différence entre les paramètres de résolution Coarse et Fine est la fréquence à laquelle la simulation est exécutée. Le paramètre Fine utilise une fréquence deux fois plus élevée que Coarse.
Bien que cela puisse sembler simple, cela a plusieurs implications sur la simulation acoustique :

* La longueur d’onde pour Coarse est deux fois plus longue que pour Fine. Par conséquent, les voxels sont deux fois plus volumineux.
* La durée nécessaire à la simulation est directement liée à la taille des voxels. Ainsi, un baking avec le paramètre Coarse est environ 16 fois plus rapide qu’avec le paramètre Fine.
* Les ouvertures (par exemple les portes ou les fenêtres) plus petites que la taille de voxel ne peuvent pas être simulées. Le paramètre Coarse peut entraîner la non-simulation de certaines de ces petites ouvertures. Par conséquent, le son ne les traversera pas au moment de l’exécution. Vous pouvez vérifier si cela se produit en affichant les voxels.
* La fréquence de simulation inférieure provoque moins de diffraction autour des angles et des bords.
* Les sources sonores ne peuvent pas se trouver à l’intérieur de voxels « remplis », à savoir des voxels qui contiennent de la géométrie. Dans ce cas de figure, aucun son n’est généré. Il est plus difficile de placer les sources sonores afin qu’elles ne soient pas à l’intérieur des plus grands voxels de Coarse que d’utiliser le paramètre Fine.
* Les plus grands voxels empiéteront plus sur les ouvertures, comme indiqué ci-dessous. La première image a été créée avec le paramètre Coarse, tandis que la deuxième est la même ouverture avec la résolution Fine. Comme indiqué par les marquages rouges, il y a beaucoup moins d’empiètement dans l’ouverture avec le paramètre Fine. La ligne bleue est la porte telle que définie par la géométrie, tandis que la ligne rouge est l’ouverture acoustique effective définie par la taille de voxel. L’impact de cet empiètement dans une situation donnée dépend entièrement de l’alignement des voxels avec la géométrie de l’ouverture, qui est déterminé par la taille et les emplacements de vos objets dans la scène.

![Porte avec paramètre Coarse](media/CoarseVoxelDoorway.png)

![Porte avec paramètre Fine](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Onglet Bake

Une fois que vous êtes satisfait de l’aperçu des données, utilisez l’onglet **Bake** pour envoyer votre baking vers le cloud.

### <a name="parts-of-the-bake-tab"></a>Éléments de l’onglet Bake

![Détail de l’onglet Bake](media/BakeTabDetails.png)

1. Le bouton d’onglet Bake, qui sert à afficher cette page.
2. Une brève description des actions à effectuer dans cette page.
3. Champs où entrer vos informations d’identification Azure une fois que votre compte Azure a été créé. Pour plus d’informations, consultez [Créer un compte Azure Batch](create-azure-account.md).
4. Balise d’image Docker pour l’ensemble d’outils acoustiques.
5. Lancez le portail Azure pour gérer vos abonnements, surveiller l’utilisation, afficher les informations de facturation, etc. 
6. Type de nœud de calcul Azure Batch à utiliser pour le calcul. Le type de nœud doit être pris en charge par votre emplacement de centre de données Azure. En cas de doute, conservez **Standard_F8s_v2**.
7. Nombre de nœuds à utiliser pour ce calcul. Le nombre que vous entrez ici affecte la durée nécessaire pour effectuer le baking. Il est limité par votre allocation de noyaux Azure Batch. L’allocation par défaut autorise uniquement deux nœuds de huit noyaux ou un nœud de 16 noyaux, mais elle peut être étendue. Pour plus d’informations sur les contraintes d’allocation de noyaux, consultez [Créer un compte Azure Batch](create-azure-account.md).
8. Cochez cette case de manière à configurer votre pool de calcul pour qu'il utilise des [nœuds basse priorité](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Le coût des nœuds de calcul basse priorité est nettement moindre, mais ils peuvent parfois être inaccessibles ou reportés.
9. Le nombre de sondes pour votre scène, tel que calculé sous l’onglet **Probes**. Le nombre de sondes détermine le nombre de simulations qui doivent être exécutées dans le cloud. Vous ne pouvez pas spécifier plus de nœuds qu’il n’y a de sondes.
10. Durée attendue nécessaire pour exécuter votre travail dans le cloud. Cela n’inclut pas le temps de démarrage des nœuds. Une fois le travail commencé, cela correspond environ à la durée après laquelle vous obtiendrez les résultats. Notez qu’il s’agit uniquement d’une estimation.
11. La durée totale de temps de traitement nécessaire pour exécuter les simulations. Il s’agit de la quantité totale de temps de calcul de nœud qui sera utilisée dans Azure. Pour plus d’informations sur l’utilisation de cette valeur, consultez [Estimation du coût de baking](#Estimating-bake-cost) ci-dessous.
12. Ce message indique où les résultats du baking seront enregistrés une fois le travail terminé.
13. (Utilisation avancée uniquement) Si pour une raison quelconque vous devez forcer Unity à oublier un baking que vous avez envoyé (par exemple si vous avez téléchargé les résultats à l’aide d’un autre ordinateur), cliquez sur le bouton **Clear State** afin d’oublier de travail qui a été soumis. Notez que cela signifie que le fichier de résultats, une fois qu’il sera prêt, ne sera **pas** téléchargé, et que **cela n’est pas la même chose qu’annuler le travail**. Le travail, s’il est en cours d’exécution, continuera à s’exécuter dans le cloud.
14. Cliquez sur le bouton Bake pour envoyer le baking vers le cloud. Pendant l’exécution d’un travail, ce bouton indique **Cancel Job**.
15. Il prépare le traitement de la simulation acoustique sur un ordinateur local. Pour plus d'informations, voir [Baking local](#Local-bake).  
16. Cette zone indique l’état du baking. Une fois terminé, elle doit indiquer **Downloaded**.

Vous pouvez toujours obtenir des informations complètes sur les travaux actifs, les pools de calcul et de stockage dans le [portail Azure](https://portal.azure.com).

Pendant l’exécution d’un travail, le bouton **Bake** indique **Cancel Job**. Utilisez ce bouton pour annuler le travail en cours. Vous devrez confirmer l’annulation. L’annulation d’un travail ne peut pas être annulée. Aucun résultat ne sera disponible et vous serez quand même facturé pour les temps de calcul Azure utilisés.

Une fois que vous avez lancé un baking, vous pouvez fermer Unity. En fonction du projet, du type de nœud et du nombre de nœuds, un baking dans le cloud peut prendre plusieurs heures. L’état du travail de baking est mis à jour quand vous rechargez le projet et ouvrez la fenêtre Acoustics. Si le travail est terminé, le fichier de sortie est téléchargé.

Les informations d’identification Azure sont stockées de manière sécurisée sur votre ordinateur local et associées à votre éditeur Unity. Elles sont utilisées exclusivement pour établir une connexion sécurisée vers Azure.

### <a name="Estimating-bake-cost"></a> Estimation du coût d'un baking Azure

Pour estimer le coût d’un baking donné, prenez la valeur affichée dans **Estimated Compute Cost**, qui est une durée, et multipliez-la par le coût horaire (dans votre devise locale) du **Type de nœud de machine virtuelle** que vous avez sélectionné. Le résultat n’inclut pas la durée nécessaire pour préparer les nœuds à l’exécution. Par exemple, si vous sélectionnez **Standard_F8s_v2** comme type de nœud, qui a un coût de 0,40 $/heure, et que l’estimation du coût de calcul est de 3 heures et 57 minutes, le coût estimé pour exécuter le travail sera de 0,40 $ * ~4 heures = ~1,60 $. Le coût réel sera probablement un peu plus élevé à cause de la durée supplémentaire nécessaire au démarrage des nœuds. Vous trouverez le coût horaire des nœuds dans la page [Tarification d’Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (sélectionnez « Optimisée pour le calcul » ou « Calcul hautes performances » comme catégorie).

### <a name="reviewing-the-bake-results"></a>Examen des résultats de baking

Une fois le baking terminé, vérifiez que les points de sonde et les voxels sont à leurs emplacements attendus en exécutant le plug-in de runtime. Pour plus d’informations, consultez [Vue d’ensemble du processus de conception pour Acoustics](design-process.md).

## <a name="Local-bake"></a>Baking local
Le baking local exécute la simulation acoustique sur votre PC au lieu de la décharger dans le cluster de calcul Azure Batch. Il peut s'agir d'une option intéressante pour expérimenter l'acoustique sans avoir recours à un abonnement Azure. Notez que la simulation acoustique est exigeante en termes de calcul et qu'elle peut prendre un certain temps en fonction de la taille de la scène, de la configuration de la simulation et de la puissance de calcul brute de l’ordinateur de traitement.

### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises
Processeur Intel 64 bits avec au moins 8 cœurs et 32 Go de RAM ou plus.

Par exemple, sur une machine de 8 cœurs avec Intel Xeon E5-1660 @ 3 GHz et 32 Go de RAM -
* Petite scène dotée de 100 sondes, environ 2 heures sont nécessaires pour un baking grossier et ~ 32 heures pour un baking à résolution fine.
* Grande scène avec 1 000 sondes, environ 20 heures peuvent être nécessaires pour une résolution grossière et ~ 21 jours pour un baking à résolution fine.

### <a name="setup-docker"></a>Configuration de Docker
Installez et configurez Docker sur le PC qui traitera la simulation-
1. Installez l'[ensemble d’outils Docker](https://www.docker.com/products/docker-desktop).
2. Lancez les paramètres Docker, accédez aux options avancées et configurez les ressources pour disposer d'au moins 8 Go de RAM. Plus vous allouerez d'UC à Docker, plus le baking sera rapide. ![Exemples de paramètres Docker](media/DockerSettings.png)
3. Accédez à « Lecteurs partagés » et activez le partage sur le lecteur utilisé pour le traitement.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Exécuter un baking local
1. Cliquez sur le bouton « Préparer un baking local » de l’onglet Baking et sélectionnez le dossier d'enregistrement des fichiers d’entrée et des scripts d’exécution. Vous pouvez ensuite exécuter le baking sur n’importe quel ordinateur, à condition qu'il respecte la configuration matérielle minimale requise et que Docker y soit installé en y copiant le dossier.
2. Lancez la simulation à l'aide du script « runlocalbake.bat ». Ce script extraira l’image Project Acoustics Docker avec l'ensemble d'outils nécessaires au traitement de la simulation et démarrera la simulation. 
3. Au terme de la simulation, copiez le fichier .ace qui en résulte dans votre projet Unity. Pour veiller à ce qu'Unity l'identifie comme un fichier binaire, ajoutez « .bytes » à l’extension de fichier (par exemple, « Scene1.ace.bytes »). Les journaux détaillés de la simulation sont stockés dans le fichier « AcousticsLog.txt ». En cas de problème, partagez ce fichier afin de faciliter le diagnostic.

## <a name="Data-Files"></a>Fichiers de données

Quatre fichiers de données sont créés par ce plug-in à différents stades. Un seul d’entre eux est nécessaire au moment de l’exécution. Les trois autres sont à l’intérieur de dossiers nommés « Editor » et ne seront pas compilés dans votre projet.

* **Assets/Editor/[SceneName]\_AcousticsParameters.asset** : Ce fichier stocke les données que vous entrez dans les champs dans l’interface utilisateur Acoustics. Vous ne pouvez pas changer l’emplacement et le nom de ce fichier. D’autres valeurs stockées dans ce fichier affectent le baking, mais elles sont destinées aux utilisateurs avancés et ne doivent pas être modifiées.
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes** : Ce fichier est créé au cours de la simulation du bake. Il contient les données de recherche utilisées par le runtime pour restituer l’acoustique de votre scène. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox** : Ce fichier stocke la géométrie d’acoustique voxélisée et les propriétés du matériel. Il est calculé à l’aide du bouton **Calculate...** sous l’onglet Probes. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml** : Ce fichier stocke les paramètres calculés à l’aide du bouton **Calculate...** de l’onglet **Probes**. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.

Veillez à ne pas supprimer le fichier *.ace.bytes téléchargé suite au baking. Ce fichier n’est pas récupérable, sauf en réeffectuant un baking de la scène.

## <a name="next-steps"></a>Étapes suivantes
* Appliquez les résultats de baking à des sources sonores dans [Vue d’ensemble du processus de conception pour Acoustics](design-process.md).

