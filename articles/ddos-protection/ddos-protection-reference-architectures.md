---
title: Architectures de référence Azure DDoS Protection
description: Découvrez les architectures de référence Azure DDoS Protection.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: ddb42dde242bb9c3f33a6dc3f8f52a147367f295
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108139108"
---
# <a name="ddos-protection-reference-architectures"></a>Architectures de référence de la protection DDoS

DDoS Protection Standard est conçu [pour les services déployés dans un réseau virtuel](../virtual-network/virtual-network-for-azure-services.md). Pour les autres services, le service DDoS Protection Basic par défaut s’applique. Les architectures de référence suivantes sont organisées par scénarios, avec regroupement des modèles d’architecture.

## <a name="virtual-machine-windowslinux-workloads"></a>Charges de travail de machines virtuelles (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Application exécutée sur des machines virtuelles à charge équilibrée

Cette architecture de référence présente un ensemble de pratiques éprouvées pour exécuter plusieurs machines virtuelles Windows dans un groupe identique derrière un équilibreur de charge, afin d’améliorer la disponibilité et l’extensibilité. Cette architecture convient à n’importe quelle charge de travail sans état, par exemple un serveur web.

![Schéma de l’architecture de référence pour une application en cours d’exécution sur des machines virtuelles à charge équilibrée](./media/ddos-best-practices/image-9.png)

Dans cette architecture, une charge de travail est répartie entre plusieurs instances de machine virtuelle. Il existe une seule adresse IP publique, et le trafic Internet est réparti entre les machines virtuelles à l’aide d’un équilibreur de charge. DDoS Protection Standard est activé sur le réseau virtuel de l’équilibreur de charge Azure (Internet) associé à l’adresse IP publique.

L’équilibreur de charge distribue les demandes Internet entrantes aux instances de machine virtuelle. Les groupes de machines virtuelles identiques permettent d’augmenter ou de réduire le nombre de machines virtuelles manuellement ou automatiquement en fonction de règles prédéfinies. Ceci est important si la ressource fait l’objet d’une attaque DDoS. Pour plus d’informations sur cette architecture de référence, consultez [cet article](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Application s’exécutant sur une architecture Windows multiniveau

Il existe de nombreuses façons d’implémenter une architecture multiniveau. Le schéma suivant présente une application web à trois niveaux standard. Cette architecture repose sur celle décrite dans l’article [Exécuter des machines virtuelles à charge équilibrée pour la scalabilité et la disponibilité](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Les niveaux Web et Business utilisent des machines virtuelles à charge équilibrée.

![Schéma de l’architecture de référence pour une application en cours d’exécution sur une architecture Windows multiniveau](./media/ddos-best-practices/image-10.png)

Dans cette architecture, DDoS Protection Standard est activé sur le réseau virtuel. Toutes les adresses IP publiques dans le réseau virtuel bénéficient d’une protection DDoS au niveau des couches 3 et 4. Pour protéger la couche 7, déployez Application Gateway dans la référence SKU WAF. Pour plus d’informations sur cette architecture de référence, consultez [cet article](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Les scénarios dans lesquels une seule machine virtuelle s’exécute derrière une adresse IP publique ne sont pas pris en charge.

### <a name="paas-web-application"></a>Application web PaaS

Cette architecture de référence montre l’exécution d’une application Azure App Service dans une seule région. Cette architecture présente un ensemble de pratiques éprouvées pour une application web utilisant [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) et [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
La région de secours est configurée pour les scénarios de basculement.

![Schéma de l’architecture de référence pour une application web PaaS](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager achemine les requêtes entrantes à Application Gateway dans l’une des régions. Pendant le fonctionnement normal, il achemine les requêtes à Application Gateway dans la région active. Si cette région n’est plus disponible, Traffic Manager bascule sur Application Gateway dans la région de secours.

Tout le trafic provenant d’Internet à destination de l’application web est acheminé à [l’adresse IP publique d’Application Gateway](../application-gateway/application-gateway-web-app-overview.md) par le biais de Traffic Manager. Dans ce scénario, App Service (application web) n’est pas directement accessible de l’extérieur et est protégé par Application Gateway. 

Nous vous recommandons de configurer la référence SKU WAF Application Gateway (mode prévention) pour mettre en place une protection contre les attaques de couche 7 (HTTP/HTTPS/Web Socket). Par ailleurs, les applications web sont configurées pour [accepter uniquement le trafic provenant de l’adresse IP d’Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Pour plus d’informations sur cette architecture de référence, consultez [cet article](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="protecting-on-premises-resources"></a>Protection des ressources locales

Vous pouvez tirer parti de l’échelle, de la capacité et de l’efficacité d’Azure DDoS Protection Standard pour protéger vos ressources locales, en hébergeant une adresse IP publique dans Azure et en redirigeant vers votre environnement local le trafic vers l’origine principale.

![Protection des ressources locales](./media/reference-architectures/ddos-on-prem.png)

Si vous disposez d’une application web qui reçoit du trafic d’Internet, vous pouvez héberger l’application web derrière Application Gateway, puis la protéger avec WAF contre les attaques web de couche 7, telles que l’injection SQL et Slowloris. Les origines principales de votre application se trouveront dans votre environnement local, qui est connecté via le VPN. 

Les ressources principales de l’environnement local ne sont pas exposées à l’Internet public. Seule l’adresse IP publique AppGW/WAF est exposée à Internet et le nom DNS de votre application mappe à cette adresse IP publique. 

Lorsque DDoS protection Standard est activé sur le réseau virtuel qui contient l’AppGW/WAF, DDoS Protection Standard protège votre application en atténuant le trafic et en acheminant le trafic supposé propre vers votre application. 

Cet [article](../azure-vmware/protect-azure-vmware-solution-with-application-gateway.md) explique comment utiliser DDoS protection Standard avec Application Gateway pour protéger une application web s’exécutant sur une solution VMware Azure.

## <a name="mitigation-for-non-web-paas-services"></a>Atténuation pour les services PaaS non web

### <a name="hdinsight-on-azure"></a>HDInsight sur Azure

Cette architecture de référence montre la configuration de DDoS Protection Standard pour un [cluster Azure HDInsight](../hdinsight/index.yml). Assurez-vous que le cluster HDInsight est lié à un réseau virtuel et que DDoS Protection est activé sur ce réseau virtuel.

![Volets « HDInsight » et « Paramètres avancés », avec les paramètres de réseau virtuel](./media/ddos-best-practices/image-12.png)

![Sélection pour activer DDoS Protection](./media/ddos-best-practices/image-13.png)

Dans cette architecture, le trafic destiné au cluster HDInsight en provenance d’Internet est acheminé à l’adresse IP publique associée à l’équilibreur de charge de la passerelle HDInsight. L’équilibreur de charge de la passerelle envoie ensuite directement le trafic aux nœuds principaux ou aux nœuds worker. Dans la mesure où DDoS Protection Standard est activé sur le réseau virtuel HDInsight, toutes les adresses IP publiques dans le réseau virtuel bénéficient d’une protection DDoS au niveau des couches 3 et 4. Cette architecture de référence peut être combinée avec les architectures de référence multiniveau/multirégion.

Pour plus d’informations sur cette architecture de référence, consultez la documentation [Étendre HDInsight à l’aide d’un réseau virtuel Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> Azure App Service Environment pour PowerApps ou la gestion des API dans un réseau virtuel avec une adresse IP publique ne sont pas pris en charge en mode natif.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un plan de protection DDoS](manage-ddos-protection.md).