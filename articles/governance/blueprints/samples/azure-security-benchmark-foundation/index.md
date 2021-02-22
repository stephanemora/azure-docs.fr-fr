---
title: Vue d’ensemble de l’exemple de blueprint Azure Security Benchmark Foundation
description: Vue d’ensemble et architecture de l’exemple de blueprint Azure Security Benchmark Foundation.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: b3b58f2fb603e23e4b188e527fa4fc60f4041a29
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095279"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint Azure Security Benchmark Foundation

L’exemple de blueprint Azure Security Benchmark Foundation offre un ensemble de modèles d’infrastructure de base qui vous permettront de créer un environnement Azure sécurisé et conforme. Avec ce blueprint, vous pouvez déployer une architecture cloud qui offre des solutions aux scénarios qui ont des obligations d’accréditation ou de conformité. Cet exemple de blueprint de base est une extension de l’[exemple de blueprint Azure Security Benchmark](../azure-security-benchmark/index.md). Il déploie et configure limites réseau, supervision et autres ressources en phase avec les stratégies et autres garde-fous définis dans le [Benchmark de sécurité Azure](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Architecture

L’environnement de base créé par cet exemple de blueprint repose sur les principes architecturaux d’un [modèle Hub-and-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Le blueprint déploie un réseau virtuel Hub qui contient des ressources communes et partagées, des services et des artefacts comme Azure Bastion, une passerelle et un pare-feu pour la connectivité et des sous-réseaux de gestion et jumpbox pour héberger une infrastructure de gestion de maintenance, d’administration et de connectivité supplémentaire/facultative. Un ou plusieurs réseaux virtuels Spoke sont déployés pour héberger des charges de travail applicatives comme des services web et de base de données. Les réseaux virtuels Spoke sont connectés au réseau virtuel Hub via l’appairage de réseaux virtuels Azure pour obtenir une connectivité fluide et sécurisée. Des Spokes supplémentaires peuvent être ajoutés en réattribuant l’exemple de blueprint ou en créant manuellement un réseau virtuel Azure et en l’appairant au réseau virtuel Hub. Toute la connectivité externe au(x) réseau(x) et sous-réseau(x) virtuels Spoke est configurée pour router par le biais du réseau virtuel Hub et via le pare-feu, la passerelle et les jumpbox de gestion.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagramme de l’architecture de l’exemple de blueprint Azure Security Benchmark Foundation" border="false":::

Ce blueprint déploie plusieurs services Azure pour fournir une base sécurisée, contrôlée et prête pour être utilisée en entreprise. Cet environnement comporte les éléments suivants :

- Les [journaux Azure Monitor](../../../../azure-monitor/platform/data-platform-logs.md) et un compte de stockage Azure pour s’assurer que les journaux de ressource, les journaux d’activité, les métriques et les flux de trafic des réseaux sont stockés dans un emplacement central pour faciliter l’interrogation, l’analytique, l’archivage et la création d’alertes.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (version standard) pour fournir une protection des ressources Azure contre les menaces.
- [Réseau virtuel Azure](../../../../virtual-network/virtual-networks-overview.md) dans les sous-réseaux Hub pour la connectivité à un réseau local, une pile d’entrées/de sorties pour la connectivité Internet et des sous-réseaux facultatifs pour déployer d’autres services de gestion et d’administration. Le réseau virtuel du Spoke contient des sous-réseaux pour héberger les charges de travail applicatives. D’autres sous-réseaux peuvent être créés après le déploiement si besoin pour prendre en charge les scénarios applicables.
- Le [Pare-feu Azure](../../../../firewall/overview.md) pour acheminer l’ensemble du trafic Internet sortant et permettre le trafic Internet entrant via un jumpbox. (Les règles de pare-feu par défaut bloquent l’ensemble du trafic Internet entrant et sortant et des règles doivent être configurées après le déploiement, le cas échéant.)
- Des [groupes de sécurité réseau](../../../../virtual-network/network-security-group-how-it-works.md) (NSG) affectés à tous les sous-réseaux (sauf les sous-réseaux appartenant au service comme Azure Bastion, la passerelle et le Pare-feu Azure) configurés pour bloquer tout le trafic Internet entrant et sortant.
- Des [groupes de sécurité applicatifs](../../../../virtual-network/application-security-groups.md) pour pouvoir regrouper les machines virtuelles Azure afin d’appliquer des stratégies de sécurité réseau communes.
- Des [tables de route](../../../../virtual-network/manage-route-table.md) pour router tout le trafic Internet sortant depuis les sous-réseaux via le pare-feu. (Les règles de Pare-feu Azure et NSG devront être configurées après le déploiement pour ouvrir la connectivité.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) pour superviser, diagnostiquer et consulter les métriques des ressources du Réseau virtuel Azure.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) pour protéger les ressources Azure contre les attaques DDoS.
- [Azure Bastion](../../../../bastion/bastion-overview.md) pour fournir une connectivité fluide et sécurisée à une machine virtuelle qui n’a pas besoin d’adresse IP publique, d’agent ou de logiciel client spécial.
- Une [passerelle VPN Azure](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) pour permettre le trafic chiffré entre un réseau virtuel Azure et un emplacement local sur le réseau Internet public.

> [!NOTE] 
> Azure Security Benchmark Foundation présente une architecture de base pour les charges de travail. Le diagramme de l’architecture ci-dessus comprend plusieurs ressources théoriques pour démontrer l’utilisation potentielle de sous-réseaux. Vous devez quand même déployer des charges de travail sur cette architecture de base.

## <a name="next-steps"></a>Étapes suivantes

Vous avez découvert une vue d’ensemble et l’architecture de l’exemple de blueprint Azure Security Benchmark Foundation.

> [!div class="nextstepaction"]
> [Blueprint Azure Security Benchmark Foundation - Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).
