---
title: Tutoriel de baking Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Ce tutoriel décrit le baking acoustique avec Project Acoustics dans Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243126"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutoriel de baking Project Acoustics Unity
Ce tutoriel décrit le baking acoustique à l’aide de Project Acoustics dans Unity.

Configuration logicielle requise :
* [Unity 2018.2+](https://unity3d.com) pour Windows ou MacOS
* Le [plug-in Project Acoustics intégré dans votre projet Unity](unity-integration.md) ou l’[exemple de contenu Project Acoustics Unity](unity-quickstart.md)
* *Facultatif :* un [compte Azure Batch](create-azure-account.md) pour accélérer le bake en utilisant le cloud computing

## <a name="open-the-project-acoustics-bake-window"></a>Ouvrir la fenêtre de baking de Project Acoustics
Dans Unity, sélectionnez **Acoustics** dans le menu **Window**.

![Éditeur Unity avec l’option Acoustics mise en surbrillance dans le menu Window](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Créer un maillage de navigation
Project Acoustics utilise un maillage de navigation pour placer des points de sonde d’auditeur pour la simulation. Vous pouvez utiliser le [workflow de maillage de navigation](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) Unity. Vous pouvez utiliser un autre package de modélisation 3D pour concevoir votre propre maillage.

## <a name="mark-acoustic-scene-objects"></a>Marquer des objets de la scène acoustique
Project Acoustics s’appuie sur deux types d’objets de scène pour la simulation :
- Les objets qui reflètent et occultent le son dans la simulation
- Le maillage de navigation du joueur qui limite les points de sonde d’auditeur dans la simulation

Les deux types d’objets sont marqués à l’aide de l’onglet **Objects**.

Étant donné que le marquage d’objets ajoute simplement les composants *AcousticsGeometry* ou *AcousticsNavigation* à l’objet, vous pouvez également utiliser le [workflow des composants Unity standard](https://docs.unity3d.com/Manual/UsingComponents.html) pour marquer ou supprimer les marques des objets. Vous pouvez uniquement marquer des renderers de maillage et des terrains. Tous les autres types d’objets seront ignorés. Les cases à cocher marquent ou suppriment la marque de tous les objets affectés.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marquer la géométrie d’occlusion et de réflexion acoustique
Ouvrez l’onglet **Objects** de la fenêtre **Acoustics**. Marquez tous les objets comme *Acoustics Geometry* s’ils doivent bloquer, réfléchir ou absorber le son. La géométrie acoustique peut comprendre des éléments comme le sol, des murs, des toits, des fenêtres et des vitres, des tapis et des meubles volumineux. Vous pouvez utiliser n’importe quel niveau arbitraire de complexité pour ces objets. La scène étant voxelisée avant la simulation, des maillages très détaillés, comme des arbres avec beaucoup de feuilles, ne demandent pas un travail de bake plus important que des objets simplifiés.

N’incluez pas d’éléments qui ne doivent pas affecter l’acoustique, comme des maillages de collision invisibles.

La transformation d’un objet pendant le calcul de sonde (par le biais de l’onglet **Probes**) est fixe dans les résultats du bake. Si des objets marqués dans la scène sont déplacés plus tard, le calcul de sonde et le baking doivent être réexécutés.

### <a name="mark-the-navigation-mesh"></a>Marquer le maillage de navigation
Les maillages de navigation qui ont été créés par le biais du workflow Unity seront détectés par le système Acoustics. Pour utiliser vos propres maillages, marquez-les à partir de l’onglet **Objects**.

### <a name="for-reference-the-objects-tab-parts"></a>Pour référence : Les composants de l’onglet Objects
Les parties de la page d’onglets (illustrées après les descriptions) sont les suivantes :

1. Les boutons de sélection d’onglet (avec l’onglet **Objects** sélectionné). Utilisez ces boutons pour parcourir les différentes étapes de bake acoustique, de gauche à droite.
1. Brève description de ce que vous pouvez faire à l’aide de cet onglet.
1. Filtres disponibles pour la fenêtre de hiérarchie. Ces options vous permettent de filtrer la fenêtre de hiérarchie pour n’afficher que les objets du type spécifié, afin de simplifier leur marquage. Si vous n’avez encore rien marqué pour l’acoustique, la sélection des deux dernières options ne vous montrera rien. Toutefois, ces options vous aident à trouver des objets une fois qu’ils sont marqués.
1. Quand aucun objet n’est sélectionné, cette section affiche l’état de tous les objets dans la scène.
    * Total : nombre total d’objets actifs et non masqués.
    * Ignored : nombre d’objets qui ne sont pas des renderers de maillage ou des terrains.
    * Mesh : nombre d’objets de renderers de maillage.
    * Terrain : nombre d’objets de terrains.
    * Geometry : nombre d’objets de maillages ou de terrains qui sont marqués comme **Acoustics Geometry**.
    * Navigation : nombre d’objets de maillages ou de terrains marqués comme **Acoustics Navigation**. Ce nombre n’inclut pas le NavMesh Unity.
1. Le nombre total d’objets « marquables » dans la scène, à savoir uniquement les renderers de maillage et les terrains. Utilisez les cases à cocher pour marquer ces objets (c’est-à-dire y ajouter le composant approprié) en tant que géométrie ou navigation pour l’acoustique.
1. Quand rien n’est sélectionné, cette note vous rappelle que vous devez sélectionner des objets pour le marquage, si nécessaire. Vous pouvez également cocher une ou deux cases pour marquer tous les objets dans la scène.
1. Quand des objets sont sélectionnés, cette section affiche uniquement l’état des objets sélectionnés.
1. Le nombre total d’objets sélectionnés « marquables ». Le fait de cocher ou de décocher les cases marque ou supprime la marque uniquement pour les objets sélectionnés.

Si vous n’avez rien sélectionné dans votre scène, l’onglet **Objects** a l’apparence suivante.

![Onglet Acoustics Objects sans rien sélectionné](media/objects-tab-no-selection-detail.png)

Si vous avez sélectionné quelque chose dans votre scène ou hiérarchie, l’onglet a l’apparence suivante.

![Onglet Acoustics Objects avec des sélections](media/objects-tab-selection-detail.png)

Si certains objets sont marqués et d’autres non, la case à cocher correspondante affiche une valeur « mixte », comme dans l’image suivante.

![Onglet Acoustics Objects avec une sélection mixte d’icônes mise en surbrillance](media/mixed-object-selection-detail.png)

Cochez la case pour marquer tous les éléments. Décochez-la pour supprimer la marque de tous les objets.

Les objets peuvent être marqués à la fois pour la géométrie et la navigation.

## <a name="select-acoustic-materials"></a>Sélectionner des matériaux acoustiques
Une fois vos objets marqués, sélectionnez le bouton **Materials**. Affectez ensuite des matériaux acoustiques. Le système de matériaux Project Acoustics est lié au système de matériaux visuels Unity. Pour que deux objets aient des matériaux acoustiques distincts, ils doivent avoir des matériaux visuels distincts.

La sélection de matériau acoustique détermine la quantité d’énergie sonore qui est reflétée par chaque surface. Le matériau acoustique par défaut a une absorption similaire à celle de l’acier. Project Acoustics suggère des matériaux en fonction du nom du matériau visuel. Vous pouvez aussi affecter le matériau acoustique **Custom** à un matériau pour activer un curseur de coefficient d’absorption réglable.

La durée de réverbération d’un matériau donné dans une pièce est inversement proportionnelle à son coefficient d’absorption. La plupart des matériaux ont des valeurs d’absorption comprises entre 0,01 et 0,20. Les matériaux qui ont des coefficients d’absorption en dehors de cette plage sont très absorbants.

![Un graphe montre la corrélation négative entre la durée de réverbération et le coefficient d’absorption.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pour référence : Éléments de l’onglet Materials
![Onglet Acoustics Materials dans Unity](media/materials-tab-detail.png)
1. Le bouton **Materials** affiche cet onglet.
2. Brève description de ce que vous pouvez faire à l’aide de cet onglet.
3. Quand cette case est cochée, seuls les matériaux utilisés par les objets marqués comme **Acoustics Geometry** sont listés. Sinon, tous les matériaux utilisés dans la scène sont listés.
4. Utilisez ces options pour changer l’ordre des options dans un menu dans la colonne **Acoustics** (6). Triez les matériaux acoustiques par nom (**Name**) ou par absorption (**Absorptivity**), de faible à élevé.
5. Liste triée par ordre alphabétique des matériaux utilisés dans la scène. Si la case **Show Marked Only** est cochée (3), seuls les matériaux utilisés par les objets marqués comme **Acoustics Geometry** sont affichés. Sélectionnez un matériau ici pour sélectionner tous les objets dans la scène qui utilisent ce matériau.
6. Matériau acoustique auquel le matériau de la scène a été affecté. Sélectionnez un élément pour changer le matériau acoustique affecté à ce matériau de scène. Pour changer l’ordre de tri de ces menus, utilisez les options **Sort Acoustics By** (4).
7. Coefficient d’absorption acoustique du matériau sélectionné dans la colonne de gauche (6). La valeur 0 signifie que le matériau est parfaitement réfléchissant (aucune absorption), alors que la valeur 1 signifie qu’il est parfaitement absorbant (aucune réflexion). Vous ne pouvez pas changer le coefficient d’absorption, sauf si le matériau sélectionné est **Custom**.
8. Pour un matériau marqué comme **Custom**, le curseur est activé. Vous pouvez déplacer le curseur ou taper une valeur pour affecter un coefficient d’absorption.

## <a name="calculate-and-review-listener-probe-locations"></a>Calculer et passer en revue les emplacements des sondes d’auditeur
Après avoir affecté les matériaux, basculez vers l’onglet **Probes**. Sélectionnez **Calculate** pour placer les points de sonde d’auditeur pour la simulation.

### <a name="what-the-calculate-button-does"></a>Ce que fait le bouton « Calculate »
Le bouton **Calculate** utilise la géométrie de votre scène acoustique sélectionnée pour la préparer à la simulation :

- Il prend la géométrie des maillages de scène et calcule un *volume de voxel*. Le volume de voxel est un volume de l’ensemble de votre scène constitué de petits « voxels » cubiques. La taille de voxel est déterminée par la fréquence de simulation, qui est contrôlée par le paramètre **Simulation Resolution**. Chaque voxel est marqué comme étant « à l’air libre » ou contenant la géométrie de la scène. Si un voxel contient une géométrie, il est marqué avec le coefficient d’absorption du matériau affecté à cette géométrie.
- Il utilise les maillages de navigation pour placer des points de sonde d’auditeur. L’algorithme équilibre les préoccupations en matière de couverture spatiale, de durée de simulation et de taille de fichier. Il vise à s’assurer que les couloirs étroits et les petits espaces ont toujours une certaine couverture. Le nombre classique de points de sonde va de 100 pour les petites scènes à quelques milliers pour les grandes scènes.

En fonction de la taille de votre scène et de la rapidité de votre ordinateur, ces calculs peuvent prendre plusieurs minutes.

### <a name="review-voxel-and-probe-placement"></a>Passer en revue le placement des voxels et des sondes
Affichez un aperçu des données des voxels et des emplacements des points de sonde pour vérifier que vous êtes prêt à effectuer le bake de votre scène. Un maillage de navigation incomplet, ou une géométrie acoustique manquante ou en trop, est en général facile à voir dans l’aperçu. Activez ou désactivez le placement des voxels et des sondes à l’aide du menu **Gizmos**.

![Menu Gizmos dans Unity](media/gizmos-menu.png)

Les voxels qui contiennent une géométrie acoustique sont affichés sous forme de cubes verts. Explorez votre scène et vérifiez que tous les éléments de la géométrie ont des voxels. La caméra de la scène doit se trouver à moins de cinq mètres de l’objet pour que les voxels soient affichés.

Si vous comparez les voxels créés avec une résolution fine et grossière, vous constaterez que les voxels grossiers sont deux fois plus volumineux.

![Aperçu des voxels grossiers dans l’éditeur Unity](media/voxel-cubes-preview.png)

Les résultats de la simulation sont interpolés entre les emplacements des points de sonde d’auditeur lors de l’exécution. Vérifiez qu’il existe des points de sonde près de tous les endroits où le joueur est censé aller dans la scène.

![Aperçu des sondes dans l’éditeur Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Soyez prudent dans le renommage des scènes
Le nom de la scène sert à connecter la scène aux fichiers qui stockent les positions des points de sonde et la voxelisation. Si vous renommez la scène après le calcul des points de sonde, les données de position et d’affectation des matériaux sont perdues et doivent être recalculées.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pour référence : Éléments de l’onglet Probes
![Onglet Acoustics Probes dans Unity](media/probes-tab-detail.png)

1. Le bouton **Probes** affiche cet onglet.
2. Brève description de ce que vous pouvez faire sous cet onglet.
3. Utilisez ces options pour définir une résolution de simulation grossière ou fine. La simulation grossière est plus rapide, mais elle présente certains inconvénients. Pour plus d’informations, consultez [Résolution de bake](bake-resolution.md).
4. Spécifie où placer les fichiers de données acoustiques. Sélectionnez le bouton «  **...**  » pour accéder à un sélecteur de dossiers. L’emplacement par défaut est *Assets/AcousticsData*. Un sous-dossier *Editor* est également créé à cet emplacement. Pour plus d’informations, consultez [Fichiers de données ajoutés par le processus de bake](#Data-Files) plus loin dans cet article.
5. Le préfixe spécifié ici sert à nommer les fichiers de données pour cette scène. La valeur par défaut est « Acoustics_ *[nom_scène]*  ».
6. Une fois les sondes calculées, les contrôles que nous venons de décrire sont désactivés. Sélectionnez le bouton **Clear** pour effacer les calculs et activer les contrôles afin de pouvoir recalculer avec de nouveaux paramètres.
7. Sélectionnez **Calculate** pour voxeliser la scène et calculer les emplacements des points de sonde. Le calcul est effectué localement sur votre ordinateur. Il doit être effectué avant d’effectuer un bake.

Dans cette version de Project Acoustics, les sondes ne peuvent pas être placées manuellement. Utilisez le processus automatisé sous l’onglet **Probes**.

Pour plus d’informations sur la résolution grossière et fine, consultez [Résolution de bake](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Effectuer un bake de votre scène avec Azure Batch
Vous pouvez effectuer un bake de votre scène sur un cluster de calcul dans le cloud en utilisant le service Azure Batch. Le plug-in Project Acoustics Unity se connecte directement à Azure Batch pour instancier, gérer et supprimer un cluster Azure Batch pour chaque bake. Sous l’onglet **Bake**, entrez vos informations d’identification Azure, sélectionnez un type de machine et une taille de cluster, puis sélectionnez **Bake**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pour référence : Éléments de l’onglet Bake
![Onglet Acoustics Bake dans Unity](media/bake-tab-details.png)

1. Le bouton **Bake** affiche cet onglet.
2. Brève description de ce que vous pouvez faire dans cette page.
3. Entrez vos informations d’identification Azure dans ces champs, une fois votre compte Azure créé. Pour plus d’informations, consultez [Créer un compte Azure Batch](create-azure-account.md).
4. Champ d’étiquette d’image Docker pour l’ensemble d’outils Acoustics.
5. Ouvre le portail Azure pour vous permettre de gérer vos abonnements, superviser l’utilisation et consulter les informations de facturation.
6. Spécifie le type de nœud de calcul Azure Batch à utiliser pour le calcul. Le type de nœud doit être pris en charge par votre emplacement de centre de données Azure. En cas de doute, conservez **Standard_F8s_v2**.
7. Nombre de nœuds à utiliser pour le calcul. Ce nombre affecte la durée du bake. Il est limité par votre allocation de noyaux Azure Batch. L’allocation par défaut autorise uniquement deux nœuds de huit noyaux ou un nœud de 16 noyaux, mais elle peut être étendue. Pour plus d’informations sur les contraintes d’allocation de noyaux, consultez [Créer un compte Azure Batch](create-azure-account.md).
8. Cochez cette case afin de configurer votre pool de calcul pour qu’il utilise des [nœuds basse priorité](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Les nœuds de calcul basse priorité ont un coût nettement inférieur, mais il se peut qu’ils ne soient pas toujours disponibles ou qu’ils soient préemptés à tout moment.
9. Le nombre de sondes pour votre scène, tel que calculé sous l’onglet **Probes**. Le nombre de sondes détermine le nombre de simulations qui doivent être exécutées dans le cloud. Vous ne pouvez pas spécifier plus de nœuds qu’il n’y a de sondes.
10. Estimation de la durée nécessaire à l’exécution de votre travail dans le cloud, sans compter le temps de démarrage du nœud. Une fois le travail lancé, ce champ affiche une estimation de la durée jusqu’à obtention des résultats.
11. La durée totale de temps de traitement nécessaire pour exécuter les simulations. Cette valeur correspond à la quantité totale de temps de calcul de nœud qui sera utilisée dans Azure. Pour plus d’informations, consultez [Estimer le coût d’un bake Azure](#Estimating-bake-cost) plus loin dans cet article.
12. Ce message indique où les résultats du bake seront enregistrés une fois le travail terminé.
13. *(Utilisation avancée uniquement :)* Ce bouton force Unity à oublier un bake que vous avez soumis. Par exemple, si vous avez téléchargé les résultats à l’aide d’un autre ordinateur, sélectionnez le bouton **Clear State** pour oublier ce travail. Le fichier de résultats, une fois prêt, ne sera *pas* téléchargé. *Cela n’est pas la même chose qu’annuler le travail. Le travail, s’il est en cours d’exécution, continuera à s’exécuter dans le cloud.*
14. Sélectionnez ce bouton pour envoyer le bake vers le cloud. Pendant l’exécution d’un travail, ce bouton indique **Cancel Job**.
15. Sélectionnez ce bouton pour préparer le traitement de la [simulation acoustique sur votre PC](#Local-bake).
16. Cette zone indique l’état du baking. Une fois le bake terminé, elle indique « Downloaded ».

Vous pouvez toujours obtenir des informations complètes sur les travaux actifs, les pools de calcul et le stockage dans le [portail Azure](https://portal.azure.com).

Pendant l’exécution d’un travail, l’étiquette du bouton **Bake** indique **Cancel Job**. Utilisez ce bouton pour annuler le travail en cours. Vous serez invité à confirmer. L’annulation d’un travail ne peut pas être annulée. Quand vous annulez, aucun résultat ne sera disponible mais vous serez quand même facturé pour les temps de calcul Azure utilisés.

Une fois que vous avez lancé un bake, vous pouvez fermer Unity. En fonction du projet, du type de nœud et du nombre de nœuds, un baking dans le cloud peut prendre plusieurs heures. L’état du travail de baking est mis à jour quand vous rechargez le projet et ouvrez la fenêtre Acoustics. Si le travail est terminé, le fichier de sortie est téléchargé.

Pour des raisons de sécurité, les informations d’identification Azure sont stockées sur votre ordinateur local et associées à votre éditeur Unity. Elles sont utilisées uniquement pour établir une connexion sécurisée vers Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Trouver le statut d’un travail en cours d’exécution dans le portail Azure

1. Recherchez l’ID du travail de bake sous l’onglet **Bake**.

    ![ID du travail de bake Unity mis en surbrillance sous l’onglet Bake](media/unity-job-id.png)  

2. Ouvrez le [portail Azure](https://portal.azure.com), accédez au compte Batch utilisé pour le bake et sélectionnez **Travaux**.

    ![Lien Travaux dans le portail Azure](media/azure-batch-jobs.png)  

3. Recherchez l’ID du travail dans la liste des travaux.

   ![État du travail de bake mis en surbrillance dans le portail Azure](media/azure-bake-job-status.png)  

4. Sélectionnez l’ID du travail pour voir l’état des tâches associées et du travail global.

   ![État de la tâche de bake](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a> Estimer le coût d’un bake Azure

Pour estimer le coût d’un bake, commencez par la valeur **Estimated Compute Cost**, qui est une durée. Multipliez cette valeur par le coût horaire dans votre devise locale pour le **type de nœud de machine virtuelle** que vous sélectionnez. Notez que le résultat n’inclura pas la durée nécessaire pour préparer les nœuds à l’exécution.

Par exemple, imaginez que vous sélectionnez **Standard_F8s_v2** comme type de nœud, qui a un coût de $0,40/hr. Si la valeur **Estimated Compute Cost** est de 3 heures et 57 minutes, le coût d’exécution du travail estimé sera de $0,40 * ~ 4 heures = ~ $1,60. Le coût réel sera probablement un peu plus élevé à cause de la durée supplémentaire nécessaire au démarrage des nœuds.

Recherchez le coût horaire des nœuds dans [Tarification Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Sélectionnez **Optimisée pour le calcul** ou **Calcul haute performance** comme catégorie.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a> Effectuer le bake de votre scène sur votre PC
Vous pouvez aussi effectuer le bake de votre scène sur votre propre PC. Cette méthode peut être pratique pour faire des essais acoustiques pour de petites scènes avant de créer un compte Azure Batch. Notez toutefois que la simulation acoustique locale peut durer longtemps, en fonction de la taille de la scène.

### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises
* Processeur x86 64 bits avec au moins 8 cœurs et 32 Go de RAM.
* [Hyper-V activé](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) pour exécuter Docker

À titre d’exemple, dans notre test sur une machine de huit cœurs avec un processeur Intel Xeon E5-1660 à 3 GHz et 32 Go de RAM :
* Une petite scène avec 100 sondes a pris environ deux heures pour un bake avec une résolution grossière, et 32 heures pour un bake avec une résolution fine.
* Une scène moyenne avec 1000 sondes a pris environ 20 heures pour un bake avec une résolution grossière, et 21 jours pour un bake avec une résolution fine.

### <a name="set-up-docker"></a>Configurer Docker
Installez et configurez Docker sur le PC qui traitera la simulation :
1. Installez [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Ouvrez les paramètres Docker, accédez à **Advanced** et configurez les ressources pour au moins 8 Go de RAM. Plus vous allouerez de processeurs à Docker, plus le bake sera rapide.  
![Exemples de paramètres Docker](media/docker-settings.png)
1. Accédez à **Lecteurs partagés** et activez le partage pour le lecteur utilisé pour le traitement.  
![Options de lecteur partagé Docker](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Exécuter le bake local
1. Sélectionnez le bouton **Prepare Local Bake** sous l’onglet **Bake**. Ensuite, sélectionnez un emplacement de dossier où enregistrer les fichiers d’entrée et les scripts d’exécution. Vous pouvez alors exécuter le bake sur n’importe quel ordinateur, à condition qu’il respecte la configuration matérielle minimale requise et que vous ayez installé Docker en y copiant le dossier.
2. Pour lancer la simulation, exécutez le script *runlocalbake.bat* sur Windows ou le script *runlocalbake.sh* sur MacOS. Ce script extrait l’image Project Acoustics Docker avec l’ensemble d’outils nécessaires au traitement de la simulation, et démarre la simulation.
3. Une fois la simulation terminée, copiez le fichier *.ace* qui en résulte dans votre projet Unity. Pour veiller à ce qu’Unity l’identifie comme un fichier binaire, ajoutez « .bytes » à l’extension de fichier (par exemple, « Scene1.ace.bytes »). Les journaux détaillés de la simulation sont stockés dans *AcousticsLog.txt.* Si vous rencontrez des problèmes, examinez ce fichier pour vous aider à diagnostiquer le problème.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a> Fichiers de données ajoutés par le processus de bake

Les quatre fichiers de données suivants sont créés pendant le processus de bake. Un des fichiers contient les résultats de la simulation et il est fourni avec votre titre. Les autres stockent les données relatives à l’éditeur Unity.

Résultat de la simulation :
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes* : ce fichier est la table de recherche du runtime. Il contient les résultats de simulation et les éléments de scène acoustique voxelisés. Vous pouvez changer le nom et l’emplacement de ce fichier sous l’onglet **Probes**.

   *Faites attention à ne pas supprimer le fichier des résultats de la simulation. Il n’est pas récupérable, sauf à refaire le bake de la scène.*

Fichiers de données de l’éditeur :
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset* : ce fichier stocke les données que vous entrez dans les champs dans l’interface utilisateur Acoustics. Vous ne pouvez pas changer le nom et l’emplacement de ce fichier.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox* : ce fichier stocke la géométrie acoustique voxelisée et les propriétés des matériaux qui sont calculées quand vous sélectionnez le bouton **Calculate** sous l’onglet **Probes**. Vous pouvez changer le nom et l’emplacement de ce fichier sous l’onglet **Probes**.
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml* : ce fichier stocke les paramètres de simulation qui sont calculés quand vous sélectionnez **Calculate**. Vous pouvez changer le nom et l’emplacement de ce fichier sous l’onglet **Probes**.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurer la table de recherche acoustique
Faites glisser la préfabrication **Project Acoustics** du panneau du projet vers votre scène :

![Préfabrication Acoustics dans Unity](media/acoustics-prefab.png)

Sélectionnez l’objet de jeu **ProjectAcoustics** et accédez à son panneau d’inspecteur. Spécifiez l’emplacement du résultat de votre bake (le fichier .ace dans *Assets/AcousticsData*) : Faites-le glisser dans le script Acoustics Manager ou sélectionnez le bouton en forme de cercle en regard de la zone de texte.

![Préfabrication Acoustics Manager dans Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Étapes suivantes
* Explorez les [contrôles de conception Unity](unity-workflow.md).
* Explorez les [principes de conception de Project Acoustics](design-process.md).
