---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753810"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Plusieurs conteneurs en cours d’exécution sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un autre port. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Remplacez le `<container-registry>` et `<container-name>` avec les valeurs des conteneurs que vous utilisez. Il est inutile d’être le même conteneur. Vous pouvez avoir le conteneur de Face et le conteneur de LUIS qui exécutent ensemble sur l’ordinateur hôte, ou vous pouvez avoir plusieurs conteneurs de visage en cours d’exécution. 

Exécutez le premier conteneur sur le port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Exécutez le second conteneur sur le port 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Chaque conteneur suivant doit être sur un autre port. 
