---
title: Présentation du pare-feu d’applications web Azure
description: Cet article fournit une vue d’ensemble du pare-feu d’applications web (WAF) Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488285"
---
# <a name="what-is-azure-web-application-firewall"></a>Qu’est-ce que le pare-feu d’applications web Azure ?

Le pare-feu d’applications web (WAF) permet de centraliser la protection de vos applications web contre les vulnérabilités et les attaques courantes. Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. L’injection de code SQL et l’exécution de scripts de site à site font partie des attaques les plus courantes.

![Vue d’ensemble de WAF](media/overview/wafoverview.png)

Il est difficile d’empêcher de telles attaques dans le code d’application. Cela peut nécessiter une maintenance rigoureuse, une mise à jour corrective et la surveillance au niveau de plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé permet de simplifier considérablement la gestion de la sécurité. Un pare-feu WAF offre également aux administrateurs d’application une meilleure garantie de protection contre les menaces et intrusions .

Une solution WAF peut également réagir plus rapidement à une menace de sécurité en corrigeant une vulnérabilité connue de façon centralisée plutôt que de sécuriser individuellement chacune des applications web.

WAF peut être déployé avec Azure Application Gateway et Azure Front Door Service. WAF comprend actuellement des fonctionnalités qui sont personnalisées pour chaque service spécifique. Pour plus d’informations sur les fonctionnalités de WAF pour chaque service, consultez la vue d’ensemble de chaque service.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le pare-feu d’applications Web sur Application Gateway, consultez [Pare-feu d’applications web sur Application Gateway](./ag/ag-overview.md).
- Pour plus d’informations sur le pare-feu d’applications Web sur Azure Front Door Service, consultez [Pare-feu d’applications web sur Azure Front Door Service](./afds/afds-overview.md).
