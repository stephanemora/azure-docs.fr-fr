---
title: Configurer la protection bot pour le pare-feu d’applications web avec Azure Front Door (préversion)
description: Découvrez le pare-feu d’applications web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846358"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurer la protection bot pour le pare-feu d’applications web (préversion)
Cet article vous montre comment configurer une règle de protection bot dans un pare-feu d’applications web (WAF) pour Front Door à l’aide d’Azure CLI, Azure PowerShell ou un modèle Azure Resource Manager.

Un ensemble de règles de protection bot managées peut être activé pour votre stratégie WAF afin d’effectuer des actions personnalisées sur les requêtes provenant d’adresses IP malveillantes. Ces adresses IP proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente Microsoft Threat Intelligence et est utilisé par de nombreux services, dont Azure Security Center.

> [!IMPORTANT]
> Un ensemble de règles de protection bot est actuellement disponible en préversion publique et fourni avec un contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Créez une stratégie de pare-feu d’applications web (WAF) de base pour Front Door en suivant les instructions décrites dans [Créer une stratégie WAF pour Azure Front Door à l’aide du portail Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Activer un ensemble de règles de protection bot

1. Dans la page de la stratégie de base créée dans la section précédente, sous **Paramètres**, cliquez sur **Règles**.
2. Dans la page de détails, sous la section **Gérer les règles**, dans le menu déroulant, cochez la case située devant la règle **BotProtection-preview-0.1**, puis sélectionnez **Enregistrer** au-dessus.
    
   ![Règle de protection bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [surveiller le pare-feu d'applications web](waf-front-door-monitor.md).
