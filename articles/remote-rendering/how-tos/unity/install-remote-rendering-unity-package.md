---
title: Installer le package Remote Rendering pour Unity
description: Explique comment installer les DLL clientes Remote Rendering pour Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720820"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installer le package Remote Rendering pour Unity

Azure Remote Rendering utilise un package Unity pour encapsuler l’intégration dans Unity.
Ce package contient la totalité de l’API C# ainsi que tous les fichiers binaires de plug-in nécessaires à l’utilisation d’Azure Remote Rendering avec Unity.
Le package se nomme **com.microsoft.azure.remote-rendering**, conformément au schéma de nommage des packages de Unity.

Vous pouvez choisir l’une des options suivantes pour installer le package Unity.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Installer le package Remote Rendering à l’aide de Mixed Reality Feature Tool

[Mixed Reality Feature Tool](https://aka.ms/MRFeatureToolDocs) ([télécharger](https://aka.ms/mrfeaturetool)) est un outil permettant d’intégrer des packages de fonctionnalités de réalité mixte dans des projets Unity. Le package ne fait pas partie du [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering) et n’est pas disponible dans le registre de packages interne de Unity.

Pour ajouter le package à un projet, vous devez :
1. [Télécharger Mixed Reality Feature Tool](https://aka.ms/mrfeaturetool)
1. Pour utiliser l’outil, suivez les [instructions complètes](https://aka.ms/MRFeatureToolDocs).
1. Dans la page **Discover Features**(Découvrir les fonctionnalités), cochez la case correspondant au package **Microsoft Azure Remote Rendering**, puis sélectionnez la version du package que vous souhaitez ajouter à votre projet

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Pour mettre à jour votre package local, sélectionnez simplement une version plus récente dans Mixed Reality Feature Tool, puis installez-la. La mise à jour du package peut parfois occasionner des erreurs dans la console. Si cela se produit, essayez de fermer, puis de rouvrir le projet.

## <a name="install-remote-rendering-package-manually"></a>Installer manuellement le package Remote Rendering

Pour installer manuellement le package Remote Rendering, vous devez :

1. Télécharger le package à partir du flux NPM de packages Mixed Reality à l’adresse `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry`.
    * Vous pouvez utiliser [NPM](https://www.npmjs.com/get-npm) et exécuter la commande suivante pour télécharger le package dans le dossier actif.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Vous pouvez aussi utiliser le script PowerShell à l’emplacement `Scripts/DownloadUnityPackages.ps1` dans le [dépôt GitHub azure-remote-rendering](https://github.com/Azure/azure-remote-rendering).
        * Remplacez le contenu de `Scripts/unity_sample_dependencies.json` par
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Exécutez la commande suivante dans PowerShell pour télécharger le package dans le répertoire de destination fourni.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Installez le package téléchargé](https://docs.unity3d.com/Manual/upm-ui-tarball.html) avec le gestionnaire de package Unity.

Pour mettre à jour votre package local, il vous suffit de réexécuter la commande correspondante que vous avez utilisée, puis de réimporter le package. La mise à jour du package peut parfois occasionner des erreurs dans la console. Si cela se produit, essayez de fermer, puis de rouvrir le projet.

## <a name="unity-render-pipelines"></a>Pipelines de rendu Unity

Remote Rendering fonctionne avec **:::no-loc text="Universal render pipeline":::** et **:::no-loc text="Standard render pipeline":::** . Pour des raisons de performances, il est recommandé d’utiliser le pipeline de rendu universel.

Pour utiliser **:::no-loc text="Universal render pipeline":::** , vous devez installer son package dans Unity. Vous pouvez effectuer cette opération à l’aide de l’interface utilisateur **Package Manager** de Unity (nom du package **Universal RP**, version 7.3.1 ou ultérieure), ou du fichier `Packages/manifest.json` comme décrit dans le [tutoriel sur la configuration de projet Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Étapes suivantes

* [Composants et objets de jeu Unity](objects-components.md)
* [Tutoriel : Afficher des modèles distants](../../tutorials/unity/view-remote-models/view-remote-models.md)
