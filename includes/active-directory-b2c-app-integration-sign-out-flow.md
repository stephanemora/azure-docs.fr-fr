---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f4677d9800f0c94d9071838b9addbeb3cd1231c9
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071326"
---
Le flux de déconnexion implique les étapes suivantes :

1. Dans l’application, l’utilisateur sélectionne **Déconnexion**.
1. L’application efface ses cookies de session, et la bibliothèque d’authentification efface son cache de jetons.
1. L’application redirige l’utilisateur vers le point de terminaison de déconnexion d’Azure AD B2C pour terminer la session Azure AD B2C.
1. L’utilisateur est redirigé vers l’application.