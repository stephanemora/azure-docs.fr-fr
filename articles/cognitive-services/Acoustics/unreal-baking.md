---
title: Tutoriel de baking Project Acoustics Unreal
titlesuffix: Azure Cognitive Services
description: Ce document décrit le processus de soumission d’un baking acoustique à l’aide de l’extension de l’éditeur Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854904"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Tutoriel de baking Project Acoustics Unreal
Ce document décrit le processus de soumission d’un baking acoustique à l’aide de l’extension de l’éditeur Unreal.

Un baking nécessite cinq étapes :

1. Créer ou étiqueter le maillage de navigation de votre joueur
2. Étiqueter la géométrie de l’acoustique
3. Assigner des propriétés de matières acoustiques à la géométrie
4. Passer en revue le placement des sondes
5. Baking

## <a name="open-the-project-acoustics-editor-mode"></a>Ouvrez le mode Éditeur de Project Acoustics

Importez le package de plug-in Project Acoustics dans votre projet. Pour plus d’informations sur cette opération, consultez la rubrique [Intégration d’Unreal](unreal-integration.md). Une fois que le plug-in est intégré, ouvrez l’interface utilisateur Acoustics en cliquant sur la nouvelle icône Mode acoustique.

![Capture d’écran de l’option Mode Unreal Editor Acoustics](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Étiqueter des acteurs pour l’acoustique

L’onglet Objects est le premier onglet qui s’affiche quand vous ouvrez le mode Acoustics. Utilisez cet onglet pour étiqueter des acteurs dans votre niveau, ce qui ajoute les étiquettes **AcousticsGeometry** ou **AcousticsNavigation** aux acteurs.

Sélectionnez un ou plusieurs objets dans le World Outliner, ou utilisez la section **Bulk Selection** pour sélectionner tous les objets d’une catégorie spécifique. Une fois que les objets sont sélectionnés, utilisez la section **Tagging** pour appliquer l’étiquette de votre choix aux objets sélectionnés.

Si un objet n’a pas l’étiquette **AcousticsGeometry** ou **AcousticsNavigation**, il est ignoré dans la simulation. Seuls les maillages statiques, les maillages de navigation et les paysages sont pris en charge. Si vous étiquetez autre chose, il est ignoré.

### <a name="for-reference-the-objects-tab-parts"></a>Pour référence : Les composants de l’onglet Objects

![Capture d’écran de l’onglet Acoustics Objects dans Unreal](media/unreal-objects-tab-details.png)

1. Les boutons de sélection d’onglet (onglet **Objects** sélectionné). Utilisez ces boutons pour parcourir les différentes étapes du baking acoustique, de haut en bas.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Sélecteurs disponibles pour les acteurs dans le niveau.
4. Le fait de cliquer sur **Select** sélectionne tous les objets dans le niveau qui correspond à au moins un des types d’acteurs activés.
5. Le fait de cliquer sur **Deselect all** efface la sélection actuelle. Cela équivaut à appuyer sur la touche Échap.
6. Utilisez ces cases d’option pour choisir s’il faut appliquer l’étiquette Geometry ou Navigation aux acteurs sélectionnés.
7. Le fait de cliquer sur **Tag** ajoute l’étiquette sélectionnée à tous les acteurs actuellement sélectionnés.
8. Le fait de cliquer sur **Untag** supprime l’étiquette sélectionnée de tous les acteurs actuellement sélectionnés.
9. Le fait de cliquer sur **Select Tagged** efface la sélection actuelle et sélectionne tous les acteurs avec l’étiquette actuellement sélectionnée.
10. Ces statistiques indiquent le nombre d’acteurs étiquetés avec chaque type d’étiquette.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Étiqueter la géométrie d’occlusion et de réflexion acoustique

Ouvrez l’onglet Objects de la fenêtre Acoustics. Marquez tous les objets comme Acoustics Geometry s’ils doivent bloquer, réfléchir ou absorber le son. La géométrie acoustique peut comprendre des éléments comme le sol, des murs, des toits, des fenêtres et des vitres, des tapis et des meubles volumineux. Vous pouvez utiliser n’importe quel niveau arbitraire de complexité pour ces objets. La scène étant voxelisée avant la simulation, des maillages très détaillés, comme des arborescences avec un grand nombre de petites feuilles, ne demandent pas un travail de baking plus important que des objets simplifiés.

N’incluez pas d’éléments qui ne doivent pas affecter l’acoustique, comme des maillages de collision invisibles.

La transformation d’un objet au moment du calcul de sonde (via l’onglet Probes, ci-dessous) est fixe dans les résultats du baking. Le déplacement de tout objet marqué dans la scène exigera de réeffectuer le calcul de la sonde et le baking de la scène.

### <a name="create-or-tag-a-navigation-mesh"></a>Créer ou étiqueter un maillage de navigation

Un maillage de navigation sert à placer des points de sonde pour la simulation. Vous pouvez utiliser l’élément [Nav Mesh Bounds Volume](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html) de Unreal ou spécifier votre propre maillage de navigation. Vous devez étiqueter au moins un objet avec **Acoustics Navigation**. Si vous utilisez le maillage de navigation d’Unreal, vérifiez que vous l’avez généré au préalable.

### <a name="acoustics-volumes"></a>Volumes Acoustics ###

Vous pouvez personnaliser vos zones de navigation de manière plus avancée avec les **volumes Acoustics**. Les **volumes Acoustics** sont des acteurs que vous pouvez ajouter à votre scène. Ils vous permettent de sélectionner des zones à inclure et à ignorer dans le maillage de navigation. L’acteur expose une propriété qui peut avoir les valeurs "Include" et "Exclude". Les volumes « Include » permettent de garantir que seules les zones du maillage de navigation qu’ils contiennent sont prises en compte. Les volumes « Exclude » marquent ces zones comme étant à ignorer. Les volumes « Exclude » sont toujours appliqués après les volumes « Include ». Veillez à étiqueter les **volumes Acoustics** en tant que **navigation Acoustics** selon le processus habituel sous l’onglet Objects. Ces acteurs ne sont ***pas*** automatiquement étiquetés.

![Capture d’écran des propriétés de volume Acoustics dans Unreal](media/unreal-acoustics-volume-properties.png)

Les volumes "Exclude" sont principalement destinés à fournir un contrôle précis des emplacement où il est préférable d’éviter de placer des sondes pour limiter l’utilisation des ressources.

![Capture d’écran de volume Acoustics Exclude dans Unreal](media/unreal-acoustics-volume-exclude.png)

Les volumes « Include » permettent de créer des sections manuelles d’une scène, par exemple si vous souhaitez diviser votre scène en plusieurs zones acoustiques. Par exemple, si vous avez une grande scène, plusieurs kilomètres carrés et deux zones d’intérêt pour lesquelles vous souhaitez créer un bake acoustique. Vous pouvez dessiner deux gros volumes « Include » dans la scène et générer des fichiers ACE pour chacun d’eux, l’un après l’autre. Ensuite, dans le jeu, vous pouvez utiliser des volumes de déclenchement combinés à des appels de blueprint pour charger le fichier ACE approprié au moment où le joueur approche de chaque vignette.

Les **volumes Acoustics** limitent uniquement la navigation, ***pas*** la géométrie. Chaque sonde d’un **volume Acoustics** "Include" extrait toujours l’ensemble de la géométrie nécessaire hors du volume au moment d’effectuer les simulations d’ondes. Ainsi, il ne doit y avoir aucune discontinuité dans l’occlusion ou tout autre signal acoustique provenant du fait que le joueur passe d’une section à une autre.

## <a name="select-acoustic-materials"></a>Sélectionner des matériaux acoustiques

Une fois vos objets étiquetés, cliquez sur le bouton **Materials** pour accéder à l’onglet du même nom. Cet onglet est utilisé pour spécifier les propriétés de chaque matériau dans le niveau. Avant qu’un acteur soit étiqueté, il est vide.

Les matériaux acoustiques contrôlent la quantité d’énergie sonore reflétée par chaque surface. Le matériau acoustique par défaut a une absorption similaire à celle du béton. Project Acoustics suggère des matériaux en fonction du nom du matériau de la scène.

La durée de réverbération d’une matière donnée dans une pièce est inversement proportionnelle à son coefficient d’absorption, la plupart des matières ayant des valeurs d’absorption comprises entre 0,01 et 0,20. Les matériaux avec des coefficients d’absorption au-delà de cette plage sont très absorbants. Par exemple, si le son d’une pièce a trop de réverbération, changez la matière acoustique des murs, du sol ou du plafond en leur affectant une matière à l’absorption plus élevée. L’affectation d’un matériau acoustique s’applique à tous les acteurs qui utilisent ce matériau de la scène.

![Graphe montrant une corrélation négative de temps de réverbération avec un coefficient d’absorption](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pour référence : Éléments de l’onglet Materials

![Capture d’écran de l’onglet Acoustics Objects dans Unreal](media/unreal-materials-tab-details.png)

1. Le bouton d’onglet **Materials**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Liste des matériaux utilisés dans le niveau, provenant des acteurs étiquetés avec **AcousticsGeometry**. Si vous cliquez sur une matière ici, tous les objets dans la scène qui utilisent cette matière sont sélectionnés.
4. Montre la matière acoustique à laquelle la matière de la scène a été affectée. Cliquez sur une liste déroulante pour réaffecter une matière de la scène à une autre matière acoustique.
5. Montre le coefficient d’absorption acoustique de la matière sélectionnée dans la colonne précédente. Une valeur nulle signifie que la matière est parfaitement réfléchissante (aucune absorption), alors que la valeur 1 signifie qu’elle est parfaitement absorbante (aucune réflexion). La modification de cette valeur met à jour le matériel acoustique (étape 4) en **Custom**.

Si vous apportez des changements aux matériaux de votre scène, vous devez changer d’onglet dans le plug-in Project Acoustics pour que ces changements soient répercutés sous l’onglet **Materials**.

## <a name="calculate-and-review-listener-probe-locations"></a>Calculer et passer en revue les emplacements des sondes d’auditeur

Après avoir affecté les matières, basculez vers l’onglet **Probes**.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pour référence : Éléments de l’onglet Probes

![Capture d’écran de l’onglet Acoustics Probes dans Unreal](media/unreal-probes-tab-details.png)

1. Le bouton d’onglet **Probes**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Utilisez ceci pour choisir une résolution de simulation grossière ou fine. La simulation grossière est plus rapide, mais a certains inconvénients. Pour plus d’informations, consultez [Résolution de bake](bake-resolution.md) ci-dessous.
4. Choisissez l’emplacement où les fichiers de données acoustiques doivent être placés à l’aide de ce champ. Cliquez sur le bouton « ... » pour utiliser un sélecteur de dossier. Pour plus d’informations sur les fichiers de données, consultez [Fichiers de données](#Data-Files) ci-dessous.
5. Les fichiers de données pour cette scène seront nommés en utilisant le préfixe fourni ici. La valeur par défaut est « [nom_niveau]_AcousticsData ».
6. Cliquez sur le bouton **Calculate** pour voxeliser la scène et calculer les emplacements des points de sonde. Cette opération est effectuée localement sur votre ordinateur, et doit être effectuée avant de lancer un baking. Une fois que les sondes ont été calculées, les contrôles ci-dessus sont désactivés, et ce bouton a alors le libellé **Clear**. Cliquez sur le bouton **Clear** pour effacer les calculs et activer les contrôles afin que vous puissiez recalculer à l’aide de nouveaux paramètres.

Les sondes doivent être placées via le processus automatisé fourni dans l’onglet **Probes**.


### <a name="what-the-calculate-button-calculates"></a>Ce que calcule le bouton « Calculate »

Le bouton **Calculate** prend toutes les données que vous avez fournies jusqu’à présent (géométrie, navigation, matériaux et le paramètre de résolution grossière/fine) et passe par plusieurs étapes :

1. Il prend la géométrie des maillages de scène et calcule un volume de voxel. Le volume de voxel est un volume en trois dimensions qui englobe votre scène entière et est composé de petits « voxels » cubiques. La taille des voxels est déterminée par la fréquence de simulation, qui est définie par le paramètre **Simulation Resolution**. Chaque voxel est marqué comme étant « à l’air libre » ou contenant la géométrie de la scène. Si un voxel contient une géométrie, il est marqué avec le coefficient d’absorption de la matière affectée à cette géométrie.
2. Il utilise ensuite les données de navigation pour calculer des emplacements acoustiquement intéressants où le joueur peut aller. Il tente de trouver un ensemble relativement petit de ces emplacements qui comprend des zones plus petites telles que des portes et des couloirs, puis des pièces, puis des espaces ouverts. Pour les petites scènes, il y a généralement moins de 100 emplacements, tandis que les grandes scènes peuvent en avoir jusqu’à 1 000.
3. Pour chacun des emplacements finaux de l’auditeur qu’il calcule, il détermine différents paramètres tels que le degré d’« ouverture » de l’espace, la taille de la pièce dans laquelle il se trouve, et ainsi de suite.
4. Les résultats de ces calculs sont stockés dans des fichiers à l’emplacement que vous spécifiez (consultez [Fichiers de données](#Data-Files) ci-dessous).

En fonction de la taille de votre scène et de la rapidité de votre ordinateur, ces calculs peuvent prendre plusieurs minutes.

Une fois ces calculs terminées, vous pouvez prévisualiser les données de voxels et les emplacements des points de sonde pour vérifier que le baking génèrera de bons résultats. Des problèmes comme un maillage de navigation incorrect ou une géométrie manquante/en trop seront en général rapidement visibles dans l’aperçu, ce qui vous permettra de les corriger.


## <a name="debug-display"></a>Affichage du débogage

Une fois le calcul des sondes terminé, un nouvel acteur apparaît dans World Outliner, appelé **AcousticsDebugRenderer**. Le fait de cocher les cases **Render Probes** et **Render Voxels** active l’affichage du débogage à l’intérieur de la fenêtre d’affichage de l’éditeur.

![Capture d’écran montrant l’acteur Acoustics Debug Renderer dans l’éditeur Unreal](media/acoustics-debug-renderer.png)

Si vous ne voyez pas de voxels ni de sondes superposés sur votre niveau, vérifiez que le rendu en temps réel est activé dans la fenêtre d’affichage.

![Capture d’écran de l’option de rendu en temps réel dans Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Les voxels sont affichés dans la fenêtre de la scène sous forme de cubes verts autour de la géométrie participante. Les voxels qui contiennent uniquement de l’air ne sont pas affichés. Il existe une grande zone verte autour de votre scène qui indique le volume de voxels complet qui sera utilisé dans la simulation.
Déplacez-vous dans votre scène et vérifiez que la géométrie d’occlusion acoustique a des voxels. Vérifiez également que les objets non acoustiques, comme les maillages de collision, n’ont pas été voxelisés. La caméra de la scène doit se trouver à moins de cinq mètres de l’objet pour que les voxels soient affichés.

Si vous comparez les voxels créés avec une résolution fine et grossière, vous constaterez que les voxels grossiers sont deux fois plus volumineux.

![Capture d’écran de l’aperçu des voxels Acoustics dans l’éditeur Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Points de sonde

Les points de sonde sont synonymes d’emplacements possibles du joueur (auditeur). Lors du baking, la simulation calcule l’acoustique en connectant tous les emplacements sources possibles à chaque point de sonde. Lors de l’exécution, l’emplacement de l’auditeur est interpolé entre les points situés à proximité des sondes.

Il est important de vérifier que des points de sonde existent partout où le joueur est censé se déplacer dans la scène. Les points de sonde sont placés sur le maillage de navigation par le moteur Project Acoustics et ils ne peuvent pas être déplacés ni modifiés : vérifiez donc que le maillage de navigation couvre tous les emplacements possibles du joueur en examinant les points de sonde.

![Capture d’écran de l’aperçu des sondes Acoustics dans Unreal](media/unreal-probes-preview.png)

Pour plus d’informations sur les différences entre la résolution grossière et la résolution fine, consultez [Résolution de bake](bake-resolution.md).

## <a name="bake-your-level-using-azure-batch"></a>Effectuer un bake de votre niveau avec Azure Batch

Vous pouvez effectuer un bake de votre scène avec un cluster de calcul dans le cloud en utilisant le service Azure Batch. Le plug-in Project Acoustics Unreal se connecte directement à Azure Batch pour instancier, gérer et supprimer un cluster Azure Batch pour chaque bake. Sous l’onglet Bake, entrez vos informations d’identification Azure, sélectionnez un type de machine et une taille de cluster, puis cliquez sur Bake.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pour référence : Éléments de l’onglet Bake

![Capture d’écran de l’onglet Acoustics Bake dans Unreal](media/unreal-bake-tab-details.png)

1. Le bouton d’onglet Bake, qui sert à afficher cette page.
2. Une brève description des actions à effectuer dans cette page.
3. Champs où entrer vos informations d’identification Azure une fois que votre compte Azure a été créé. Pour plus d’informations, consultez [Créer un compte Azure Batch](create-azure-account.md).
4. Lancez le portail Azure pour gérer vos abonnements, surveiller l’utilisation, afficher les informations de facturation, etc. 
5. Type de nœud de calcul Azure Batch à utiliser pour le calcul. Le type de nœud doit être pris en charge par votre emplacement de centre de données Azure. En cas de doute, conservez **Standard_F8s_v2**.
6. Nombre de nœuds à utiliser pour ce calcul. Le nombre que vous entrez ici affecte la durée nécessaire pour effectuer le baking. Il est limité par votre allocation de noyaux Azure Batch. L’allocation par défaut autorise uniquement deux nœuds de huit noyaux ou un nœud de 16 noyaux, mais elle peut être étendue. Pour plus d’informations sur les contraintes d’allocation de noyaux, consultez [Créer un compte Azure Batch](create-azure-account.md).
7. Cochez cette case de manière à configurer votre pool de calcul pour qu'il utilise des [nœuds basse priorité](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Le coût des nœuds de calcul basse priorité est nettement moindre, mais ils peuvent parfois être inaccessibles ou reportés.
8. Durée attendue nécessaire pour exécuter votre travail dans le cloud. Cela n’inclut pas le temps de démarrage des nœuds. Une fois le travail commencé, cela correspond environ à la durée après laquelle vous obtiendrez les résultats. Notez qu’il s’agit uniquement d’une estimation.
9. La durée totale de temps de traitement nécessaire pour exécuter les simulations. Il s’agit de la quantité totale de temps de calcul de nœud qui sera utilisée dans Azure. Pour plus d’informations sur l’utilisation de cette valeur, consultez [Estimation du coût de baking](#Estimating-bake-cost) ci-dessous.
10. Cliquez sur le bouton Bake pour envoyer le baking vers le cloud. Pendant l’exécution d’un travail, ce bouton indique **Cancel Job**. S’il existe des erreurs sur cet onglet, ou si le workflow sur l’onglet **Probes** n’est pas terminé, ce bouton est désactivé.
11. Le nombre de sondes pour votre scène, tel que calculé sous l’onglet **Probes**. Le nombre de sondes détermine le nombre de simulations qui doivent être exécutées dans le cloud. Vous ne pouvez pas spécifier plus de nœuds qu’il n’y a de sondes.
12. Ce message vous indique l’état actuel du travail ou, s’il existe des erreurs dans cet onglet, la nature de ces erreurs.

Vous pouvez toujours obtenir des informations complètes sur les travaux actifs, les pools de calcul et de stockage dans le [portail Azure](https://portal.azure.com).

Pendant l’exécution d’un travail, le bouton **Bake** indique **Cancel Job**. Utilisez ce bouton pour annuler le travail en cours. L’annulation d’un travail ne peut pas être annulée. Aucun résultat ne sera disponible et vous serez quand même facturé pour les temps de calcul utilisés avant l’annulation.

Une fois que vous avez lancé un baking, vous pouvez fermer Unreal. En fonction du projet, du type de nœud et du nombre de nœuds, un baking dans le cloud peut prendre plusieurs heures. L’état du travail de baking est mis à jour quand vous rechargez le projet et ouvrez la fenêtre Acoustics. Si le travail est terminé, le fichier de sortie est téléchargé.

Les informations d’identification Azure sont stockées de façon sécurisée sur votre machine locale et associées à votre projet Unreal. Elles sont utilisées exclusivement pour établir une connexion sécurisée vers Azure.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a> Estimation du coût d'un baking Azure

Pour estimer le coût d’un baking donné, prenez la valeur affichée dans **Estimated Compute Cost**, qui est une durée, et multipliez-la par le coût horaire (dans votre devise locale) du **Type de nœud de machine virtuelle** que vous avez sélectionné. Le résultat n’inclut pas la durée nécessaire pour préparer les nœuds à l’exécution. Par exemple, si vous sélectionnez **Standard_F8s_v2** comme type de nœud, qui a un coût de 0,40 $/heure, et que l’estimation du coût de calcul est de 3 heures et 57 minutes, le coût estimé pour exécuter le travail sera de 0,40 $ * ~4 heures = ~1,60 $. Le coût réel sera probablement un peu plus élevé à cause de la durée supplémentaire nécessaire au démarrage des nœuds. Vous trouverez le coût horaire des nœuds dans la page [Tarification d’Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (sélectionnez « Optimisée pour le calcul » ou « Calcul hautes performances » comme catégorie).

### <a name="reviewing-the-bake-results"></a>Examen des résultats de baking

Une fois le baking terminé, vérifiez que les points de sonde et les voxels sont à leurs emplacements attendus en exécutant le plug-in de runtime.

## <a name="data-files"></a><a name="Data-Files"></a>Fichiers de données

Quatre fichiers de données sont créés par ce plug-in à différents stades. Seul l’un d’entre eux est nécessaire lors de l’exécution et est placé dans le dossier Content/Acoustics de votre projet, qui est automatiquement ajouté au chemin de packaging de votre projet. Les trois autres sont trouvent dans le dossier Acoustics Data et ne sont pas packagés.

* **[Projet]/Config/ProjectAcoustics.cfg** : Ce fichier stocke les données que vous entrez dans les champs de l’interface utilisateur de Acoustics Mode. Vous ne pouvez pas changer l’emplacement et le nom de ce fichier. D’autres valeurs stockées dans ce fichier affectent le baking, mais elles sont destinées aux utilisateurs avancés et ne doivent pas être modifiées.
* **[Projet]/Content/Acoustics/[nom_niveau]\_AcousticsData.ace** : Ce fichier est créé au cours de la simulation du bake. Il contient les données de recherche utilisées par le runtime pour restituer l’acoustique de votre scène. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**. Si vous souhaitez renommer ce fichier après sa création, supprimez l’UAsset de votre projet Unreal, renommez le fichier en dehors d’Unreal dans l’Explorateur de fichiers, puis réimportez ce fichier Unreal pour produire un nouvel UAsset. Renommer l’UAsset ne suffira pas en soi.
* **[Projet]/Plugins/ProjectAcoustics/AcousticsData/[nom_niveau]\_AcousticsData.vox** : Ce fichier stocke la géométrie d’acoustique voxélisée et les propriétés du matériel. Il est calculé à l’aide du bouton **Calculate** de l’onglet **Probes**. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.
* **[Projet]/Plugins/ProjectAcoustics/AcousticsData/[nom_niveau]\_AcousticsData\_config.xml** : Ce fichier stocke les paramètres calculés à l’aide du bouton **Calculate** de l’onglet **Probes**. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.

Veillez à ne pas supprimer le fichier *.ace. téléchargé depuis Azure. Ce fichier n’est pas récupérable, sauf en réeffectuant un baking de la scène.

## <a name="next-steps"></a>Étapes suivantes
* Explorer les [contrôles de conception pour Unreal](unreal-workflow.md)
* Explorer les [principes de conception de Project Acoustics](design-process.md)

