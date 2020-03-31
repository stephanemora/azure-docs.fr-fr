---
title: Tutoriel de conception Project Acoustics Unreal
titlesuffix: Azure Cognitive Services
description: Ce tutoriel décrit le workflow de conception pour Project Acoustics dans Unreal et Wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854242"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Tutoriel de conception Project Acoustics Unreal/Wwise
Ce tutoriel décrit la configuration et le workflow de la conception pour Project Acoustics dans Unreal et Wwise.

Prérequis quant aux logiciels :
* Un projet Unreal avec les plug-ins Project Acoustics Wwise et Unreal

Pour obtenir un projet Unreal avec Project Acoustics, vous pouvez :
* Suivre les instructions de [Intégration de Project Acoustics et Unreal](unreal-integration.md) pour ajouter Project Acoustics à votre projet Unreal,
* ou utiliser l’[exemple de projet Project Acoustics](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Configurer les propriétés de Wwise à l’échelle du projet
Wwise a des courbes d’obstruction et d’occlusion globales qui affectent la façon dont le plug-in Project Acoustics pilote le DSP audio Wwise.

### <a name="design-wwise-occlusion-curves"></a>Concevoir des courbes d’occlusion Wwise
Quand Project Acoustics est actif, il répond aux courbes de volume d’occlusion, de filtre passe-bas (LPF) et de filtre passe-haut (HPF) que vous définissez dans Wwise. Nous vous recommandons de définir votre type de courbe de volume en linéaire, avec une valeur de -100 dB pour une valeur d’occlusion de 100.

Avec cette valeur, si la simulation Project Acoustics calcule une occlusion de -18 dB, il entrera sur la courbe ci-dessous à X=18, et la valeur correspondante de Y sera l’atténuation appliquée. Pour faire une occlusion à la moitié, définissez le point de terminaison à -50 dB au lieu de -100 dB, ou à -200 dB pour exagérer l’occlusion. Vous pouvez adapter et affiner les courbes qui conviennent le mieux à votre jeu.
 
![Capture d’écran de l’éditeur de courbes d’occlusion Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Désactiver les courbes d’obstruction Wwise
Les courbes d’obstruction Wwise affectent le niveau de sécheresse dans l’isolation, mais Project Acoustics utilise des contrôles de conception et la simulation pour appliquer des ratios humide/sec. Nous vous recommandons de désactiver de la courbe de volume d’obstruction. Pour concevoir l’humidité, utilisez le contrôle Wetness Adjust décrit plus loin.
 
Si vous utilisez des courbes LPF/HPF d’obstruction à d’autres fins, vérifiez que vous les avez définies sur Y=0 à X=0 (autrement dit, il n’existe pas de filtre LPF ou HPF quand il n’y a pas d’obstruction).

![Capture d’écran de l’éditeur de courbes d’obstruction Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Concevoir les paramètres du mixer Project Acoustics
Vous pouvez contrôler les propriétés de réverbération globale en consultant l’onglet du plug-in Mixer du bus Project Acoustics. Double-cliquez sur « Project Acoustics Mixer (Custom) » pour ouvrir le panneau des paramètres du plug-in Mixer.

Vous pouvez également voir que le plug-in Mixer a une option « Perform Spatialization ». Si vous préférez utiliser la spatialisation intégrée de Project Acoustics, cochez la case « Perform Spatialization » et choisissez HRTF ou Panning. Veillez à désactiver les bus auxiliaires secs que vous avez configurés, sinon vous entendrez deux fois le chemin direct. Utilisez « Wetness Adjust » et « Reverb Time Scale Factor » pour exercer un contrôle global sur le mix de réverbération. Notez que vous devez redémarrer Unreal, puis regénérer les banques de sons avant d’atteindre Play pour choisir des modifications de la configuration du plug-in Mixer, comme « Perform Spatialization ».

![Capture d’écran des options du plug-in Mixer Wwise Project Acoustics](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Définir des contrôles de conception Project Acoustics dans la hiérarchie acteur-mixeur de Wwise
Pour contrôler les paramètres acteur-mixeur individuel, double-cliquez sur l’acteur-mixeur, puis cliquez sur son onglet Mixer Plug-in. Vous pouvez changer ici n’importe quel paramètre au niveau d’un son individuel. Ces valeurs sont combinées avec celles définies du côté de Unreal (décrits ci-dessous). Par exemple, si le plug-in Project Acoustics Unreal définit Outdoorness Adjustment sur un objet à 0,5, et que Wwise le définit à -0,25, le Outdoorness Adjustment résultant appliqué à ce son est de 0,25.

![Capture d’écran des paramètres de Mixer par son dans la hiérarchie des acteurs de Mixer Wwise](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Vérifiez que le bus auxiliaire a un envoi sec (dry) et que le bus de sortie a un envoi humide (wet).
N’oubliez pas que la configuration requise acteur-mixeur interchange le routage sec et humide habituel dans Wwise. Il produit un signal de réverbération sur le bus de sortie de l’acteur-mixeur (défini sur Project Acoustics Bus) et un signal sec sur le bus auxiliaire défini par l’utilisateur. Ce routage est nécessaire en raison des fonctionnalités de l’API du plug-in Wwise Mixer utilisées par le plug-in Project Acoustics Wwise.

![Capture d’écran de l’éditeur Wwise montrant des instructions de conception des voix pour Project Acoustics](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurer les courbes d’atténuation de distance
Vérifiez que les courbes d’atténuation utilisées par les acteurs-mixeurs avec Project Acoustics ont un envoi auxiliaire défini par l’utilisateur défini sur « output bus volume ». Wwise le fait par défaut pour les courbes d’atténuation nouvellement créées. Si vous migrez un projet existant, vérifiez les paramètres de vos courbes.

Par défaut, la simulation Project Acoustics a un rayon de 45 mètres autour de l’emplacement du joueur. Nous recommandons généralement de définir votre courbe d’atténuation à -200 dB autour de cette distance. Cette distance n’est pas une contrainte figée. Pour certains sons, comme des armes, vous pouvez souhaiter un rayon plus grand. Dans ce cas, l’inconvénient est que seule la géométrie située dans les 45 m de l’emplacement du joueur est prise en compte. Si le joueur est dans une pièce et que la source d’un son est en dehors de la salle à une distance de 100 m, il sera bloqué correctement. Si la source est dans une pièce et que le joueur est dehors à une distance de 100m, il ne sera pas bloqué correctement.

![Capture d’écran des courbes d’atténuation Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Égalisation postmixage ###
 Vous pouvez également ajouter un égaliseur postmixage. Vous pouvez considérer le bus Project Acoustics comme un bus de réverbération classique (en mode de réverbération par défaut), et lui appliquer un filtre pour effectuer l’égalisation. Vous pouvez en voir un échantillon dans l’exemple de projet Wwise Project Acoustics.

![Capture d’écran de l’égaliseur postmixage Wwise](media/wwise-post-mixer-eq.png)

Par exemple, un filtre passe-haut peut contribuer à gérer les basses des enregistrements en champ proche qui génèrent une réverbération puissante et irréaliste. Vous pouvez également affiner le contrôle postbake en modifiant l’égaliseur via les RTPC, ce qui vous permet de changer la couleur de la réverbération en cours de jeu.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Définir les propriétés de Project Acoustics à l’échelle de la scène

L’acteur Acoustics Space expose de nombreux contrôles qui modifient le comportement du système et qui sont pratiques lors du débogage.

![Capture d’écran de contrôles Unreal Acoustics Space](media/acoustics-space-controls.png)

* **Acoustics Data :** Une ressource acoustique avec baking du répertoire Content/Acoustics doit être affectée à ce champ. Le plug-in Project Acoustics ajoute automatiquement le répertoire Content/Acoustics aux répertoires packagés de votre projet.
* **Tile size :** Les étendues de la région autour de l’auditeur dont les données acoustiques doivent être chargées dans la RAM. Dès lors que les sondes d’auditeur immédiatement autour de l’acteur sont chargées, les résultats sont les mêmes que dans le cas du chargement des données acoustiques pour toutes les sondes. Des vignettes plus grandes utilisent davantage de RAM, mais réduisent les E-S disque
* **Auto Stream :** Quand cette option est activée, elle charge automatiquement les nouvelles vignettes quand l’auditeur atteint le bord d’une région chargée. Quand elle est désactivée, vous devez charger manuellement les nouvelles vignettes via du code ou des blueprints.
* **Cache Scale :** contrôle la taille du cache utilisé pour les requêtes acoustiques. Un cache plus petit utilise moins de RAM, mais peut augmenter l’utilisation du processeur pour chaque requête.
* **Acoustics Enabled :** Un contrôle de débogage qui permet une bascule A/B rapide de la simulation Project Acoustics. Ce contrôle est ignoré dans les configurations livrées. Le contrôle est pratique pour détecter si un bogue audio particulier provient des calculs acoustiques ou d’un autre problème dans le projet Wwise.
* **Update Distances :** Utilisez cette option si vous voulez utiliser les informations acoustiques avec baking préalable pour les requêtes de distance. Ces requêtes sont similaires aux raycastings, mais elles ont été précalculées et consomment donc beaucoup moins de CPU. Les réflexions discrètes sur la surface la plus proche de l’auditeur sont un exemple d’utilisation. Pour tirer pleinement parti de ceci, vous devez utiliser du code ou des blueprints pour interroger les distances.
* **Draw Stats :** Alors que le `stat Acoustics` de l’UE peut fournir des informations sur le CPU, cet affichage d’état montre le fichier ACE chargé, l’utilisation de la RAM et d’autres informations d’état dans le coin supérieur gauche de l’écran.
* **Draw Voxels :** Voxels de superposition proches de l’auditeur montrant la grille de voxels utilisée lors de l’interpolation à l’exécution. Si un émetteur est à l’intérieur d’un voxel lors de l’exécution, les requêtes acoustiques échouent.
* **Draw Probes :** Afficher toutes les sondes pour cette scène. Leurs couleurs diffèrent selon l’état de leur chargement.
* **Draw Distances :** Si Update Distances est activé, cette commande affiche une zone sur la surface la plus proche de l’auditeur dans des directions quantifiées autour de celui-ci.

## <a name="actor-specific-acoustics-design-controls"></a>Contrôles de conception acoustique spécifiques à l’acteur
Ces contrôles de conception sont limités à un composant audio individuel dans Unreal.

![Capture d’écran des contrôles de composant audio Unreal](media/audio-component-controls.png)

* **Occlusion Multiplier :** Contrôle l’effet d’occlusion. Les valeurs > 1 amplifient l’occlusion. Les valeurs < 1 la réduisent.
* **Wetness Adjustment :** dB de réverbération supplémentaires
* **Decay Time Multiplier :** Contrôle le RT60 de façon multiplicative, en fonction de la sortie de la simulation acoustique
* **Outdoorness Adjustment :** Contrôle l’extériorité de la réverbération. Les valeurs plus proches de 0 sont plus à l’intérieur, les valeurs plus proches de 1 sont plus extérieures. Cet ajustement est additif : une valeur définie sur -1 applique un facteur « intérieur », une valeur définie sur + 1 applique un facteur « extérieur ».
* **Transmission Db :** Rendre un son « à travers le mur » supplémentaire avec ce niveau sonore combiné avec la ligne de vue en fonction de l’atténuation de la distance.
* **Wet Ratio Distance Warp :** Ajuste les caractéristiques de la réverbération sur la source comme si elle était plus proche/lointaine, sans affecter le chemin direct.
* **Play on Start :** Activez ou désactivez cette option pour spécifier si le son doit être lu automatiquement au début de la scène. Option activée par défaut.
* **Show Acoustic Parameters :** Afficher les informations de débogage directement en haut du composant dans le jeu. (uniquement pour les configurations non livrées)

## <a name="blueprint-functionality"></a>Fonctionnalités des blueprints
L’acteur Acoustics Space est accessible via un blueprint, fournissant des fonctionnalités comme le chargement d’une carte ou la modification des paramètres via un script de niveau. Nous fournissons ici deux exemples.

### <a name="add-finer-grained-control-over-streaming-load"></a>Ajouter un contrôle plus fin sur le chargement en streaming
Pour gérer les données acoustiques de streaming vous-même au lieu du streaming automatique en fonction de la position du joueur, vous pouvez utiliser la fonction de blueprint Force Load Tile :

![Capture d’écran des options Blueprint Streaming dans Unreal](media/blueprint-streaming.png)

* **Target :** Acteur AcousticsSpace
* **Center Position :** Centre de la région dont les données doivent être chargées
* **Unload Probes Outside Tile :** Si cette option est activée, toutes les sondes qui ne sont pas dans la nouvelle région sont déchargées de la RAM. Si elle est désactivée, la nouvelle région est chargée dans la mémoire, tout en conservant les sondes existantes également chargées dans la mémoire.
* **Block on Completion :** Fait du chargement de la vignette une opération synchrone

La taille de la vignette doit être déjà définie avant d’appeler Force Load Tile. Par exemple, vous pouvez faire ceci pour charger un fichier ACE, définir la taille de la vignette et effectuer le streaming dans une région :

![Capture d’écran des options Streaming Setup dans Unreal](media/streaming-setup.png)

La fonction de plan Load Acoustics Data utilisée dans cet exemple présente les paramètres suivants :

* **Target :** L’acteur AcousticsSpace.
* **Nouveau bake :** La ressource de données acoustiques à charger. En laissant ce paramètre vide/ou en le définissant sur null, le bake actuel est déchargé sans en charger de n nouveau.

### <a name="optionally-query-for-surface-proximity"></a>Interroger (facultatif) la proximité de la surface
Si vous voulez voir à quelle distance les surfaces se trouvent dans une direction particulière autour de l’auditeur, vous pouvez utiliser la fonction Query Distance. Cette fonction peut être utile pour piloter des réflexions directionnelles retardées, ou pour toute autre logique de jeu pilotée par la proximité des surfaces. La requête consomme moins de ressources que le raycasting, car les résultats sont extraits de la table de recherche acoustique.

![Capture d’écran de l’exemple de requête de distance Blueprint](media/distance-query.png)

* **Target :** Acteur AcousticsSpace
* **Look Direction :** La direction dans laquelle interroger, centrée sur l’auditeur
* **Distance :** Si la requête réussit, la distance avec la surface la plus proche
* **Return Value :** Valeur booléenne : true si la requête a réussi ; sinon false

## <a name="next-steps"></a>Étapes suivantes
* Explorer les concepts qui sous-tendent le [processus de conception](design-process.md)
* [Créer un compte Azure](create-azure-account.md) pour effectuer le bake de votre propre scène


