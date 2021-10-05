---
title: Accès conditionnel dans Azure Synapse Analytics
description: Cet article explique l’accès conditionnel dans Azure Synapse Analytics
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602508"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Accès conditionnel dans Azure Synapse Analytics

Vous pouvez désormais configurer des stratégies d’accès conditionnel pour les espaces de travail Azure Synapse. L’accès conditionnel est un outil fourni par Azure Active Directory pour rassembler plusieurs signaux tels que le type d’appareil et l’emplacement IP de l’appareil afin de prendre des décisions pour accorder l’accès, bloquer l’accès ou appliquer l’authentification multifacteur pour une ressource. Les stratégies d’accès conditionnel sont configurées dans Azure Active Directory. En savoir plus sur l’[accès conditionnel](../../active-directory/conditional-access/overview.md).


## <a name="configure-conditional-access"></a>Configurer l’accès conditionnel
Les étapes suivantes montrent comment configurer une stratégie d’accès conditionnel pour les espaces de travail Azure Synapse.

1. Connectez-vous au portail Azure à l’aide d’un compte disposant des *autorisations d’accès d’administrateur global*, sélectionnez **Azure Active Directory**, choisissez **Sécurité** dans le menu. 
2. Sélectionnez **Accès conditionnel**, puis choisissez **+ Nouvelle stratégie** et donnez un nom à la stratégie.
3. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, cochez l’option **Sélectionner des utilisateurs et des groupes**, puis sélectionnez un utilisateur ou un groupe Azure AD pour l’accès conditionnel. Cliquez sur Sélectionner, puis sur Terminé.
4. Sélectionnez **Applications cloud**, puis cliquez sur **Sélectionner des applications**. Sélectionnez **Passerelle Microsoft Azure Synapse**. Cliquez ensuite sur Sélectionner, puis sur Terminé.
5. Sous **Contrôles d’accès**, sélectionnez **Accorder**, puis cochez la stratégie que vous souhaitez appliquer et sélectionnez Terminé.
6. Basculez **Activer la stratégie** sur **Activé**, puis sélectionnez Créer.


## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les stratégies d’accès conditionnel et leurs composants.
- [Stratégies d’accès conditionnel courantes](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [Créer une stratégie d’accès conditionnel](../../active-directory/conditional-access/concept-conditional-access-policies.md)