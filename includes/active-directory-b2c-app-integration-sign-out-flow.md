---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: f21c0a37d169885e60ae9dea34663dc0a86e161f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801212"
---
Le flux de déconnexion implique les étapes suivantes :

1. Dans l’application, les utilisateurs se déconnectent.
1. L’application efface ses objets de session, et la bibliothèque d’authentification efface son cache de jetons.
1. L’application mène les utilisateurs vers le point de terminaison de déconnexion d’Azure AD B2C pour terminer la session Azure AD B2C.
1. Les utilisateurs sont redirigés vers l’application.
