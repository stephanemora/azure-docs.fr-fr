---
title: Gestion des unités administratives (préversion) - Azure Active Directory | Microsoft Docs
description: Utilisation d'unités administratives pour une délégation plus précise des autorisations dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736784"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Gestion des unités administratives dans Azure Active Directory (préversion publique)

Cet article décrit les unités administratives dans Azure Active Directory (Azure AD), qui est un conteneur de ressources qui peut être utilisé pour déléguer des autorisations d’administration sur des sous-ensembles d’utilisateurs et appliquer des stratégies à un sous-ensemble d’utilisateurs. Dans Azure AD, les unités administratives permettent aux administrateurs centraux de déléguer des autorisations aux administrateurs régionaux ou de définir une stratégie à un niveau granulaire.

Cela est utile dans les organisations disposant de divisions indépendantes, par exemple, une grande université qui se compose de nombreuses écoles autonomes (école de commerce, école d’ingénieurs, etc.) qui sont indépendantes les unes des autres. Ces divisions comportent leurs propres administrateurs informatiques qui contrôlent les accès, gèrent les utilisateurs et définissent des stratégies pour leur département en particulier. Les administrateurs centraux souhaitent accorder à ces administrateurs régionaux des autorisations relatives aux utilisateurs de leurs divisions et non à l’ensemble d’entre eux. Plus précisément, en s’appuyant sur le même exemple, un administrateur central peut créer une unité administrative pour une école particulière (une école de commerce) et la remplir uniquement par les utilisateurs de cette école de commerce. De son côté, l’administrateur central peut attribuer un rôle défini au personnel informatique de l’école de commerce. Concrètement, il lui donne des autorisations administratives relatives à l’unité administrative de l’école de commerce uniquement.

> [!IMPORTANT]
> Pour utiliser des unités administratives, l’administrateur de l’unité administrative doit disposer d’une licence Azure Active Directory Premium. Pour plus d'informations, consultez la section [Prise en main d’Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>

Du point de vue de l'administrateur central, une unité administrative est un objet de répertoire qui peut être créé et rempli par des ressources. **Dans cette version préliminaire, ces ressources peuvent être uniquement des utilisateurs.** Une fois créée et remplie, l'unité administrative peut être utilisée comme un cadre permettant de limiter l’autorisation accordée aux ressources contenues dans l’unité administrative.

## <a name="managing-administrative-units"></a>Gestion des unités administratives

Dans cette version préliminaire, vous pouvez créer et gérer des unités administratives à l'aide du module Azure Active Directory pour les cmdlets Windows PowerShell. Pour en savoir plus sur la façon de procéder, voir [Utilisation des unités administratives](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

Pour plus d’informations sur la configuration logicielle requise et sur l’installation du module Azure AD, ainsi que les cmdlets du module Azure AD pour la gestion d’unités administratives, et notamment la syntaxe, les descriptions de paramètres et des exemples, voir [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Étapes suivantes

[Éditions d’Azure Active Directory](../fundamentals/active-directory-whatis.md)
