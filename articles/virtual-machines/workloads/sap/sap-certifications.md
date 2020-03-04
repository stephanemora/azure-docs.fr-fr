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
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598372"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifications et configurations SAP en cours sur Microsoft Azure

SAP et Microsoft ont une longue expérience de collaboration dans le cadre d’un fort partenariat qui présente des avantages mutuels pour leurs clients respectifs. Microsoft met constamment à jour sa plateforme et envoie régulièrement de nouvelles informations sur les certifications pour SAP afin de garantir que Microsoft Azure est la meilleure plateforme pour exécuter vos charges de travail SAP. Les tableaux suivants décrivent nos configurations prises en charge par Azure et la liste toujours plus grande de certifications SAP. 

## <a name="sap-hana-certifications"></a>Certifications SAP HANA
Références :

- [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pour le support SAP HANA des machines virtuelles Azure natives et des instances HANA volumineuses.

| Produit SAP | Systèmes d’exploitation pris en charge | Offres Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluant le logiciel client HANA composé des pilotes SQLODBC, ODBO (Windows uniquement), ODBC et JDBC, HANA Studio et HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Famille de machine virtuelle de la série D |
| Business One sur HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilité contrôlée pour GS5. Prise en charge complète de M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA sur Azure (grandes instances) [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA sur Azure (grandes instances) [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise pour BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA sur Azure (grandes instances) [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA sur Azure (grandes instances) <br /> [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Gardez à l’esprit que SAP utilise le terme « clustering » dans [Plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) comme synonyme de « Scale-out » et NON de « clustering à haute disponibilité »

## <a name="sap-netweaver-certifications"></a>Certifications SAP NetWeaver
Microsoft Azure est certifié pour les produits SAP suivants, avec un support complet de la part de Microsoft et de SAP.
Références :

- [1928533 - Applications SAP sur Azure : produits et types de machine virtuelle Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533) pour toutes les applications SAP NetWeaver, notamment SAP TREX, SAP LiveCache et SAP Content Server. Et toutes les bases de données, à l’exception de SAP HANA.


| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |N/A |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows et Oracle uniquement), DB2, SAP ASE |A5 à A11, D11 à D14, DS11 à DS14, DS11_v2 à DS15_v2, GS1 à GS5, D2s_v3 à D64s_v3, D2as_v4 à D64as_v4, E2s_v3 à E64s_v3, E2as_v4 à E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Autre charge de travail SAP prise en charge sur Azure

| Produit SAP | SE invité | SGBDR | Types de machine virtuelle |
| --- | --- | --- | --- |
| SAP Business One sur SQL Server | Windows  | SQL Server | Tous les types de machine virtuelle certifiés NetWeaver<br /> [Note SAP n°928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows et Linux | | Tous les types de machine virtuelle certifiés NetWeaver<br /> Note SAP n° 2451795 |
| Plateforme SAP Business Objects BI | Windows et Linux | | Note SAP n° 2145537 |
| SAP Data Services 4.2 | | | Note SAP n° 2288344 |
| Plateforme commerciale SAP Hybris 5.x et 6.x | Windows | SQL Server, Oracle | Tous les types de machine virtuelle certifiés NetWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
