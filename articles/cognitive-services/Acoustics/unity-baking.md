---
title: Tutoriel de baking Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Ce tutoriel décrit le baking acoustique avec Project Acoustics dans Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8875674b0f9c621a573dda591b4dc2b6f018a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790227"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutoriel de baking Project Acoustics Unity
Ce tutoriel décrit le baking acoustique avec Project Acoustics dans Unity.

Configuration logicielle requise :
* [Unity 2018.2+](http://unity3d.com) pour Windows
* Le [plug-in Project Acoustics intégré dans votre projet Unity](unity-integration.md) ou l’[exemple de contenu Project Acoustics Unity](unity-quickstart.md)
* Facultatif : Un [compte Azure Batch](create-azure-account.md) pour accélérer le baking en utilisant le cloud computing

## <a name="open-the-project-acoustics-bake-window"></a>Ouvrir la fenêtre de baking de Project Acoustics
Choisissez **Window > Acoustics** dans le menu Unity :

![Capture d’écran de l’éditeur Unity avec l’option de menu Acoustics mise en surbrillance](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Créer un maillage de navigation
Project Acoustics utilise un maillage de navigation pour placer des points de sonde d’auditeur pour la simulation. Vous pouvez utiliser le [workflow de maillage de navigation](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) d’Unity ou utiliser un autre package de modélisation 3D pour concevoir votre propre maillage. 

## <a name="mark-acoustic-scene-objects"></a>Marquer des objets de la scène acoustique
Project Acoustics s’appuie sur deux types d’objets de scène pour la simulation : les objets qui vont réfléchir et bloquer le son dans la simulation, et le maillage de navigation du joueur qui contraint les points de sonde d’auditeur dans la simulation. Les deux types d’objets sont marqués à l’aide de l’onglet **Objects**. 

Étant donné que le marquage d’objets ajoute simplement les composants **AcousticsGeometry** ou **AcousticsNavigation** à l’objet, vous pouvez également utiliser le [workflow des composants Unity standard](https://docs.unity3d.com/Manual/UsingComponents.html) pour marquer ou supprimer les marques des objets. Seuls les Mesh Renderers et Terrains peuvent être marqués. Tous les autres types d’objets seront ignorés. Les cases à cocher marquent ou annulent le marquage de tous les objets affectés.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marquer la géométrie d’occlusion et de réflexion acoustique
Ouvrez l’onglet **Objects** de la fenêtre **Acoustics**. Marquez tous les objets comme **Acoustics Geometry** s’ils doivent bloquer, réfléchir ou absorber le son. La géométrie acoustique peut comprendre des éléments comme le sol, des murs, des toits, des fenêtres et des vitres, des tapis et des meubles volumineux. Vous pouvez utiliser n’importe quel niveau arbitraire de complexité pour ces objets. La scène étant voxelisée avant la simulation, des maillages très détaillés, comme des arborescences avec un grand nombre de petites feuilles, ne demandent pas un travail de baking plus important que des objets simplifiés.

N’incluez pas d’éléments qui ne doivent pas affecter l’acoustique, comme des maillages de collision invisibles.

La transformation d’un objet au moment du calcul de sonde (par le biais de l’onglet **Probes**, ci-dessous) est fixe dans les résultats de baking. Le déplacement de tout objet marqué dans la scène exigera de réeffectuer le calcul de la sonde et le baking de la scène.

### <a name="mark-the-navigation-mesh"></a>Marquer le maillage de navigation
Les maillages de navigation créés avec le flux de travail Unity seront détectés par le système acoustique. Pour utiliser vos propres maillages, marquez-les à partir de l’onglet **Objects**.

### <a name="for-reference-the-objects-tab-parts"></a>Pour référence : Les composants de l’onglet Objects
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

Si vous n’avez rien sélectionné dans votre scène, l’onglet Objects se présente comme dans l’image suivante :

![Capture d’écran de l’onglet Acoustics Objects sans sélection](media/objects-tab-no-selection-detail.png)

Si vous avez sélectionné quelque chose dans votre scène ou hiérarchie, vous aurez les paramètres suivants :

![Capture d’écran de l’onglet Acoustics Objects avec une sélection](media/objects-tab-selection-detail.png)

Si certains objets sont marqués et d’autres non, la case à cocher correspondante affiche une valeur « mixte » :

![Capture d’écran de l’onglet Acoustics Objects avec l’icône de sélection mixte mise en surbrillance](media/mixed-object-selection-detail.png)

Un clic sur la case à cocher force le marquage de tous les objets ; un autre clic annule le marquage de tous les objets.

Les objets peuvent être marqués à la fois pour la géométrie et la navigation.

## <a name="select-acoustic-materials"></a>Sélectionner des matériaux acoustiques
Une fois vos objets marqués, cliquez sur le bouton **Materials** et affectez des matériaux acoustiques. Le système des matériaux de Project Acoustics est lié au système de matériaux visuels de Unity : pour que deux objets aient des matériaux acoustiques distincts, ils doivent avoir des matériaux visuels distincts.

Les matériaux acoustiques contrôlent la quantité d’énergie sonore reflétée par chaque surface. Le matériau acoustique par défaut a une absorption similaire à celle du béton. Project Acoustics suggère des matériaux en fonction du nom du matériau visuel. Vous pouvez affecter le matériau acoustique « Custom » à un matériau pour activer un curseur de coefficient d’absorption.

La durée de réverbération d’une matière donnée dans une pièce est inversement proportionnelle à son coefficient d’absorption, la plupart des matières ayant des valeurs d’absorption comprises entre 0,01 et 0,20. Les matières avec des coefficients d’absorption en dehors de cette plage sont très absorbantes.

![Graphe montrant une corrélation négative de temps de réverbération avec un coefficient d’absorption](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pour référence : Éléments de l’onglet Materials
![Capture d’écran de l’onglet Acoustics Materials dans Unity](media/materials-tab-detail.png)

1. Le bouton d’onglet **Materials**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Quand cette case est cochée, seuls les matières utilisées par les objets marqués comme **Acoustics Geometry** sont répertoriées. Sinon, toutes les matières utilisées dans la scène sont répertoriées.
4. Utilisez ces options pour changer l’ordre du menu déroulant qui s’affiche quand vous cliquez sur une liste déroulante dans la colonne Acoustics ci-dessous (6). **Name** trie les matières acoustiques par nom. « Absorptivity » les trie par ordre d’absorption de faible à élevée.
5. La liste des matières utilisées dans la scène, triées par ordre alphabétique. Si la case **Show Marked Only** est cochée (3), seules les matières utilisées par les objets marqués comme **Acoustics Geometry** sont affichées. Si vous cliquez sur une matière ici, tous les objets dans la scène qui utilisent cette matière sont sélectionnés.
6. Montre la matière acoustique à laquelle la matière de la scène a été affectée. Cliquez sur une liste déroulante pour réaffecter une matière de la scène à une autre matière acoustique. Vous pouvez changer l’ordre de tri du menu affiché quand vous cliquez sur un élément ici à l’aide des options **Sort Acoustics By:** ci-dessus (4).
7. Montre le coefficient d’absorption acoustique de la matière sélectionnée dans la colonne précédente. Une valeur nulle signifie que la matière est parfaitement réfléchissante (aucune absorption), alors que la valeur 1 signifie qu’elle est parfaitement absorbante (aucune réflexion). Le coefficient d’absorption ne peut pas être changé, sauf si la  matière sélectionnée est « Custom ».
8. Pour une matière affectée à « Custom », le curseur n’est plus désactivé et vous pouvez choisir le coefficient d’absorption à l’aide du curseur ou en tapant une valeur.

## <a name="calculate-and-review-listener-probe-locations"></a>Calculer et passer en revue les emplacements des sondes d’auditeur
Après avoir affecté les matériaux, passez à l’onglet **Probes** et cliquez sur **Calculate** pour placer des points de sonde d’auditeur pour la simulation.

### <a name="what-the-calculate-button-calculates"></a>Ce que calcule le bouton « Calculate... »
Le bouton **Calculate...** utilise la géométrie de votre scène acoustique sélectionnée pour préparer votre scène à la simulation :

1. Il prend la géométrie des maillages de scène et calcule un volume de voxel. Le volume de voxel est un volume en trois dimensions qui englobe votre scène entière et est composé de petits « voxels » cubiques. La taille des voxels est déterminée par la fréquence de simulation, qui est définie par le paramètre **Simulation Resolution**. Chaque voxel est marqué comme étant « à l’air libre » ou contenant la géométrie de la scène. Si un voxel contient une géométrie, il est marqué avec le coefficient d’absorption de la matière affectée à cette géométrie.
2. Il utilise le ou les maillages de navigation pour placer des points de sonde d’auditeur. L’algorithme équilibre les demandes concurrentes liées à la couverture spatiale, à la durée de la simulation et à la taille de fichier, tout en garantissant que les corridors étroits et les petits espaces ont toujours une certaine quantité de couverture. Le nombre classique de points de sonde va de 100 pour les petites scènes à quelques milliers pour les grandes scènes.

En fonction de la taille de votre scène et de la rapidité de votre ordinateur, ces calculs peuvent prendre plusieurs minutes.

### <a name="review-voxel-and-probe-placement"></a>Passer en revue le placement des voxels et des sondes
Affichez un aperçu des données des voxels et les emplacements des points de sonde pour vérifiez que vous êtes prêt à effectuer le bake de votre scène. Un maillage de navigation incomplet, ou une géométrie acoustique manquante ou en trop, est en général visible rapidement dans l’aperçu. L’emplacement des voxels et des sondes peut être activé ou désactivé à l’aide du menu Gizmos :

![Capture d’écran du menu Gizmos dans Unity](media/gizmos-menu.png)

Les voxels contenant une géométrie acoustique sont affichés sous forme de cubes vert. Explorez votre scène et vérifiez que tous les éléments de la géométrie ont des voxels. La caméra de la scène doit se trouver à moins de cinq mètres de l’objet pour que les voxels soient affichés.

Si vous comparez les voxels créés avec une résolution fine et grossière, vous constaterez que les voxels grossiers sont deux fois plus volumineux.

![Capture d’écran de l’aperçu des voxels grossiers dans l’éditeur Unity](media/voxel-cubes-preview.png)

Les résultats de la simulation sont interpolés entre les emplacements des points de sonde d’auditeur lors de l’exécution. Vérifiez qu’il existe des points de sonde près de tous les endroits où le joueur est censé se déplacer dans la scène.

![Capture d’écran de l’aperçu des sondes dans l’éditeur Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Soyez prudent dans le renommage des scènes
Le nom de la scène sert à connecter la scène aux fichiers stockant les positions des points de sonde et la voxelisation. Si vous renommez la scène après le calcul des points de sonde, les données de position et d’affectation des matières sont perdues et doivent être recalculées.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pour référence : Éléments de l’onglet Probes
![Capture d’écran de l’onglet Acoustics Probes dans Unity](media/probes-tab-detail.png)

1. Le bouton d’onglet **Probes**, qui sert à afficher cette page.
2. Une brève description de ce que vous devez faire à l’aide de cette page.
3. Utilisez ces cases à cocher pour choisir une résolution de simulation grossière ou fine. La simulation grossière est plus rapide, mais a certains inconvénients. Pour plus d’informations, consultez [Résolution de bake](bake-resolution.md) ci-dessous.
4. Choisissez l’emplacement où les fichiers de données acoustiques doivent être placés à l’aide de ce champ. Cliquez sur le bouton « ... » pour utiliser un sélecteur de dossier. La valeur par défaut est **Assets/AcousticsData**. Un sous-dossier **Editor** sera également créé sous cet emplacement. Pour plus d’informations sur les fichiers de données, consultez [Fichiers de données](#Data-Files) ci-dessous.
5. Les fichiers de données pour cette scène seront nommés en utilisant le préfixe fourni ici. La valeur par défaut est « Acoustics_[nom_scène] ».
6. Une fois que les sondes ont été calculées, les contrôles ci-dessus sont désactivés. Cliquez sur le bouton **Clear** pour effacer les calculs et activer les contrôles afin que vous puissiez recalculer à l’aide de nouveaux paramètres.
7. Cliquez sur le bouton **Calculate...**  pour voxeliser la scène et calculer les emplacements des points de sonde. Cette opération est effectuée localement sur votre ordinateur, et doit être effectuée avant de lancer un baking.

Dans cette version de Project Acoustics, les sondes ne peuvent pas être placées manuellement. Elles doivent être placées à l’aide du processus automatisé fourni sous l’onglet **Probes**.

Pour plus d’informations sur les différences entre la résolution grossière et la résolution fine, consultez [Résolution de bake](bake-resolution.md).

## <a name="bake-your-scene-using-azure-batch"></a>Effectuer un bake de votre scène avec Azure Batch
Vous pouvez effectuer un bake de votre scène avec un cluster de calcul dans le cloud en utilisant le service Azure Batch. Le plug-in Project Acoustics Unity se connecte directement à Azure Batch pour instancier, gérer et supprimer un cluster Azure Batch pour chaque bake. Sous l’onglet **Bake**, entrez vos informations d’identification Azure, sélectionnez un type de machine et une taille de cluster, puis cliquez sur **Bake**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pour référence : Éléments de l’onglet Bake
![Capture d’écran de l’onglet Acoustics Bake dans Unity](media/bake-tab-details.png)

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
14. Cliquez sur le bouton **Bake** pour envoyer le bake dans le cloud. Pendant l’exécution d’un travail, ce bouton indique **Cancel Job**.
15. Il prépare le traitement de la [simulation acoustique sur votre PC](#Local-bake).
16. Cette zone indique l’état du baking. Une fois terminé, elle doit indiquer **Downloaded**.

Vous pouvez toujours obtenir des informations complètes sur les travaux actifs, les pools de calcul et de stockage dans le [portail Azure](https://portal.azure.com).

Pendant l’exécution d’un travail, le bouton **Bake** indique **Cancel Job**. Utilisez ce bouton pour annuler le travail en cours. Vous devrez confirmer l’annulation. L’annulation d’un travail ne peut pas être annulée. Aucun résultat ne sera disponible et vous serez quand même facturé pour les temps de calcul Azure utilisés.

Une fois que vous avez lancé un baking, vous pouvez fermer Unity. En fonction du projet, du type de nœud et du nombre de nœuds, un baking dans le cloud peut prendre plusieurs heures. L’état du travail de baking est mis à jour quand vous rechargez le projet et ouvrez la fenêtre Acoustics. Si le travail est terminé, le fichier de sortie est téléchargé.

Les informations d’identification Azure sont stockées de manière sécurisée sur votre ordinateur local et associées à votre éditeur Unity. Elles sont utilisées exclusivement pour établir une connexion sécurisée vers Azure.

### <a name="Estimating-bake-cost"></a> Estimation du coût d'un baking Azure

Pour estimer le coût d’un baking donné, prenez la valeur affichée dans **Estimated Compute Cost**, qui est une durée, et multipliez-la par le coût horaire (dans votre devise locale) du **Type de nœud de machine virtuelle** que vous avez sélectionné. Le résultat n’inclut pas la durée nécessaire pour préparer les nœuds à l’exécution. Par exemple, si vous sélectionnez **Standard_F8s_v2** comme type de nœud, qui a un coût de 0,40 $/heure, et que l’estimation du coût de calcul est de 3 heures et 57 minutes, le coût estimé pour exécuter le travail sera de 0,40 $ * ~4 heures = ~1,60 $. Le coût réel sera probablement un peu plus élevé à cause de la durée supplémentaire nécessaire au démarrage des nœuds. Vous trouverez le coût horaire des nœuds dans la page [Tarification d’Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (sélectionnez « Optimisée pour le calcul » ou « Calcul hautes performances » comme catégorie).

## <a name="Local-bake"></a> Effectuer le bake de votre scène sur votre PC
Vous pouvez effectuer le bake de votre scène sur votre propre PC. Ceci peut être pratique pour faire des essais acoustiques avec des petites scènes avant de créer un compte Azure Batch. Notez que la simulation acoustique peut durer longtemps, selon la taille de la scène.

### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises
* Processeur x86 64 bits avec au moins 8 cœurs et 32 Go de RAM.

À titre d’exemple, dans notre test sur une machine de 8 cœurs avec un processeur Intel Xeon E5-1660 à 3 GHz et 32 Go de RAM -
* Une petite scène avec 100 sondes peut prendre environ 2 heures pour un bake avec une résolution grossière, et 32 heures pour un bake avec une résolution fine.
* Une scène moyenne avec 1 000 sondes peut prendre environ 20 heures pour un bake avec une résolution grossière, et 21 jours pour un bake avec une résolution fine.

### <a name="setup-docker"></a>Configuration de Docker
Installez et configurez Docker sur le PC qui traitera la simulation-
1. Installez l'[ensemble d’outils Docker](https://www.docker.com/products/docker-desktop).
2. Lancez les paramètres Docker, accédez aux options avancées et configurez les ressources pour disposer d’au moins 8 Go de RAM. Plus vous allouerez d'UC à Docker, plus le baking sera rapide. ![Capture d’écran de l’exemple de paramètres Docker](media/docker-settings.png)
3. Accédez à « Lecteurs partagés » et activez le partage sur le lecteur utilisé pour le traitement.![Capture d’écran des options de lecteur partagé Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Exécuter un baking local
1. Cliquez sur le bouton « Prepare Local Bake » de l’onglet **Bake**, et sélectionnez un dossier où les fichiers d’entrée et les scripts d’exécution doivent être enregistrés. Vous pouvez ensuite exécuter le baking sur n’importe quel ordinateur, à condition qu'il respecte la configuration matérielle minimale requise et que Docker y soit installé en y copiant le dossier.
2. Lancez la simulation à l'aide du script « runlocalbake.bat ». Ce script extraira l’image Project Acoustics Docker avec l'ensemble d'outils nécessaires au traitement de la simulation et démarrera la simulation. 
3. Au terme de la simulation, copiez le fichier .ace qui en résulte dans votre projet Unity. Pour veiller à ce qu'Unity l'identifie comme un fichier binaire, ajoutez « .bytes » à l’extension de fichier (par exemple, « Scene1.ace.bytes »). Les journaux d’activité détaillés de la simulation sont stockés dans le fichier « AcousticsLog.txt ». En cas de problème, partagez ce fichier afin de faciliter le diagnostic.

## <a name="Data-Files"></a> Fichiers de données ajoutés par le processus de bake

Quatre fichiers de données sont créés au cours du processus de bake. Un des fichiers contient les résultats de la simulation et il est fourni avec votre titre. Les autres fichiers stockent les données relatives à l’éditeur Unity.

Résultat de la simulation :
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes** : Il s’agit de la table de recherche à l’exécution : elle contient les résultats de la simulation et les éléments de la scène acoustique voxelisée. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.

Faites attention à ne pas supprimer le fichier des résultats de la simulation. Il n’est pas récupérable, sauf à refaire le bake de la scène.

Fichiers de données de l’éditeur :
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset** : Ce fichier stocke les données que vous entrez dans les champs dans l’interface utilisateur Acoustics. Vous ne pouvez pas changer l’emplacement et le nom de ce fichier.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox** : Ce fichier stocke la géométrie acoustique voxelisée et les propriétés des matériaux qui sont calculées avec le bouton **Calculate...** de l’onglet Probes. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml** : Ce fichier stocke les paramètres de simulation calculés avec le bouton **Calculate...** de l’onglet **Probes**. L’emplacement et le nom de ce fichier peuvent être changés à l’aide des champs sous l’onglet **Probes**.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurer la table de recherche acoustique
Faites glisser et déposez la préfabrication **Project Acoustics** depuis le panneau du projet vers votre scène :

![Capture d’écran de l’onglet Acoustics Prefab dans Unity](media/acoustics-prefab.png)

Cliquez sur le Game Object **ProjectAcoustics** et accédez à son panneau Inspecteur. Spécifiez l’emplacement de votre résultat de baking (le fichier .ACE, dans **Assets/AcousticsData**) en le faisant glisser-déposer dans le script Acoustics Manager, ou en cliquant sur le bouton de cercle en regard de la zone de texte.

![Capture d’écran de l’onglet Acoustics Manager dans Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Étapes suivantes
* Explorer les [contrôles de conception pour Unity](unity-workflow.md)
* Explorer les [principes de conception de Project Acoustics](design-process.md)

