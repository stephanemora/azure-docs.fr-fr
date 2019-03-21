---
title: Projet acoustique Unreal et l’intégration de Wwise
titlesuffix: Azure Cognitive Services
description: Cette procédure décrit l’intégration du projet acoustique Unreal et plug-ins Wwise dans votre projet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 19565ef239ba3ea1f791f80e4599a63b944c491b
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137865"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projet acoustique Unreal et l’intégration de Wwise
Cette procédure fournit les étapes d’intégration détaillées du package de plug-in acoustique de projet dans votre projet de jeu Unreal et Wwise existant. 

Configuration logicielle requise :
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1. +
* [Plug-in de Wwise pour Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Si vous utilisez une intégration directe du SDK Wwise au lieu d’utiliser les plug-ins Wwise Unreal, consultez le projet acoustique Unreal plug-in et ajuster les appels d’API de Wwise.

Si vous souhaitez utiliser acoustique de projet avec un moteur audio autre que Wwise, contactez-nous sur le [forums du projet acoustique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Vous pouvez utiliser le plug-in Project acoustique Unreal pour interroger les données acoustiques et puis effectuer des appels d’API à votre moteur.

## <a name="download-project-acoustics"></a>Télécharger le projet acoustique
Si vous n’êtes déjà fait, téléchargez le [package de plug-in de projet acoustique](https://www.microsoft.com/download/details.aspx?id=57346)). 

Nous avons inclus un plug-in Unreal Engine et un plug-in de mixer Wwise dans le package. Le plug-in Unreal assure l’intégration de l’éditeur et le runtime. Cours de jeu, le plug-in Project acoustique Unreal calcule les paramètres, tels qu’occlusion pour chaque objet de jeu chaque frame. Ces paramètres sont traduites en appels d’API de Wwise.

## <a name="review-integration-steps"></a>Passez en revue les étapes d’intégration

Voici les principales étapes pour installer le package et le déployer dans votre jeu.
1. Installer le plug-in de projet acoustique Wwise mixer
2. (Re) déployer Wwise à votre jeu. Cette étape propage le plug-in de mixer dans votre projet de jeu.
3. Ajouter le projet acoustique Unreal plug-in à votre jeu
4. Étendre les fonctionnalités de plug-in Unreal de Wwise
5. Générez des jeux et vérifiez que Python est activée
6. Configurer votre projet Wwise à utiliser le projet acoustique
7. Programme d’installation audio dans Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installer le plug-in de mixer acoustique de projet
* Ouvrez Wwise Lanceur, puis dans le **plug-ins** sous l’onglet sous **installer nouveaux plug-ins**, sélectionnez **ajouter depuis le répertoire**. 

    ![Installer le plug-in Wwise](media/wwise-install-new-plugin.png)

* Choisissez le `AcousticsWwisePlugin\ProjectAcoustics` répertoire qui a été inclus dans le package que vous avez téléchargé. Il contient le groupe de plug-in de mixer Wwise.

* Wwise installera le plug-in. Projet acoustique doit maintenant s’afficher dans la liste des plug-ins installés dans Wwise.
![Installation de plug-in U E intégration Post Mixer](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) déployer Wwise dans votre jeu
Redéployer Wwise à votre jeu même si vous avez déjà intégré Wwise. Ceci sélectionne le plug-in Wwise acoustique de projet.

* **Plug-in du moteur :** Si vous avez Wwise installé comme un plug-in de jeu dans un projet C++ Unreal, ignorez cette étape. S’il est installé à la place comme un plug-in du moteur, par exemple, car votre projet Unreal est Blueprint, Wwise déploiement uniquement avec nos plug-in de mixer est plus complexe. Créer un projet C++ Unreal factice, vide, fermez-la si Unreal éditeur s’ouvre, suivez la procédure restante pour déployer de Wwise dans ce projet factice. Copiez ensuite le plug-in Wwise déployé.
 
* Dans le Lanceur de Wwise, cliquez sur le **Unreal Engine** tabulation, puis cliquez sur le menu hamburger situé en regard **Recent Projects moteur Unreal** et sélectionnez **rechercher un projet**. Ouvrez le projet Unreal de votre jeu `.uproject` fichier.

    ![Onglet de Wwise Unreal](media/wwise-unreal-tab.png)

* Puis cliquez sur **Wwise d’intégrer dans le projet** ou **Wwise de modifier dans le projet**. Cette étape (re) intègre Wwise binaires dans votre projet, incluant désormais le plug-in de mixer acoustique du projet.

* **Plug-in du moteur :** Si vous utilisez Wwise comme un plug-in de moteur et créé le projet factice, comme indiqué ci-dessus, copiez le dossier Wwise déployé : `[DummyUProject]\Plugins\Wwise` et collez-la sur `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` est le chemin du projet C++ Unreal vide, et `[UESource]` est où vous avez les sources Unreal Engine installés. Une fois que vous avez terminé copie, vous pouvez supprimer le projet factice.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Ajouter le projet acoustique Unreal plug-in à votre jeu
 
* Copie le `Unreal\ProjectAcoustics` dossier du plug-in de package et créer un nouveau dossier `[UProjectDir]\Plugins\ProjectAcoustics`, où `UProjectDir` est le dossier de projet de votre jeu contenant le `.uproject` fichier.
  * **Plug-in de moteur**: Si vous utilisez Wwise comme un plug-in du moteur, vous devez utiliser le projet acoustique comme un plug-in Unreal engine. Au lieu du répertoire de destination ci-dessus, utilisez : `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Vérifiez une `Wwise` dossier en même temps que le `ProjectAcoustics` dossier. Il contient le plug-in de Wwise, ainsi que les fichiers binaires pour le plug-in de mixer vous (re-) déployé à l’étape 2 ci-dessus.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Étendre les fonctionnalités de plug-in Unreal de Wwise
* Le plug-in Project acoustique Unreal exige un comportement supplémentaire exposée à partir du plug-in Wwise Unreal API par [ces instructions](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Nous avons inclus un fichier de commandes pour automatiser la procédure de mise à jour corrective. 
* À l’intérieur de `Plugins\ProjectAcoustics\Resources`, exécutez `PatchWwise.bat`. L’image de l’exemple ci-dessous utilise notre exemple de projet AcousticsGame.

    ![Correctif Wwise Script](media/patch-wwise-script.png)

* Si vous n’avez pas installé le SDK DirectX, vous devrez Commentez la ligne contenant DXSDK_DIR dans `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Mettez en commentaire le DXSDK](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Générez des jeux et vérifiez que Python est activée

* Compilez votre jeu et vous assurer qu’il se génère correctement. Sinon, vérifiez les étapes précédentes avec soin avant de continuer. 
* Ouvrez votre projet dans l’éditeur Unreal. 
* **Plug-in du moteur :** Si vous utilisez ProjectAcoustics en tant que plug-in du moteur, également vous assurer qu’elle est activée, répertorié sous « intégrées » plug-ins.
* Vous devriez voir un nouveau mode, qui indique le que projet acoustique a été intégré.

    ![Intégral de modèle acoustique](media/acoustics-mode-full.png)

* Vérifiez le plug-in de Python pour Unreal activé. Cela est nécessaire pour l’intégration de l’éditeur fonctionner correctement.

    ![Assurez-vous de Python](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Programme d’installation de Wwise projet

Un exemple de projet Wwise est inclus dans le téléchargement d’exemples. Nous vous recommandons d’envisager en même temps que ces instructions. Les captures d’écran ci-dessous sont tirés de ce projet.

### <a name="bus-setup"></a>Programme d’installation de bus
* Le plug-in Project acoustique Unreal recherchera le plug-in de mixer associé sur un bus avec ce ***exacte*** nom : `Project Acoustics Bus`. Créer un nouveau bus audio portant ce nom. Le plug-in de mixer peut fonctionner dans différentes configurations, mais pour l’instant nous partons du principe qu'il servira à réverbération uniquement de traitement. Ce bus contiendra le signal de réverbération mixte pour toutes les sources qui utilisent acoustique. Il peut combiner en amont dans n’importe quel bus mélange de structure, Voici un exemple ci-dessous, prises à partir de notre exemple de projet Wwise inclus dans le téléchargement d’exemple.

    ![Bus d’acoustique](media/acoustics-bus.png)

* La configuration de canal sur le bus doit être définie à une des : `1.0, 2.0, 4.0, 5.1 or 7.1`. Autres configurations entraînera aucune sortie sur ce bus.

    ![Bus d’acoustique](media/acoustics-bus-channel-config.png)

* Accédez maintenant dans le projet acoustique bus détails et assurez-vous de que vous pouvez voir l’onglet de plug-in de Mixer

    ![Bus d’acoustique](media/mixer-tab-enable.png)

* Accédez à l’onglet de plug-in de Mixer, puis ajouter le mélangeur d’acoustique projet plug-in sur le bus

    ![Ajouter des plug-in de Mixer](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Paramétrage de la hiérarchie mixer d’acteur
* Pour des raisons de performances, acoustique de projet s’applique audio DSP à toutes les sources simultanément. Cela nécessite le plug-in de fonctionner comme un plug-in de mixer. Wwise nécessite des plug-ins de mixer doivent se trouver sur le bus de sortie, même si le bus de sortie porte généralement le signal de sortie sec. Projet acoustique nécessite le signal dry être acheminé via aux bus pendant que le signal mouillé est effectuée le `Project Acoustics Bus`. Le processus suivant prend en charge la migration progressive vers ce flux de signal.

* Par exemple, vous avez un projet existant avec une hiérarchie de mixer d’acteur contenant voudraient, armes et autres au niveau supérieur. Chacun possède des bus de sortie correspondant pour sa combinaison sec. Permet de dire que vous souhaitez migrer voudraient pour utiliser acoustique. Tout d’abord créer un bus aux correspondant pour effectuer leur dry mixage secondaire qui est un enfant du bus de sortie voudraient. Par exemple, nous avons utilisé un préfixe « Dry » dans l’image ci-dessous pour organiser ces, bien que le nom exact n’est pas important. N’importe quel mètres ou les effets que vous aviez sur le bus voudraient continueront de fonctionner comme avant.

    ![Programme d’installation de Wwise sec combinaison](media/wwise-dry-mix-setup.png)

* Puis modifiez la structure de sortie de bus pour l’acteur voudraient-mixer comme suit, avec projet acoustique Bus définir en tant que sortie Bus et Dry_Footsteps défini en tant qu’un bus aux défini par l’utilisateur.

    ![Programme d’installation de Wwise acteur Mixer Bus](media/actor-mixer-bus-settings.png)

* Maintenant voudraient tous les reçoivent un traitement acoustique et de sortie leur réverbération sur le Bus d’acoustique du projet. Le signal sec est acheminé via Dry_Footsteps et spatialized comme d’habitude.

* Projet acoustique s’applique uniquement aux sons qui ont un emplacement 3D dans le monde entier. Suivant [Wwise documentation](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), les propriétés de positionnement doivent être définie comme indiqué. Le paramètre « spatialisation 3D » peut être « Position » ou « Position + Orientation » en fonction des besoins.

    ![Paramètres de positionnement Wwise acteur](media/wwise-positioning.png)

* Définition du Bus de sortie à certains autres bus combine en amont dans **projet acoustique Bus** ne fonctionnera pas. Wwise impose cette exigence de plug-ins de mixer.

* Si vous souhaitez un enfant dans la hiérarchie de mixer d’acteur voudraient à ne pas utiliser acoustique, vous pouvez toujours utiliser le « parent les substitutions » sur ce dernier d’annuler l’abonnement il.

* Si vous utilisez des envois de jeu - ou définis par l’utilisateur pour la réverbération sur n’importe quel acteur-mixer dans le jeu, désactivez ceux sur cet acteur-mixer d’éviter d’appliquer réverbération à deux reprises.

### <a name="spatialization"></a>Spatialisation
Par défaut, le plug-in de mixer projet acoustique Wwise applique réverbération convolution, en laissant Wwise pour faire spatialisation panoramique. Lorsque vous utilisez acoustique de projet dans cette configuration par défaut de réverbération uniquement, vous êtes libre d’utiliser n’importe quelle méthode de configuration et spatialisation de canal sur votre combinaison dry, ce qui vous permet de combiner presque n’importe quel spatial avec effet de reverb acoustique de le projet. Les options disponibles sont [spatializers de BINAURAUX basée sur les Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) et [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projet acoustique inclut un spatial facultatif qui prend en charge le rendu basé sur les objets d’une haute résolution HRTF et panoramique. Cochez la case « Effectuer spatialisation » sur les paramètres de plug-in de mixer et choisir entre HRTF ou Panoramique et désactiver les envoie aux défini par l’utilisateur configuré ci-dessus pour tous les bus dry afin d’éviter spatializing à deux reprises, à la fois avec le plug-in de projet acoustique mixer et Wwise. Impossible de modifier le mode spatialisation en temps réel, car elle nécessite une régénération de la banque de son. Vous devez redémarrer Unreal, puis régénérer les banques de sons avant d’atteindre play pour récupérer les modifications de configuration de plug-in de mixer telles que la case à cocher « Effectuer spatialisation ».

![Paramètres de spatialisation Mixer plug-in](media/mixer-spatial-settings.png)

Malheureusement, autres plug-ins spatial basé sur l’objet ne peut pas être pris en charge pour l’instant car ils sont implémentés en tant que plug-ins de mixer et Wwise n’autorise actuellement pas plusieurs plug-ins de mixer affectés à un seul acteur-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Programme d’installation audio dans Unreal
* Vous devez d’abord intégrer votre niveau de jeu pour produire un élément multimédia acoustique, qui sera placé dans `Content\Acoustics`. Consultez le [Unreal didacticiel gâteaux](unreal-baking.md) et reprendre ici. Certains niveaux préalablement créées est inclus dans l’exemple de package.
* Créer un acteur acoustique espace dans votre scène. Créer un seul de ces acteurs dans un niveau car il représente l’acoustique pour le niveau entier. 

    ![L’espace acoustique](media/create-acoustics-space.png)

* La ressource de données acoustiques cuite attribue à l’emplacement de données d’acoustique sur l’acteur acoustique espace. Votre scène a maintenant acoustique !

    ![Attribuer d’immobilisation acoustique](media/acoustics-asset-assign.png)

* Maintenant, ajoutez un acteur vide et procédez comme suit :

    ![Utilisation du composant acoustique](media/acoustics-component-usage.png)

1. Ajouter un composant acoustique Audio à l’acteur. Ce composant étend le composant audio Wwise avec des fonctionnalités pour acoustique du projet.
2. La zone de lecture sur Start est activée par défaut, ce qui déclenchera l’événement Wwise associé au démarrage de niveau.
3. La case à cocher Afficher les paramètres d’acoustique permet d’imprimer à l’écran informations sur la source de débogage.
    ![Déboguer des valeurs](media/debug-values.png)
4. Affecter un événement Wwise par le flux de travail habituel Wwise
5. Vérifiez qu’utiliser du son Spatial est désactivé. À ce stade, si vous utilisez acoustique de projet pour un composant audio particulier, vous ne peut pas utiliser simultanément moteur Spatial Audio de Wwise pour acoustique.

Vous êtes prêt. Déplacer la scène et Explorer les effets acoustiques !

## <a name="next-steps"></a>Étapes suivantes
* [Conception](unreal-workflow.md) didacticiel pour acoustique de projet dans Unreal/Wwise
* [Apprenez à effectuer intègre](unreal-baking.md) pour votre jeu scènes 
