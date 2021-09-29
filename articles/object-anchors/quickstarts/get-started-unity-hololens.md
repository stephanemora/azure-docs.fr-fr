---
title: 'Démarrage rapide : Créer une application HoloLens avec Unity'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application HoloLens Unity en utilisant Object Anchors.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 09/08/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 53bcc84fc63666e64ffaf502a4348de87c1ab8c7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796979"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Démarrage rapide : Créer une application HoloLens avec Azure Object Anchors dans Unity

Ce démarrage rapide explique comment créer une application HoloLens Unity qui utilise [Azure Object Anchors](../overview.md). Azure Object Anchors est un service cloud géré qui convertit les ressources 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Lorsque vous aurez terminé, vous disposerez d’une application HoloLens générée avec Unity, capable de détecter des objets dans le monde physique.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Préparer les paramètres de build Unity.
> * Exporter le projet HoloLens Visual Studio.
> * Déployer l’application et l’exécuter sur un appareil HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Dans Unity, ouvrez le projet `quickstarts/apps/unity/basic`.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Configure Account](../../../includes/object-anchors-get-started-configure-account.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

Après l’écran de démarrage de Unity, un message s’affiche indiquant que l’observateur d’objets a été initialisé.

L’application recherche des objets dans le champ perspective actuel, puis les suit une fois détectés. Une instance est supprimée quand elle est éloignée de 6 mètres ou plus de l’emplacement de l’utilisateur. Le texte de débogage affiche des détails d’une instance, par exemple, l’ID, l’horodatage mis à jour et le ratio de couverture des surfaces.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)

> [!div class="nextstepaction"]
> [Résolution des problèmes de détection d’objets](../troubleshoot/object-detection.md)
