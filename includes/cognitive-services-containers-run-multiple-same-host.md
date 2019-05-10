---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520874"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Plusieurs conteneurs en cours d’exécution sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un autre port exposé. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Vous pouvez avoir ce conteneur et un autre conteneur de Service COGNITIF qui exécutent ensemble sur l’ordinateur hôte, ou vous pouvez avoir plusieurs conteneurs du même conteneur Cognitive Service en cours d’exécution.
