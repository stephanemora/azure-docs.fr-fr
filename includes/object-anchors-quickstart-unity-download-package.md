---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044633"
---
L’étape suivante consiste à télécharger le package Azure Object Anchors pour Unity.

# <a name="download-with-web-browser"></a>[Télécharger avec le navigateur web](#tab/unity-package-web-ui)

Localisez [ici](https://aka.ms/aoa/unity-sdk/package) le package Azure Object Anchors pour Unity. Sélectionnez la version souhaitée et téléchargez le package à l’aide du bouton **Télécharger**.

# <a name="download-with-npm"></a>[Télécharger avec NPM](#tab/unity-package-npm)

Cette étape nécessite que <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> soit installé et disponible.

Exécutez la commande suivante en remplaçant `<version_number>` par la version d’Azure Object Anchors que vous souhaitez télécharger :

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Pour lister les versions disponibles du package Azure Object Anchors, exécutez la commande suivante :
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Le package Azure Object Anchors sera téléchargé dans le dossier où vous avez exécuté la commande.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installer avec Mixed Reality Feature Tool (bêta)](#tab/unity-package-mixed-reality-feature-tool)

Passez à l’étape suivante. Vous utiliserez <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">Mixed Reality Feature Tool</a> dans une étape ultérieure.

---
