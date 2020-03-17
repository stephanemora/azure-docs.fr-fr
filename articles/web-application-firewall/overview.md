---
title: Présentation du pare-feu d’applications web Azure
description: Cet article fournit une vue d’ensemble du pare-feu d’applications web (WAF) Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851190"
---
# <a name="what-is-azure-web-application-firewall"></a>Qu’est-ce que le pare-feu d’applications web Azure ?

Le pare-feu d’applications web (WAF) permet de centraliser la protection de vos applications web contre les vulnérabilités et les attaques courantes. Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. L’injection de code SQL et l’exécution de scripts de site à site font partie des attaques les plus courantes.

![Vue d’ensemble de WAF](media/overview/wafoverview.png)

Il est difficile d’empêcher de telles attaques dans le code d’application. Cela peut nécessiter une maintenance rigoureuse, une mise à jour corrective et la surveillance au niveau de plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé permet de simplifier considérablement la gestion de la sécurité. Un pare-feu WAF offre également aux administrateurs d’application une meilleure garantie de protection contre les menaces et intrusions .

Une solution WAF peut également réagir plus rapidement à une menace de sécurité en corrigeant une vulnérabilité connue de façon centralisée plutôt que de sécuriser individuellement chacune des applications web.

## <a name="supported-services"></a>Services pris en charge

WAF peut être déployé avec [Azure Application Gateway](../application-gateway/overview.md) et [Azure Front Door Service](../frontdoor/front-door-overview.md). Les deux services sont des équilibreurs de charge de couche 7 (HTTP/S). Toutefois, Application Gateway est un service régional et Front Door est un service mondial. WAF comprend des fonctionnalités qui sont personnalisées pour chaque service spécifique.

Pour plus d’informations, consultez la vue d’ensemble de WAF pour chaque service.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le pare-feu d’applications Web sur Application Gateway, consultez [Pare-feu d’applications web sur Application Gateway](./ag/ag-overview.md).
- Pour plus d’informations sur le pare-feu d’applications web sur Azure Front Door Service, consultez [Pare-feu d’applications web sur Azure Front Door Service](./afds/afds-overview.md).
