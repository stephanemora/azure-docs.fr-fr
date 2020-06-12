---
title: Installer le package Remote Rendering pour Unity
description: Explique comment installer les DLL clientes Remote Rendering pour Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3375b28d94956d5c368db4bf3026bdf52ee2d58e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021141"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installer le package Remote Rendering pour Unity

Azure Remote Rendering utilise un package Unity pour encapsuler l’intégration dans Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gérer les packages Remote Rendering dans Unity

Les packages Unity sont des conteneurs qui peuvent être gérés par le biais de l’outil [Package Manager](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html) de Unity.
Ce package contient la totalité de l’API C# ainsi que tous les fichiers binaires de plug-in nécessaires à l’utilisation d’Azure Remote Rendering avec Unity.
Le package se nomme **com.microsoft.azure.remote-rendering**, conformément au schéma de nommage des packages de Unity.

Le package ne fait pas partie du [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering) et n’est pas disponible dans le registre de packages interne de Unity. Pour l’ajouter à un projet, vous devez modifier manuellement le fichier `manifest.md` du projet pour ajouter ce qui suit :
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Après avoir ajouté le package, vous pouvez utiliser l’outil Package Manager de Unity pour vous assurer d’avoir la dernière version.
Pour obtenir des instructions plus complètes, consultez le [Tutoriel : Configuration d’un projet Unity en partant de zéro](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Pipelines de rendu Unity

Remote Rendering fonctionne avec **:::no-loc text="Universal render pipeline":::** et **:::no-loc text="Standard render pipeline":::** . Pour des raisons de performances, il est recommandé d’utiliser le pipeline de rendu universel.

Pour utiliser **:::no-loc text="Universal render pipeline":::** , vous devez installer son package dans Unity. Vous pouvez effectuer cette opération à l’aide de l’interface utilisateur **Package Manager** de Unity (nom du package **Universal RP**, version 7.2.1 ou ultérieure), ou du fichier `Packages/manifest.json` comme décrit dans le [tutoriel sur la configuration de projet Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Étapes suivantes

* [Composants et objets de jeu Unity](objects-components.md)
* [Tutoriel : Configuration d’un projet Unity en partant de zéro](../../tutorials/unity/project-setup.md)
