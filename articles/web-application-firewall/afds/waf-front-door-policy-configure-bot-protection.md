---
title: Configurer la protection bot pour le pare-feu d’applications web avec Azure Front Door (préversion)
description: Découvrez le pare-feu d’applications web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186711"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurer la protection bot pour le pare-feu d’applications web (préversion)
Cet article vous montre comment configurer une règle de protection bot dans un pare-feu d’applications web (WAF) pour Front Door à l’aide d’Azure CLI, d’Azure PowerShell ou d’un modèle Azure Resource Manager.

Un ensemble de règles de protection bot managées peut être activé pour votre stratégie WAF afin d’effectuer des actions personnalisées sur les requêtes provenant d’adresses IP malveillantes. Ces adresses IP proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente Microsoft Threat Intelligence et est utilisé par de nombreux services, dont Azure Security Center.

> [!IMPORTANT]
> Un ensemble de règles de protection bot est actuellement disponible en préversion publique et fourni avec un contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Créez une stratégie de pare-feu d’applications web (WAF) de base pour Front Door en suivant les instructions décrites dans [Créer une stratégie WAF pour Azure Front Door à l’aide du portail Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Activer un ensemble de règles de protection bot

1. Dans la page de la stratégie de base créée dans la section précédente, sous **Paramètres**, cliquez sur **Règles**.
2. Dans la page de détails, sous la section **Gérer les règles**, dans le menu déroulant, cochez la case située devant la règle **BotProtection-preview-0.1**, puis sélectionnez **Enregistrer** au-dessus.
    
   ![Règle de protection bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [surveiller le pare-feu d'applications web](waf-front-door-monitor.md).
