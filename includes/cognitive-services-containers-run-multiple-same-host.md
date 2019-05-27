---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124315"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Exécuter plusieurs conteneurs sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port exposé différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Vous pouvez avoir ce conteneur, et un autre conteneur Azure Cognitive Services qui s’exécutent ensemble sur l’hôte. Vous pouvez également disposer de plusieurs conteneurs du même conteneur Cognitive Services en cours d’exécution.
