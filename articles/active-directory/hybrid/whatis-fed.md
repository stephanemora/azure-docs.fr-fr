---
title: Qu’est-ce que la fédération avec Azure AD ? | Microsoft Docs
description: Décrit la fédération avec Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 278935ae7d29680e4fa741d59370e75ee0e1e93d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197702"
---
# <a name="what-is-federation-with-azure-ad"></a>Qu’est-ce que la fédération avec Azure AD ?

La fédération est une collection de domaines pour lesquels une confiance est établie. Le niveau de confiance peut varier, mais il inclut généralement l’authentification et presque toujours l’autorisation. Une fédération classique peut inclure plusieurs organisations qui ont établi une confiance pour un accès partagé à un ensemble de ressources.

Vous pouvez fédérer votre environnement local avec Azure AD et utiliser cette fédération pour l’authentification et l’autorisation.  Cette méthode de connexion garantit que toutes les opérations d’authentification de l’utilisateur se produisent localement.  Cette méthode permet aux administrateurs d’implémenter des niveaux de contrôle d’accès plus stricts. La fédération est disponible avec AD FS et PingFederate.

![Identité fédérée](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Si vous choisissez d’utiliser la Fédération avec Active Directory Federation Services (AD FS), vous avez la possibilité de configurer la synchronisation de hachage de mot de passe en tant que dispositif de secours en cas de défaillance de votre infrastructure AD FS.


## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que l’identité hybride ?](whatis-phs.md)
- [Présentation d’Azure AD Connect et Connect Health](whatis-azure-ad-connect.md)
- [Qu’est-ce que la synchronisation de hachage de mot de passe ?](whatis-phs.md)
- [Qu’est-ce que la fédération ?](whatis-fed.md)
- [Qu’est-ce que l’authentification unique ?](how-to-connect-sso.md)
- [Fonctionnement de la fédération](how-to-connect-fed-whatis.md)
- [Fédération avec PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
