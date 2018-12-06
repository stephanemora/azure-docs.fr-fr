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
ms.openlocfilehash: 902c4c94cc7366af342816e9b4776af603711c8b
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275437"
---
# <a name="know-the-terms"></a>Maîtriser la terminologie

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS** : Infrastructure as a service.
- **PaaS** : Platform as a service.
- **SaaS** : Software as a service.
- **Composant SAP** : une application SAP spécifique, tel que ERP Central Component (ECC), Business Warehouse (BW), Gestionnaire de solutions ou Enterprise Portal (EP). Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP** : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
- **Paysage SAP** : fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
- **Système SAP** : la combinaison de couche SGBD/couche Application, tel que celui d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites locaux et Azure. Un système SAP est déployé en local ou dans Azure. Vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Pour SAP HANA sur Azure (grandes instances), vous devez héberger la couche Application SAP des systèmes SAP dans des machines virtuelles et l’instance SAP HANA sur une unité dans le tampon SAP HANA sur Azure (grandes instances).
- **Tampon de grande instance** : une pile d’infrastructure matérielle certifiée TDI SAP HANA et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA certifié TDI qui est déployé dans des tampons de grande instance dans différentes régions Azure. Le terme associé *Grande instance HANA* est la forme abrégée de *SAP HANA sur Azure (grandes instances)* largement utilisée dans ce guide de déploiement technique.
- **Entre différents locaux :** décrit un scénario dans lequel les machines virtuelles sont déployées vers un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, Azure Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure des abonnements Azure. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. 

   Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Pour plus d’informations, consultez [Planifier et concevoir pour la passerelle VPN Azure](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Créer un réseau virtuel avec une connexion de site à site à l’aide du Portail Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Abonné** : un client déployé dans le tampon de grande instance HANA est isolé dans un *abonné.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles sur le niveau de tampon de grande instance HANA. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires sur le niveau de tampon de grande instance HANA.
- **Catégorie de référence SKU** : pour la grande instance HANA, les deux catégories suivantes de références SKU sont proposées :
    - **Classe de type I** : S72, S72m, S96, S144, S144m, S192, S192m et S192xm
    - **Classe de type II** : S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm et S960m


De nombreuses ressources supplémentaires sont disponibles sur le déploiement d’une charge de travail SAP dans le cloud. Si vous planifiez un déploiement de SAP HANA dans Azure, vous devez être expérimenté, connaitre les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Avant de continuer, consultez [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations. 

**Étapes suivantes**
- Voir [Certification HLI](hana-certification.md)