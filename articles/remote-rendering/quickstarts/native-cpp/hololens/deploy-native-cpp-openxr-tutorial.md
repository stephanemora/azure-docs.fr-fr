---
title: Déployer le tutoriel OpenXR C++ natif sur HoloLens
description: Démarrage rapide qui montre comment exécuter le tutoriel OpenXR C++ natif sur HoloLens
author: florianborn71
ms.author: flborn
ms.date: 07/01/2021
ms.topic: quickstart
ms.openlocfilehash: 41049aa7c4bd4b5e1ea5dedb3da7578f37a05d55
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667561"
---
# <a name="quickstart-deploy-native-c-openxr-sample-to-hololens"></a>Démarrage rapide : Déployer l’exemple OpenXR C++ natif sur HoloLens

Ce démarrage rapide explique comment déployer et exécuter l’application de tutoriel C++ native basée sur OpenXR sur un appareil HoloLens 2.

Dans ce guide de démarrage rapide, vous allez apprendre à :

> [!div class="checklist"]
>
>* Générer l’application de tutoriel pour HoloLens.
>* Modifier les informations d’identification ARR dans le code source.
>* Déployer et exécuter l’exemple sur l’appareil.

## <a name="prerequisites"></a>Prérequis

Pour pouvoir accéder au service Azure Remote Rendering, vous devez d’abord [créer un compte](../../../how-tos/create-an-account.md).

Les logiciels suivants doivent être installés :

* SDK Windows 10.0.18362.0 [(télécharger)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Installez la dernière version de Visual Studio 2019 [(télécharger)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Outils Visual Studio pour Mixed Reality](/windows/mixed-reality/install-the-tools). Plus précisément, les installations de *charge de travail* suivantes sont obligatoires :
  * **Développement Desktop en C++**
  * **Développement de la plateforme Windows universelle (UWP)**
* Git [(télécharger)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Cloner le dépôt des exemples ARR

La première étape consiste à cloner le dépôt Git, qui héberge les exemples globaux Azure Remote Rendering. Ouvrez une invite de commandes (tapez `cmd` dans le menu Démarrer de Windows) et accédez au répertoire dans lequel vous souhaitez stocker l’exemple de projet ARR.

Exécutez les commandes suivantes :

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

La dernière commande crée dans le répertoire ARR un sous-répertoire contenant les différents exemples de projets pour Azure Remote Rendering.

Le tutoriel HoloLens C++ se trouve dans le sous-répertoire *NativeCpp/HoloLens-OpenXr*.

## <a name="build-the-project"></a>Créer le projet

Ouvrez le fichier solution *BasicXrApp.sln* situé dans le sous-répertoire *NativeCpp/HoloLens-OpenXr* à l’aide de Visual Studio 2019.

Basculez la configuration de build sur *Débogage* (ou *Release*) et *ARM64*. Vérifiez également que le mode du débogueur est défini sur *Appareil* plutôt que *Ordinateur distant* :

![Configuration de Visual Studio](media/vs-config-native-cpp-tutorial.png)

Les informations d’identification du compte étant codées en dur dans le code source du tutoriel, vous devez les remplacer par des informations d’identification valides. Pour ce faire, ouvrez le fichier `OpenXrProgram.cpp` dans Visual Studio et changez la partie où le client est créé dans la fonction `InitARR()` :

```cpp
// 2. Create Client
{
    // Users need to fill out the following with their account data and model
    RR::SessionConfiguration init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
}
```

Plus spécifiquement, modifiez les valeurs suivantes :
* `init.AccountId`, `init.AccountKey` et `init.AccountDomain` pour utiliser les données de votre compte. Consultez le paragraphe sur la façon de [récupérer les informations du compte](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Spécifiez où créer la session de rendu à distance en modifiant la partie région de la chaîne `init.RemoteRenderingDomain` pour les [régions](../../../reference/regions.md) autres que `westus2`, par exemple `"westeurope.mixedreality.azure.com"`.
* Vous pouvez aussi remplacer `m_sessionOverride` par un ID de session existant. Des sessions peuvent être créées en dehors de cet exemple, en utilisant le [script PowerShell](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) ou l’[API REST de session](../../../how-tos/session-rest-api.md) directement.
La création d’une session en dehors de l’exemple est recommandée quand celui-ci doit s’exécuter plusieurs fois. Si aucune session n’est transmise, l’exemple crée une nouvelle session à chaque démarrage, ce qui peut prendre plusieurs minutes.

L’application peut maintenant être compilée.

## <a name="launch-the-application"></a>Lancer l’application

1. Connectez l’appareil HoloLens à votre PC avec un câble USB.
1. Activez l’appareil HoloLens et attendez que le menu de démarrage apparaisse.
1. Démarrez le débogueur dans Visual Studio (F5). Il déploie automatiquement l’application sur l’appareil.

L’exemple d’application doit être lancé, et un volet de texte doit s’afficher pour vous informer de l’état actuel de l’application. L’état au moment du démarrage est soit le démarrage d’une nouvelle session, soit la connexion à une session existante. Une fois le chargement du modèle terminé, le modèle de moteur intégré s’affiche juste à hauteur de tête. En ce qui concerne l’occlusion, le modèle de moteur interagit correctement avec les cubes en rotation qui sont rendus localement.

Si vous souhaitez relancer l’exemple, vous le trouverez également dans le menu Démarrer de l’appareil HoloLens, mais notez qu’un ID de session ayant expiré peut y être compilé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez réussi à compiler et à déployer un exemple OpenXR natif avec des capacités ARR, vous pouvez commencer à améliorer l’exemple en ajoutant du code d’interaction pour les objets distants :

> [!div class="nextstepaction"]
> [Concepts : Entités](../../../concepts/entities.md)

Vous pouvez également injecter votre propre modèle converti :

> [!div class="nextstepaction"]
> [Démarrage rapide : Convertir un modèle](../../../quickstarts/convert-model.md)
