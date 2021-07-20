---
title: 'Démarrage rapide : Mettre à niveau l’application de démarrage rapide vers Unity 2020'
description: Dans ce démarrage rapide, vous allez apprendre à mettre à niveau une application de démarrage rapide vers Unity 2020 pour créer une application HoloLens Unity en utilisant Object Anchors.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 6ee047cd39eb092c55ed417995bf1e9df6134c5e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094864"
---
# <a name="quickstart-upgrade-quickstart-app-to-unity-2020"></a>Démarrage rapide : Mettre à niveau l’application de démarrage rapide vers Unity 2020

Dans ce démarrage rapide, vous allez mettre à niveau une application Unity HoloLens utilisant [Azure Object Anchors](../overview.md) d’Unity 2019 vers Unity 2020. Azure Object Anchors est un service cloud géré qui convertit les ressources 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Lorsque vous aurez terminé, vous disposerez d’une application HoloLens générée avec Unity, capable de détecter des objets dans le monde physique.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Mettre à niveau l’application d’Unity 2019 vers Unity 2020.
> * Mettre à niveau les dépendances de package.
> * Mettre à niveau les paramètres de build Unity.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

* Toutes les conditions préalables des démarrages rapides [Unity HoloLens](get-started-unity-hololens.md) ou [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md).
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub avec Unity 2020.3.8 F1 ou version ultérieure</a>

## <a name="open-and-upgrade-the-sample-project"></a>Ouvrir et mettre à niveau l’exemple de projet

Suivez la procédure contenue dans les démarrages rapides [Unity HoloLens](get-started-unity-hololens.md) ou [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md) pour cloner le [référentiel d’exemples](https://github.com/Azure/azure-object-anchors), puis téléchargez le package Azure Object Anchors pour Unity.

Ouvrez Unity Hub. Sélectionnez le bouton **Ajouter** et choisissez le projet `quickstarts/apps/unity/basic` ou `quickstarts/apps/unity/mrtk`. Ensuite, sous la colonne **Version Unity**, sélectionnez la version Unity 2020 dans la liste déroulante que vous avez installée sur votre ordinateur. Sous la colonne **Plateforme cible**, sélectionnez **Plateforme Windows universelle**. Sélectionnez enfin la colonne **Nom du projet** et ouvrez l’exemple dans Unity.

:::image type="content" source="./media/upgrade-unity-2020.png" alt-text="Mise à niveau Unity 2020":::

Une boîte de dialogue s’affiche et vous invite à confirmer la mise à niveau de votre projet. Sélectionnez le bouton **Confirmer**.

:::image type="content" source="./media/confirm-unity-upgrade.png" alt-text="Confirmer la mise à niveau Unity":::

## <a name="upgrade-package-dependencies"></a>Mettre à niveau les dépendances de package

Une fois le processus de mise à niveau terminé, **Unity Editor** s’ouvre.

Suivez la documentation de <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> pour configurer l’outil et découvrir comment l’utiliser.

Sous la section **Prise en charge de la plateforme**, installez le package de fonctionnalités du **Plug-in Mixed Reality OpenXR**, version 1.0.0 ou plus récente, dans le dossier du projet Unity. Si vous utilisez le projet `quickstarts/apps/unity/mrtk`, ouvrez également la section **Mixed Reality Toolkit**, recherchez les packages de fonctionnalités **Mixed Reality Toolkit Foundation** et **Mixed Reality Toolkit Tools** et mettez-les à niveau vers la version 2.7.0 ou ultérieure.

Revenez à **Unity Editor**. L’installation des packages de fonctionnalités **Mixed Reality Feature Tool** peut prendre quelques minutes.

Une boîte de dialogue s’affiche pour vous demander de confirmer l’activation du nouveau système d’entrée. Sélectionnez le bouton **Oui**.

:::image type="content" source="./media/new-input-system.png" alt-text="Nouveau système d’entrée":::

 Si une boîte de dialogue vous demandant de remplacer les nuanceurs MRTK s’affiche, sélectionnez **Oui**.

:::image type="content" source="./media/mrtk-shaders.png" alt-text="Nuanceurs mrtk":::

Une fois le processus d’installation terminé, Unity redémarre automatiquement.

## <a name="update-configuration-settings"></a>Mettre à jour les paramètres de configuration du système

De retour dans **Unity Editor**, suivez la documentation relative à la <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">Configuration de la gestion du plug-in XR pour OpenXR</a> pour configurer la **Gestion du plug-in XR** dans **Paramètres de projet**. Suivez ensuite la documentation relative à l’<a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">Optimisation</a> pour appliquer les paramètres de projet recommandés pour HoloLens 2.

## <a name="update-mrtk-settings"></a>Mettre à jour les paramètres MRTK

Si vous utilisez le projet `quickstarts/apps/unity/mrtk`, il vous faudra également apporter quelques ajustements à MRTK. Si tel est le cas, suivez les étapes ci-dessous. Sinon, passez à la section **Générer, déployer et exécuter l’application**.

Dans **Unity Editor**, accédez à `Assets/MixedReality.AzureObjectAnchors/Scenes` et ouvrez **AOASampleScene**. Sous le volet **Hiérarchie** , sélectionnez l’objet **MixedRealityToolkit**.

:::image type="content" source="./media/open-sample-scene.png" alt-text="ouvrir un exemple de scène":::

Sous le volet **Inspecteur**, sélectionnez le bouton **Caméra**, puis remplacez le profil **ObsoleteXRSDKCameraProfile** par **DefaultMixedRealityCameraProfile**.

:::image type="content" source="./media/update-camera-profile.png" alt-text="Mettre à jour le profil de caméra":::

Toujours sous le volet **Inspecteur**, sélectionnez le bouton **Entrée** , puis développez la liste déroulante **Fournisseurs de données d’entrée**. Suivez la documentation <a href="/windows/mixed-reality/mrtk-unity/configuration/getting-started-with-mrtk-and-xrsdk#configuring-mrtk-for-the-xr-sdk-pipeline" target="_blank">Configuration de MRTK pour le pipeline du kit de développement logiciel (SDK) XR</a> pour configurer les fournisseurs de données d’entrée appropriés (**OpenXRDeviceManager** et **WindowsMixedRealityDeviceManager**).

:::image type="content" source="./media/update-input-profile.png" alt-text="Mettre à jour le profil d’entrée":::

## <a name="build-deploy-and-run-the-app"></a>Créer, déployer et exécuter l’application

Votre projet est désormais entièrement mis à niveau vers Unity 2020. Suivez les instructions contenues dans les démarrages rapides [Unity HoloLens](get-started-unity-hololens.md) ou [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md) pour créer, déployer et exécuter l’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
