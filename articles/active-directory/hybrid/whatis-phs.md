---
title: Qu’est-ce que la synchronisation de hachage de mot de passe avec Azure AD ? | Microsoft Docs
description: Décrit la synchronisation de hachage de mot de passe.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "66253857"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Qu’est-ce que la synchronisation de hachage de mot de passe avec Azure AD ?
La synchronisation de hachage de mot de passe est l’une des méthodes de connexion utilisées pour accomplir l’identité hybride. Azure AD Connect synchronise le hachage du mot de passe d’un utilisateur entre une instance Active Directory locale et une instance Azure AD basée sur le cloud.

La synchronisation de hachage de mot de passe est une extension de la fonctionnalité de synchronisation d’annuaires implémentée par la synchronisation Azure AD Connect. Vous pouvez utiliser cette fonctionnalité pour vous connecter à des services Azure AD comme Office 365. Vous vous connectez au service à l’aide du mot de passe que vous utilisez pour vous connecter à votre instance locale d’Active Directory.

![Qu’est-ce qu’Azure AD Connect ?](./media/how-to-connect-password-hash-synchronization/arch1.png)

La synchronisation de hachage de mot de passe est utile car elle réduit à un seul le nombre de mots de passe que vos utilisateurs doivent tenir à jour. La synchronisation de hachage de mot de passe peut :

* Améliorer la productivité de vos utilisateurs.
* Réduire vos coûts de support technique.  

Si vous le souhaitez, vous pouvez configurer la synchronisation de hachage de mot de passe en tant que dispositif de secours si vous choisissez d’utiliser la [Fédération avec Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) comme méthode de connexion.

Pour utiliser la synchronisation de hachage de mot de passe dans votre environnement, vous devez :

* Installer Azure AD Connect.  
* Configurez la synchronisation d’annuaire entre votre instance Active Directory locale et votre instance Azure Active Directory.
* Activer la synchronisation de hachage de mot de passe.



Pour plus d’informations, consultez [Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md)
- [Présentation d’Azure AD Connect et Connect Health](whatis-azure-ad-connect.md)
- [Qu’est-ce que l’authentification directe (PTA) ?](how-to-connect-pta.md)
- [Qu’est-ce que la fédération ?](whatis-fed.md)
- [Qu’est-ce que l’authentification unique ?](how-to-connect-sso.md)
- [Fonctionnement de la synchronisation de hachage de mot de passe](how-to-connect-password-hash-synchronization.md)
