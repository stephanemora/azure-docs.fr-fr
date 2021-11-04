---
title: Protection DDoS L7 incluse avec la passerelle Load Balancer et le partenaire NVA
description: Découvrez comment créer et activer la protection DDoS L7 incluse avec la passerelle Load Balancer et le partenaire NVA
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.author: yitoh
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79e4507cb573dfe6893081df737a0e604810fbc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096814"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>Protection DDoS L7 incluse avec la passerelle Load Balancer et le partenaire NVA

Azure DDoS Protection est toujours activé, mais n’est pas inclus et prend 30 à 60 secondes à partir du moment où une attaque est détectée jusqu’à ce qu’elle soit atténuée. Azure DDoS Protection Standard fonctionne également à L3/4 (couche réseau) et n’inspecte pas la charge utile des paquets, c.-à-d. la couche application (L7).  
Les charges de travail qui sont très sensibles à la latence et qui ne tolèrent pas 30 à 60 secondes de temps de démarrage pour la protection DDoS pour le lancement requièrent une protection incluse. La protection incluse implique que tout le trafic passe toujours par le pipeline de protection DDoS. En outre, pour des scénarios tels que la protection web ou la protection de la charge de travail de jeu (UDP), il devient essentiel d’inspecter la charge utile du paquet pour atténuer les attaques à volume extrêmement faible qui exploitent la vulnérabilité dans la couche d’application (L7). 

Le partenaire NVA déployé avec la passerelle Load Balancer et intégré à Azure DDoS Protection Standard offre une protection DDoS L7 incluse pour des scénarios de haute performance et de haute disponibilité. La protection DDoS L7 incluse associée à Azure DDoS Protection standard offre une protection L3-L7 complète contre les attaques DDoS volumétriques et à faible volume. 

## <a name="what-is-a-gateway-load-balancer"></a>Qu’est-ce qu’une passerelle Load Balancer ?
La passerelle Load Balancer est une référence SKU d’Azure Load Balancer conçue spécifiquement pour les scénarios à haute performance et haute disponibilité avec des appliances virtuelles réseau tierces (NVA).

Avec les fonctionnalités de la passerelle LB, vous pouvez déployer, mettre à l’échelle et gérer facilement des NVA, en chaînage d’une passerelle LB à votre point de terminaison public, en un seul clic.  Vous pouvez insérer des appliances pour divers scénarios tels que les pare-feu, l’analyse avancée des paquets, les systèmes de détection et de prévention des intrusions et des scénarios personnalisés qui répondent à vos besoins dans le chemin d’accès réseau avec la passerelle LB. Dans les scénarios avec NVA, il est particulièrement important que les flux soient « symétriques », ce qui garantit que les sessions sont maintenues et symétriques. La passerelle LB maintient la symétrie du flux vers une instance spécifique dans le pool principal.

Pour plus d’informations sur la passerelle Load Balancer, consultez le produit et la documentation [Passerelle LB](https://aka.ms/gatewaylb). 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>Protection DDoS incluse avec la passerelle LB et le partenaire NVA

Les attaques DDoS sur les charges de travail sensibles à latence élevée (par exemple, les jeux) peuvent entraîner une panne allant de 2 à 10 secondes, entraînant une interruption de la disponibilité. La passerelle Load Balancer permet de protéger ces charges de travail en garantissant que les NVA pertinentes sont injectées dans le chemin d’entrée du trafic Internet. Une fois chaînés à une configuration standard Load Balancer frontend ou IP standard sur une machine virtuelle, aucune configuration supplémentaire n’est nécessaire pour garantir que le trafic vers et depuis le point de terminaison d’application est envoyé à la passerelle LB. 

Le trafic entrant est toujours inspecté via les NVA dans le chemin d’accès et le trafic nettoyé est renvoyé à l’infrastructure principale (serveurs Gamer). 

Le trafic transite du réseau virtuel du consommateur au réseau virtuel du fournisseur, puis revient au réseau virtuel du consommateur. Le réseau virtuel du consommateur et le réseau virtuel du fournisseur peuvent se trouver dans différents abonnements, locataires ou régions, ce qui permet une plus grande flexibilité et une gestion simplifiée.

![Protection DDoS incluse par le biais de la passerelle Load Balancer](./media/ddos-glb.png)
 
L’activation de Azure DDoS Protection Standard sur le réseau virtuel du réseau public standard Load Balancer frontend ou le réseau virtuel de la machine virtuelle offre une protection contre les attaques DDoS L3/4. 
1.  Le trafic de jeu non filtré à partir d’Internet est dirigé vers l’adresse IP publique des serveurs de jeux Load Balancer. 
2.  Le trafic de jeu non filtré est redirigé vers l’adresse IP privée chaînée de la passerelle Load Balancer. 
3.  Le trafic de jeu non filtré est inspecté pour les attaques DDoS en temps réel via le partenaire NVA. 
4.  Le trafic de jeu filtré est renvoyé aux serveurs de jeu pour le traitement final.
5.  Azure DDoS Protection Standard sur les serveurs des joueurs Load Balancer protège des attaques DDoS L3/4 et les stratégies de protection DDoS sont automatiquement ajustées pour les profils de trafic des serveurs de jeu et l’échelle des applications. 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [partenaires de protection DDoS L7 incluse](https://aka.ms/inlineddospartners)
- En savoir plus sur [Azure DDoS Protection Standard](https://aka.ms/ddosprotectiondocs)
- En savoir plus sur la [passerelle Load Balancer](https://aka.ms/gatewaylb)
