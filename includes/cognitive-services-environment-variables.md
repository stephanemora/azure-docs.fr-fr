---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274668"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurer une variable d’environnement pour l’authentification

Les applications ont besoin d’authentifier l’accès à Cognitive Services qu’elles utilisent. Pour l’authentification, nous vous recommandons de créer une variable d’environnement destinée à stocker les clés pour vos ressources Azure. 

Après avoir obtenu votre clé, écrivez-la dans une variable d’environnement sur la machine locale exécutant l’application. Pour définir la variable d’environnement, ouvrez une fenêtre de console et suivez les instructions pour votre système d’exploitation. Remplacez `your-key` par l’une des clés de votre ressource.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Après avoir ajouté la variable d’environnement, vous devrez peut-être redémarrer tous les programmes en cours d’exécution qui devront la lire, y compris la fenêtre de console. Par exemple, si vous utilisez Visual Studio comme éditeur, redémarrez Visual Studio avant d’exécuter l’exemple.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil bash_profile, et ajoutez la variable d’environnement :

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.

***