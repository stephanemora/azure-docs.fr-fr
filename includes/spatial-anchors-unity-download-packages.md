---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719760"
---
Pour télécharger les packages requis, vous devez installer <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a>.

Exécutez la commande suivante en remplaçant `<version_number>` par la version d’Azure Spatial Anchors que vous souhaitez télécharger dans le dossier actuel :

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Pour lister les versions disponibles du package Azure Spatial Anchors, exécutez la commande suivante :
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> Le kit SDK ASA 2.7.0 est la version minimale prise en charge. Si vous utilisez Unity 2020, le kit SDK ASA 2.9.0 est la version minimale prise en charge.

Le package Azure Spatial Anchors principal est téléchargé dans le dossier où vous avez exécuté la commande.

Répétez cette étape afin de télécharger le package pour chaque plateforme (Android/iOS/HoloLens) à prendre en charge dans votre projet.

| Plateforme | Nom du package                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<numéro_de_version> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<numéro_de_version>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<numéro_de_version> |