---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 88d0292048edcc6846d0e1637b79f115d1321e70
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369107"
---
Dans le modèle multi-locataire, la structure des ressources d’application logique ne peut inclure qu’un seul workflow. En raison de cette relation un-à-un, l’application logique et le workflow sont souvent considérés et référencés comme des synonymes. Toutefois, dans le modèle à locataire unique, la structure des ressources d’application logique peut inclure plusieurs workflows. Cette relation un-à-plusieurs signifie que dans la même application logique, les workflows peuvent partager et réutiliser d’autres ressources. Les workflows de la même application logique et du même locataire offrent également des performances améliorées en raison de cette location partagée et de la proximité les unes des autres. Cette structure des ressources a la même apparence et le même fonctionnement qu’Azure Functions où une application de fonction peut héberger de nombreuses fonctions.

Pour obtenir plus d’informations et pour connaître les bonnes pratiques relatives à l’organisation des workflows, aux performances et à la mise à l’échelle dans votre application logique, passez en revue les mêmes [conseils sur Azure Functions](../articles/azure-functions/functions-best-practices.md) que ceux que vous pouvez généralement appliquer à des applications logiques Azure à locataire unique.