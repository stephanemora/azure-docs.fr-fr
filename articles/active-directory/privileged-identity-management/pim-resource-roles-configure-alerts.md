---
title: Gérer les alertes de sécurité pour les ressources Azure à l’aide de Privileged Identity Management | Microsoft Docs
description: Décrit les alertes de sécurité PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5d65201c8940e63e3ba514ae0a2dc8a247d7b859
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233510"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Gérer les alertes de sécurité pour les ressources Azure à l’aide de Privileged Identity Management
Privileged Identity Management (PIM) pour les ressources Azure génère des alertes en cas d’activité suspecte ou non sécurisée dans votre environnement. Lorsqu’une alerte est déclenchée, elle s’affiche sur la page Alertes. 

![Page des alertes](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Consulter les alertes
Sélectionnez une alerte pour obtenir un rapport qui répertorie les utilisateurs ou les rôles à l’origine de son déclenchement et fournit des conseils pour sa correction.

![Rapport d’alerte](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alertes
| Alerte | Niveau de gravité | Déclencheur | Recommandation |
| --- | --- | --- | --- |
| **Trop de propriétaires affectés à une ressource** |Moyenne |Un trop grand nombre d’utilisateurs possèdent le rôle de propriétaire. |Passez en revue les utilisateurs de la liste et réaffectez-en certains à des rôles moins privilégiés. |
| **Trop de propriétaires permanents affectés à une ressource** |Moyenne |Trop d’utilisateurs sont affectés de façon permanente à un rôle. |Passez en revue les utilisateurs sur la liste et réaffectez-en certains pour demander une activation pour l’utilisation du rôle. |
| **Rôle dupliqué créé** |Moyenne |Plusieurs rôles ont les mêmes critères. |Utilisez un seul de ces rôles. |


### <a name="severity"></a>Niveau de gravité
* **Élevé** : nécessite une action immédiate, car il s’agit d’une violation de stratégie. 
* **Moyen** : ne nécessite pas d’action immédiate, mais signale une violation potentielle de la stratégie.
* **Faible** : ne nécessite pas d’action immédiate, mais suggère une modification de la stratégie.

## <a name="configure-security-alert-settings"></a>Configurez les paramètres d'alerte de sécurité
Dans la page Alertes, accédez aux **paramètres**.
![Paramètres](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Personnalisez les paramètres des différentes alertes pour travailler avec votre environnement et les objectifs de sécurité.
![Personnaliser les paramètres](media/azure-pim-resource-rbac/rbac-alert-settings.png)
