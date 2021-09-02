---
title: Configurer des identités managées dans Azure Virtual Desktop – Azure
description: Comment configurer des identités managées pour vos clients dans Azure Virtual Desktop avec Azure AD, Azure AD DS ou AD DS.
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531494"
---
# <a name="set-up-managed-identities"></a>Configurer des identités managées

Étant donné qu’Azure Virtual Desktop ne prend actuellement pas en charge les profils externes ni « identités », vos utilisateurs ne peuvent pas accéder aux applications que vous hébergez avec leurs propres informations d’identification d’entreprise. Au lieu de cela, vous devez créer des identités pour eux dans le domaine Active Directory que vous utilisez pour le streaming d’application à distance et la synchronisation des objets utilisateur sur le locataire Azure Active Directory (Azure AD) associé.

Dans cet article, nous expliquons comment vous pouvez gérer les identités d’utilisateurs pour fournir un environnement sécurisé à vos clients. Nous abordons également les différentes parties qui composent une identité.

## <a name="requirements"></a>Configuration requise

Les identités que vous créez doivent respecter les conditions suivantes :

- Les identités doivent être des [identités hybrides](../../active-directory/hybrid/whatis-hybrid-identity.md), ce qui signifie qu’elles existent à la fois dans [Active Directory (AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10)) et dans [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Vous pouvez utiliser [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) ou [Azure Active Directory Domain Services (Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds) pour créer ces identités. Pour en savoir plus sur chaque méthode, consultez [Comparer les solutions d’identité](../../active-directory-domain-services/compare-identity-solutions.md).
- Vous devez placer les utilisateurs de différentes organisations dans des locataires Azure AD distincts pour empêcher les violations de la sécurité. Nous vous recommandons de créer un domaine Active Directory et un locataire Azure Active Directory par organisation cliente. Ce locataire doit disposer de son propre abonnement Azure AD DS ou AD DS dédié à ce client.

Les deux sections suivantes vous expliquent comment créer des identités avec AD DS et Azure AD DS. Pour suivre [les instructions de sécurité pour les applications inter-organisations](security.md), vous devez répéter le processus pour chaque client.

## <a name="managing-users-with-active-directory-domain-services"></a>Gestion des utilisateurs avec Active Directory Domain Services

Dans cette méthode, vous allez configurer des identités hybrides à l’aide d’un contrôleur de domaine Active Directory pour gérer des identités d’utilisateurs et les synchroniser avec Azure AD.

Cette méthode implique la configuration de contrôleurs de domaine Active Directory pour gérer les identités d’utilisateurs et la synchronisation des utilisateurs avec Azure AD pour créer des identités hybrides. Ces identités peuvent ensuite être utilisées pour accéder aux applications hébergées dans Azure Virtual Desktop. Dans cette configuration, les utilisateurs sont synchronisés depuis Active Directory sur Azure AD et les machines virtuelles hôtes de session sont jointes au domaine AD DS.

Pour configurer une identité dans AD DS :

1. [Créez un locataire Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) et un abonnement pour votre client.

2. [Installez Active Directory Domain Services](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-) sur la machine virtuelle Windows Server que vous utilisez pour le client.

3. Installez et configurez [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md) sur une machine virtuelle distincte jointe à un domaine pour synchroniser les comptes d’utilisateur depuis Active Directory sur Azure Active Directory.

4. Si vous prévoyez de gérer les machines virtuelles à l’aide d’Intune, activez les [appareils joints à Azure AD Hybride](../../active-directory/devices/hybrid-azuread-join-plan.md) avec Azure AD Connect.

5. Une fois que vous avez configuré l’environnement, [créez de nouveaux utilisateurs](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10)) dans Active Directory. Ces utilisateurs doivent être synchronisés automatiquement avec Azure AD.

6. Quand vous déployez des hôtes de session dans votre pool hôte, utilisez le nom de domaine Active Directory pour joindre les machines virtuelles et assurez-vous que les hôtes de session disposent d’une ligne de vue sur le contrôleur de domaine.

Cette configuration vous donne davantage de contrôle sur votre environnement, mais sa complexité peut le rendre plus difficile à gérer. Toutefois, cette option vous permet de fournir des applications basées sur Azure AD à vos utilisateurs. Cela vous permet également de gérer les machines virtuelles de vos utilisateurs avec Intune.

## <a name="managing-users-with-azure-active-directory-domain-services"></a>Gestion des utilisateurs avec Azure Active Directory Domain Services

Les identités Azure AD DS sont stockées dans un service PaaS (Platform as a Service) Active Directory managé par Microsoft, où Microsoft gère deux contrôleurs de domaine Active Directory qui permettent aux utilisateurs d’utiliser AD DS dans leurs abonnements Azure. Dans cette configuration, les utilisateurs sont synchronisés depuis Azure AD sur Azure AD DS et les hôtes de session sont joints au domaine Azure AD DS. Les identités Azure AD DS sont plus faciles à gérer, mais elles n’offrent pas autant de contrôle que les identités AD DS ordinaires. Vous pouvez uniquement joindre les machines virtuelles Azure Virtual Desktop au domaine Azure AD DS. Vous ne pouvez pas les gérer avec Intune.

Pour créer une identité avec Azure AD DS :

1. [Créez un locataire Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) et un abonnement pour votre client.

2. [Déployez Azure AD Directory Services](../../active-directory-domain-services/tutorial-create-instance.md) dans l’abonnement de l’utilisateur.

3. Une fois que vous avez terminé la configuration de l’environnement, [créez des utilisateurs](../../active-directory/fundamentals/add-users-azure-active-directory.md) dans Azure Active Directory. Ces objets utilisateur sont automatiquement synchronisés avec Azure AD DS.

4. Lors du déploiement d’hôtes de session dans un pool hôte, utilisez le nom de domaine Azure AD DS pour joindre les machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les considérations de sécurité pour la configuration des identités et des locataires, consultez les [Instructions de sécurité pour les applications inter-organisations](security.md).
