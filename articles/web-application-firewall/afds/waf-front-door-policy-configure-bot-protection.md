---
title: Configurer la protection bot pour le pare-feu d’applications web avec Azure Front Door (préversion)
description: Découvrez le pare-feu d’applications web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934646"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurer la protection bot pour le pare-feu d’applications web (préversion)
Cet article explique comment configurer une règle de protection bot dans le pare-feu d’applications web (WAF) Azure pour Front Door à l’aide du Portail Azure. Une règle de protection bot peut également être configurée à l’aide de CLI, d'Azure PowerShell ou du modèle Azure Resource Manager.

> [!IMPORTANT]
> Un ensemble de règles de protection bot, actuellement disponible en préversion publique, est fourni avec un contrat de niveau de service en préversion. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Créez une stratégie de pare-feu d’applications web (WAF) de base pour Front Door en suivant les instructions décrites dans [Créer une stratégie WAF pour Azure Front Door à l’aide du portail Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Activer un ensemble de règles de protection bot

Dans la page **Règles managées** lors de la création d’une stratégie de pare-feu d’applications web, commencez par rechercher la section **Ensemble de règles managées**, cochez la case située devant la règle **Microsoft_BotManager_1.0** du menu déroulant, puis sélectionnez **Vérifier + créer**.

   ![Règle de protection bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [surveiller le pare-feu d'applications web](waf-front-door-monitor.md).
