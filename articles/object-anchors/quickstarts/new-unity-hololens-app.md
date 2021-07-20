---
title: 'Démarrage rapide : Créer une application HoloLens Unity'
description: Dans ce démarrage rapide, vous allez apprendre à créer une application HoloLens Unity en utilisant Object Anchors.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 174701b16d8ae9c8c8e5b1edf863aa9f1a65336c
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094868"
---
# <a name="quickstart-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-object-anchors"></a>Démarrage rapide : Instructions pas à pas pour créer une application HoloLens Unity avec Azure Object Anchors

Ce démarrage rapide vous montre comment créer une application HoloLens Unity avec [Azure Object Anchors](../overview.md). Azure Object Anchors est un service cloud géré qui convertit les ressources 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Lorsque vous aurez terminé, vous disposerez d’une application HoloLens générée avec Unity, capable de détecter des objets dans le monde physique.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

* Toutes les conditions préalables des démarrages rapides [Unity HoloLens](get-started-unity-hololens.md) ou [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md).
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub avec Unity 2020.3.8 F1 ou version ultérieure</a>

## <a name="getting-started"></a>Prise en main

Nous allons tout d’abord configurer notre projet et la scène Unity :

1. Démarrez Unity Hub.
1. Sélectionnez **Nouveau**, puis Unity 2020.3.8 F1 ou version ultérieure.
1. Vérifiez que l’option **3D** est sélectionnée.
1. Nommez votre projet et renseignez **Location** (Emplacement) pour enregistrer le projet.
1. Sélectionnez **Create** (Créer).
1. Lorsque **Unity Editor** s’ouvre, enregistrez la scène vide par défaut dans un nouveau fichier à l’aide de : **Fichier** > **Enregistrer sous**.
1. Sélectionnez le dossier **Scènes**, nommez la nouvelle scène **Main**, puis appuyez sur le bouton **Enregistrer**.

## <a name="configure-as-uwp"></a>Configurer en tant que plateforme Windows universelle

1. Sélectionnez **Fichier -> Paramètres de génération**.
1. Sélectionnez **Plateforme Windows universelle**, puis **Changer de plateforme**.
1. Si Unity Editor indique que vous devez télécharger d’abord certains composants, téléchargez-les et installez-les.

## <a name="install-mixed-reality-feature-tool-feature-packages"></a>Installer les packages de fonctionnalités Mixed Reality Feature Tool

1. Suivez la documentation de <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> pour configurer l’outil et découvrir comment l’utiliser.
1. Sous la section **Prise en charge de la plateforme**, installez le package de fonctionnalités du **Plug-in Mixed Reality OpenXR**, version 1.0.0 ou plus récente, dans le dossier du projet Unity.
1. Dans la section **Azure Mixed Reality Services**, installez le package de fonctionnalités d'**Microsoft Azure Object Anchors** dans le dossier de projet Unity.
1. Revenez à **Unity Editor**. L’installation des packages de fonctionnalités **Mixed Reality Feature Tool** peut prendre quelques minutes.
1. Une boîte de dialogue s’affiche pour vous demander de confirmer l’activation du nouveau système d’entrée.
1. Sélectionnez le bouton **Oui**.
1. Une fois le processus d’installation terminé, Unity redémarre automatiquement.

## <a name="configure-openxr"></a>Configurer OpenXR

1. Vous devez toujours vous trouver dans la fenêtre **Paramètres de build**.
1. Sélectionnez le bouton **Paramètres du lecteur...** .
1. La fenêtre **Paramètres du projet** s’ouvre.
1. Sélectionnez l’entrée **XR Plug-in Management**.
1. Suivez la documentation relative à la <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">Configuration deXR Plugin Management for OpenXR</a> pour configurer **OpenXR** avec l’**ensemble de fonctionnalités Microsoft HoloLens** dans la liste **Fournisseurs de plug-ins**.

## <a name="set-capabilities"></a>Définir les fonctionnalités

1. Vous devez toujours vous trouver dans la fenêtre **Paramètres de build**.
1. Sélectionnez l’entrée **Lecteur**.
1. Dans le **Panneau Inspecteur** pour **Paramètres du lecteur**, vérifiez que l’icône des **Paramètres de la plateforme Windows universelle** est sélectionnée.
1. Dans la section **Paramètres de publication**, assurez-vous que **InternetClientServer**, **WebCam** et **SpatialPerception** sont sélectionnés.

## <a name="set-up-the-project-settings"></a>Configurer les paramètres du projet

1. Vous devez toujours vous trouver dans la fenêtre **Paramètres de build**.
1. Sélectionnez l’entrée **Qualité**.
1. Dans la colonne située sous le logo **Plateforme Windows universelle**, sélectionnez la flèche sur la ligne **Par défaut**, puis sélectionnez **Très faible**. Vous savez que le paramètre est correctement appliqué lorsque la zone dans la colonne **Plateforme Windows universelle** et la ligne **Très faible** est verte.
1. Fermez les fenêtres **Paramètres du projet** et **Paramètres de build**.
1. Suivez la documentation relative à l’<a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">Optimisation</a> pour appliquer les paramètres de projet recommandés pour HoloLens 2.

## <a name="set-up-the-main-virtual-camera"></a>Configurer la caméra virtuelle principale

1. Dans le panneau **Hiérarchie**, sélectionnez **Caméra principale**.
1. Dans **Inspector**, définissez sa position de transformation sur **0,0,0**.
1. Recherchez la propriété **Clear Flags** et remplacez **Skybox** par **Solid Color** dans la liste déroulante.
1. Sélectionnez le champ **Arrière-plan** pour ouvrir un sélecteur de couleurs.
1. Définissez **R, G, B et A** sur **0**.
1. Remplacez la propriété **Clipping Planes Near** par 0.1.
1. Sélectionnez **Ajouter un composant**, puis recherchez et ajoutez le **Pilote de pose suivi**.
1. Sélectionnez **Fichier** > **Enregistrer**.

## <a name="trying-it-out"></a>Essai

Pour vérifier que tout fonctionne correctement, générez votre application **Unity** et déployez-la à partir de **Visual Studio**. Suivez le chapitre 6 dans <a href="/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio" target="_blank">**Réalité mixte - Principes fondamentaux - Cours 100 : Bien démarrer avec Unity**</a> pour cela. Vous devez voir l’écran de démarrage Unity, puis un écran clair.

## <a name="create-your-script"></a>Créer votre script

1. Dans le volet **Project**, créez un dossier, **Scripts**, sous le dossier **Assets**.
1. Cliquez avec le bouton droit sur le dossier, puis sélectionnez **Créer >** , **Script C#** . Nommez-le **ObjectSearch**.
1. Accédez à **GameObject** -> **Create Empty**.
1. Sélectionnez-le, puis dans **Inspecteur** renommez-le de **GameObject** à **Object Observer**.
1. Sélectionnez **Ajouter un composant**, puis recherchez et ajoutez le script **ObjectSearch**.
1. Sélectionnez **Fichier** > **Enregistrer**.

## <a name="start-implementing-your-app"></a>Commencer à implémenter votre solution

Vous êtes prêt à commencer à ajouter votre propre code au script **ObjectSearch** à l’aide du kit de développement logiciel (SDK) Object Anchors. Vous pouvez consulter [Présentation du kit de développement logiciel (SDK)](../concepts/sdk-overview.md) comme point de départ pour apprendre les principes de base et utiliser un exemple de code pour l’essayer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
