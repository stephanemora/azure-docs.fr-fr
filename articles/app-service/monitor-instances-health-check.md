---
title: Superviser l’intégrité des instances App Service
description: Découvrez comment superviser l’intégrité des instances App Service en utilisant le contrôle d’intégrité.
keywords: service Azure App, application web, contrôle d’intégrité, router le trafic, instances saines, chemin, supervision
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 8892723ec1a53c59e3e6183b5d53c2e61db4e5d0
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575226"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Superviser des instances App Service à l’aide du contrôle d’intégrité

![Échec du contrôle d’intégrité][2]

Cet article utilise le contrôle d’intégrité dans le portail Azure pour superviser des instances App Service. Le contrôle d’intégrité augmente la disponibilité de votre application en supprimant les instances qui ne sont pas saines. Votre [plan App Service](/azure/app-service/overview-hosting-plans) doit être mis à l’échelle vers deux instances ou plus pour utiliser le contrôle d’intégrité. Le chemin du contrôle d’intégrité doit vérifier les composants critiques de votre application. Par exemple, si votre application dépend d’une base de données et d’un système de messagerie, le point de terminaison de contrôle d’intégrité doit se connecter à ces composants. Si l’application ne peut pas se connecter à un composant critique, le chemin doit retourner un code de réponse de niveau 500 pour indiquer que l’application n’est pas saine.

## <a name="what-app-service-does-with-health-checks"></a>Que fait App Service avec les contrôles d’intégrité ?

- Quand un chemin est donné sur votre application, le contrôle d’intégrité effectue un test ping de ce chemin sur toutes les instances de votre application App Service par intervalles de 1 minute.
- Si une instance ne répond pas par un code d’état compris entre 200 et 299 (compris) à l’issue de deux requêtes ou plus, ou ne parvient pas à répondre à la commande ping, le système détermine qu’elle n’est pas saine et la supprime.
- Après cette suppression, le contrôle d’intégrité continue d’effectuer un test ping sur l’instance qui n’est pas saine. Si celle-ci continue à ne pas répondre correctement, App Service redémarre la machine virtuelle sous-jacente dans un effort de rétablir l’instance à un état sain.
- Si une instance n’est pas saine pendant une heure, elle sera remplacée par une nouvelle instance.
- De plus, dans le cadre d’un scale-up ou d’un scale-out, App Service effectue un test ping sur le chemin de contrôle d’intégrité pour vérifier que de nouvelles instances sont prêtes.

> [!NOTE]
> Le contrôle d’intégrité ne suit pas les redirections 302. Une instance au plus sera remplacée chaque heure, avec un maximum de trois instances par jour et par plan App Service.
>

## <a name="enable-health-check"></a>Activer le contrôle d’intégrité

![Navigation du contrôle d’intégrité dans le portail Azure][3]

- Pour activer le contrôle d’intégrité, accédez au portail Azure et sélectionnez votre application App Service.
- Sous **Supervision**, sélectionnez **Contrôle d’intégrité**.
- Sélectionnez **Activer** et fournissez un chemin d’URL valide pour votre application, par exemple `/health` ou `/api/health`.
- Cliquez sur **Enregistrer**.

> [!CAUTION]
> Les modifications apportées à la configuration du contrôle d’intégrité entraînent le redémarrage de votre application. Pour réduire l’impact sur les applications de production, nous vous recommandons de [configurer des emplacements de préproduction](deploy-staging-slots.md) et de basculer vers des emplacements de production.
>

### <a name="configuration"></a>Configuration

En plus de la configuration des options de contrôle d’intégrité, vous pouvez aussi configurer les [paramètres d’application](configure-common.md) suivants :

| Nom du paramètre d’application | Valeurs autorisées | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Nombre maximal d’échecs de test ping. Par exemple, quand la valeur est `2`, vos instances sont supprimées à l’issue de `2` échecs de test ping. De plus, si vous effectuez un scale-up ou un scale-out, App Service effectue un test ping sur le chemin de contrôle d’intégrité pour vérifier que de nouvelles instances sont prêtes. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Pour éviter de submerger les instances saines, pas plus de la moitié des instances est exclue. Par exemple, si un plan App Service est mis à l’échelle vers quatre instances et que trois d’entre elles ne sont pas saines, seules deux sont exclues au maximum. Les deux autres instances (une saine et une non saine) continuent de recevoir des requêtes. Dans le pire des cas, où aucune instance n’est saine, aucune ne sera exclue. Pour remplacer ce comportement, définissez le paramètre d’application sur une valeur comprise entre `0` et `100`. Plus la valeur est élevée, plus le nombre d’instances non saines supprimées est élevé (la valeur par défaut est 50). |

#### <a name="authentication-and-security"></a>Authentification et sécurité

Le contrôle d’intégrité s’intègre aux fonctionnalités d’authentification et d’autorisation d’App Service. Aucun paramètre supplémentaire n’est nécessaire si ces fonctionnalités de sécurité sont activées. En revanche, si vous utilisez votre propre système d’authentification, le chemin du contrôle d’intégrité doit autoriser l’accès anonyme. Si le protocole HTTP **S** uniquement est activé sur le site, la requête de contrôle d’intégrité est envoyée par le biais du protocole HTTP **S**.

Les équipes de développement des grandes entreprises doivent souvent adhérer à des exigences de sécurité pour les API exposées. Pour sécuriser le point de terminaison du contrôle d’intégrité, vous devez d’abord utiliser des fonctionnalités comme des [restrictions d’adresse IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), des [certificats clients](app-service-ip-restrictions.md#set-an-ip-address-based-rule) ou un réseau virtuel pour restreindre l’accès à l’application. Vous pouvez sécuriser le point de terminaison du contrôle d’intégrité en exigeant que la propriété `User-Agent` de la requête entrante corresponde à `ReadyForRequest/1.0`. La propriété User-Agent ne peut pas être falsifiée, car la requête a déjà été sécurisée par les fonctionnalités de sécurité précédentes.

## <a name="monitoring"></a>Surveillance

Après avoir fourni le chemin de contrôle d’intégrité de votre application, vous pouvez superviser l’intégrité de votre site à l’aide d’Azure Monitor. Dans le panneau **Contrôle d’intégrité** du portail, cliquez sur **Métriques** dans la barre d’outils supérieure. Un nouveau panneau s’ouvre, dans lequel vous pouvez voir l’état d’intégrité historique du site et créer une nouvelle règle d’alerte. Pour plus d’informations sur la surveillance de vos sites, [consultez le guide sur Azure Monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Étapes suivantes
- [Créez une alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Créez une alerte de journal d’activité pour surveiller tous les échecs d’opérations de diminution et d’augmentation de la taille des instances de la mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
