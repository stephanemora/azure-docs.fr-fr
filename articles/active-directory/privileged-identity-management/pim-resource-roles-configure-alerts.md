---
title: Configurer des alertes de sécurité pour les rôles de ressources Azure dans Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des alertes de sécurité pour les rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023083"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurer les alertes de sécurité pour les rôles de ressources Azure dans Privileged Identity Management

Privileged Identity Management (PIM) génère des alertes en cas d’activité suspecte ou non fiable dans votre organisation Azure Active Directory (Azure AD). Lorsqu’une alerte est déclenchée, elle s’affiche sur la page Alertes.

![Page des alertes des ressources Azure répertorie les alertes, le niveau de risque et leur nombre](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Consulter les alertes

Sélectionnez une alerte pour obtenir un rapport qui répertorie les utilisateurs ou les rôles à l’origine de son déclenchement et fournit des conseils pour sa correction.

![Rapport d’alerte montrant la dernière durée d’analyse, la description, les mesures d’atténuation, le type, la gravité, l’impact sur la sécurité et la manière d’éviter ce problème la prochaine fois](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertes

| Alerte | severity | Déclencheur | Recommandation |
| --- | --- | --- | --- |
| **Trop de propriétaires affectés à une ressource** |Moyenne |Un trop grand nombre d’utilisateurs possèdent le rôle de propriétaire. |Passez en revue les utilisateurs de la liste et réaffectez-en certains à des rôles moins privilégiés. |
| **Trop de propriétaires permanents affectés à une ressource** |Moyenne |Trop d’utilisateurs sont affectés de façon permanente à un rôle. |Passez en revue les utilisateurs sur la liste et réaffectez-en certains pour demander une activation pour l’utilisation du rôle. |
| **Rôle dupliqué créé** |Moyenne |Plusieurs rôles ont les mêmes critères. |Utilisez un seul de ces rôles. |

### <a name="severity"></a>severity

- **Haute** : nécessite une action immédiate, car il s’agit d’une violation de stratégie. 
- **Moyenne** : ne nécessite pas d’action immédiate, mais signale une violation potentielle de la stratégie.
- **Faible** : ne nécessite pas d’action immédiate, mais suggère une modification de la stratégie.

## <a name="configure-security-alert-settings"></a>Configurez les paramètres d'alerte de sécurité

Dans la page Alertes, accédez aux **paramètres**.

![Page des alertes avec paramètres mis en surbrillance](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personnalisez les paramètres des différentes alertes pour travailler avec votre environnement et les objectifs de sécurité.

![Page de configuration d’une alerte permettant d’activer et de configurer des paramètres](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
