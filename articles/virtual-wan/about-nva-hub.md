---
title: 'Azure Virtual WAN : À propos de l’appliance virtuelle réseau dans le hub'
description: Dans cet article, vous allez découvrir les appliances virtuelles réseau dans le hub Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: e70b899ea0133704e4ecab42dd4c2a09bca41804
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226309"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>À propos de l’appliance virtuelle réseau dans un hub Azure Virtual WAN (préversion)

Azure Virtual WAN a travaillé avec des partenaires de mise en réseau pour mettre en place une automatisation qui permet de connecter facilement les équipements locaux des clients (CPE) à une passerelle VPN Azure dans le hub virtuel. Azure travaille avec certains partenaires de mise en réseau pour permettre aux clients de déployer une appliance virtuelle réseau (NVA) tierce directement dans le hub virtuel. Cela permet aux clients qui souhaitent connecter les CPE de leur filiale à une NVA de la même marque dans le hub virtuel de tirer parti des capacités SD-WAN propriétaires de bout en bout.

Barracuda Networks et Cisco Systems sont les premiers partenaires à fournir des NVA qui peuvent être déployées directement sur le hub Virtual WAN.  Pour obtenir la documentation de leur produit respectif, consultez [Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan), [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) et [VMware SD-WAN](https://kb.vmware.com/s/article/82746). Azure travaille avec un plus grand nombre de partenaires, alors attendez-vous à ce que d’autres offres suivent.

> [!NOTE]
> Seules les offres NVA disponibles au déploiement dans le hub Virtual WAN peuvent être déployées dans ce dernier. Elles ne peuvent pas être déployées dans un réseau virtuel arbitraire dans Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Comment cela fonctionne-t-il ?

Les NVA disponibles au déploiement directement dans le hub Azure Virtual WAN sont conçues spécifiquement pour être utilisés dans le hub virtuel. L’offre NVA est publiée sur Place de marché Azure en tant qu’application managée. Les clients peuvent déployer l’offre directement à partir de Place de marché Azure, ou ils peuvent la déployer à partir du hub virtuel via le portail Azure.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Vue d’ensemble du processus":::

L’offre NVA de chaque partenaire proposera une expérience et des fonctionnalités légèrement différentes en fonction de la configuration requise pour le déploiement. Toutefois, il existe des points communs à toutes les offres des partenaires pour une NVA dans le hub Virtual WAN.

* Une expérience Applications managées proposée via Place de marché Azure.
* Une capacité et facturation basées sur les unités de l’infrastructure NVA.
* Des métriques d’intégrité exposées via Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Application managée

Toutes les offres NVA disponibles au déploiement dans le hub Virtual WAN disposeront d’une **application managée** disponible dans Place de marché Azure. Les applications managées permettent aux partenaires d’effectuer les opérations suivantes :

* Créer une expérience de déploiement personnalisée pour leur NVA.
* Fournir un modèle Resource Manager spécialisé qui leur permet de créer les NVA directement dans le hub Virtual WAN.
* Facturer des frais de licence logicielle directement ou via Place de marché Azure.
* Exposer des propriétés personnalisées et des compteurs de ressources.

Les partenaires NVA peuvent créer des ressources différentes en fonction du déploiement de l’appliance, des licences de configuration et des besoins de gestion. Lorsqu’un client crée une NVA dans le hub Virtual WAN, comme pour toutes les applications managées, deux groupes de ressources sont créés dans leur abonnement.

* **Groupe de ressources du client** : contient un espace réservé pour l’application managée. Les partenaires peuvent l’utiliser pour exposer les propriétés du client qu’ils choisissent ici.
* **Groupe de ressources managé** : les clients ne peuvent pas configurer ni modifier directement les ressources de ce groupe de ressources, car celui-ci est contrôlé par l’éditeur de l’application managée. Ce groupe de ressources contient la ressource **NetworkVirtualAppliances**.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Groupes de ressources Applications managées":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Unités d’infrastructure NVA

Lorsque vous créez une NVA dans le hub Virtual WAN, vous devez choisir le nombre d’unités d’infrastructure NVA avec lequel vous souhaitez la déployer. Une **unité d’infrastructure NVA** est une unité de capacité de bande passante agrégée pour une NVA du hub Virtual WAN. Une **unité d’infrastructure NVA** est similaire à une [unité d’échelle](pricing-concepts.md#scale-unit) de VPN en ce qui concerne la capacité et le dimensionnement.

* Une unité d’infrastructure NVA représente 500 Mbits/s de bande passante agrégée pour toutes les connexions de site de branche entrant dans cette NVA, à un coût de 0,25 $/heure.
* Azure prend en charge 1 à 80 unités d’infrastructure NVA pour un déploiement NVA donné dans un hub virtuel.
* Chaque partenaire peut proposer différents offres groupées d’unités d’infrastructure NVA qui sont un sous-ensemble de toutes les configurations d’unités d’infrastructure NVA prises en charge.

À l’instar des unités d’échelle VPN, si vous choisissez *1 unité d’infrastructure NVA de 500 Mbits/s*, cela implique que deux instances sont créées pour la redondance, chacune d’un débit maximal de 500 Mbits/s. Par exemple, si vous avez cinq branches offrant chacune un débit de 10 Mbits/s, vous avez besoin d’un agrégat de 50 Mbits/s à l’extrémité de la tête. La planification de la capacité agrégée de la NVA doit être effectuée après évaluation de la capacité nécessaire pour prendre en charge le nombre de branches conduisant au hub.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Processus de configuration des appliances virtuelles réseau

Les partenaires ont collaboré pour fournir une expérience qui configure automatiquement la NVA dans le cadre du processus de déploiement. Une fois que la NVA a été approvisionnée dans le hub virtuel, toute configuration supplémentaire qui peut être requise pour la NVA doit être effectuée par le biais du portail des partenaires NVA ou de l’application de gestion. L’accès direct à la NVA n’est pas disponible.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Ressources de site et de connexion avec des NVA

Contrairement aux configurations de la passerelle VPN Azure, vous n’avez pas besoin de créer de ressources de **site**, de ressources de **connexion site à site** ni de ressources de **connexion point à site** pour connecter vos sites de branche à votre NVA dans le hub Virtual WAN. Tout est géré par le biais du partenaire NVA.

Vous devez toujours créer des connexions de hub à réseau virtuel pour connecter votre hub Virtual WAN à vos réseaux virtuels Azure.

## <a name="supported-regions"></a><a name="regions"></a>Régions prises en charge

La NVA dans le hub virtuel est disponible en préversion dans les régions suivantes :

|Région géopolitique | Régions Azure|
|---|---|
| Amérique du Nord| USA Ouest, USA Centre Sud, USA Est 2   |
| Amérique du Sud | Brésil Sud |
| Europe | Europe Ouest, Royaume-Uni Sud|
|  Moyen-Orient | Émirats arabes unis Nord |
| Asia | Japon Est |
| Australie | Australie Est |

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Je suis partenaire d’appliance réseau et je souhaite obtenir notre NVA dans le hub.  Puis-je participer à ce programme partenaire ?

Malheureusement, nous n’avons pas la capacité d’accepter les offres de nouveaux partenaires pour le moment. Merci de revenir vers nous en novembre.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Puis-je déployer n’importe quelle NVA à partir de Place de marché Azure dans le hub Virtual WAN ?

À ce stade, seuls [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer) et [l’application Cisco Cloud vWAN](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) sont disponibles au déploiement dans le hub Virtual WAN.

### <a name="what-is-the-cost-of-the-nva"></a>Quel est le coût de la NVA ?

Vous devez acheter une licence pour la NVA auprès du fournisseur NVA.  Pour votre NVA Barracuda CloudGen WAN sous licence Barracuda, consultez la [page CloudGen WAN de Barracuda](https://www.barracuda.com/products/cloudgenwan). Actuellement, Cisco offre uniquement un modèle de licence BYOL (apportez votre propre licence) qui doit être achetée directement auprès de Cisco. En outre, des frais vous seront facturés par Microsoft pour les unités d’infrastructure NVA que vous consommez, ainsi que pour toute autre ressource que vous utilisez. Pour plus d’informations, voir les [concepts de tarification](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Puis-je déployer une NVA sur un hub De base ?

Non. Vous devez utiliser un hub Standard si vous souhaitez déployer une NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Puis-je déployer une NVA dans un hub sécurisé ?

Oui. Barracuda CloudGen WAN peut être déployé dans un hub à l’aide de Pare-feu Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Puis-je connecter un appareil CPE dans ma filiale à une NVA Barracuda CloudGen WAN dans le hub ?

Non. Barracuda CloudGen WAN est uniquement compatible avec des appareils CPE Barracuda. Pour en savoir plus sur les exigences de CloudGen WAN, consultez la [page CloudGen WAN de Barracuda](https://www.barracuda.com/products/cloudgenwan). Pour Cisco, il existe plusieurs appareils CPE SD-WAN qui sont compatibles. Consultez la documentation [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) pour connaître les CPE compatibles.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Quels sont les scénarios de routage pris en charge avec une NVA dans le hub ?

Tous les scénarios de routage pris en charge par Virtual WAN sont pris en charge par les NVA dans le hub.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez l’article [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
