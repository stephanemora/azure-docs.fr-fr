---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: fdafb1320c40a44bcdd9a365bd53baec431627d3
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071325"
---
Lorsqu’un utilisateur tente de se connecter à votre application, l’application lance une demande d’authentification auprès du point de terminaison d’autorisation via un [flux d’utilisateur](../articles/active-directory-b2c/user-flow-overview.md). Le flux d’utilisateur définit et contrôle l’expérience de l’utilisateur. Une fois que l’utilisateur a terminé le flux d’utilisateur, Azure AD B2C génère un jeton, puis redirige l’utilisateur vers votre application.

Si vous ne l’avez pas déjà fait, [créez un flux d’utilisateur ou une stratégie personnalisée](../articles/active-directory-b2c/add-sign-up-and-sign-in-policy.md).
