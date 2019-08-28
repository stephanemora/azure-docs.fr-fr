---
title: Alertes de sécurité dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes de sécurité et les différents types disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647321"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertes de sécurité dans Azure Security Center

Cet article présente les différents types d’alertes de sécurité disponibles dans Azure Security Center (ASC). Il existe une variété d’alertes pour les nombreux types de ressources. ASC génère des alertes pour les ressources déployées sur Azure et pour les ressources déployées sur les environnements cloud hybride et locaux. 

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?

Les alertes sont les notifications que Security Center génère lorsqu'il détecte des menaces sur vos ressources. Il hiérarchise et répertorie les alertes ainsi que les informations vous permettant d’analyser rapidement le problème. Security Center fournit également des recommandations sur la manière dont vous pouvez corriger les problèmes liés à une attaque.

## <a name="how-does-security-center-detect-threats"></a>Comment Security Center détecte-t-il les menaces ?

Pour détecter les menaces réelles et réduire le nombre de faux positifs, Security Center collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu. Security Center analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces.

ASC supervise les ressources déployées sur Azure ou sur d’autres environnements cloud hybride et locaux. Pour en savoir plus sur la détection des menaces et sur les réponses apportées à celles-ci, consultez [Comment Security Center détecte les menaces et y répond](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Types d’alertes de sécurité

Les rubriques suivantes vous guident tout au long des différentes alertes ASC en fonction des types de ressources :

* [Alertes liées aux serveurs et machines virtuelles IaaS](security-center-alerts-iaas.md)
* [Alertes liées au calcul natif](security-center-alerts-compute.md)
* [Alertes liées aux services de données](security-center-alerts-data-services.md)

Les rubriques suivantes expliquent comment Security Center s’appuie sur les différentes données de télémétrie qu’il collecte à partir de l’intégration à l’infrastructure Azure afin d’appliquer des couches de protection supplémentaires pour les ressources déployées sur Azure :

* [Alertes liées à la couche de services](security-center-alerts-service-layer.md)
* [Intégration aux produits de sécurité Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Présentation des incidents d’alerte

Un incident de sécurité collecte des alertes associées, au lieu de lister des alertes individuelles. Security Center utilise la [corrélation des alertes intelligentes cloud](security-center-alerts-cloud-smart.md) pour mettre en corrélation différentes alertes et signaux faibles en incidents de sécurité.

À l’aide d’incidents, Security Center vous offre une vue unique d’une campagne d’attaque et de toutes les alertes associées. Cette vue vous permet de comprendre rapidement quelles actions l’attaquant a effectuées et quelles ressources ont été impactées. Pour plus d’informations, consultez [Corrélation des alertes intelligentes cloud](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Bien démarrer avec les alertes

Consultez les rubriques suivantes pour en savoir plus sur les ressources supervisées par ASC et pour obtenir des instructions sur la façon de répondre aux alertes présentées par ASC.

* Pour connaître les plateformes et fonctionnalités qui sont protégées par ASC, consultez [Plateformes et fonctionnalités prises en charge par Azure Security Center](security-center-os-coverage.md).  
* Pour comprendre les incidents de sécurité et comment ASC y répond, consultez [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md). 
* Pour découvrir comment gérer les alertes que vous recevez, consultez [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).
* Pour plus d’informations sur la façon de valider la configuration de Security Center et de stimuler une alerte de test, consultez [Validation des alertes dans Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Passer au niveau Standard pour la détection avancée

Pour configurer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. 

1. Dans le menu Centre de sécurité, sélectionnez **Stratégie de sécurité**.
2. Pour les abonnements que vous souhaitez déplacer vers le niveau Standard, cliquez sur **Modifier les paramètres**. 
3. Dans la page Paramètres, sélectionnez **Niveau tarifaire**. 
   Un essai gratuit est disponible pendant un mois. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté les alertes de sécurité et les différents types d’alertes disponibles dans Azure Security Center. Pour plus d’informations, consultez les rubriques suivantes :

* [Guide des opérations et de planification d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [FAQ d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq) : forum aux questions concernant l’utilisation de ce service.

