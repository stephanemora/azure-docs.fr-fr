---
title: Licences requises pour utiliser Privileged Identity Management – Azure Active Directory | Microsoft Docs
description: Décrit les licences requises pour utiliser Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895111"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licences requises pour utiliser Privileged Identity Management

Pour utiliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM), votre annuaire doit avoir une licence valide. De plus, les licences doivent être affectées aux administrateurs et aux utilisateurs concernés. Cet article décrit les licences requises pour utiliser Privileged Identity Management.

## <a name="prerequisites"></a>Prérequis

Pour utiliser Privileged Identity Management, votre annuaire doit avoir l’une des licences payantes ou d’essai gratuit suivantes :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quels utilisateurs doivent avoir des licences ?

Chaque administrateur ou utilisateur qui interagit avec Privileged Identity Management ou en reçoit un avantage doit disposer d’une licence. Voici quelques exemples :

- Administrateurs ayant des rôles Azure AD gérés dans PIM
- Administrateurs ayant des rôles de ressources Azure gérés dans PIM
- Administrateurs affectés au rôle Administrateur de rôle privilégié
- Utilisateurs désignés comme étant éligibles pour les rôles Azure Active Directory gérés à l’aide de PIM
- Utilisateurs en mesure d’approuver et de refuser des requêtes dans PIM
- Utilisateurs affectés à un rôle de ressource Azure avec des attributions juste-à-temps ou directes (à durée définie)  
- Utilisateurs affectés à une révision d’accès
- Utilisateurs qui effectuent des révisions d’accès

Pour plus d’informations sur l’attribution de licences aux utilisateurs, consultez [Assigner ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Que se passe-t-il quand une licence expire ?

Si une licence Azure AD Premium P2, une licence EMS E5 ou une licence d’essai expire, les fonctionnalités Privileged Identity Management ne sont plus disponibles dans votre annuaire :

- Les affectations de rôle permanentes à des rôles Azure AD sont annulées.
- Le service Privileged Identity Management sur le Portail Azure, ainsi que les cmdlets API Graph et les interfaces PowerShell de Privileged Identity Management ne permettent plus aux utilisateurs d’activer des rôles privilégiés, de gérer les accès privilégiés ou de procéder à des révisions d’accès des rôles privilégiés.
- Les affectations de rôle éligibles de rôles Azure AD sont supprimées, car les utilisateurs ne sont plus en mesure d’activer des rôles privilégiés.
- Les révisions d’accès des rôles Azure AD en cours touchent à leur fin et les paramètres de configuration Privileged Identity Management sont supprimés.
- Privileged Identity Management n’envoie plus d’e-mails concernant les modifications apportées aux attributions de rôles.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer Privileged Identity Management](pim-deployment-plan.md)
- [Commencer à utiliser Privileged Identity Management](pim-getting-started.md)
- [Rôles non gérables dans Privileged Identity Management](pim-roles.md)
