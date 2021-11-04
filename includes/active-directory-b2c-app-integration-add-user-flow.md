---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: f2d96d8babc11034769258b5ea63b8a3d23fdc5b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007564"
---
Lorsqu’un utilisateur tente de se connecter à votre application, l’application lance une requête d’authentification auprès du point de terminaison d’autorisation via un [flux d’utilisateur](../articles/active-directory-b2c/user-flow-overview.md). Le flux d’utilisateur définit et contrôle l’expérience de l’utilisateur. Une fois que l’utilisateur a terminé le flux d’utilisateur, Azure AD B2C génère un jeton, puis redirige l’utilisateur vers votre application.

Si vous ne l’avez pas déjà fait, [créez un flux d’utilisateur ou une stratégie personnalisée](../articles/active-directory-b2c/add-sign-up-and-sign-in-policy.md).
