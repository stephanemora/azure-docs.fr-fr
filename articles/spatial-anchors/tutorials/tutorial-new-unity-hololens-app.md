---
title: Tutoriel – Instructions pas à pas pour créer une application HoloLens Unity avec Azure Spatial Anchors | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à créer une application HoloLens Unity avec Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c831e8fdacf5103619374605dd980ab1f6735047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135300"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Didacticiel : Instructions pas à pas pour créer une application HoloLens Unity avec Azure Spatial Anchors

Ce tutoriel vous montre comment créer une application HoloLens Unity avec Azure Spatial Anchors.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants :

1. Une machine Windows dotée de <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> avec la charge de travail de **développement pour la plateforme Windows universelle** et le composant **SDK Windows 10 (version 10.0.17763.0 ou plus récente)** , et <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
2. L’[extension Visual Studio (VSIX) C++/WinRT](https://aka.ms/cppwinrt/vsix) pour Visual Studio doit être installée à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Un appareil HoloLens avec le [mode développeur](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) activé. L’appareil utilisé dans cet article est un HoloLens doté de la [mise à jour Windows 10 d’octobre 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (aussi appelée RS5). Pour mettre à jour votre appareil HoloLens vers la dernière version, ouvrez l’application **Paramètres**, accédez à **Mise et sécurité**, puis sélectionnez le bouton **Vérifier les mises à jour**.

## <a name="getting-started"></a>Prise en main

Nous allons tout d’abord configurer notre projet et la scène Unity :
1. Démarrez Unity.
2. Sélectionnez **Nouveau**.
4. Vérifiez que l’option **3D** est sélectionnée.
5. Nommez votre projet et renseignez **Location** (Emplacement) pour enregistrer le projet.
6. Cliquez sur **Créer un projet**.
7. Enregistrez la scène par défaut vide dans un nouveau fichier via : **File** > **Save As**.
8. Nommez la nouvelle scène **Main** et appuyez sur le bouton **Save**.

**Configurer les paramètres du projet**

Nous allons maintenant définir les paramètres du projet Unity qui nous permettent de cibler le kit SDK Windows Holographique pour le développement. 

Tout d’abord, définissons les paramètres de qualité de notre application. 
1. Sélectionnez **Edit** > **Project Settings** > **Quality**
2. Dans la colonne située sous le logo **Windows Store**, cliquez sur la flèche de la ligne **Default** et sélectionnez **Very low**. Vous savez que le paramètre est correctement appliqué lorsque la zone dans la colonne **Windows Store** et la ligne **Very Low** est verte.

Nous devons informer Unity que l’application que nous tentons d’exporter doit créer une vue immersive au lieu d’une vue en 2D. Nous créons une vue immersive par l’activation de la prise en charge de la réalité virtuelle sur Unity ciblant le kit SDK Windows 10.

1. Accédez à **Edit** > **Project Settings** > **Player**.
2. Dans **Inspector Panel** pour **Player Settings**, sélectionnez l’icône **Windows Store**.
3. Développez le groupe **XR Settings**.
4. Dans la section **Rendering**, cochez la case **Virtual Reality Supported** pour ajouter une nouvelle liste du kit SDK **Virtual Reality SDK**.
5. Vérifiez que **Windows Mixed Reality** apparaît dans la liste. Dans le cas contraire, sélectionnez le bouton **+** en bas de la liste et choisissez **Windows Mixed Reality**.
 
> [!NOTE]
> Si vous ne voyez pas l’icône Windows Store, vérifiez que vous avez bien sélectionné Windows Store .NET Scripting Backend avant l’installation. Si ce n’est pas le cas, vous devrez peut-être réinstaller Unity avec l’installation de Windows appropriée.

**Vérifier la configuration .NET**
1. Accédez à **Edit** > **Project Settings** > **Player** (**Player** est peut-être resté ouvert depuis l’étape précédente).
2. Dans **Inspector Panel** pour **Player Settings**, sélectionnez l’icône **Windows Store**.
3. Dans **Other Settings** de la section Configuration, assurez-vous que l’option **Scripting Backend** est définie sur **.NET**.

**Définir les fonctionnalités**
1. Accédez à **Edit** > **Project Settings** > **Player** (**Player** est peut-être resté ouvert depuis l’étape précédente).
2. Dans **Inspector Panel** pour **Player Settings**, sélectionnez l’icône **Windows Store**.
3. Dans **Publishing Settings** de la section Configuration, cochez les options **InternetClientServer** et **SpatialPerception**.

**Configurer la caméra virtuelle principale**
1. Dans **Hierarchy Panel**, sélectionnez **Main Camera**.
2. Dans **Inspector**, définissez sa position de transformation sur **0,0,0**.
3. Recherchez la propriété **Clear Flags** et remplacez **Skybox** par **Solid Color** dans la liste déroulante.
4. Cliquez sur le champ **Background** pour ouvrir un sélecteur de couleurs.
5. Définissez **R, G, B et A** sur **0**.
6. Sélectionnez **Add Component** et recherchez **Spatial Mapping Collider**.

**Créer notre script**
1. Dans le volet **Project**, créez un dossier, **Scripts**, sous le dossier **Assets**. 
2. Cliquez avec le bouton droit sur le dossier, puis sélectionnez **Create >** , **C# Script**. Intitulez-le **AzureSpatialAnchorsScript**. 
3. Accédez à **GameObject** -> **Create Empty**. 
4. Sélectionnez-le, puis dans **Inspector** renommez-le depuis **GameObject** pour **MixedRealityCloud**. Sélectionnez **Add Component**, puis recherchez et ajoutez le script **AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Essai
Pour vérifier que tout fonctionne correctement, générez votre application **Unity** et déployez-la à partir de **Visual Studio**. Suivez le chapitre 6 dans [**Réalité mixte - Principes fondamentaux - Cours 100 : Bien démarrer avec Unity**](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) pour cela. Vous devez voir l’écran de démarrage Unity, puis un écran clair.

## <a name="place-an-object-in-the-real-world"></a>Placer un objet dans le monde réel
Vous allez maintenant créer et placer un objet à l’aide de votre application. Ouvrez la solution Visual Studio que nous avons créée lorsque nous [avons déployé notre application](#trying-it-out). 

Pour commencer, ajoutez les importations suivantes à `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Ajoutez ensuite les variables membres suivantes à votre classe `AzureSpatialAnchorsScript` : 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Ajoutez maintenant le code suivant à votre méthode `Start()`. Ce code se raccorde à `GestureRecognizer`, qui est prévu pour détecter l’existence d’un clic aérien et appeler `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Nous devons à présent ajouter la méthode `HandleTap()` suivante sous `Update()`. Un raycast va être effectué pour obtenir un point d’impact sur lequel placer une sphère. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Nous devons maintenant créer la sphère. La sphère est blanche au départ, mais cette valeur est ajustée par la suite. Ajoutez la méthode `CreateAndSaveSphere()` suivante :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Exécutez votre application à partir de **Visual Studio** pour la valider à nouveau. Cette fois-ci, appuyez sur l’écran pour créer et placer votre sphère blanche sur la surface de votre choix.

## <a name="set-up-the-dispatcher-pattern"></a>Configurer le modèle de répartiteur

Lorsque vous travaillez avec Unity, toutes les API Unity, comme les API que vous utilisez pour effectuer des mises à jour de l’interface utilisateur, doivent se produire sur le thread principal. Dans le code que nous allons écrire, par contre, nous obtiendrons des rappels sur d’autres threads. Comme nous voulons mettre à jour l’interface utilisateur dans ces rappels, nous avons besoin d’un moyen pour passer d’un thread secondaire au thread principal. Pour exécuter du code sur le thread principal à partir d’un thread secondaire, nous allons utiliser le modèle de répartiteur. 

Ajoutons une variable membre, dispatchQueue, qui est une File d’attente d’Actions. Nous allons envoyer (push) des Actions sur la file d’attente, puis les retirer de la file d’attente pour les exécuter sur le thread principal. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Adjoignons ensuite un moyen permettant d’ajouter une Action à la File d’attente. Ajoutons `QueueOnUpdate()` juste après `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Utilisons maintenant la boucle Update() pour vérifier l’existence d’une Action en file d’attente. Si tel est le cas, nous la retirerons de la file d’attente et nous l’exécuterons.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obtenir le kit SDK Azure Spatial Anchors

Nous allons maintenant télécharger le kit SDK Azure Spatial Anchors. Accédez à la [page des versions Azure Spatial Anchors de GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). Sous Assets (Ressources), téléchargez le fichier **AzureSpatialAnchors.unitypackage**. 

Dans Unity, accédez à **Assets**, cliquez sur **Import Package** > **Custom Package...** . Accédez au package et sélectionnez **Open**.

Dans la nouvelle fenêtre **Import Unity Package** qui s’affiche, sélectionnez **None** en bas à gauche. Ensuite, sous **AzureSpatialAnchorsPlugin** > **plugins**, sélectionnez **Common**, **Editor** et **HoloLens**. Cliquez sur **Import** dans l’angle inférieur droit.

Nous devons à présent restaurer les packages Nuget pour obtenir le kit SDK Azure Spatial Anchors. Générez à partir de **Unity**, puis ouvrez et générez de nouveau la solution **Visual Studio** obtenue, tel que décrit en détail à la section [Essai](#trying-it-out). 

Dans votre solution **Visual Studio**, ajoutez l’importation suivante à votre `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Ajoutez ensuite les variables de membre suivantes dans votre classe `AzureSpatialAnchorsScript` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Attacher une ancre spatiale Azure locale à l’ancre locale

Configurons CloudSpatialAnchorSession d’Azure Spatial Anchor. Nous allons commencer par ajouter la méthode `InitializeSession()` suivante à l’intérieur de votre classe `AzureSpatialAnchorsScript`. Une fois appelée, elle assurera la création et l’initialisation d’une session Azure Spatial Anchors pendant le démarrage de votre application.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Nous devons maintenant écrire du code pour gérer les appels de délégué. Nous leur en ajouterons d’autres au fur et à mesure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

À présent, raccordez la méthode `initializeSession()` dans la méthode `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Enfin, ajoutez le code suivant à votre méthode `CreateAndSaveSphere()`. Ce code a pour effet d’attacher une ancre spatiale Azure locale à la sphère que vous placez dans le monde réel.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Avant de poursuivre, vous devez créer un identificateur et une clé de compte Azure Spatial Anchors si vous n’en possédez pas déjà. Pour vous en procurer, suivez les instructions dans la section ci-dessous.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Charger votre ancre locale dans le cloud

Lorsque vous détenez l’identificateur et la clé de votre compte Azure Spatial Anchors, affichez puis collez `Account Id` dans `SpatialAnchorsAccountId`, et `Account Key` dans `SpatialAnchorsAccountKey`.

Enfin, raccordez tout ensemble. Dans votre méthode `SpawnNewAnchoredObject()`, ajoutez le code suivant. Il appelle la méthode `CreateAnchorAsync()` aussitôt que la sphère est créée. Une fois la méthode retournée, le code ci-dessous apporte une dernière mise à jour à la sphère en lui donnant la couleur bleue.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Exécutez une nouvelle fois votre application à partir de **Visual Studio**. Bougez votre tête, puis faites un geste d’appui (air tap) pour placer votre sphère. Une fois que le nombre d’images est suffisant, la sphère devient jaune et le chargement cloud débute. À l’issue du chargement, la sphère passe au bleu. Si vous le souhaitez, vous pouvez aussi utiliser la fenêtre Sortie dans **Visual Studio** pour superviser les messages de journal envoyés par votre application. Vous serez en mesure d’observer la progression de recommended for create, ainsi que l’identificateur d’ancre que le cloud retourne à la fin du chargement.

> [!NOTE]
> Si vous obtenez « DllNotFoundException : Impossible de charger la DLL AzureSpatialAnchors : le module spécifié est introuvable. », vous devez **Supprimer** et **Générer** à nouveau votre solution.

## <a name="locate-your-cloud-spatial-anchor"></a>Localiser l’ancre spatiale cloud

Une fois que votre ancre est chargée dans le cloud, vous pouvez essayer de la localiser à nouveau. Ajoutons le code suivant à votre méthode `HandleTap()`. Ce code a pour effet de :

* Appelez `ResetSession()`, ce qui arrête `CloudSpatialAnchorSession` et supprime la sphère bleue existante de l’écran.
* Initialisez `CloudSpatialAnchorSession` de nouveau. Nous procédons ainsi pour nous assurer que l’ancre à localiser provient du cloud, et qu’il ne s’agit pas de l’ancre locale que vous avez créée.
* Créez un **Watcher** qui recherche l’ancre chargée sur Azure Spatial Anchors.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Ajoutons maintenant nos méthodes `ResetSession()` et `CleanupObjects()`. Vous pouvez les mettre en dessous de `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Nous devons maintenant raccorder le code qui sera appelé dès que l’ancre recherchée sera localisée. À l’intérieur de `InitializeSession()`, ajoutez les rappels suivants :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
À présent, ajoutons le code qui crée et place une sphère verte une fois la localisation de CloudSpatialAnchor effectuée. Il réactive également les appuis sur l’écran, ce qui vous permet de répéter une nouvelle fois le scénario dans son intégralité : création, chargement et relocalisation d’une nouvelle ancre.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

Et voilà ! Exécutez une dernière fois votre application depuis **Visual Studio** pour essayer l’ensemble du scénario de bout en bout. Déplacez votre appareil et placez votre sphère blanche. Ensuite, bougez votre tête pour capturer les données de l’environnement jusqu’à ce que la sphère devienne jaune. Votre ancre locale est alors chargée et la sphère devient bleue. Enfin, appuyez une nouvelle fois sur l’écran de façon à supprimer l’ancre locale, puis recherchez son équivalent cloud. Continuez de déplacer votre appareil jusqu’à ce que l’ancre spatiale cloud soit localisée. Une sphère verte doit alors apparaître à l’emplacement approprié, et vous pouvez effacer et répéter une nouvelle fois l’ensemble du scénario.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]