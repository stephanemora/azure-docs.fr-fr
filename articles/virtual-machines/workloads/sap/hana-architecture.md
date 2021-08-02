---
title: Architecture de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez l’architecture de déploiement de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 547e48e9cecb672c5274bc001178b2ea2aaf47af
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577648"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architecture SAP HANA (grandes instances) sur Azure

Cet article décrit l’architecture de déploiement de SAP HANA sur Azure (grandes instances) (également appelée BareMetal infrastructure). 

À un niveau élevé, la solution SAP HANA sur Azure (grandes instances) dispose de la couche Application SAP sur des machines virtuelles. La couche base de données se trouve sur la grande instance HANA certifiée SAP située dans la même région Azure que les machines virtuelles Azure IaaS.

> [!NOTE]
> Déployez la couche application SAP dans la même région Azure que la couche système de gestion de base de données (SGBD) SAP. Cette règle est bien documentée dans les informations publiées sur les charges de travail SAP sur Azure. 

## <a name="architectural-overview"></a>Vue d’ensemble de l’architecture

L’architecture globale de SAP HANA sur Azure (grandes instances) fournit une configuration matérielle certifiée TDI SAP. Le matériel est un serveur non virtualisé, sans SE, hautes performances pour la base de données SAP HANA. Elle offre également la flexibilité d’Azure sur le plan de la mise à l’échelle des ressources de façon à ce que la couche Application SAP réponde à vos besoins.

![Présentation de l’architecture de SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image1-architecture.png)

L’architecture présentée est divisée en trois sections :

- **À droite** : montre une infrastructure locale qui exécute différentes applications dans des centres de données afin que les utilisateurs finaux puissent accéder à des applications métier (LOB), telles que SAP. Dans l’idéal, cette infrastructure locale est connectée à Azure avec [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Au centre** : montre Azure IaaS et, dans ce cas, l’utilisation des machines virtuelles pour héberger SAP ou d’autres applications qui utilisent SAP HANA en tant que SGBD. Les instances HANA plus petites qui fonctionnent avec la mémoire que les machines virtuelles fournissent sont déployées dans des machines virtuelles avec leur couche Application. Pour plus d’informations sur les machines virtuelles, consultez[Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).

   Les services réseau Azure permettent de regrouper des systèmes SAP ainsi que d’autres applications dans des réseaux virtuels. Ces réseaux virtuels se connectent à des systèmes locaux et à SAP HANA sur Azure (grandes instances).

   Pour les bases de données et les applications SAP NetWeaver prises en charge pour être exécutées dans Azure, consultez [Remarque sur la prise en charge SAP n° 1928533 - Applications SAP sur Azure : types de machines virtuelles Azure et produits pris en charge](https://launchpad.support.sap.com/#/notes/1928533). Pour la documentation sur le déploiement des solutions SAP sur Azure, consultez :

  -  [Utiliser SAP sur des machines virtuelles Windows](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md)

- **À gauche** : montre le matériel certifié TDI SAP HANA dans le tampon de grande instance Azure. Les unités HANA (grandes instances) se connectent aux réseaux virtuels de votre abonnement Azure via la même technologie que celle qu’utilise le système local pour se connecter à Azure. En mai 2019, nous avons introduit une optimisation qui permet la communication entre les unités HANA (grandes instances) et les machines virtuelles Azure, sans la passerelle ExpressRoute. Cette optimisation, appelée ExpressRoute FastPath, est représentée dans le schéma ci-dessus par les lignes rouges.

## <a name="components-of-the-azure-large-instance-stamp"></a>Composants du tampon de grande instance Azure

Le tampon de grande instance Azure combine les composants suivants :

- **Calcul** : serveurs basés sur différentes générations de processeurs Intel Xeon qui offrent la capacité de calcul nécessaire et sont certifiés SAP HANA.
- **Réseau** : une structure réseau haut débit unifiée qui relie le calcul, le stockage et les composants LAN.
- **Stockage** : une infrastructure de stockage accessible via une structure réseau unifiée. La capacité de stockage fournie varie selon la configuration SAP HANA sur Azure (grandes instances) déployée. Une capacité de stockage supplémentaire est disponible pour un coût mensuel additionnel.

## <a name="tenants"></a>Locataires

Dans l’infrastructure multilocataire du tampon de grande instance, les clients sont déployés en tant que locataires isolés. Au moment du déploiement de l’abonné, nommez un abonnement Azure au sein de votre inscription Azure. Cet abonnement Azure correspond à la grande instance HANA facturée. Ces locataires ont une relation 1:1 avec l’abonnement Azure. En termes de réseau, il est possible d’accéder à une unité de grande instance HANA déployée sur un abonné dans une région Azure à partir de différents réseaux virtuels qui appartiennent à différents abonnements Azure. Ces abonnements Azure doivent appartenir à la même inscription Azure.

## <a name="availability-across-regions"></a>Disponibilité dans les régions

Comme avec les machines virtuelles, SAP HANA sur Azure (grandes instances) est proposé dans plusieurs régions Azure. Pour offrir des fonctionnalités de récupération d’urgence, vous pouvez choisir d’accepter. Les différents tampons de grande instance d’une même région géopolitique sont connectés entre eux. Par exemple, les tampons de grande instance HANA des USA Ouest et des USA Est sont connectés via une liaison réseau dédiée à des fins de réplication de la récupération d’urgence.

## <a name="available-skus"></a>Références SKU disponibles

Tout comme Azure vous permet de choisir entre différents types de machines virtuelles, vous avez le choix entre différentes références (SKU) de HANA (grandes instances). Vous pouvez sélectionner la référence (SKU) appropriée pour le type de charge de travail SAP HANA spécifique. SAP applique la mémoire aux rapports de socket de processeur pour différentes charges de travail en fonction des générations de processeurs Intel. Pour plus d’informations sur les références (SKU) disponibles, consultez [Références (SKU) disponibles pour HLI](hana-available-skus.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrir l’architecture réseau de SAP HANA (grandes instances).

> [!div class="nextstepaction"]
> [Architecture réseau de SAP HANA (grandes instances)](hana-network-architecture.md)
