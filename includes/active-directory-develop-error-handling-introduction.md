---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760686"
---
Cet article donne une vue d’ensemble des différents types d’erreurs et des recommandations pour la gestion des erreurs de connexion courantes.

## <a name="msal-error-handling-basics"></a>Notions de base de la gestion des erreurs MSAL

Dans Microsoft Authentication Library (MSAL), les exceptions sont destinées aux développeurs d’applications à des fins de résolution de problèmes. Elles n’ont pas vocation à être vues par les utilisateurs finaux. Les messages liés aux exceptions ne sont pas localisés.

Quand vous traitez les exceptions et les erreurs, vous pouvez utiliser le type d’exception lui-même et le code d’erreur pour les distinguer.  Pour obtenir la liste des codes d’erreur, consultez [Codes d’erreur d’authentification et d’autorisation Azure AD](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Durant la connexion, vous pouvez rencontrer des erreurs concernant les consentements, l’accès conditionnel (MFA, gestion des appareils, restrictions basées sur l’emplacement), l’émission et l’échange de jetons, et les propriétés utilisateur.

La section suivante fournit plus d’informations sur la gestion des erreurs pour votre application.