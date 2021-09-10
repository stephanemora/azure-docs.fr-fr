---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227684"
---
Le flux de déconnexion implique les étapes suivantes :

1. Dans l’application, les utilisateurs se déconnectent.
1. L’application efface ses objets de session, et la bibliothèque d’authentification efface son cache de jeton.
1. L’application dirige les utilisateurs vers le point de terminaison de déconnexion Azure AD B2C pour mettre fin à la session Azure AD B2C.
1. Les utilisateurs sont redirigés vers l’application.
