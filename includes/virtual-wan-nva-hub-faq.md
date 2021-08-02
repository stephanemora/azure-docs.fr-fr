---
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/02/2021
ms.author: cherylmc
ms.openlocfilehash: cf11d4614cd4b6c3c387ef83b63da506a1702afc
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411902"
---
### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub-can-i-join-this-partner-program"></a>Je suis partenaire d’appliance réseau et je souhaite obtenir notre NVA dans le hub. Puis-je participer à ce programme partenaire ?

Malheureusement, nous n’avons pas la capacité d’accepter les offres de nouveaux partenaires pour le moment. Revenez nous voir à une date ultérieure !

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Puis-je déployer n’importe quelle NVA à partir de Place de marché Azure dans le hub Virtual WAN ?

À ce stade, seuls [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer), [l’application Cisco Cloud vWAN](https://azuremarketplace.microsoft.com/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) et [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) sont disponibles au déploiement dans le hub Virtual WAN.

### <a name="what-is-the-cost-of-the-nva"></a>Quel est le coût de la NVA ?

Vous devez acheter une licence pour la NVA auprès du fournisseur NVA. Pour votre NVA Barracuda CloudGen WAN sous licence Barracuda, consultez la [page CloudGen WAN de Barracuda](https://www.barracuda.com/products/cloudgenwan). Actuellement, Cisco offre uniquement un modèle de licence BYOL (apportez votre propre licence) qui doit être achetée directement auprès de Cisco. En outre, des frais vous seront facturés par Microsoft pour les unités d’infrastructure NVA que vous consommez, ainsi que pour toute autre ressource que vous utilisez. Pour plus d’informations, voir les [concepts de tarification](../articles/virtual-wan/pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Puis-je déployer une NVA sur un hub De base ?

Non. Vous devez utiliser un hub Standard si vous souhaitez déployer une NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Puis-je déployer une NVA dans un hub sécurisé ?

Oui. Il est possible de déployer des NVA de partenaires dans un hub à l’aide de Pare-feu Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Puis-je connecter un appareil CPE dans ma filiale à une NVA Barracuda CloudGen WAN dans le hub ?

Non. Barracuda CloudGen WAN est uniquement compatible avec des appareils CPE Barracuda. Pour en savoir plus sur les exigences de CloudGen WAN, consultez la [page CloudGen WAN de Barracuda](https://www.barracuda.com/products/cloudgenwan). Pour Cisco, il existe plusieurs appareils CPE SD-WAN qui sont compatibles. Consultez la documentation [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) pour connaître les CPE compatibles.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Quels sont les scénarios de routage pris en charge avec une NVA dans le hub ?

Tous les scénarios de routage pris en charge par Virtual WAN sont pris en charge par les NVA dans le hub.
