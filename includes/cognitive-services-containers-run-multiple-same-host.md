---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000541"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Exécuter plusieurs conteneurs sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port exposé différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Vous pouvez avoir ce conteneur, et un autre conteneur Azure Cognitive Services qui s’exécutent ensemble sur l’hôte. Vous pouvez également disposer de plusieurs conteneurs du même conteneur Cognitive Services en cours d’exécution.
