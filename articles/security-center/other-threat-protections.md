---
title: Protections supplémentaires contre les menaces dans Azure Security Center
description: En savoir plus sur la protection contre les menaces disponible dans Azure Security Center au-delà d’Azure Defender
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: c2bed032510e278663a1d1d9f10043eaa6e9b0db
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858193"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Protections supplémentaires contre les menaces dans Azure Security Center
En plus des [protections Azure Defender](azure-defender.md) intégrées, Azure Security Center offre les fonctionnalités de protection contre les menaces suivantes.

> [!TIP]
> Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez activer Azure Defender sur l’abonnement contenant les charges de travail applicables.

## <a name="threat-protection-for-azure-network-layer"></a>Protection contre les menaces pour la couche réseau Azure <a name="network-layer"></a>
L’analytique de la couche réseau de Security Center est basée sur des exemples de [données IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), qui sont des en-têtes de paquets collectés par des routeurs de base Azure. En fonction de ce flux de données, les modèles Machine Learning de Security Center identifient et signalent les activités de trafic malveillantes. Security Center utilise également la base de données Microsoft Threat Intelligence pour enrichir les adresses IP.

Certaines configurations réseau empêchent Security Center de générer des alertes en cas d’activité réseau suspecte. Pour que Security Center génère des alertes réseau, assurez-vous que :
- Votre machine virtuelle a une adresse IP publique (ou se trouve sur un équilibreur de charge avec une adresse IP publique).
- Le trafic de sortie réseau de votre machine virtuelle n’est pas bloqué par une solution IDS externe.

Pour obtenir la liste des alertes de la couche réseau Azure, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Protection contre les menaces pour Azure Cosmos DB (préversion)<a name="cosmos-db"></a>

Les alertes Azure Cosmos DB sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB.

Pour plus d'informations, consultez les pages suivantes :

* [Protection avancée contre les menaces pour Azure Cosmos DB (préversion)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste des alertes de protection contre les menaces pour Azure Cosmos DB (préversion)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-azure-security-center-recommendations-in-microsoft-cloud-app-security"></a>Afficher les recommandations d’Azure Security Center dans Microsoft Cloud App Security <a name="azure-mcas"></a>

Microsoft Cloud App Security est un répartiteur de sécurité d’accès cloud (CASB) qui prend en charge différents modes de déploiement, y compris la collecte de journaux, les connecteurs API et un proxy inverse. Il offre une grande visibilité, un contrôle des déplacements des données, et des analyses sophistiquées pour identifier et combattre les cybermenaces sur l'ensemble de vos services cloud Microsoft et tiers.

Si vous avez activé Microsoft Cloud App Security et si vous avez sélectionné l’intégration à partir des paramètres de Security Center, vos recommandations de durcissement de la sécurité de Security Center s’affichent dans Cloud App Security sans aucune configuration supplémentaire.

> [!NOTE]
> Security Center stocke les données de client liées à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Security Center dans la zone géographique de la ressource, il stocke les données aux États-Unis. Quand Microsoft Cloud App Security est activé, ces informations sont stockées conformément aux règles de géolocalisation de Microsoft Cloud App Security. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Envoyer en streaming des alertes de sécurité à partir d’autres services Microsoft <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-security-center"></a>Afficher les alertes Azure WAF dans Security Center <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. Le pare-feu d’applications web d’Application Gateway suit l’ensemble des règles de base 3.0 ou 2.2.9 d’Open Web Application Security Project. La solution WAF est mise à jour automatiquement pour offrir une protection contre les nouvelles vulnérabilités. 

Si vous avez une licence pour Azure WAF, vos alertes WAF sont envoyées en streaming vers Security Center sans configuration supplémentaire. Pour plus d’informations sur les alertes générées par le pare-feu d’applications web, consultez [Règles et groupes de règles CRS de pare-feu d’applications web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-security-center"></a>Afficher les alertes Azure DDoS Protection dans Security Center <a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. Elles sont devenues un problème de sécurité majeur, en particulier si vous déplacez vos applications vers le cloud. Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible sur Internet.

Pour vous défendre contre les attaques DDoS, achetez une licence Azure DDoS Protection, puis veillez à suivre les bonnes pratiques de conception d’applications. DDoS Protection fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md).

Si Azure DDoS Protection est activé, vos alertes DDoS sont envoyées en streaming vers Security Center sans aucune configuration supplémentaire. Pour plus d’informations sur les alertes générées par le service Protection DDoS, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les alertes de sécurité provenant de ces fonctionnalités de protection contre les menaces, consultez les articles suivants :

* [Tableau de référence pour toutes les alertes Azure Security Center](alerts-reference.md)
* [Alertes de sécurité dans le Centre de sécurité Azure](security-center-alerts-overview.md)
* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exporter en continu les données Security Center](continuous-export.md)
