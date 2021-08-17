---
title: 'Guide de démarrage rapide : procédure pas à pas MRTK détaillée'
description: Dans ce guide de démarrage rapide, vous bénéficiez d’une couverture approfondie de l’exemple d’application Azure Object Anchors MRTK Unity
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 515d44cc7cb40972c67424799163bc57deba9182
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371571"
---
# <a name="quickstart-in-depth-mrtk-walkthrough"></a>Guide de démarrage rapide : procédure pas à pas MRTK détaillée

Ce guide vous offre une couverture approfondie de [l’exemple d’application Azure Object Anchors MRTK Unity](get-started-unity-hololens-mrtk.md). Il est destiné à fournir des informations sur la conception de l’exemple. En lisant ce guide, les développeurs peuvent accélérer leur compréhension des concepts clés d’Azure Object Anchors dans l’exemple.

## <a name="project-layout"></a>Disposition du project

Les ressources créées pour l’exemple d’ancrage d’objets Azure MRTK Unity sont stockées dans `Assets\MixedReality.AzureObjectAnchors`. Les sous-dossiers sont les suivants :

- **Icônes**
  - Contient des icônes personnalisées utilisées dans le menu accessible à l’utilisateur.
- **Matériaux**
  - Contient des nuanceurs et des matériaux pour la visualisation de la reconstruction des surfaces et un nuanceur de *profondeur uniquement*, qui écrit dans la mémoire tampon de profondeur pour faciliter la stabilisation des hologrammes autour du texte.
- **Prefabs**
  - Contient une Unity réutilisable `GameObjects`. En particulier, `TrackableObjectPrefab` représente l’objet créé lorsque les ancres d’objets Azure détectent un objet.
- **Profils**
  - Contient des profils MRTK personnalisés qui décrivent les fonctionnalités minimales requises de MRTK pour activer l’application.
- **Scènes**
  - Contient le `AOASampleTestScene`, qui est la scène principale de l’exemple.
- **Scripts**
  - Contient les scripts écrits pour l’exemple.

## <a name="unity-scene"></a>Scène Unity

**Espace de lecture Mixed Reality** – MRTK réutilisable en grande partie

- <a href="/windows/mixed-reality/develop/unity/mrtk-getting-started" target="_blank">Présentation de MRTK pour Unity</a>.
- Une interface utilisateur est attachée à l’appareil photo qui détaille l’état global de Azure Object Anchors (consultez `OverlayDebugText.cs`).

**Espace de lecture d’objet Mixed Reality** : principalement lié à Azure Object Anchors, mais avec certains contrôles MRTK. Deux scripts, `TrackableObjectSearch` et `ObjectTracker`, attachés au parent, représentent l’interface principale avec Azure Object Anchors.

- **Menu**
  - Code MRTK principalement, mais les interactions de l’interface utilisateur sont dirigées vers la fonctionnalité Azure Object Anchors.
  - Le script `TrackableObjectMenu` joint effectue le travail principal de routage des événements de l’interface utilisateur MRTK vers des appels Azure Object Anchors appropriés.
  - <a href="/windows/mixed-reality/design/hand-menu" target="_blank">Menu MRTK</a>.
- **WorkspaceBoundingBox**
  - Contient des scripts MRTK associés au contrôle d’un cadre englobant.
  - Contient également un objet enfant `ModelVis`, qui est utilisé pour visualiser le modèle Azure Object Anchors avant qu’une détection ne s’est produite pour faciliter l’alignement pendant des détections difficiles.

## <a name="menu-walkthrough"></a>Procédure pas à pas de menu

Avant de plonger dans les scripts, nous allons d’abord parcourir les éléments de menu. De cette façon, nous pouvons faire référence à la façon dont ces éléments de menu interagissent avec les scripts.

:::image type="content" source="./media/mrtk-menus.png" alt-text="Menus mrtk":::

Les sous-menus en bas et à droite ne s’affichent pas automatiquement, mais sont basculés avec `Search Area Settings` et `Tracker Settings` , respectivement.

### <a name="main-menu"></a>Menu principal

- **Commencer la recherche**
  - Démarre la recherche d’objets dans la zone de recherche spécifiée.
- **Activer/désactiver le mappage spatial**
  - Cycles entre l’illustration du mappage spatial lors de la recherche, le mappage spatial toujours affiché et le mappage spatial jamais affiché.
- **Paramètres de suivi/zone de recherche Paramètres**
  - Active/désactive leurs sous-menus respectifs.
- **Démarrer le suivi/arrêter le suivi**
  - Commence ou termine le suivi des Diagnostics.
- **Télécharger le suivi**
  - Télécharge le suivi des diagnostics vers Microsoft pour l’analyse du débogage.

### <a name="tracker-settings"></a>Paramètres du suivi

- **Haute précision**
  - Lorsqu’il est activé, `ObjectInstanceTrackingMode` a la valeur `HighLatencyAccuratePosition`.
  - Lorsqu’il est désactivé, `ObjectInstanceTrackingMode` a la valeur `LowLatencyCoarsePosition`.
