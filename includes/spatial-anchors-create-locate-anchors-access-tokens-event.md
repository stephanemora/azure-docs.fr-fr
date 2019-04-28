---
ms.openlocfilehash: a673f02af9d92b7135e47961f13e0899a1f0e02f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232405"
---
Si un jeton d’accès n’est pas défini, vous devez gérer le `TokenRequired` événement, ou implémentez le `tokenRequired` méthode sur le protocole de délégué.

Vous pouvez gérer l’événement de façon synchrone en définissant la propriété sur les arguments d’événement.
