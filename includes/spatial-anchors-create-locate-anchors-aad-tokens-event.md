---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110691"
---
Comme avec les jetons d’accès, si un jeton Azure AD n’est pas défini, vous devez gérer l’événement TokenRequired ou implémenter la méthode tokenRequired sur le protocole de délégué.

Vous pouvez gérer l’événement de façon synchrone en définissant la propriété sur les arguments d’événement.
