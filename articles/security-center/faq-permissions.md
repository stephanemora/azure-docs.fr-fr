---
title: FAQ sur Azure Security Center - Questions sur les autorisations
description: Ces Questions fréquentes (FAQ) et leurs réponses apportent des informations sur les autorisations dans Azure Security Center, produit qui vous permet de prévenir, détecter et contrer les menaces.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106591"
---
# <a name="permissions"></a>Autorisations

## <a name="how-do-permissions-work-in-azure-security-center"></a>Comment fonctionnent les autorisations dans Azure Security Center ?

Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Pour plus d’informations sur les rôles et les actions autorisées dans Security Center, consultez l’article [Permissions in Azure Security Center (Autorisations dans Azure Security Center)](security-center-permissions.md).



## <a name="who-can-modify-a-security-policy"></a>Qui peut modifier une stratégie de sécurité ?

Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné.

Pour savoir comment configurer une stratégie de sécurité, consultez [Définition de stratégies de sécurité dans le Centre de sécurité Azure](tutorial-security-policy.md).