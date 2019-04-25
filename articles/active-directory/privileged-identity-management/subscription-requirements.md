---
title: Configuration requise pour utiliser PIM - Azure Active Directory de licence | Microsoft Docs
description: Décrit les licences requises pour utiliser Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e16209a185623b6e15650f70141edd6394e337
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437454"
---
# <a name="license-requirements-to-use-pim"></a>Licences requises pour utiliser PIM

Pour utiliser Azure Active Directory (Azure AD) Privileged Identity Management (PIM), votre annuaire doit avoir une licence valide. De plus, les licences doivent être affectées aux administrateurs et aux utilisateurs concernés. Cet article décrit les licences requises pour utiliser PIM.

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser PIM, votre annuaire doit avoir l’une des licences payantes ou d’essai gratuit suivantes :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quels utilisateurs doivent avoir des licences ?

Chaque administrateur ou utilisateur qui utilise ou reçoit un avantage de PIM doit avoir une licence. Voici quelques exemples :

- Administrateurs ayant des rôles Azure AD gérés dans PIM
- Administrateurs ayant des rôles de ressources Azure gérés dans PIM
- Administrateurs affectés au rôle Administrateur de rôle privilégié
- Utilisateurs désignés comme éligibles aux rôles Azure AD gérés à l’aide de PIM
- Utilisateurs en mesure d’approuver et de refuser des requêtes dans PIM
- Utilisateurs affectés à un rôle de ressource Azure avec des attributions juste-à-temps ou directes (à durée définie)  
- Utilisateurs affectés à une révision d’accès
- Utilisateurs qui effectuent des révisions d’accès

Pour plus d’informations sur l’attribution de licences aux utilisateurs, consultez [Assigner ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Que se passe-t-il quand une licence expire ?

Quand une licence Azure AD Premium P2, une licence EMS E5 ou une licence d’essai expire, les fonctionnalités PIM ne sont plus disponibles dans votre annuaire :

- Les affectations de rôle permanentes à des rôles Azure AD sont annulées.
- Le service PIM dans le portail Azure ainsi que les applets de commande API Graph et les interfaces PowerShell de PIM ne peuvent plus être utilisés par les utilisateurs pour activer des rôles privilégiés, gérer l’accès privilégié ou effectuer des vérifications d’accès des rôles privilégiés.
- Les affectations de rôle éligibles de rôles Azure AD sont supprimées, car les utilisateurs ne sont plus en mesure d’activer des rôles privilégiés.
- Les vérifications d’accès en cours des rôles Azure AD sont arrêtées, et les paramètres de configuration de PIM sont supprimés.
- PIM n’envoie plus d’e-mails sur les changements d’attribution de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer PIM](pim-deployment-plan.md)
- [Commencer à utiliser PIM](pim-getting-started.md)
- [Rôles que vous ne pouvez pas gérer dans PIM](pim-roles.md)
