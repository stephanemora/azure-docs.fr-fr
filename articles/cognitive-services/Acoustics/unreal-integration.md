---
title: Intégration de Project Acoustics Unreal et Wwise
titlesuffix: Azure Cognitive Services
description: Cet article décrit l’intégration des plug-ins Project Acoustics Unreal et Wwise dans votre projet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243062"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Intégration de Project Acoustics Unreal et Wwise
Cet article explique comment intégrer le package de plug-in Project Acoustics dans votre projet de jeu Unreal et Wwise existant.

Configuration logicielle requise :
* [Unreal Engine](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Plug-in Wwise pour Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Si vous utilisez une intégration directe du SDK Wwise au lieu du plug-in Wwise Unreal, consultez le plug-in Project Acoustics Unreal et ajustez les appels de l’API Wwise.

Pour utiliser Project Acoustics avec un moteur audio autre que Wwise, faites une requête d’amélioration sur le [forum de discussion Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Vous pouvez utiliser le plug-in Project Acoustics Unreal pour interroger les données acoustiques et effectuer des appels d’API à votre moteur.

## <a name="download-project-acoustics"></a>Télécharger Project Acoustics
Si ce n’est déjà fait, téléchargez le [package de plug-in Project Acoustics Unreal et Wwise](https://www.microsoft.com/download/details.aspx?id=58090).

Un plug-in de moteur Unreal et un plug-in de mélangeur Wwise sont inclus dans le package. Le plug-in Unreal assure l’intégration de l’éditeur et du runtime. Au cours du jeu, le plug-in Project Acoustics Unreal calcule les paramètres, comme l’occlusion pour chaque image d’objet de jeu. Ces paramètres sont traduits en appels de l’API Wwise.

## <a name="integration-steps"></a>Étapes d'intégration

Procédez comme suit pour installer le package et le déployer dans votre jeu.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Installer le plug-in de mélangeur Project Acoustics
1. Ouvrez Wwise Launcher. Sous l’onglet **Plug-ins** sous **Install New Plugins**, sélectionnez **Add From Directory**.

    ![Installer un plug-in dans Wwise Launcher](media/wwise-install-new-plugin.png)

1. Sélectionnez le répertoire *AcousticsWwisePlugin\ProjectAcoustics* qui figure dans le package de téléchargement. Il contient le bundle du plug-in de mélangeur Wwise.

   Wwise installe le plug-in. Project Acoustics doit s’afficher dans la liste des plug-ins installés dans Wwise.  
![Liste des plug-ins installés dans Wwise après l’installation de Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Redéployer Wwise dans votre jeu
Redéployez Wwise dans votre jeu même si vous avez déjà intégré Wwise. Cette étape intègre le plug-in Project Acoustics Wwise.

   > [!NOTE]
   > **Plug-in de moteur :** Si vous avez installé Wwise comme plug-in de jeu dans un projet Unreal C++, ignorez cette étape. En revanche, s’il est installé comme plug-in de moteur, parce que votre projet Unreal est Blueprint uniquement par exemple, le déploiement de Wwise avec notre plug-in de mélangeur est plus complexe. Créez un projet Unreal C++ vide factice. Fermez Unreal Editor s’il s’ouvre et suivez la procédure restante pour déployer Wwise dans le projet factice. Copiez ensuite le plug-in Wwise déployé.
 
1. Dans Wwise Launcher, sélectionnez l’onglet **Unreal Engine**. Sélectionnez le menu (icône) « Hamburger » en regard de **Recent Unreal Engine Projects**, puis sélectionnez **Browse for project**. Ouvrez le fichier *.project* de projet Unreal de votre jeu.

    ![Onglet Unreal Engine dans Wwise Launcher](media/wwise-unreal-tab.png)

1. Sélectionnez **Integrate Wwise in Project** ou **Modify Wwise in Project**. Cette étape intègre les binaires Wwise dans votre projet, notamment le plug-in de mélangeur Project Acoustics.

   > [!NOTE]
   > **Plug-in de moteur :** Si vous utilisez Wwise en tant que plug-in de moteur et que vous avez créé le projet factice comme décrit précédemment, copiez le dossier déployé par Wwise : *[DummyUProject]\Plugins\Wwise*. Collez-le sur *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* est le chemin du projet Unreal C++ vide, et *[UESource]* est l’emplacement d’installation des sources du moteur Unreal. Après avoir copié le dossier, vous pouvez supprimer le projet factice.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Ajouter le plug-in Project Acoustics Unreal à votre jeu
 
1. Copiez le dossier *Unreal\ProjectAcoustics* dans le package de plug-in. Créez un dossier *[UProjectDir]\Plugins\ProjectAcoustics*, où *[UProjectDir]* est le dossier du projet de votre jeu qui contient le fichier *.uproject*.

   > [!NOTE]
   > **Plug-in de moteur** : Si vous utilisez Wwise comme plug-in de moteur, vous devez également utiliser Project Acoustics comme plug-in de moteur Unreal. Au lieu du répertoire de destination mentionné précédemment, utilisez *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Vérifiez que vous voyez un dossier *Wwise* avec le dossier *ProjectAcoustics*. Il contient le plug-in Wwise ainsi que les binaires pour le plug-in de mélangeur que vous avez déployé précédemment.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Étendre les fonctionnalités du plug-in Wwise Unreal
Le plug-in Project Acoustics Unreal nécessite l’exposition d’un comportement supplémentaire à partir de l’API du plug-in Wwise Unreal conformément à [ces instructions](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Nous avons inclus un fichier batch pour automatiser la procédure de mise à jour corrective.

* Dans *Plugins\ProjectAcoustics\Resources*, exécutez *PatchWwise.bat*. L’exemple d’image suivant utilise notre exemple de projet AcousticsGame.

    ![Fenêtre de l’Explorateur Windows avec le script pour corriger le projet Wwise mis en surbrillance](media/patch-wwise-script.png)

* Si vous n’avez pas installé le kit SDK DirectX : En fonction de la version de Wwise que vous utilisez, vous devrez peut-être ajouter un commentaire sur la ligne qui contient `DXSDK_DIR` dans *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* :

    ![Éditeur de code montrant « DXSDK » avec des commentaires](media/directx-sdk-comment.png)

* Si vous compilez à l’aide de Visual Studio 2019 : Pour contourner une erreur de liaison avec Wwise, remplacez manuellement la valeur par défaut `VSVersion` dans *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* par **vc150** :

    ![Éditeur de code montrant « VSVersion » remplacé par « vc150 »](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Générer le jeu et vérifier que Python est activé

1. Compilez votre jeu et assurez-vous qu’il se génère correctement. Si ce n’est pas le cas, vérifiez les étapes précédentes avec soin avant de continuer.

1. Ouvrez votre projet dans Unreal Editor.

    > [!NOTE]
    > **Plug-in de moteur :** Si vous utilisez ProjectAcoustics en tant que plug-in de moteur, vérifiez également qu’il est activé sous les plug-ins « intégrés ».

    Vous devriez voir un nouveau mode, qui indique que Project Acoustics a été intégré.

    ![Mode Acoustics complet dans Unreal](media/acoustics-mode-full.png)

1. Vérifiez que le plug-in Python pour Unreal est activé afin que l’intégration de l’éditeur fonctionne correctement.

    ![Extensions Python activées dans Unreal Editor](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Configurer votre projet Wwise pour utiliser Project Acoustics

Un exemple de projet Wwise est inclus dans le téléchargement d’exemples. Nous vous recommandons de l’afficher avec ces instructions. Les captures d’écran plus loin dans cet article sont issues de ce projet.

#### <a name="bus-setup"></a>Configuration du bus
Le plug-in Project Acoustics Unreal recherche le plug-in de mélangeur associé sur un bus portant le nom exact `Project Acoustics Bus`. Créez un bus audio qui porte le même nom. Le plug-in de mélangeur peut fonctionner dans différentes configurations. Toutefois, pour le moment, nous partons du principe qu’il sera utilisé uniquement à des fins de traitement de la réverbération. Ce bus transporte le signal de réverbération mélangée pour toutes les sources qui utilisent Acoustics. Il peut mixer en amont dans n’importe quelle structure de mixage de bus. Vous trouverez un exemple ci-dessous, issu de l’exemple de projet Wwise inclus dans l’exemple téléchargé.

![Bus Wwise affichant le bus Project Acoustics](media/acoustics-bus.png)

1. Affectez à la configuration de canal sur le bus la valeur *1.0*, *2.0*, *4.0*, *5.1* ou *7.1*. Tout autre paramètre n’entraîne aucune sortie sur le bus.

    ![Options de configuration de canal pour le bus Project Acoustics](media/acoustics-bus-channel-config.png)

1. Accédez aux détails du bus Project Acoustics et vérifiez que vous pouvez voir l’onglet **Mixer Plug-in**.

    ![Wwise avec l’onglet Mixer Plug-in pour le bus Project Acoustics activé](media/mixer-tab-enable.png)

1. Accédez à l’onglet **Mixer Plug-in**, puis ajoutez au bus le plug-in de mélangeur Project Acoustics.

    ![Diagramme illustrant l’ajout du plug-in de mélangeur Project Acoustics au bus Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Configuration de la hiérarchie acteur-mixer
Pour optimiser les performances, Project Acoustics applique le traitement de signal numérique audio à toutes les sources simultanément. Ainsi, le plug-in doit fonctionner comme un plug-in de mélangeur. Wwise nécessite que les plug-ins de mélangeur se trouvent sur le bus de sortie, même si le bus de sortie transporte généralement le signal de sortie sec. Project Acoustics nécessite que le signal sec soit routé via des bus auxiliaires alors que le signal humide est transporté sur le `Project Acoustics Bus`. Le processus suivant prend en charge la migration progressive vers ce flux de signal.

Supposons que vous avez un projet existant avec une hiérarchie acteur-mélangeur contenant *Footsteps*, *Weapons* et autres au niveau supérieur. Chacun a un bus de sortie correspondant pour son mixage sec. Disons que vous voulez migrer Footsteps pour utiliser Acoustics. Tout d’abord, créez un bus auxiliaire correspondant pour transporter le sous-mixage sec qui est un enfant du bus de sortie Footsteps. Par exemple, nous avons utilisé un préfixe « Dry » dans l’image suivante pour organiser les bus, bien que le nom exact ne soit pas important. Les compteurs ou effets que vous aviez sur le bus Footsteps continueront de fonctionner comme avant.

![Configuration du mixage sec Wwise recommandée](media/wwise-dry-mix-setup.png)

Modifiez ensuite la structure de sortie de bus pour la hiérarchie acteur-mélangeur Footsteps comme suit, avec le *bus Project Acoustics* défini en tant que **bus de sortie** et *Dry_Footsteps* indiqué comme bus auxiliaire défini par l’utilisateur.

![Configuration de bus acteur-mélangeur Wwise recommandée](media/actor-mixer-bus-settings.png)

Maintenant, l’ensemble des footsteps bénéficient d’un traitement acoustique et effectuent la sortie de leur réverbération sur le bus Project Acoustics. Le signal sec est acheminé via Dry_Footsteps et spatialisé comme d’habitude.

Project Acoustics s’applique uniquement aux sons avec un emplacement 3D dans le monde. Selon la [documentation Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), les propriétés de positionnement doivent être définies comme indiqué. Le paramètre **3D Spatialization** peut être *Position* ou *Position + Orientation* en fonction des besoins.

![Paramètres recommandés de positionnement d’acteur Wwise](media/wwise-positioning.png)

Vous ne pouvez pas définir le *bus de sortie* sur un autre bus qui mixe en amont dans le **bus Project Acoustics**. Wwise impose cette exigence pour les plug-ins de mélangeur.

Si vous souhaitez qu’un enfant dans la hiérarchie acteur-mélangeur Footsteps n’utilise pas l’acoustique, vous pouvez toujours utiliser « override parent » (remplacer le parent) pour l’annuler.

Si vous utilisez des envois définis par le jeu ou l’utilisateur pour la réverbération sur n’importe quel acteur-mélangeur dans le jeu, désactivez-les sur cet acteur-mélangeur pour éviter d’appliquer la réverbération à deux reprises.

#### <a name="spatialization"></a>Spatialisation
Le plug-in de mélangeur Project Acoustics Wwise applique une réverbération de convolution par défaut, laissant à Wwise le soin d’effectuer la spatialisation panoramique. Lorsque vous utilisez Project Acoustics dans cette configuration de réverbération uniquement par défaut, vous êtes libre d’utiliser n’importe quelle méthode de configuration et de spatialisation de canal sur votre mixage sec. Vous pouvez ainsi associer presque n’importe quel spatialiseur à la réverbération Project Acoustics. Les options disponibles sont [Ambisonics-based binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) (Outils de spatialisation de type binaural basés sur Ambisonics) et [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics inclut un spatialiseur facultatif qui prend en charge le panoramique et le rendu HRTF haute résolution basé sur objet. Cochez la case **Perform Spatialization** dans les paramètres du plug-in de mélangeur, puis choisissez entre *HRTF* ou *Panning*. Par ailleurs, désactivez les envois auxiliaires définis par l’utilisateur vers tous les bus secs afin d’éviter un doublon de la spatialisation par le plug-in de mélangeur Project Acoustics et Wwise. Il est impossible de modifier le mode de spatialisation en temps réel, car une regénération de la banque de sons est nécessaire. Redémarrez Unreal, puis regénérez les banques de sons avant de sélectionner Play pour intégrer des modifications de la configuration du plug-in de mélangeur, comme le paramètre de la case **Perform Spatialization**.

![Paramètres de spatialisation du plug-in de mélangeur Wwise](media/mixer-spatial-settings.png)

Malheureusement, d’autres plug-ins spatialiseur basés sur des objets ne sont pas pris en charge pour l’instant. Ils sont implémentés en tant que plug-ins de mélangeur et Wwise n’autorise pas l’attribution de plusieurs plug-ins de mélangeur à un seul acteur-mélangeur.  

### <a name="audio-setup-in-unreal"></a>Configuration audio dans Unreal
1. Vous devez d’abord effectuer le bake de votre niveau de jeu pour produire une ressource acoustique, qui sera placée dans *Content\Acoustics*. Consultez le [tutoriel Unreal Bake](unreal-baking.md). Certains niveaux pré-baked sont inclus dans l’exemple de package.

1. Créez un acteur Acoustics Space dans votre scène. Créez un seul de ces acteurs dans un niveau, car il représente l’acoustique pour le niveau entier.

    ![Création d’un acteur Acoustics Space dans Unreal Editor](media/create-acoustics-space.png)

1. Attribuez la ressource de données acoustique ayant fait l’objet d’un bake à l’emplacement Acoustics Data sur l’acteur Acoustics Space. Votre scène est maintenant dotée de l’acoustique !

    ![Attribution de la ressource acoustique dans Unreal Editor](media/acoustics-asset-assign.png)

1. Ajoutez un acteur vide. Configurez-le comme suit.

    ![Unreal Editor montrant l’utilisation du composant acoustique dans un acteur vide](media/acoustics-component-usage.png)

       
    <sup>1</sup> Ajoutez un composant Acoustics Audio à l’acteur. Ce composant ajoute la fonctionnalité Project Acoustics au composant audio Wwise.
        
    <sup>2</sup> La case **Play on Start** est cochée par défaut. Ce paramètre déclenche un événement Wwise associé au démarrage du niveau.</li>
         
    <sup>3</sup> Utilisez la case à cocher **Show Acoustics Parameters** pour imprimer les informations de débogage à l’écran sur la source.

    ![Panneau Acoustics d’Unreal Editor sur la source sonore avec les valeurs de débogage activées](media/debug-values.png)

    <sup>4</sup> Affectez un événement Wwise par workflow Wwise habituel.
       
    <sup>5</sup> Vérifiez que l’option **Use Spatial Audio** est désactivée. Si vous utilisez Project Acoustics pour un composant audio particulier, vous ne pouvez pas utiliser simultanément le moteur Spatial Audio de Wwise pour l’acoustique.</li>
       
Vous êtes prêt. Explorez la scène et les effets acoustiques !

## <a name="next-steps"></a>Étapes suivantes
* Testez le [tutoriel de conception Project Acoustics Unreal/Wwise](unreal-workflow.md).
* Apprenez à [effectuer des bakes](unreal-baking.md) pour les scènes de votre jeu.
