---
title: 'Exemple : Project Acoustics'
titlesuffix: Azure Cognitive Services
description: Cette procédure pas à pas décrit l’exemple de scène Unity pour Project Acoustics, notamment le déploiement sur ordinateur de bureau et casque de réalité virtuelle.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7d8ba2f25bd53b407ab6860bc57163a79b7d228a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174259"
---
# <a name="unity-sample-walkthrough"></a>Procédure pas à pas pour l’exemple Unity
Il s’agit d’une procédure pas à pas de l’exemple Project Acoustics. Pour plus d’informations sur Project Acoustics, consultez l’[Introduction à Project Acoustics](what-is-acoustics.md). Pour vous aider à ajouter le package Project Acoustics à un projet Unity existant, utilisez le guide [Bien démarrer](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Configuration requise pour exécuter l’exemple de projet
* Unity 2018.2 +, avec la version du runtime de script .NET 4.x
* Éditeur Unity Windows 64 bits
* L’exemple prend en charge les cibles Windows Desktop, UWP et Android, y compris les casques audiovisuels
* Abonnement Azure Batch nécessaire pour le processus de baking

## <a name="sample-project-setup"></a>Configuration de l’exemple de projet
Téléchargez et importez **MicrosoftAcoustics.Sample.unitypackage**. Lors de l’importation, les paramètres de projet tels que **Spatialiseur** et **Version du runtime de script** sont mis à jour pour satisfaire la configuration requise du plug-in. Une fois cette opération terminée, vous verrez une erreur retournée par **AcousticsGeometry.cs** dans la console Unity, concernant le remplacement de la version du Runtime de script par **.NET 4.x Équivalent**. Ce changement de paramètre s’effectue dans le cadre de l’importation de package, mais nécessite un redémarrage de Unity. Redémarrez Unity.

## <a name="running-the-sample"></a>Exécution de l’exemple
L’exemple inclut une scène de démonstration, **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Cette scène a trois sources audio. Par défaut, une seule source audio est en cours de lecture, et les deux autres sont en pause. Elles se trouvent sous **Sound Sources** dans la **Hiérarchie**. Pour aider à créer un script de navigation générique, la caméra principale est un enfant de l’objet CameraHolder. 

![Vue hiérarchique](media/SampleHierarchyView.png)

La scène a déjà subi un baking et contient un fichier ACE associé à la préfabrication **MicrosoftAcoustics** dans la **Hiérarchie**. 

Écoutez la scène en cliquant sur le bouton de lecture dans l’éditeur Unity. Sur le Bureau, utilisez W, A, S, D et la souris pour vous déplacer. Pour comparer l’audio de la scène avec et sans acoustique, appuyez sur le bouton **R** jusqu’à ce que le texte en superposition s’affiche en rouge et indique « Acoustics: Disabled » (Acoustique : désactivé). Pour afficher les raccourcis clavier des autres contrôles, appuyez sur **F1**. Tous les contrôles sont également utilisables à l’aide de la souris : cliquez avec le bouton droit sur l’action à effectuer pour la sélectionner, puis cliquez avec le bouton gauche pour lancer l’action.

## <a name="targeting-other-platforms"></a>Ciblage d’autres plateformes
L’exemple contient des paramètres pour s’exécuter sur Windows Desktop, UWP, Windows Mixed Reality, Android et Oculus Go. Par défaut, le projet est configuré pour Windows Desktop. Pour cibler une plateforme de réalité virtuelle, accédez aux paramètres de lecteur (**Edit > Project Settings > Player**), recherchez **XR Settings** et cochez la case **Virtual Reality Supported**.

![Activer la réalité virtuelle](media/VRSupport.png)  

Connectez un casque de réalité virtuelle à votre PC. Accédez à **File > Build Settings**, puis cliquez sur **Build and Run** pour déployer l’exemple sur votre casque de réalité virtuelle. Naviguez dans la scène à l’aide des commandes de mouvement de votre casque, ou essayez d’utiliser les touches W, A, S, D sur le clavier.    
Pour cibler Android et Oculus Go, choisissez Android dans le menu **Build Settings**. Cliquez sur **Switch Target**, puis **Build and Run**. Cette action déploie l’exemple de scène sur votre appareil Android connecté. Pour plus d’informations sur le développement Unity pour Android, consultez la [documentation Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cibler Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Étapes suivantes
* [Créer un compte Azure](create-azure-account.md) pour vos propres bakes
* Explorer le [processus de conception](design-process.md)

