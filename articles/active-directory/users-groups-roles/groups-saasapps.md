---
title: Utilisation d’un groupe pour gérer l’accès aux applications SaaS - Azure Active Directory | Microsoft Docs
description: Comment utiliser les groupes dans Azure Active Directory pour attribuer l’accès à des applications SaaS intégrées à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 26a726f200ea64895a7ebf289c030089c7de5555
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506258"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Utilisation d’un groupe pour gérer l’accès aux applications SaaS

En utilisant Azure Active Directory (Azure AD) avec une licence Azure AD Premium ou Azure AD Basic, vous pouvez utiliser les groupes pour attribuer l’accès à une application SaaS intégrée à Azure AD. Par exemple, si vous souhaitez attribuer l’accès à différentes applications SaaS au service marketing, vous pouvez créer un groupe comportant les utilisateurs du service marketing, puis allouer ce groupe aux cinq applications SaaS dont il a besoin. De cette manière, vous pouvez gagner du temps en gérant de manière centralisée l’adhésion du service marketing. Ensuite, les utilisateurs sont affectés à l’application lorsqu’ils sont ajoutés en tant que membres du groupe marketing. Leurs attributions sont retirées de l’application lors de leur suppression du groupe marketing. Cette fonctionnalité peut être utilisée avec des centaines d’applications pouvant être ajoutées à partir de la galerie d’applications Azure AD.

> [!IMPORTANT]
> Vous pouvez utiliser cette fonctionnalité uniquement après avoir commencé un essai gratuit d’Azure AD Premium ou après avoir acheté des licences Azure AD Premium ou Azure AD Basic. L’attribution basée sur le groupe est prise en charge uniquement pour les groupes de sécurité. Les appartenances à des groupes imbriquées ne sont pas prises en charge pour l’affectation basée sur le groupe à des applications à ce stade.

**Pour attribuer à un utilisateur ou groupe l’accès à une application SaaS**

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Applications d’entreprise**.
2. Sélectionnez une application que vous avez ajoutée à partir de la galerie d’applications pour l’ouvrir.
3. Sélectionnez **Utilisateurs et groupes**, puis **Ajouter un utilisateur**.
4. Dans **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes** pour ouvrir la liste de sélection **Utilisateurs et groupes**.
6. Sélectionnez autant de groupes ou utilisateurs que vous le souhaitez, puis cliquez ou appuyez sur **Sélectionner** pour les ajouter à la liste **Ajouter une attribution**. Vous pouvez également affecter un rôle à un utilisateur à ce stade.
7. Sélectionnez **Affecter** pour affecter les utilisateurs ou groupes à l’application d’entreprise sélectionnée.

### <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](groups-settings-cmdlets.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
