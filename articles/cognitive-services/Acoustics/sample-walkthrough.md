---
title: Procédure pas à pas pour l’exemple Project Acoustics - Cognitive Services
description: Cette procédure pas à pas décrit l’exemple de scène Unity pour Project Acoustics, notamment le déploiement sur ordinateur de bureau et casque de réalité virtuelle.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b738cc2fc7db6987b8f4ad54a2c53cc9e69989b3
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181120"
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
L’exemple inclut une scène de démonstration, **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Cette scène comporte une seule source audio spatialisée lue à partir d’un cube flottant (nommé **AudioHolder** dans la **Hiérarchie**). Pour aider à créer un script de navigation générique, la caméra principale est un enfant de l’objet CameraHolder. 

![Vue hiérarchique](media/SampleHierarchyView.png)

La scène a déjà subi un baking et contient un fichier ACE associé à la préfabrication **MicrosoftAcoustics** dans la **Hiérarchie**. 

Écoutez la scène en cliquant sur le bouton de lecture dans l’éditeur Unity. Utilisez W, A, S, D et la souris pour vous déplacer. Pour comparer la scène avec et sans acoustique, cliquez sur le bouton gauche de la souris ou le bouton de commande principal. Pour passer en revue les différentes sources sonores, cliquez sur le bouton droit de la souris ou sur le bouton Précédent de votre commande.

## <a name="targeting-other-platforms"></a>Ciblage d’autres plateformes
L’exemple contient des paramètres pour s’exécuter sur Windows Desktop, UWP, Windows Mixed Reality, Android et Oculus Go. Par défaut, le projet est configuré pour Windows Desktop. Pour cibler une plateforme de réalité virtuelle, accédez aux paramètres de lecteur (**Edit > Project Settings > Player**), recherchez **XR Settings** et cochez la case **Virtual Reality Supported**.

![Activer la réalité virtuelle](media/VRSupport.png)  

Connectez un casque de réalité virtuelle à votre PC. Accédez à **File > Build Settings**, puis cliquez sur **Build and Run** pour déployer l’exemple sur votre casque de réalité virtuelle. Naviguez dans la scène à l’aide des commandes de mouvement de votre casque, ou essayez d’utiliser les touches W, A, S, D sur le clavier.    
Pour cibler Android et Oculus Go, choisissez Android dans le menu **Build Settings**. Cliquez sur **Switch Target**, puis **Build and Run**. Cette action déploie l’exemple de scène sur votre appareil Android connecté. Pour plus d’informations sur le développement Unity pour Android, consultez la [documentation Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cibler Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Étapes suivantes
* [Créer un compte Azure](create-azure-account.md) pour vos propres bakes
* Explorer le [processus de conception](design-process.md)

