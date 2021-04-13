---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076693"
---
L’étape suivante consiste à télécharger les packages Azure Spatial Anchors pour Unity. 

> [!WARNING]
> Le kit SDK ASA 2.7.0 est la version minimale prise en charge. Si vous utilisez Unity 2020, le kit SDK ASA 2.9.0 est la version minimale prise en charge.

Pour utiliser Azure Spatial Anchors dans Unity, vous devez télécharger le package principal ainsi qu’un package spécifique pour chaque plateforme (Android/iOS/HoloLens) que vous envisagez de prendre en charge.

| Plateforme | Nom du package                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<numéro_de_version> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<numéro_de_version> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<numéro_de_version>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<numéro_de_version> |

# <a name="download-with-web-browser"></a>[Télécharger avec le navigateur web](#tab/unity-package-web-ui)

Localisez [ici](https://aka.ms/aoa/unity-sdk/package) le package Azure Spatial Anchors principal pour Unity. Sélectionnez la version souhaitée et téléchargez le package à l’aide du bouton **Télécharger**. Répétez cette étape afin de télécharger le package pour chaque plateforme que vous envisagez de prendre en charge.

# <a name="download-with-npm"></a>[Télécharger avec NPM](#tab/unity-package-npm)

Cette étape nécessite que <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> soit installé et disponible.

Exécutez la commande suivante en remplaçant `<version_number>` par la version d’Azure Spatial Anchors que vous souhaitez télécharger dans le dossier actuel :

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Pour lister les versions disponibles du package Azure Spatial Anchors, exécutez la commande suivante :
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Le package Azure Spatial Anchors principal est téléchargé dans le dossier où vous avez exécuté la commande. Répétez cette étape afin de télécharger le package pour chaque plateforme que vous envisagez de prendre en charge.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installer avec Mixed Reality Feature Tool (bêta)](#tab/unity-package-mixed-reality-feature-tool)

Passez à l’étape suivante. Vous utiliserez <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> dans une étape ultérieure.

---