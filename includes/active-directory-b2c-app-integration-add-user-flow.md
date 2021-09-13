---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: b6b0808f01cc47397c4f2585033733fa7b6ce05a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227755"
---
Lorsqu’un utilisateur tente de se connecter à votre application, l’application lance une requête d’authentification auprès du point de terminaison d’autorisation via un [flux d’utilisateur](../articles/active-directory-b2c/user-flow-overview.md). Le flux d’utilisateur définit et contrôle l’expérience de l’utilisateur. Une fois que l’utilisateur a terminé le flux d’utilisateur, Azure AD B2C génère un jeton, puis redirige l’utilisateur vers votre application.

Si vous ne l’avez pas déjà fait, [créez un flux d’utilisateur ou une stratégie personnalisée](../articles/active-directory-b2c/add-sign-up-and-sign-in-policy.md).
