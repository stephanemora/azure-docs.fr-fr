---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936395"
---
### <a name="run-the-service"></a>Exécuter le service

1. Appuyez sur F5 (ou tapez `azds up` dans la fenêtre du terminal) pour exécuter le service. Le service s’exécute automatiquement dans le nouvel espace sélectionné `default/scott`. 
1. Vous pouvez vérifier que votre service s’exécute dans son propre espace en réexécutant `azds list`. Tout d’abord, vous remarquez qu’une instance de `mywebapi` est en cours d’exécution dans l’espace `default/scott` (la version exécutée dans l’espace `default` reste active mais n’apparaît pas). Ensuite, l’URL du point d’accès de `webfrontend` commence par le texte « scott.s. ». Elle est unique dans l’espace `default/scott`. Cette URL spéciale signifie que les demandes envoyées à l’« URL scott » tenteront d’abord d’accéder aux services de l’espace `default/scott` ; en cas d’échec, elles reviendront aux services de l’espace `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Cette fonctionnalité intégrée d’Azure Dev Spaces permet de tester du code dans un environnement partagé, sans que chaque développeur ait à recréer toute la pile de services dans son espace. Ce routage nécessite que le code de votre application transmette les en-têtes de propagation, comme indiqué à l’étape précédente de ce guide.

### <a name="test-code-in-a-space"></a>Tester le code dans un espace
Pour tester votre nouvelle version de `mywebapi` avec `webfrontend`, ouvrez votre navigateur et accédez à l’URL du point d’accès public de `webfrontend`, puis à la page À propos de. Votre nouveau message doit être affiché.

Maintenant, supprimez la partie « scott.s » de l’URL, puis actualisez le navigateur. Vous devriez revenir à l’ancien comportement (la version `mywebapi` s’exécute dans `default`).
