---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e3762744d2d1f021531694b2d9a3f75ca1c3ed49
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291059"
---
### <a name="run-the-service"></a>Exécuter le service

1. Appuyez sur F5 (ou tapez `azds up` dans la fenêtre du terminal) pour exécuter le service. Le service s’exécute automatiquement dans le nouvel espace sélectionné `default/scott`. 
1. Vous pouvez vérifier que votre service s’exécute dans son propre espace en réexécutant `azds list-up`. Tout d’abord, vous remarquez qu’une instance de `mywebapi` est en cours d’exécution dans l’espace `default/scott` (la version exécutée dans l’espace `default` reste active mais n’apparaît pas). Si vous exécutez `azds list-uris`, vous remarquerez que l’URL du point d’accès de `webfrontend` commence par le texte « scott.s. ». Elle est unique dans l’espace `default/scott`. Cette URL spéciale signifie que les demandes envoyées à l’« URL scott » tenteront d’abord d’accéder aux services de l’espace `default/scott` ; en cas d’échec, elles reviendront aux services de l’espace `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Cette fonctionnalité intégrée d’Azure Dev Spaces permet de tester du code dans un environnement partagé, sans que chaque développeur ait à recréer toute la pile de services dans son espace. Ce routage nécessite que le code de votre application transmette les en-têtes de propagation, comme indiqué à l’étape précédente de ce guide.

### <a name="test-code-in-a-space"></a>Tester le code dans un espace
Pour tester votre nouvelle version de `mywebapi` avec `webfrontend`, ouvrez votre navigateur et accédez à l’URL du point d’accès public de `webfrontend`, puis à la page À propos de. Votre nouveau message doit être affiché.

Maintenant, supprimez la partie « scott.s » de l’URL, puis actualisez le navigateur. Vous devriez revenir à l’ancien comportement (la version `mywebapi` s’exécute dans `default`).
