---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632361"
---
Une fois votre Observateur est créé, le `AnchorLocated` événement est déclenché pour chaque point d’ancrage demandé. Cet événement se déclenche lorsqu’une ancre est située, ou si le point d’ancrage ne peut pas être localisé. Si cette situation se produit, la raison est indiquée dans l’état. Après le traitement de tous les points d’ancrage pour un observateur, ou introuvable, la `LocateAnchorsCompleted` événement se déclenche.
