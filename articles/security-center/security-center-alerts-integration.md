---
title: Détection des menaces pour les produits de sécurité Azure dans Azure Security Center
description: Cette rubrique présente les produits de sécurité Azure pour lesquels Azure Security Center peut fournir la détection des menaces.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 1ea207f0ba09e0637a08632d5c56591fd1335b22
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665715"
---
# <a name="threat-detection-for-azure-waf-and-azure-ddos-protection"></a>Détection des menaces pour Azure WAF et Azure DDoS Protection

Azure Security Center peut vous fournir une détection des menaces pour les produits de sécurité Azure suivants (une licence distincte est requise pour chaque produit) :

* [Pare-feu d’applications web Azure](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Pare-feu d’applications web Azure <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. Le pare-feu d’applications web d’Application Gateway suit l’ensemble des règles de base 3.0 ou 2.2.9 d’Open Web Application Security Project. Il est automatiquement mis à jour pour apporter une protection contre les nouvelles vulnérabilités, sans aucune configuration supplémentaire requise. Les alertes du pare-feu d’applications web sont envoyées en streaming à Security Center. Pour plus d’informations sur les alertes générées par le pare-feu d’applications web, consultez [Règles et groupes de règles CRS de pare-feu d’applications web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection <a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. Elles sont devenues un problème de sécurité majeur, en particulier si vous déplacez vos applications vers le cloud. 

Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible sur Internet.

Combiné aux bonnes pratiques de conception d’application, Azure DDoS Protection assure une excellente protection contre les attaques DDoS. DDoS Protection fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Pour obtenir la liste des alertes Azure DDoS Protection, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureddos).