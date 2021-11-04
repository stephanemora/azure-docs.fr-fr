---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 0a29cf2d195e1fdd0bda03f718b1693fffaa5e9c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007597"
---
Le flux de déconnexion implique les étapes suivantes :

1. Dans l’application, les utilisateurs se déconnectent.
1. L’application efface ses objets de session, et la bibliothèque d’authentification efface son cache de jeton.
1. L’application dirige les utilisateurs vers le point de terminaison de déconnexion Azure AD B2C pour mettre fin à la session Azure AD B2C.
1. Les utilisateurs sont redirigés vers l’application.
