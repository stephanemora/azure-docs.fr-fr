---
title: Gestion des unités administratives (préversion) - Azure AD | Microsoft Docs
description: Utilisation d'unités administratives pour une délégation plus précise des autorisations dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028411"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestion des unités administratives dans Azure Active Directory (préversion)

Cet article décrit les unités administratives dans Azure Active Directory (Azure AD). Une unité administrative est une ressource Azure AD qui peut être un conteneur pour d’autres ressources Azure AD. Dans cette préversion, ces ressources peuvent être uniquement des utilisateurs. Par exemple, un administrateur de compte d’utilisateur étendu à l’unité administrative peut mettre à jour des informations de profil, réinitialiser des mots de passe et attribuer des licences aux utilisateurs uniquement dans leur unité administrative.

Vous pouvez utiliser des unités administratives pour déléguer des autorisations administratives à des sous-ensembles d’utilisateurs et appliquer des stratégies à un sous-ensemble d’utilisateurs. Vous pouvez utiliser des unités administratives pour déléguer des autorisations à des administrateurs régionaux ou définir une stratégie à un niveau précis.

## <a name="deployment-scenario"></a>Scénario de déploiement

Les unités administratives peuvent être utiles dans les organisations disposant de divisions indépendantes. Prenons l’exemple d’une grande université qui se compose de nombreuses écoles autonomes (école de commerce, école d’ingénieurs, etc.) comportant chacune ses propres administrateurs informatiques qui contrôlent les accès, gèrent les utilisateurs et définissent des stratégies pour leur école. Un administrateur central peut créer une unité administrative pour l’école de commerce et la remplir uniquement avec les étudiants et le personnel de cette école. L’administrateur central peut ensuite ajouter le personnel informatique de l’école de commerce à un rôle étendu qui donne des autorisations administratives relatives uniquement aux utilisateurs Azure AD de l’unité administrative de l’école de commerce.

## <a name="license-requirements"></a>Conditions de licence :

L’utilisation des unités administratives nécessite une licence Azure Active Directory Premium pour chaque administrateur d’unité administrative. Pour plus d'informations, consultez la section [Prise en main d’Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Gestion des unités administratives

Dans cette préversion, la seule façon de créer et gérer des unités administratives consiste à utiliser le module Azure Active Directory pour les applets de commande Windows PowerShell comme décrit dans [Utilisation des unités administratives](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Pour plus d’informations sur la configuration logicielle requise et sur l’installation du module Azure AD, et pour obtenir des informations de référence sur les applets de commande du module Azure AD pour la gestion d’unités administratives, notamment la syntaxe, les descriptions de paramètres et les exemples, consultez [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Étapes suivantes

[Éditions d’Azure Active Directory](../fundamentals/active-directory-whatis.md)
