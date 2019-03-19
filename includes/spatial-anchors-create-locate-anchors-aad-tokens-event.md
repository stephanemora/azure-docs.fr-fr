---
ms.openlocfilehash: 307e4d4e21eca3a568a7e4761058b67aa7d5236f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751848"
---
Comme avec les jetons d’accès, si un jeton AAD n’est pas défini, vous devez gérer l’événement TokenRequired ou implémenter la méthode tokenRequired sur le protocole de délégué.

Vous pouvez gérer l’événement de façon synchrone en définissant la propriété sur les arguments d’événement.
