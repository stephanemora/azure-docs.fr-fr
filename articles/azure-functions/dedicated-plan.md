---
title: Hébergement dédié Azure Functions
description: Découvrez les avantages de l’exécution d’Azure Functions sur un plan d’hébergement App Service dédié.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937469"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Plans d’hébergement dédiés pour Azure Functions

Cet article présente l’hébergement de votre application de fonction dans un plan App Service, y compris dans un environnement ASE (App Service Environment). Pour d’autres options d’hébergement, consultez l’[article sur le plan d’hébergement](functions-scale.md).

Un plan App Service définit un ensemble de ressources de calcul pour l’exécution d’une application. Ces ressources de calcul sont analogues à la [_batterie de serveurs_](https://wikipedia.org/wiki/Server_farm) dans l’hébergement classique. Une ou plusieurs applications de fonction peuvent être configurées pour s’exécuter sur les mêmes ressources de calcul (plan App Service) que d’autres applications App Service, par exemple des applications web. Ces plans incluent les plans De base, Standard, Premium et les références SKU isolées. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/overview-hosting-plans.md).

Envisagez un plan App Service dans les situations suivantes :

* Vous disposez de machines virtuelles existantes, sous-utilisées qui exécutent déjà d’autres instances App Service.
* Vous souhaitez fournir une image personnalisée sur laquelle vos fonctions s’exécuteront.

## <a name="billing"></a>Facturation

Le coût des applications de fonction dans un plan App Service est le même que pour d’autres ressources App Service. Cela diffère de l’hébergement Azure Functions [Plan de consommation](consumption-plan.md) ou [Plan Premium](functions-premium-plan.md), qui incluent des composants de coût basés sur la consommation. Vous êtes facturé uniquement pour le plan, quel que soit le nombre d’applications de fonction ou d’applications web qui s’y exécutent. Pour en savoir plus, consultez la [page de tarification App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Always On

Si vous utilisez un plan App Service, vous devez activer le paramètre **Always On** afin que l’application de fonction s’exécute correctement. Dans un plan App Service, comme le runtime des fonctions devient inactif après quelques minutes d’inactivité, seuls des déclencheurs HTTP peuvent « relancer » vos fonctions. Le paramètre **Always On** est disponible uniquement dans un plan App Service. Dans un plan Consommation, la plateforme active automatiquement les applications de fonction.

Même lorsque le paramètre Always On est activé, le délai d’exécution des fonctions individuelles est contrôlé par le paramètre `functionTimeout` dans le fichier projet [host.json](functions-host-json.md#functiontimeout).

## <a name="scaling"></a>Mise à l'échelle

À l’aide d’un plan App Service, vous pouvez effectuer manuellement un scale-out en ajoutant des instances de machine virtuelle supplémentaires. Vous pouvez également activer la mise à l’échelle automatique, bien que celle-ci soit plus lente que la mise à l’échelle élastique du plan Premium. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Vous pouvez également effectuer une montée en puissance en choisissant un autre plan App Service. Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Lorsque vous exécutez des fonctions JavaScript (Node.js) dans un plan App Service, vous devez choisir un plan qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans App Service à cœur unique](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Environnements App Service

L’exécution dans une instance [App Service Environment](../app-service/environment/intro.md) vous permet d’isoler entièrement vos fonctions et de tirer parti d’un nombre d’instances plus élevé que dans un plan App Service. Pour commencer, voir la rubrique .

Si vous souhaitez simplement exécuter votre application de fonction dans un réseau virtuel, vous pouvez le faire à l’aide du [plan Premium](functions-premium-plan.md). Pour en savoir plus, consultez [Établir l’accès privé aux sites avec Azure Functions](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Étapes suivantes

+ [Options d’hébergement Azure Functions](functions-scale.md)
+ [Vue d’ensemble du plan Azure App Service](../app-service/overview-hosting-plans.md)