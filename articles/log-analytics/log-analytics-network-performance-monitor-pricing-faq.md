---
title: FAQ sur la tarification d’Azure Network Performance Monitor | Microsoft Docs
description: Forum aux questions - Azure Network Performance Monitor
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Modification de la tarification d’Azure Network Performance Monitor

Nous avons pris note de vos commentaires et avons récemment introduit une [nouvelle expérience de tarification](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) pour différents services de surveillance sur Azure.

Ce document présente les modifications de tarification relatives à Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) dans un format de questions/réponses simple à lire.

Network Performance Monitor comporte trois composants :
* [Analyseur de performances](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Moniteur de points de terminaison de service](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) et
* [Moniteur ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

La section ci-dessous décrit les modifications de tarification pour les composants ci-dessus.

## <a name="performance-monitor-pm"></a>Analyseur de performances (PM)

**Comment l’analyseur de performances était-il facturé dans l’ancien modèle ?**

La facturation de NPM était basée sur l’utilisation/la consommation de deux composants :
* Nœuds : toutes les transactions synthétiques commencent et se terminent au niveau des nœuds. Les nœuds sont également appelés agents ou MMA (Microsoft Monitoring Agent).
* Données : les résultats des différents tests de réseau sont stockés dans le référentiel Log Analytics.

Dans l’ancien modèle, la facture était calculée en fonction du nombre de nœuds et du volume de données généré. 

**Comment l’utilisation de l’Analyseur de performances est-elle facturée dans le nouveau modèle ?**

La fonctionnalité de l’Analyseur de performances dans NPM est maintenant facturée sur une combinaison de : 

* liens de sous-réseau surveillés et
* volume de données

**Qu’est un lien de sous-réseau ?**

L’Analyseur de performances surveille la connectivité entre deux emplacements ou plus sur le réseau.  La connexion entre un groupe de nœuds/agents sur un sous-réseau et un groupe de nœuds sur un autre sous-réseau est appelée un lien de sous-réseau.

**J’ai deux sous-réseaux (sous-réseau A et B) et plusieurs agents sur chaque sous-réseau.  L’Analyseur de performances surveille la connectivité entre tous les agents sur le sous-réseau A et tous les agents sur le sous-réseau B.  Serai-je facturé en fonction du nombre de connexions entre sous-réseaux ?**

Non. Pour la facturation, toutes les connexions du sous-réseau A vers le sous-réseau B sont regroupées dans un même lien de sous-réseau, et vous êtes facturé pour une seule connexion.  L’Analyseur de performances continuera à surveiller la connectivité entre différents agents sur chaque sous-réseau.

**Quels sont les coûts de surveillance d’un lien de sous-réseau ?**

Reportez-vous à la section intitulée [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) pour le coût de surveillance d’un seul lien de sous-réseau pour un mois entier.

**Quels sont les frais relatifs aux données générées par l’Analyseur de performances ?**

Les frais d’ingestion (chargement de données vers Log Analytics, traitement et indexation) sont disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics.  (Section : Ingestion des données).

Les frais de conservation des données (à savoir, les données conservées à la discrétion du client, après le premier mois) sont également disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/log-analytics/).  (Section : Conservation des données).


## <a name="expressroute-monitor-erm"></a>Moniteur ExpressRoute (ERM)

**Quels sont les frais d’utilisation du moniteur ExpressRoute ?**

Les frais relatifs au moniteur ExpressRoute sont facturés en fonction du volume de données généré pendant la surveillance.   Reportez-vous à la question « Quels sont les frais relatifs aux données générées par l’Analyseur de performances ? » pour plus d’informations.

**J’utilise ERM pour surveiller plusieurs circuits ExpressRoute. Suis-je facturé en fonction du nombre de circuits surveillés ?**

Vous n’êtes pas facturé en fonction du nombre de circuits ou du type d’homologation (par exemple, homologation privée, homologation Microsoft).  Vous êtes facturé en fonction du volume de données, comme expliqué ci-dessus.

**Quel est le volume de données généré lors de la surveillance d’un seul circuit ?**

Le volume de données généré par mois, lors de la surveillance d’une connexion d’homologation privée, est le suivant :

|Percentile      |Données/mois (Mo)|
| :---:          |           ---:|
|50<sup>e</sup> |            192|
|60<sup>e</sup> |            256|
|70<sup>e</sup> |            360|
|80<sup>e</sup> |            498|
|90<sup>e</sup> |            870|
|95<sup>e</sup> |           1560|


Selon le tableau ci-dessus, les clients au 50ème percentile payent 192 Mo de données. À 2,30 $ USD/Go pour le premier mois, le coût de la surveillance d’un circuit est de 0,43 USD (= 192 * 2,30 / 1024) pour le premier mois.

**Quelles sont certaines des raisons de variations dans le volume de données ?**

Le volume de données de surveillance généré dépend de plusieurs facteurs tels que :
* nombre d’agents : la précision de l’isolation des pannes augmente à mesure que le nombre d’agents augmente
* nombre de tronçons sur le réseau
* nombre de chemins d’accès entre la source et la destination

Les clients aux percentiles supérieurs (dans le tableau ci-dessus) surveillent généralement leurs circuits à partir de plusieurs points stratégiques sur leur réseau local.  Plusieurs agents sont également placés plus en profondeur dans le réseau, loin du routeur de périphérie du fournisseur de services. Les agents sont souvent placés au niveau de plusieurs sites utilisateur, branches et racks de centres de données.

## <a name="service-endpoint-monitor-sepm"></a>Moniteur de points de terminaison de service (SEPM)

**Quels sont les frais d’utilisation du moniteur de points de terminaison de service ?**

Les frais d’utilisation du moniteur de points de terminaison de service sont calculés en fonction de :
* nombre de connexions
* volume de données

**Qu’est-ce qu’une connexion ?**

Une connexion est un test d’accessibilité à un point de terminaison (URL ou service réseau) à partir d’un seul agent pour le mois entier. Par exemple, la surveillance d’une connexion à bing.com à partir de trois agents correspond à trois connexions.

**Quels sont les coûts du moniteur de points de terminaison de service ?**

- Reportez-vous à la section [Contrôle de la connexion](https://azure.microsoft.com/pricing/details/network-watcher/) pour le coût de la surveillance d’un point de terminaison pour le mois entier.
- Les frais de données sont disponibles sur la [page de tarification](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics.  (Section : Ingestion des données).

## <a name="references"></a>Références

- [FAQ sur la tarification de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) : la section FAQ contient des informations sur le niveau gratuit, la tarification par nœud, etc.