- **Alignement vertical souple**
  - Lorsqu’il est activé, `AllowedVerticalOrientationInDegrees` est de 10 degrés. Cette fonctionnalité permet la détection d’objets qui se trouvent sur des rampes.
  - Lorsqu’il est désactivé, `AllowedVerticalOrientationInDegrees` est de 0 degrés.
- **Autoriser la modification de la mise à l’échelle**
  - Lorsqu’il est activé, `MaxScaleChange` est de 0,1. Cette fonctionnalité permet à Azure Object Anchors d’ajuster l’échelle de l’objet en fonction des ajustements de l’échelle de HoloLens.
  - Lorsqu’il est désactivé, `MaxScaleChange` est de 0.
- **Curseur de taux de couverture**
  - Ajuste le taux de couverture requis pour la détection d’objets pour prendre en compte une correspondance. Une valeur faible augmente la fréquence de détection. Cette fonctionnalité peut être souhaitable pour les objets difficiles à détecter, mais elle peut également entraîner l’augmentation de faux positifs dans la détection d’objets.

### <a name="search-area-settings"></a>Paramètres de zone de recherche

- **Zone de recherche de verrouillage**
  - Lorsqu’il est activé, il empêche l’utilisateur de modifier la zone de recherche.
- **Ajuster automatiquement la zone de recherche**
  - Lorsqu’il est activé, il permet aux scripts de déplacer la zone de recherche pour affiner le processus de détection.
- **Maillage de cycle**
  - Parcourt les maillages pour les objets `.ou` qui peuvent être détectés et sans maillage.

## <a name="scripts"></a>scripts ;

- **AutonomousSearchArea.cs**
  - Ce script est attaché à `WorkspaceBoundingBox`. Le script tente de mettre à l’échelle et de placer `WorkspaceBoundingBox` automatiquement. Il est activé lorsque `Auto-Adjust Search Area` est activé.
- **ObjectAnchorsSubscription.cs**
  - Ce script encapsule les informations requises pour télécharger les données de diagnostic.
- **ObjectTracker.cs**
  - Ce script crée un pont entre Unity et les aspects de détection du kit de développement logiciel (SDK) d’Azure Object Anchors.
- **ObjectTrackerDiagnostics.cs**
  - Ce script gère la fonctionnalité de diagnostics du kit de développement logiciel (SDK) d’Azure Object Anchors.
- **OverlayDebugText.cs**
  - Ce script est attaché à la caméra principale. Il est responsable de l’affichage de l’état global de l’exemple et d’Azure Object Anchors à l’utilisateur.
- **PositionDebugInfo.cs**
  - Ce script force simplement le texte de débogage attaché à un objet détecté pour le faire face à l’utilisateur.
- **SearchAreaController.cs**
  - Ce script gère l’état du `WorkspaceBoundingBox` utilisé pour indiquer où Azure Object Anchors doit rechercher des objets.
- **SearchAreaModelVisualization.cs**
  - Ce script active la fonctionnalité `Cycle Mesh` sous `Search Area Settings`.
- **SpatialMappingController.cs**
  - Ce script gère le moment où le mappage spatial doit être activé en fonction de l’interaction avec `Toggle Spatial Mapping` sous `Main Menu`.
- **TextToSpeech.cs**
  - Ce script prend du texte et le convertit en voix.
- **TrackableObjectData.cs**
  - Ce script représente les données pour les objets qui peuvent être suivis.
- **TrackableObjectDataLoader.cs**
  - Ce script effectue le travail de chargement des fichiers `.ou` et de leur transformation en éléments `TrackableObjectData`.
- **TrackableObjectMenu.cs**
  - La plupart des interactions utilisateur sont acheminées de l’interface utilisateur vers ce script puis vers le script approprié. Par exemple, `TrackableObjectMenu` a `ToggleSpatialMapping` , qui est routé vers `SpatialMappingController`.
- **TrackableObjectSearch.cs**
  - Ce script assure une gestion légère de la zone de recherche. En particulier, l’interaction dans laquelle l’utilisateur appuie sur Espace et la zone de recherche est placée devant l’utilisateur. En outre, l’intérieur de la zone de recherche n’est pas rendu pendant la recherche ou pendant la détection d’un objet, pour empêcher le rendu complet d’obturer l’objet.
- **TrackedObject.cs**
  - Script principal sur le `TrackableObjectPrefab`. Il maintient l’état de visualisation d’un objet détecté par Azure Object Anchors.
- **TrackedObjectData.cs**
  - Ce script contient des informations sur un objet qu’Azure Object Anchors suit actuellement.

## <a name="prefabs"></a>Prefabs

- **TrackableObjectPrefab**
  - Prefab créé lorsqu’un objet est détecté par AzureObjectAnchors. Un enfant de ce Prefab, `LogicalCenter` , représente le centre de l’objet et est un emplacement approprié pour démarrer lors de la tentative d’attachement du contenu enfant aux objets détectés. Le texte d’informations affiché par l’échantillon est attaché à ce centre logique.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
