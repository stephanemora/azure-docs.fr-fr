---
title: Intégration de produits de sécurité Azure avec Azure Security Center
description: Cette rubrique présente les produits de sécurité Azure qui ont été intégrés à Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 693e7d35a0bb4c7dfbb3e033690a5e86e2c398a8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278344"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Intégration de produits de sécurité Azure dans Azure Security Center

Azure Security Center vous fournit des licences Microsoft supplémentaires pour utiliser les produits de sécurité suivants :

* [Pare-feu d’applications web Azure](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Pare-feu d’applications web Azure <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. Le pare-feu d’applications web d’Application Gateway suit l’ensemble des règles de base 3.0 ou 2.2.9 d’Open Web Application Security Project. Il est automatiquement mis à jour pour apporter une protection contre les nouvelles vulnérabilités, sans aucune configuration supplémentaire requise. Les alertes du pare-feu d’applications web sont envoyées en streaming à Security Center. Pour plus d’informations sur les alertes générées par le pare-feu d’applications web, consultez [Règles et groupes de règles CRS de pare-feu d’applications web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. Elles sont devenues un problème de sécurité majeur, en particulier si vous déplacez vos applications vers le cloud. 

Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible sur Internet.

Combiné aux bonnes pratiques de conception d’application, Azure DDoS Protection assure une excellente protection contre les attaques DDoS. DDoS Protection fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Le service Protection DDoS Standard peut atténuer les types d’attaques suivants :

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Attaque volumétrique détectée**|L’objectif de cette attaque consiste à submerger la couche réseau d’une quantité substantielle de trafic apparemment légitime. Cette attaque inclut les saturations UDP, les saturations par amplification et autres saturations par falsification de paquets. Le service Protection DDoS Standard atténue ces attaques potentielles de plusieurs gigaoctets en les absorbant et en les nettoyant automatiquement, grâce à l’échelle du réseau global.|
|**Attaque de protocole détectée**|Ces attaques rendent une cible inaccessible, en exploitant une faille dans les piles de protocole des couches 3 et 4. Elles incluent les attaques par saturation SYN, les attaques par réflexion et d’autres attaques de protocole. Le service Protection DDoS Standard atténue ces attaques en faisant la distinction entre le trafic légitime et le trafic malveillant, qu’il bloque par l’intermédiaire du client.|
|**Attaque de la couche Ressource (Application) détectée**|ces attaques ciblent les paquets d’application web pour interrompre la transmission des données entre des hôtes. Les attaques incluent les violations de protocole HTTP, l’injection SQL, les scripts de site à site et autres attaques de la couche 7. Utilisez le pare-feu d’applications web Azure Application Gateway avec le service Protection DDoS Standard pour vous défendre contre ces attaques. Il existe également des offres de pare-feu d’application web tiers sur la Place de marché Azure.|
