---
title: Prendre en charge le trafic élevé Application Gateway
description: Cet article fournit des conseils pour configurer Azure Application Gateway en vue de la prise en charge de grands volumes de trafic réseau.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 56622ee2c014bd8dbca7c61ec00b927c56f63a40
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740397"
---
# <a name="application-gateway-high-traffic-support"></a>Prendre en charge le trafic élevé avec Application Gateway

>[!NOTE]
> Cet article décrit quelques recommandations pour vous aider à configurer votre Application Gateway afin de gérer le trafic supplémentaire résultant d’un volume de trafic élevé qui peut se produire en raison de l’épidémie de COVID-19.

Vous pouvez utiliser Application Gateway avec le pare-feu d’applications Web (WAF) pour disposer d’un moyen évolutif et sécurisé de gérer le trafic vers vos applications web.

Les suggestions suivantes vous aident à configurer Application Gateway avec WAF pour gérer le trafic supplémentaire.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Utiliser la référence (SKU) v2 plutôt que la v1 pour ses capacités de mise à l’échelle automatiques et ses avantages en matière de performances
La v2 applique la mise à l’échelle automatique pour s’assurer que votre Application Gateway peut monter en puissance à mesure que le trafic augmente. Elle offre également d’autres avantages significatifs en matière de performances, par exemple des performances 5 fois supérieures de déchargement TLS, des temps de déploiement et de mise à jour plus courts, la redondance de zone, etc., par rapport à la v1. Pour plus d’informations, consultez la [documentation de la v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Définir le nombre d’instances maximal sur la valeur maximale possible (125)
 
En supposant que vous disposiez d’une SKU Application Gateway v2, la définition du nombre d’instances maximal sur la valeur maximale possible de 125 permet à Application Gateway de monter en charge en fonction des besoins. Cela lui permet de gérer l’augmentation possible du trafic vers vos applications. Seules les unités de capacité (CU) que vous utilisez vous seront facturées.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Définir le nombre minimal d’instances en fonction de votre utilisation moyenne d’unités de capacité

En supposant que vous disposiez d’Application Gateway v2, le processus de montée en charge automatique prend six à sept minutes. Avec un nombre d’instances minimal plus élevé, Application Gateway est mieux en mesure de gérer votre trafic lorsque la charge augmente, car les pics de trafic ne nécessitent pas d’opération de mise à l’échelle automatique.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alerte si une certaine mesure dépasse 75 % de l’utilisation moyenne des unités de capacité 
Pour obtenir une explication détaillée de nos mesures et d’autres procédures pas à pas, consultez la documentation [Mesures Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization). 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemple : configuration d’une alerte à 75 % de l’utilisation moyenne des unités de capacité

Cet exemple montre comment utiliser le portail Azure pour configurer une alerte quand l’utilisation moyenne des unités de capacité atteint 75 %. 
1. Accédez à votre **Application Gateway**.
2. Dans le volet de gauche, sélectionnez **Mesures** sous l’onglet **Surveillance**. 
3. Ajoutez une mesure pour la **moyenne des unités de calcul actuelles**. 
![Configuration de la métrique WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Si vous avez défini le nombre minimal d’instances comme étant l’utilisation moyenne des unités de capacité, vous pouvez définir une alerte quand 75 % de vos instances minimales sont en cours d’utilisation. Par exemple, si votre utilisation moyenne est de 10 CU, définissez une alerte sur 7,5 CU. Cela vous avertit si votre utilisation augmente et cela vous donne le temps de réagir en fonction. Vous pouvez augmenter la valeur minimale si vous pensez que ce niveau de trafic sera maintenu, afin de vous avertir que le trafic peut augmenter. 
![Configuration de l’alerte WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Vous pouvez définir l’alerte pour qu’elle se produise un pourcentage d’utilisation des unités de capacité inférieur ou supérieur, en fonction de la sensibilité que vous souhaitez pour les pics de trafic potentiels.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurer WAF avec le géofiltrage et la protection bot pour bloquer les attaques
Si vous souhaitez ajouter une couche de sécurité devant votre application, utilisez Application Gateway WAF_v2 pour les fonctionnalités WAF. Vous pouvez configurer la référence v2 pour autoriser uniquement l’accès à vos applications à partir d’un ou plusieurs pays/régions. Vous configurez une règle personnalisée WAF pour autoriser ou bloquer explicitement le trafic en fonction de la géolocalisation. Pour plus d’informations, consultez [Règles personnalisées de géofiltrage](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) et [Comment configurer des règles personnalisées sur Application Gateway WAF_v2 via PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Activez la protection bot pour bloquer les mauvais robots connus. Cela devrait réduire le volume de trafic qui arrive jusqu’à votre application. Pour plus d’informations, consultez [Protection bot avec instructions de configuration](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Activer les diagnostics sur Application Gateway et WAF

Les journaux de diagnostic vous permettent de consulter les journaux de pare-feu, les journaux de performances et les journaux d’accès. Vous pouvez utiliser ces journaux d’activité dans Azure pour gérer les passerelles Application Gateway et résoudre les problèmes associés. Pour plus d’informations, consultez la [documentation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurer une stratégie TLS pour plus de sécurité
Vérifiez que vous utilisez la dernière version de la stratégie TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Cela garantit le chiffrement TLS 1.2 et les chiffrements renforcés. Pour plus d’informations, consultez [Configuration des versions de stratégie TLS et des suites de chiffrement via PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
