---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632352"
---
Comme avec les jetons d’accès, si un jeton Azure AD n’est pas défini, vous devez gérer l’événement TokenRequired ou implémenter la méthode tokenRequired sur le protocole de délégué.

Vous pouvez gérer l’événement de façon synchrone en définissant la propriété sur les arguments d’événement.
