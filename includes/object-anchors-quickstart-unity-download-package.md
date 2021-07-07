---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 06/10/2021
ms.author: crtreasu
ms.openlocfilehash: 8ad55355307cfc8656b6f6bf9b074d90daf7e2e8
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006890"
---
L’étape suivante consiste à télécharger le package Azure Object Anchors pour Unity.

# <a name="download-with-web-browser"></a>[Télécharger avec le navigateur web](#tab/unity-package-web-ui)

Repérez le package Azure Object Anchors pour Unity (`com.microsoft.azure.object-anchors.runtime`) [ici](https://aka.ms/aoa/unity-sdk/package). Sélectionnez la version souhaitée et téléchargez le package à l’aide du bouton **Télécharger**.

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

Passez à l’étape suivante. Vous utiliserez <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> dans une étape ultérieure.

---