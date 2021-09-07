---
title: Dépendances Linux pour le micro-agent (préversion)
description: Cet article décrit les différentes dépendances du système d’exploitation Linux pour le micro-agent Defender pour IoT.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: e878052e534ce7740fff1fdd462d2c95fcb11609
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481431"
---
# <a name="micro-agent-linux-dependencies-preview"></a>Dépendances Linux pour le micro-agent (préversion)

Cet article décrit les différentes dépendances du système d’exploitation Linux pour le micro-agent Defender pour IoT. 

## <a name="linux-dependencies"></a>Dépendances Linux

Le tableau ci-dessous indique les dépendances Linux pour chaque composant. 

| Composant | Dépendance | Type | Requis par le SDK IoT | Notes |
|--|--|--|--|--|
| **Base** |  |  |  |  |
|  | libcurl-openssl (libcurl) | Bibliothèque | ✔ |  |
|  | libssl | Bibliothèque | ✔ |  |
|  | uuid | Bibliothèque | ✔ |  |
|  | pthread | Indicateur de compilation ulibc | ✔ |  |
|  | libuv1 | Bibliothèque |  |  |
|  | sudo | Paquet |  |  |
|  | uuid-runtime | Paquet |  |  |
| **Collecteur Informations système** |  |  |  |  |
|  | uname | Appel système |  |  |
| **Collecteur Base de référence** |  |  |  |  |
|  | BusyBox | Indicateur de compilation Linux |  |  |
|  | Bash | Indicateur de compilation Linux |  |  |
| **Collecteur Processus** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | Configuration du noyau |  |  |
|  | CONFIG_PROC_EVENTS=y | Configuration du noyau |  |  |
| **Collecteur Réseau** |  |  |  |  |
|  | libpcap | Bibliothèque |  |  |
|  | CONFIG_PACKET=y | Configuration du noyau |  |  |
|  | CONFIG_NETFILTER =y | Configuration du noyau |  | Facultatif – Amélioration des performances |

## <a name="next-steps"></a>Étapes suivantes

[Installer le micro-agent Defender pour IoT (préversion)](quickstart-standalone-agent-binary-installation.md).