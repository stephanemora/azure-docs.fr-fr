---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569434"
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

Le package Azure Spatial Anchors principal est téléchargé dans le dossier où vous avez exécuté la commande.

Répétez cette étape afin de télécharger le package pour chaque plateforme (Android/iOS/HoloLens) à prendre en charge dans votre projet.

| Plateforme | Nom du package                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<numéro_de_version> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<numéro_de_version>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<numéro_de_version> |