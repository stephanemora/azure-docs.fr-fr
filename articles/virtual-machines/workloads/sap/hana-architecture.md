---
title: Architecture de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Architecture de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d077487f85c789bcdfea3d91e29ee0d44ce82de0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239433"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architecture SAP HANA (grandes instances) sur Azure

À un niveau élevé, SAP HANA sur la solution Azure (grandes instances) dispose de la couche Application SAP résidant dans des machines virtuelles. La couche de bases de données réside sur le matériel configuré TDI SAP situé dans un tampon de grande Instance dans la même région Azure connectée à Azure IaaS.

> [!NOTE]
> Déployez la couche Application SAP dans la même région Azure que la couche SGBD SAP. Cette règle est bien documentée dans les informations publiées sur les charges de travail SAP sur Azure. 

L’architecture globale de SAP HANA sur Azure (grandes instances) fournit une configuration matérielle certifiée TDI SAP, c’est-à-dire un serveur non virtualisé, nu et hautes performances pour la base de données SAP HANA. Elle fournit également la capacité et la flexibilité d’Azure pour mettre à l’échelle les ressources pour que la couche Application SAP réponde à vos besoins.

![Présentation de l’architecture de SAP HANA sur Azure (grandes instances)](./media/hana-overview-architecture/image1-architecture.png)

L’architecture présentée est divisée en trois sections :

- **droit**: Montre une infrastructure sur site qui exécute différentes applications dans les données centres afin que les utilisateurs finaux puissent accéder métier des applications, telles que SAP. Dans l’idéal, cette locale infrastructure est connectée à Azure avec [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centre**: Montre Azure IaaS et, dans ce cas, utilisez des machines virtuelles pour héberger SAP ou autres applications qui utilisent SAP HANA comme un système SGBD. Les instances HANA plus petites qui fonctionnent avec la mémoire que les machines virtuelles fournissent sont déployées dans des machines virtuelles avec leur couche Application. Pour plus d’informations sur les machines virtuelles, consultez[Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).

   Les services réseau Azure permettent de regrouper des systèmes SAP ainsi que d’autres applications dans des réseaux virtuels. Ces réseaux virtuels se connectent à des systèmes locaux et à SAP HANA sur Azure (grandes instances).

   Pour les applications SAP NetWeaver et les bases de données qui sont prises en charge pour s’exécuter dans Azure, consultez [SAP Support Note #1928533 – applications SAP sur Azure : Produits et types de machines virtuelles Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533). Pour la documentation sur le déploiement des solutions SAP sur Azure, consultez :

  -  [Utiliser SAP sur des machines virtuelles Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **gauche**: Répertorie le matériel certifié TDI SAP HANA dans le tampon de grande Instance Azure. Les unités de grande Instance HANA sont connectées aux réseaux virtuels de votre abonnement Azure à l’aide de la même technologie que la connectivité locale à Azure. À compter de mai 2019, une optimisation a été commercialisée qui permet de communiquer entre les unités de grande Instance HANA et les machines virtuelles Azure sans l’intervention de la passerelle ExpressRoute. Cette optimisation appelée ExpressRoute rapide chemin d’accès s’affiche dans cette architecture (lignes rouges). 

Le tampon de grande instance Azure combine les composants suivants :

- **Informatique**: Serveurs qui sont basées sur différents génération de processeurs Intel Xeon qui fournissent des capacités de calcul nécessaires et sont certifiés SAP HANA.
- **Réseau** : Une structure réseau haut débit unifiée qui relie le calcul, de stockage et les composants de réseau local.
- **Stockage** : Une infrastructure de stockage qui est accessible via une structure réseau unifiée. La capacité de stockage spécifique fournie varie selon la configuration SAP HANA sur Azure (grandes instances) spécifique déployée. Une capacité de stockage supplémentaire est disponible pour un coût mensuel supérieur.

Dans l’infrastructure multilocataire du tampon de grande instance, les clients sont déployés en tant que locataires isolés. Au moment du déploiement de l’abonné, nommez un abonnement Azure au sein de votre inscription Azure. Cet abonnement Azure correspond à la grande instance HANA facturée. Ces locataires ont une relation 1:1 avec l’abonnement Azure. En termes de réseau, il est possible d’accéder à une unité de grande instance HANA déployée sur un abonné dans une région Azure à partir de différents réseaux virtuels qui appartiennent à différents abonnements Azure. Ces abonnements Azure doivent appartenir à la même inscription Azure. 

Comme avec les machines virtuelles, SAP HANA sur Azure (grandes instances) est proposé dans plusieurs régions Azure. Pour offrir des fonctionnalités de récupération d’urgence, vous pouvez choisir d’accepter. Les différents tampons de grande instance d’une même région géopolitique sont connectés entre eux. Par exemple, les tampons de grande instance HANA des États-unis de l’Ouest et des États-Unis de l’Est sont connectés via une liaison réseau dédiée à des fins de réplication de la récupération d’urgence. 

Tout comme vous avez le choix entre différents types de machines virtuelles avec des machines virtuelles Azure, vous pouvez choisir parmi différentes références SKU de la grande instance HANA adaptées aux divers types de charges de travail de SAP HANA. SAP applique la mémoire aux rapports de socket de processeur pour différentes charges de travail en fonction des générations de processeurs Intel. Le tableau qui suit affiche les types de références SKU proposées.

Vous pouvez trouver le [références SKU disponibles pour HLI](hana-available-skus.md).

**Étapes suivantes**
- Voir [Architecture réseau de SAP HANA (grandes instances)](hana-network-architecture.md)