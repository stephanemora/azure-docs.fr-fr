---
title: Certifications Microsoft Azure pour SAP | Microsoft Docs
description: Liste mise à jour des configurations et certifications en cours pour SAP sur la plateforme Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/21/2021
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: a367c3753ae6d02dd4267a0135960ed630ef78c0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720660"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifications et configurations SAP en cours sur Microsoft Azure

SAP et Microsoft ont une longue expérience de collaboration dans le cadre d’un fort partenariat qui présente des avantages mutuels pour leurs clients respectifs. Microsoft met constamment à jour sa plateforme et envoie régulièrement de nouvelles informations sur les certifications pour SAP afin de garantir que Microsoft Azure est la meilleure plateforme pour exécuter vos charges de travail SAP. Les tableaux suivants décrivent nos configurations prises en charge par Azure et la liste toujours plus grande de certifications SAP. Cette liste est une liste de présentation qui peut différer légèrement des listes SAP officielles. L’accès aux données détaillées est documenté dans l’article [Logiciels SAP dont le déploiement est pris en charge sur Azure](./sap-supported-product-on-azure.md)

## <a name="sap-hana-certifications"></a>Certifications SAP HANA
Références :

- [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pour le support SAP HANA des machines virtuelles Azure natives et des instances HANA volumineuses.

| Produit SAP | Systèmes d’exploitation pris en charge | Offres Azure |
| --- | --- | --- |
| Business One sur HANA | SUSE Linux Enterprise | [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:120) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:125) |
| HANA Enterprise pour BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24;v:105) |


## <a name="sap-netweaver-certifications"></a>Certifications SAP NetWeaver
Microsoft Azure est certifié pour les produits SAP suivants, avec un support complet de la part de Microsoft et de SAP.
Références :

- [1928533 - Applications SAP sur Azure : produits et types de machine virtuelle Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533) pour toutes les applications SAP NetWeaver, notamment SAP TREX, SAP LiveCache et SAP Content Server. Et toutes les bases de données, à l’exception de SAP HANA.


| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP BusinessObjects BI | Windows |N/A |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, M32(d)ms_v2, M64(d)s_v2, M64(d)ms_v2, M128(d)s_v2, M128(d)ms_v2, M192i(d)s_v2, M192i(d)ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Autre charge de travail SAP prise en charge sur Azure

| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business One sur SQL Server | Windows  | SQL Server | Tous les types de machine virtuelle certifiés NetWeaver<br /> [Note SAP n°928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows et Linux | | Tous les types de machine virtuelle certifiés NetWeaver<br /> Note SAP n° 2451795 |
| Plateforme SAP Business Objects BI | Windows et Linux | | Note SAP n° 2145537 |
| SAP Data Services 4.2 | | | Note SAP n° 2288344 |
| SAP Hybris Commerce Platform  | Windows | SQL Server, Oracle | Tous les types de machine virtuelle certifiés NetWeaver <br /> [Documentation Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce Platform  | SLES 12 ou plus récent | SAP HANA | Tous les types de machine virtuelle certifiés NetWeaver <br /> [Documentation Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce Platform  | RHEL 7 ou plus récent | SAP HANA | Tous les types de machine virtuelle certifiés NetWeaver <br /> [Documentation Hybris]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce Platform 1811 et ultérieur  | Windows, SLES ou RHEL | SQL Azure DB | Tous les types de machine virtuelle certifiés NetWeaver <br /> [Documentation Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
