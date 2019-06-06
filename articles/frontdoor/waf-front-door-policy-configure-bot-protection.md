---
title: Configurer la protection de robot de pare-feu d’applications web avec Azure porte d’entrée (version préliminaire)
description: Découvrez le pare-feu d’applications web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481623"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurer la protection de robot de pare-feu d’applications web (version préliminaire)
Cet article vous montre comment configurer des règles de protection de robot dans le pare-feu d’applications Azure web (WAF) pour porte d’entrée à l’aide d’Azure CLI, Azure PowerShell ou Azure Resource Manager d’un modèle.

Un ensemble de règles de protection Bot géré peut être activé pour votre WAF effectuer des actions personnalisées sur les demandes provenant des adresses IP malveillantes. Les adresses IP sont issus les flux de Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente les menaces de Microsoft et est utilisé par plusieurs services, dont Azure Security Center.

> [!IMPORTANT]
> Ensemble de règles de protection de robot est actuellement en version préliminaire publique et est fourni avec un contrat de niveau de service en version préliminaire. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Créer une stratégie de pare-feu d’applications Web de base pour la porte d’entrée en suivant les instructions décrites dans [créer une stratégie de pare-feu d’applications Web pour Azure porte d’entrée à l’aide du portail Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Activer l’ensemble de règles de protection de robot

1. Dans la page de stratégie de base que vous avez créé dans la section précédente, sous **paramètres**, cliquez sur **règles**.
2. Dans la page de détails, sous la **gérer les règles** section, dans le menu déroulant, sélectionnez la case à cocher devant la règle **BotProtection-version préliminaire-0,1**, puis sélectionnez **enregistrer**ci-dessus.
    
   ![Règle de protection de robot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [surveiller WAF](waf-front-door-monitor.md).
