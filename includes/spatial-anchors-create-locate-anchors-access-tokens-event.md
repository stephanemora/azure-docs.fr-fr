---
ms.openlocfilehash: a673f02af9d92b7135e47961f13e0899a1f0e02f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176762"
---
Si un jeton d’accès n’est pas défini, vous devez gérer l’événement `TokenRequired`, ou implémentez la méthode `tokenRequired` sur le protocole délégué.

Vous pouvez gérer l’événement de façon synchrone en définissant la propriété sur les arguments d’événement.
