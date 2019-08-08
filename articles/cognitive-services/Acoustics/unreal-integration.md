---
title: Intégration de Project Acoustics Unreal et Wwise
titlesuffix: Azure Cognitive Services
description: Cette procédure décrit l’intégration des plug-ins Project Acoustics Unreal et Wwise dans votre projet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706620"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Intégration de Project Acoustics Unreal et Wwise
Cette procédure indique les étapes d’intégration détaillées du package de plug-in Project Acoustics dans votre projet de jeu Unreal et Wwise existant. 

Configuration logicielle requise :
* [Unreal Engine](https://www.unrealengine.com/) 4.20 ou 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Plug-in Wwise pour Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Si vous utilisez une intégration directe du SDK Wwise au lieu d’utiliser les plug-ins Wwise Unreal, consultez le plug-in Project Acoustics Unreal et ajustez les appels de l’API Wwise.

Si vous souhaitez utiliser Project Acoustics avec un moteur audio autre que Wwise, faites une requête d’amélioration sur le [forum de discussion Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Vous pouvez utiliser le plug-in Project Acoustics Unreal pour interroger les données acoustiques et effectuer des appels d’API à votre moteur.

## <a name="download-project-acoustics"></a>Télécharger Project Acoustics
Si ce n’est déjà fait, téléchargez le [package de plug-in Project Acoustics Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Nous avons inclus un plug-in Unreal Engine et un plug-in de mixer Wwise dans le package. Le plug-in Unreal assure l’intégration de l’éditeur et du runtime. Au cours du jeu, le plug-in Project Acoustics Unreal calcule les paramètres, comme l’occlusion pour chaque image d’objet de jeu. Ces paramètres sont traduits en appels de l’API Wwise.

## <a name="review-integration-steps"></a>Passer en revue les étapes d'intégration

Voici les principales étapes pour installer le package et le déployer dans votre jeu.
1. Installer le plug-in de mixer Project Acoustics Wwise
2. (Re)déployez Wwise dans votre jeu. Cette étape propage le plug-in de mixer dans votre projet de jeu.
3. Ajouter le plug-in Project Acoustics Unreal à votre jeu
4. Étendre les fonctionnalités du plug-in Unreal de Wwise
5. Générer le jeu et vérifie que Python est activé
6. Configurer votre projet Wwise pour utiliser Project Acoustics
7. Configuration audio dans Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installer le plug-in de mixer Project Acoustics
* Ouvrez Wwise Launcher puis, dans l’onglet **Plugins** sous **Install New Plugins**, sélectionnez **Add From Directory**. 

    ![Capture d’écran de l’installation d’un plug-in dans Wwise Launcher](media/wwise-install-new-plugin.png)

* Choisissez le répertoire `AcousticsWwisePlugin\ProjectAcoustics` inclus dans le package que vous avez téléchargé. Il contient le bundle du plug-in de mixer Wwise.

* Wwise installe le plug-in. Project Acoustics doit maintenant s’afficher dans la liste des plug-ins installés dans Wwise.
![Capture d’écran de la liste des plug-ins installés dans Wwise après l’installation de Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re)déployer Wwise dans votre jeu
Redéployez Wwise dans votre jeu même si vous avez déjà intégré Wwise. Ceci sélectionne le plug-in Project Acoustics Wwise.

* **Plug-in de moteur :** Si vous avez installé Wwise comme plug-in de jeu dans un projet Unreal C++, ignorez cette étape. Mais s’il est installé comme plug-in de moteur, si votre projet Unreal est Blueprint uniquement par exemple, le déploiement de Wwise avec notre plug-in de mixer est plus complexe. Créer un projet Unreal C++ factice et vide, fermez-le si l’éditeur Unreal s’ouvre et suivez la procédure restante pour déployer Wwise dans ce projet factice. Copiez ensuite le plug-in Wwise déployé.
 
* Depuis Wwise Launcher, cliquez sur l’onglet **Unreal Engine**, puis cliquez sur le menu hamburger en regard de **Recent Unreal Engine Projects** et sélectionnez **Browse for project**. Ouvrez le fichier `.uproject` de projet Unreal de votre jeu.

    ![Capture d’écran de l’onglet Unreal dans Wwise Launcher](media/wwise-unreal-tab.png)

* Puis cliquez sur **Intégrer Wwise dans le projet** ou **Modifier Wwise dans le projet**. Cette étape (ré)intègre les binaires Wwise dans votre projet, incluant désormais le plug-in de mixer Project Acoustics.

* **Plug-in de moteur :** Si vous utilisez Wwise comme plug-in de moteur et que vous avez créé le projet factice comme indiqué ci-dessus, copiez le dossier Wwise déployé : `[DummyUProject]\Plugins\Wwise` et collez-le dans `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` est le chemin du projet Unreal C++ vide, et `[UESource]` est l’emplacement d’installation des sources Unreal Engine. Une fois la copie terminée, vous pouvez supprimer le projet factice.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Ajouter le plug-in Project Acoustics Unreal à votre jeu
 
* Copiez le dossier `Unreal\ProjectAcoustics`dans le package du plug-in et créez un nouveau dossier `[UProjectDir]\Plugins\ProjectAcoustics`, où `UProjectDir` est le dossier de projet de votre jeu contenant le fichier `.uproject`.
  * **Plug-in de moteur** : Si vous utilisez Wwise comme plug-in de moteur, vous devez utiliser Project Acoustics comme plug-in de moteur Unreal également. Au lieu du répertoire de destination ci-dessus, utilisez : `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Vérifiez que le dossier `Wwise` apparaît aux côtés du dossier `ProjectAcoustics`. Il contient le plug-in Wwise, ainsi que les binaires pour le plug-in de mixer que vous avez (re)déployé à l’étape 2 ci-dessus.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Étendre les fonctionnalités du plug-in Unreal de Wwise
* Le plug-in Project Acoustics Unreal nécessite l’exposition d’un comportement supplémentaire à partir de l’API du plug-in Unreal Wwise conformément à [ces instructions](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Nous avons inclus un fichier batch pour automatiser la procédure de mise à jour corrective. 
* Dans `Plugins\ProjectAcoustics\Resources`, exécutez `PatchWwise.bat`. L’exemple d’image ci-dessous utilise notre exemple de projet AcousticsGame.

    ![Capture d’écran de l’Explorateur Windows montrant le script fourni permettant de corriger Wwise](media/patch-wwise-script.png)

* Si le SDK DirectX n’est pas installé sur votre machine, vous devez mettre en commentaire la ligne contenant DXSDK_DIR dans `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Capture d’écran de l’éditeur de code montrant DXSDK avec des commentaires](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Générer le jeu et vérifie que Python est activé

* Compilez votre jeu et assurez-vous qu’il se génère correctement. Sinon, vérifiez les étapes précédentes avec soin avant de continuer. 
* Ouvrez votre projet dans Unreal Editor. 
* **Plug-in de moteur :** Si vous utilisez ProjectAcoustics en tant que plug-in de moteur, vérifiez également qu’il est activé, répertorié sous les plug-ins « intégrés ».
* Vous devriez voir un nouveau mode, qui indique que Project Acoustics a été intégré.

    ![Capture d’écran de Unreal montrant Acoustics Mode Full](media/acoustics-mode-full.png)

* Confirmez que le plug-in Python est activé pour Unreal. Cela est nécessaire pour que l’intégration de l’éditeur fonctionne correctement.

    ![Capture d’écran de l’activation des extensions Python dans l’éditeur Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Configuration de projet Wwise

Un exemple de projet Wwise est inclus dans le téléchargement d’exemples. Nous vous recommandons de le consulter en même temps que ces instructions. Les captures d’écran ci-dessous sont tirées de ce projet.

### <a name="bus-setup"></a>Configuration du bus
* Le plug-in Project Acoustics Unreal recherche le plug-in de mixer associé sur un bus avec ce ***nom*** exact : `Project Acoustics Bus`. Créez un nouveau bus audio portant ce nom. Le plug-in de mixer peut fonctionner dans différentes configurations, mais pour l’instant nous partons du principe qu'il servira à uniquement à des fins de traitement de la réverbération. Ce bus transporte le signal de réverbération mélangée pour toutes les sources qui utilisent Acoustics. Il peut mixer en amont dans n’importe quelle structure de mixage de bus. Vous trouverez un exemple ci-dessous, issu de notre exemple de projet Wwise inclus dans l’exemple téléchargé.

    ![Capture d’écran des bus Wwise affichant le bus Project Acoustics](media/acoustics-bus.png)

* La configuration du canal sur le bus doit être définie sur l’une des valeurs : `1.0, 2.0, 4.0, 5.1 or 7.1`. Aucune sortie sur ce bus n’aura lieu avec d’autres configurations.

    ![Capture d’écran des options de configuration de canal pour le bus Project Acoustics](media/acoustics-bus-channel-config.png)

* Accédez maintenant aux détails du bus Project Acoustics et vérifiez que vous pouvez voir l’onglet Plug-in de mixer

    ![Capture d’écran de Wwise montrant comment activer l’onglet Plug-in de mixer pour le bus Project Acoustics](media/mixer-tab-enable.png)

* Accédez à l’onglet Plug-in de mixer, puis ajoutez au bus le plug-in de mixer Project Acoustics

    ![Capture d’écran de Wwise montrant comment ajouter le plug-in de mixer Project Acoustics](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Configuration de la hiérarchie acteur-mixer
* Pour des raisons de performances, Project Acoustics applique le DSP audio à toutes les sources simultanément. Le plug-in est requis pour fonctionner comme plug-in de mixer. Wwise nécessite que les plug-ins de mixer se trouvent sur le bus de sortie, même si le bus de sortie transporte généralement le signal de sortie sec. Project Acoustics nécessite que le signal sec soit acheminé via des bus auxiliaires alors que le signal humide est transporté sur le `Project Acoustics Bus`. Le processus suivant prend en charge la migration progressive vers ce flux de signal.

* Si, par exemple, vous avez un projet existant avec une hiérarchie acteur-mixer contenant Footsteps, Weapons et autres au niveau supérieur. Chacun a un bus de sortie correspondant pour son dry mix. Disons que vous voulez migrer Footsteps pour utiliser Acoustics. Tout d’abord, créez un bus auxiliaire correspondant pour transporter le dry submix qui est un enfant du bus de sortie Footsteps. Par exemple, nous avons utilisé un préfixe « Dry » dans l’image ci-dessous pour les organiser, bien que le nom exact ne soit pas important. Les compteurs ou effets que vous aviez sur le bus Footsteps continueront de fonctionner comme avant.

    ![Capture d’écran de la configuration combinaison Dry Mix Wwise recommandée](media/wwise-dry-mix-setup.png)

* Puis modifiez la structure de sortie de bus pour la hiérarchie acteur-mixer Footsteps comme suit, avec le bus Project Acoustics défini en tant que Bus de sortie et Dry_Footsteps défini en tant que bus auxiliaire défini par l’utilisateur.

    ![Capture d’écran de la configuration de bus acteur-mixer Wwise recommandée](media/actor-mixer-bus-settings.png)

* Maintenant, l’ensemble des footsteps bénéficient d’un traitement acoustique et effectuent la sortie de leur réverbération sur le bus Project Acoustics. Le signal sec est acheminé via Dry_Footsteps et spatialisé comme d’habitude.

* Project Acoustics s’applique uniquement aux sons avec un emplacement 3D dans le monde. Selon la [documentation Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), les propriétés de positionnement doivent être définies comme indiqué. Le paramètre « 3D Spatialization » peut être « Position » ou « Position + Orientation » en fonction des besoins.

    ![Capture d’écran des paramètres recommandés de positionnement d’acteur Wwise](media/wwise-positioning.png)

* La définition du bus de sortie sur un autre bus qui mixe en amont dans le **bus Project Acoustics** ne fonctionne pas. Wwise impose cette exigence pour les plug-ins de mixer.

* Si vous souhaitez qu’un enfant dans la hiérarchie acteur-mixer Footsteps n’utilise pas l’acoustique, vous pouvez toujours utiliser « override parent » (remplacer le parent) pour l’annuler.

* Si vous utilisez des envois définis par le jeu ou l’utilisateur pour la réverbération sur n’importe quel acteur-mixer dans le jeu, désactivez-les sur cet acteur-mixer pour éviter d’appliquer réverbération à deux reprises.

### <a name="spatialization"></a>Spatialisation
Par défaut, le plug-in de mixer Project Acoustics Wwise applique une réverbération de convolution, laissant à Wwise le soin d’effectuer la spatialisation panoramique. Lorsque vous utilisez Project Acoustics dans cette configuration de réverbération uniquement par défaut, vous êtes libre d’utiliser n’importe quelle méthode de configuration et de spatialisation de canal sur votre dry mix, ce qui vous permet d’associer presque n’importe quel outil de spatialisation à la réverbération de Project Acoustics. Les options disponibles sont [Ambisonics-based binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) (Outils de spatialisation de type binaural basés sur Ambisonics) et [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics inclut un outil de spatialisation facultatif qui prend en charge le panoramique et le rendu HRTF haute résolution basé sur objet. Cochez la case « Perform Spatialization » (Effectuer la spatialisation) dans les paramètres du plug-in de mixer et choisissez entre HRTF ou Panning (Panoramique) et désactivez les envois auxiliaires définis par l’utilisateur configurés ci-dessus vers tous les bus dry afin d’éviter un doublon de la spatialisation, avec le plug-in de mixer Project Acoustics et Wwise. Il est impossible de modifier le mode spatialisation en temps réel, car une régénération de la banque de sons est nécessaire. Vous devez redémarrer Unreal, puis regénérer les banques de sons avant d’atteindre Play pour choisir des modifications de la configuration du plug-in Mixer, comme « Perform Spatialization ».

![Capture d’écran des paramètres de spatialisation du plug-in de mixer Wwise](media/mixer-spatial-settings.png)

Malheureusement, les autres plug-ins de spatialisation basés sur objet ne sont pas pris en charge pour l’instant, car ils sont implémentés en tant que plug-ins de mixer et Wwise n’autorise actuellement pas l’attribution de plusieurs plug-ins de mixer à un seul acteur-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Configuration audio dans Unreal
* Vous devez d’abord effectuer le baking de votre niveau de jeu pour produire une ressource acoustique, qui sera placée dans `Content\Acoustics`. Consultez le [tutoriel Unreal Bake](unreal-baking.md) et revenez ici. Certains niveaux pré-baked sont inclus dans l’exemple de package.
* Créez un acteur Acoustics Space dans votre scène. Créez un seul de ces acteurs dans un niveau, car il représente l’acoustique pour le niveau entier. 

    ![Capture d’écran de Unreal Editor montrant la création de l’acteur Acoustics Space](media/create-acoustics-space.png)

* Maintenant, attribuez la ressource de données d’acoustique baked à l’emplacement Données d’acoustique sur l’acteur Acoustics Space. Votre scène est maintenant dotée de l’acoustique !

    ![Capture d’écran de Unreal Editor montrant l’attribution de la ressource acoustique](media/acoustics-asset-assign.png)

* Maintenant, ajoutez un acteur vide et procédez comme suit :

    ![Capture d’écran de Unreal Editor montrant l’utilisation du Composant acoustique dans un acteur vide](media/acoustics-component-usage.png)

1. Ajoutez un composant Acoustics Audio à l’acteur. Ce composant étend le composant audio Wwise avec des fonctionnalités pour Project Acoustics.
2. La case Play on Start (Lire au démarrage) est cochée par défaut, ce qui déclenche l’événement Wwise associé au démarrage du niveau.
3. Utilisez la case à cocher Show Acoustics Parameters (Afficher les paramètres d’acoustique) pour imprimer les informations de débogage à l’écran sur la source.
    ![Capture d’écran de Unreal Editor, panneau Acoustics, sur la source de son avec les valeurs de débogage activées](media/debug-values.png)
4. Affecter un événement Wwise par le flux de travail Wwise habituel
5. Vérifiez que l’option Use Spatial Audio (Utiliser le son spatial) est désactivée. À ce stade, si vous utilisez Project Acoustics pour un composant audio particulier, vous ne peut pas utiliser simultanément le moteur Spatial Audio de Wwise pour l’acoustique.

Vous êtes prêt. Explorez la scène et les effets acoustiques !

## <a name="next-steps"></a>Étapes suivantes
* Tutoriel de [conception](unreal-workflow.md) pour Project Acoustics dans Unreal/Wwise
* [Apprenez à effectuer le baking](unreal-baking.md) pour les scènes de votre jeu 
