---
title: Intégration de Security Center aux produits de sécurité Azure | Microsoft Docs
description: Cette rubrique présente les produits de sécurité Azure qui ont été intégrés à Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 64a636cc4452de1ef4a2d0e94629e7d8e3a5878d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295732"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Intégration de Security Center aux produits de sécurité Azure dans ASC

Security Center offre aux clients des licences Microsoft supplémentaires pour leur permettre d’intégrer leurs sélections à Security Center et les voir de manière centralisée.

* [Pare-feu d’applications web Azure](#azure-waf)
* [Service de protection DDoS Azure](#azure-ddos)

## Pare-feu d’applications web Azure <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus la cible d’attaques malveillantes qui exploitent des vulnérabilités connues. Le WAF d’Application Gateway suit l’ensemble de règles CRS (Core Rule Set) 3.0 ou 2.2.9 d’OWASP (Open Web Application Security Project). Il est automatiquement mis à jour pour apporter une protection contre les nouvelles vulnérabilités, sans aucune configuration supplémentaire requise. Les alertes générées par le WAF sont envoyées en streaming à Security Center. Pour plus d’informations sur les alertes générées par le WAF, consultez cet [article](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Service de protection DDoS Azure<a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. C’est pourquoi elles sont devenues un problème de sécurité majeur pour les clients qui déplacent leurs applications vers le cloud. 

Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible via Internet.

Combiné aux bonnes pratiques de conception d’application, le service de protection DDoS Azure assure une excellente protection contre les attaques DDoS. Le service de protection DDoS Azure fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Le service Protection DDoS Standard peut atténuer les types d’attaques suivants :

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Attaque volumétrique détectée**|L’objectif de cette attaque consiste à submerger la couche réseau d’une quantité substantielle de trafic apparemment légitime. Cette attaque inclut les saturations UDP, les saturations par amplification et autres saturations par falsification de paquets. Le service Protection DDoS Standard atténue ces attaques potentielles de plusieurs gigaoctets en les absorbant et en les purgeant, grâce à l’échelle du réseau global d’Azure, automatiquement.|
|**Attaque de protocole détectée**|ces attaques rendent une cible inaccessible, en exploitant une faille dans la pile de protocole des couches 3 et 4. Elles incluent les attaques par saturation SYN, les attaques par réflexion et autres attaques de protocole. Le service Protection DDoS Standard atténue ces attaques en faisant la distinction entre le trafic légitime et le trafic malveillant, qu’il bloque par l’intermédiaire du client.|
|**Attaque de la couche Ressource (Application) détectée**|ces attaques ciblent les paquets d’application web pour interrompre la transmission des données entre des hôtes. Les attaques incluent les violations de protocole HTTP, l’injection SQL, les scripts de site à site et autres attaques de la couche 7. Utilisez le pare-feu d’applications web Azure Application Gateway avec le service Protection DDoS Standard pour vous défendre contre ces attaques. Il existe également des offres de pare-feu d’application web tiers sur la Place de marché Azure.|
