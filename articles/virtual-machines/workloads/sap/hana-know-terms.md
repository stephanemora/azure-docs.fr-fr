---
title: Terminologie de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Terminologie de SAP HANA sur Azure (grandes instances).
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
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481035"
---
# <a name="know-the-terms"></a>Maîtriser la terminologie

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS**: Infrastructure en tant que service.
- **PaaS**: Plateforme en tant que service.
- **SaaS**: Logiciel en tant que service.
- **Composant SAP**: Une application SAP individuelle, tels que ERP Central Component (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP**: Un ou plusieurs composants SAP regroupement logiquement pour exécuter une fonction métier, telles que le développement, assurance qualité, formation, récupération d’urgence ou de production.
- **Paysage SAP**: Fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
- **Système SAP**: La combinaison de la couche SGBD et de la couche d’application de, par exemple, un système de développement SAP ERP, une SAP BW tester le système et un système de production SAP CRM. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites locaux et Azure. Un système SAP est déployé en local ou dans Azure. Vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Pour SAP HANA sur Azure (grandes instances), vous devez héberger la couche Application SAP des systèmes SAP dans des machines virtuelles et l’instance SAP HANA sur une unité dans le tampon SAP HANA sur Azure (grandes instances).
- **Tampon de grande Instance**: Une pile d’infrastructure matérielle certifiée SAP HANA TDI et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes Instances) :** Nom officiel de l’offre dans Azure exécuter des instances HANA dans sur du matériel certifié TDI SAP HANA est déployée dans les tampons de grande Instance dans différentes régions Azure. Le terme associé *Grande instance HANA* est la forme abrégée de *SAP HANA sur Azure (grandes instances)* largement utilisée dans ce guide de déploiement technique.
- **Entre différents locaux**: Décrit un scénario dans lequel les machines virtuelles sont déployées à un abonnement Azure qui dispose de site à site, multisite ou connectivité Azure ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, Azure Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure des abonnements Azure. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. 

   Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Pour plus d’informations, consultez [passerelle VPN Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [créer un réseau virtuel avec une connexion de site à site à l’aide du portail Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant** : Un client déployé dans le tampon de grande Instance HANA est isolé dans un *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles sur le niveau de tampon de grande instance HANA. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires sur le niveau de tampon de grande instance HANA.
- **Catégorie de référence (SKU)**: Pour la grande Instance HANA, les deux catégories suivantes de références SKU sont proposées :
    - **Classe type I**: S72, S72m, S96, S144, S144m, S192, S192m et S192xm
    - **Classe type II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm et S960m


De nombreuses ressources supplémentaires sont disponibles sur le déploiement d’une charge de travail SAP dans le cloud. Si vous planifiez un déploiement de SAP HANA dans Azure, vous devez être expérimenté, connaitre les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Avant de continuer, consultez [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations. 

**Étapes suivantes**
- Voir [Certification HLI](hana-certification.md)