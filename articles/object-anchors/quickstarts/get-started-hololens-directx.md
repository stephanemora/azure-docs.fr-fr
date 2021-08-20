---
title: 'Démarrage rapide : Créer une application HoloLens avec DirectX'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application HoloLens en utilisant Object Anchors.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: eee130b0736c87b118b38f19e7523c07a431e5c9
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202883"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Démarrage rapide : Créer une application HoloLens avec Azure Object Anchors, en C++/WinRT et DirectX

Ce démarrage rapide explique comment créer une application HoloLens à l’aide d’[Azure Object Anchors](../overview.md) en C++/WinRT et DirectX. Azure Object Anchors est un service Cloud géré qui convertit les ressources 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Lorsque vous aurez terminé, vous disposerez d’une application HoloLens capable de détecter un objet et sa pose dans une application holographique DirectX 11 (Windows universel).

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer et charger de façon autonome une application HoloLens
> * Détecter un objet et visualiser son modèle

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

* Un objet physique dans votre environnement et son modèle 3D (CAO ou numérisé).
* Une machine Windows avec les éléments suivants installés :
  * <a href="https://git-scm.com" target="_blank">Git pour Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> avec la charge de travail de **développement pour la plateforme Windows universelle** et le composant **SDK Windows 10 (version 10.0.18362.0 ou plus récente)**
* Un appareil HoloLens 2 à jour et pour lequel le [mode développeur](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) est activé.
  * Pour mettre à jour votre appareil HoloLens avec la dernière version, ouvrez l’application **Paramètres**, accédez à **Mise à jour et sécurité**, puis sélectionnez **Rechercher les mises à jour**.

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Ouvrez `quickstarts/apps/directx/DirectXAoaSampleApp.sln` dans Visual Studio.

Pour **Configuration de la solution** choisissez **Mise en production**, pour **Plateforme de solution** choisissez **ARM64**, puis sélectionnez **Appareil** dans les options de cible de déploiement.

## <a name="configure-the-account-information"></a>Configurer les informations de compte

L’étape suivante consiste à configurer l’application pour qu’elle utilise vos informations de compte. Vous avez noté les valeurs de **Clé de compte**, **ID de compte** et **Domaine de compte** dans la section [Créer un compte Object Anchors](#create-an-object-anchors-account).

Ouvrez `Assets\ObjectAnchorsConfig.json`.

Localisez le champ `AccountId` et remplacez `Set me` par l’ID du compte.

Localisez le champ `AccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `AccountDomain` et remplacez `Set me` par le domaine du compte.

À présent, générez le projet **AoaSampleApp** en cliquant avec le bouton droit sur le projet et en sélectionnant **Générer**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Configurer le projet Visual Studio pour le déploiement":::

## <a name="deploy-the-app-to-hololens"></a>Déployer l’application sur HoloLens

Une fois l’exemple de projet compilé avec succès, vous pouvez déployer l’application sur HoloLens.

Allumez l’appareil HoloLens, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB. Assurez-vous que l'**appareil** est la cible de déploiement choisie (voir ci-dessus).

Cliquez avec le bouton droit sur le projet **AoaSampleApp**, puis cliquez sur **Déployer** dans le menu contextuel pour installer l’application. Si aucune erreur ne s’affiche dans la **Fenêtre Sortie** de Visual Studio, l’application est installée sur HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Déployer une application sur HoloLens":::

Avant de lancer l’application, vous devez charger un modèle objet. Suivez les instructions dans la section **Ingérer un modèle objet et détecter son instance** ci-dessous.

Pour lancer et déboguer l’application, sélectionnez **Déboguer > Démarrer le débogage**. Pour arrêter l’application, sélectionnez **Arrêter le débogage** ou appuyez sur **Maj + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Ingérer un modèle objet et détecter son instance

Vous devez créer un modèle objet pour exécuter l’exemple d’application. Supposons que vous disposiez déjà d’un modèle de maillage 3D CAO ou numérisé d’un objet dans votre espace. Reportez-vous à [Démarrage rapide : Ingestion d’un modèle 3D](./get-started-model-conversion.md) sur la création d’un modèle.

Téléchargez ce modèle, **chair.ou** dans notre exemple, sur votre ordinateur. Ensuite, dans le portail de l’appareil HoloLens, sélectionnez **Système > Explorateur de fichiers > LocalAppData > AoaSampleApp > LocalState**, puis sélectionnez **Parcourir...** . Sélectionnez ensuite votre fichier de modèle, **chair.ou** par exemple, puis sélectionnez **Télécharger**. Vous devriez alors voir le fichier de modèle dans le cache local.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Modèle de chargement du portail":::

Sur HoloLens, lancez l’application **AoaSampleApp** (si elle est déjà ouverte, fermez-la, puis rouvrez-la). Rapprochez-vous de l’objet cible (chair) (à une distance de 2 mètres maximum) et analysez-le en l’examinant sous différentes perspectives. Vous devriez voir un cadre englobant rose autour de l’objet avec des points jaunes proches de la surface de l’objet, ce qui indique qu’il a été détecté.

:::image type="content" source="./media/chair-detection.png" alt-text="Détection de chaise":::

Figure : une chaise détectée est rendue avec son cadre englobant (rose), un nuage de points (jaunes) et une zone de recherche (grand cadre jaune).

Vous pouvez définir un espace de recherche pour l’objet dans l’application en cliquant dans l’air avec la main droite ou gauche. L’espace de recherche se transforme en une sphère de 2 mètres de rayon, un cadre englobant 4 m3 et un tronc de vue. Pour les objets plus grands comme les voitures, le meilleur choix consiste généralement à utiliser la sélection du tronc de vue tout en se tenant dans un coin de l’objet à une distance d’environ 2 mètres.
Chaque fois que la zone de recherche change, l’application supprime les instances actuellement suivies, puis tente à nouveau de les retrouver dans la nouvelle zone de recherche.

Cette application peut suivre plusieurs objets à la fois. Pour cela, chargez plusieurs modèles dans le dossier **LocalState** et définissez une zone de recherche qui couvre tous les objets cibles. La détection et le suivi de plusieurs objets peuvent prendre plus de temps.

L’application aligne fidèlement un modèle 3D sur son équivalent physique. L’utilisateur peut appuyer dans l’air avec sa main gauche pour activer le mode de suivi haute précision, qui calcule une pose plus précise. Il s’agit toujours d’une fonctionnalité expérimentale, qui consomme davantage de ressources système et qui peut entraîner une plus grande instabilité de la pose estimée. Appuyez à nouveau dans l’air avec la main gauche pour revenir au mode de suivi normal.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingestion d’un modèle 3D](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Concepts : Vue d’ensemble du Kit de développement logiciel (SDK)](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FORUM AUX QUESTIONS](../faq.md)

> [!div class="nextstepaction"]
> [Kit SDK de conversion](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
