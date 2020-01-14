---
title: Contrôle de sécurité Azure - Sécurité réseau
description: Contrôle de sécurité - Sécurité réseau
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563955"
---
# <a name="security-control-network-security"></a>Contrôle de sécurité : Sécurité réseau

Les recommandations en matière de sécurité réseau se concentrent sur la spécification des protocoles réseau, des ports TCP/UDP et des services connectés au réseau auxquels l'accès aux services Azure est autorisé ou refusé.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 : Protéger les ressources à l'aide de groupes de sécurité réseau ou du Pare-feu Azure sur votre réseau virtuel

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Customer |

Assurez-vous qu'un groupe de sécurité réseau a été appliqué à tous les déploiements de sous-réseaux du réseau virtuel avec des contrôles d'accès réseau spécifiques aux ports et sources approuvés de votre application. Utilisez les Services Azure en veillant à activer la Liaison privée, déployez le service dans votre réseau virtuel ou connectez-vous en mode privé à l'aide de points de terminaison privés. Pour les exigences spécifiques au service, reportez-vous à la recommandation de sécurité relative à ce service.

Pour les cas d'usage spécifiques, vous pouvez également satisfaire les exigences en implémentant le Pare-feu Azure.

Informations générales sur la Liaison privée : https://docs.microsoft.com/azure/private-link/private-link-overview

Créer un réseau virtuel :

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Créer un groupe de sécurité réseau (NSG) avec une configuration de sécurité :

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Déployer et configurer le Pare-feu Azure :

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 : Surveiller et consigner la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.2 | 9.3, 12.2 | Customer |

Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour sécuriser vos ressources réseau dans Azure. Activez les journaux de flux NSG et transférez-les vers un compte de stockage pour l'audit du trafic.

Activer les journaux de flux NSG :

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Comprendre la sécurité réseau fournie par Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.3 | 9.5 | Customer |

Déployez un pare-feu d'applications web (WAF) Azure devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Activez le paramètre de diagnostic du WAF et ingérez les journaux dans un compte de stockage, un hub d'événements ou un espace de travail Log Analytics.

Déployer Azure WAF :

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications présentant des adresses IP connues comme étant malveillantes

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.4 | 12.3 | Customer |

Activez la protection DDoS Standard sur vos réseaux virtuels Azure pour vous protéger des attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications présentant des adresses IP connues comme étant malveillantes.

Déployez le Pare-feu Azure à chaque extrémité du réseau de l'organisation en activant la fonctionnalité de renseignement sur les menaces et en la configurant sur &quot;Alerter et refuser&quot; pour vous protéger de tout trafic réseau malveillant.

Utilisez la fonctionnalité d'accès réseau juste-à-temps d'Azure Security Center pour configurer les groupes de sécurité réseau (NSG) et limiter l'exposition des points de terminaison aux adresses IP approuvées pendant une période limitée.

Utilisez la fonctionnalité de renforcement du réseau adaptatif d'Azure Security Center pour recommander des configurations NSG qui limitent les ports et les adresses IP sources en fonction du trafic réel et du renseignement sur les menaces.

Configurer la protection DDoS :

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Déployer le Pare-feu Azure :

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Comprendre la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Comprendre la fonctionnalité de renforcement du réseau adaptatif d'Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Comprendre la fonctionnalité de contrôle d'accès réseau juste-à-temps d'Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 : Consigner les paquets réseau et les journaux de flux

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.5 | 12.5, 15.8 | Customer |

Consignez les journaux de flux des groupes de sécurité réseau (NSG) dans un compte de stockage pour générer des enregistrements de flux. Si cela s'avère nécessaire pour analyser une activité anormale, activez la capture de paquets Network Watcher.

Activer les journaux de flux NSG : https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Activer Network Watcher : https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

Déployez le Pare-feu Azure à chaque extrémité du réseau de l'organisation en activant la fonctionnalité de renseignement sur les menaces et en la configurant sur &quot;Alerter et refuser&quot; pour vous protéger de tout trafic réseau malveillant.

Déployer le Pare-feu Azure : https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Configurer des alertes avec le Pare-feu Azure : https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

Déployez Azure Application Gateway pour les applications web en activant le protocole HTTPS/SSL pour les certificats approuvés.

Déployer Application Gateway : https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Configurer Application Gateway pour utiliser le protocole HTTPS : https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Comprendre l'équilibrage de charge de niveau 7 avec les passerelles d'applications web Azure : https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.8 | 1.5 | Customer |

Utilisez des balises de service de Réseau virtuel &nbsp;pour définir des contrôles d'accès réseau sur les groupes de sécurité réseau ou le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Comprendre et utiliser les balises de service : https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.9 | 11,1 | Customer |

Définissez et implémentez des configurations de sécurité standard pour les ressources réseau à l'aide d'Azure Policy.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d'environnement clés, tels que les modèles Azure Resource Manager, les contrôles RBAC et les stratégies, au sein d'une seule définition de blueprint. Vous pouvez appliquer le blueprint aux nouveaux abonnements et environnements, et affiner le contrôle et la gestion via le contrôle de version.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exemples Azure Policy pour la mise en réseau :

https://docs.microsoft.com/azure/governance/policy/samples/#network

Créer une instance d'Azure Blueprint :

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.1 | 11.2 | Customer |

Utilisez des balises pour les groupes de sécurité réseau (NSG) et autres ressources liées à la sécurité réseau et au trafic. Concernant les règles NSG individuelles, utilisez le champ &quot;Description&quot; afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Créer et utiliser des balises :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Créer un réseau virtuel :

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Créer un groupe de sécurité réseau (NSG) avec une configuration de sécurité :

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 1.11 | 11.3 | Customer |

Utilisez Azure Policy pour valider (et/ou corriger) la configuration des ressources réseau.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exemples Azure Policy pour la mise en réseau :

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous au contrôle de sécurité suivant : [Journalisation et supervision](security-control-logging-monitoring.md)