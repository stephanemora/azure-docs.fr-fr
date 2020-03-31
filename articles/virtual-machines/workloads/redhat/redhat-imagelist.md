---
title: Images Red Hat Enterprise Linux dans Azure | Microsoft Docs
description: En savoir plus sur les images Red Hat Enterprise Linux dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133794"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Images RHEL (Red Hat Enterprise Linux) disponibles dans Azure
Azure propose une variété d’images RHEL pour différents cas d’usage.

> [!NOTE]
> Toutes les images RHEL sont disponibles dans les clouds Azure publics et Azure Government. Elles ne sont pas disponibles dans les clouds Azure Chine.

## <a name="list-of-rhel-images"></a>Liste des images RHEL
Voici la liste des images RHEL disponibles dans Azure. Sauf indication contraire, toutes les images sont partitionnées et attachées aux dépôts RHEL standard (et non EUS, E4S). Les images suivantes sont actuellement disponibles pour une utilisation générale :

Offre| SKU | Partitionnement | Approvisionnement | Notes
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agent Linux |
|             | 6.8      | RAW    | Agent Linux |
|             | 6.9      | RAW    | Agent Linux |
|             | 6.10     | RAW    | Agent Linux |
|             | 7-RAW    | RAW    | Agent Linux | Famille d’images RHEL 7.x <br> Attachement à des dépôts standard par défaut (et non EUS).
|             | 7-LVM    | LVM    | Agent Linux | Famille d’images RHEL 7.x <br> Attachement à des dépôts standard par défaut (et non EUS). Si vous recherchez une image RHEL standard à déployer, utilisez cet ensemble d’images et/ou son équivalent de 2e génération.
|             | 7lvm-gen2| LVM    | Agent Linux | Famille d’images RHEL 7.x, 2e génération. <br> Attachement à des dépôts standard par défaut (et non EUS). Si vous recherchez une image RHEL standard à déployer, utilisez cet ensemble d’images et/ou son équivalent de 1re génération.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Famille d’images RHEL 7.x <br> Attachement à des dépôts standard par défaut (et non EUS).
|             | 7.2      | RAW    | Agent Linux |
|             | 7.3      | RAW    | Agent Linux |
|             | 7.4      | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut à compter d’avril 2019
|             | 74-gen2  | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut
|             | 7.5      | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut à compter de juin 2019
|             | 75-gen2  | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut
|             | 7.6      | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut à compter de mai 2019
|             | 76-gen2  | RAW    | Agent Linux | Attaché à des référentiels EUS par défaut
|             | 7,7      | LVM    | Agent Linux | Attaché à des référentiels EUS par défaut
|             | 8        | LVM    | Agent Linux | Famille d’images RHEL 8.x
|             | 8-gen2   | LVM    | Agent Linux | Hyper-V Génération 2 - Famille d’images RHEL 8.x.
RHEL-SAP      | 7.4      | LVM    | Agent Linux | RHEL 7.4 pour SAP HANA et les applications métiers. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 74sap-gen2| LVM    | Agent Linux | RHEL 7.4 pour SAP HANA et les applications métiers. Image de 2e génération. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 7.5       | LVM    | Agent Linux | RHEL 7.5 pour SAP HANA et les applications métiers. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 75sap-gen2| LVM    | Agent Linux | RHEL 7.5 pour SAP HANA et les applications métiers. Image de 2e génération. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 7.6       | LVM    | Agent Linux | RHEL 7.6 for SAP HANA et Business Applications. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 76sap-gen2| LVM    | Agent Linux | RHEL 7.6 for SAP HANA et Business Applications. Image de 2e génération. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
|             | 7,7       | LVM    | Agent Linux | RHEL 7.7 for SAP HANA et Business Applications. Attachement à des dépôts E4S, facture une prime pour SAP et RHEL, ainsi que des frais de calcul de base.
RHEL-SAP-HANA | 6.7       | RAW    | Agent Linux | RHEL 6.7 pour SAP HANA. Obsolète, privilégier les images RHEL-SAP.
|             | 7.2       | LVM    | Agent Linux | RHEL 7.2 pour SAP HANA. Obsolète, privilégier les images RHEL-SAP.
|             | 7.3       | LVM    | Agent Linux | RHEL 7.3 pour SAP HANA. Obsolète, privilégier les images RHEL-SAP.
RHEL-SAP-APPS | 6.8       | RAW    | Agent Linux | RHEL 6.8 pour applications métiers SAP. Obsolète, privilégier les images RHEL-SAP.
|             | 7.3       | LVM    | Agent Linux | RHEL 7.3 pour applications métiers SAP. Obsolète, privilégier les images RHEL-SAP.
RHEL-HA       | 7.4       | LVM    | Agent Linux | RHEL 7.4 avec add-on HA. Facture une prime pour HA et RHEL en plus des frais de calcul de base.
|             | 7.5       | LVM    | Agent Linux | RHEL 7.5 avec add-on HA. Facture une prime pour HA et RHEL en plus des frais de calcul de base.
|             | 7.6       | LVM    | Agent Linux | RHEL 7.6 avec add-on HA. Facture une prime pour HA et RHEL en plus des frais de calcul de base.
RHEL-SAP-HA   | 7.4          | LVM    | Agent Linux | RHEL 7.4 for SAP avec services de haute disponibilité et de mise à jour. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 74sapha-gen2 | LVM    | Agent Linux | RHEL 7.4 for SAP avec services de haute disponibilité et de mise à jour. Image de 2e génération. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 7.5          | LVM    | Agent Linux | RHEL 7.5 for SAP avec services de haute disponibilité et de mise à jour. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 7.6          | LVM    | Agent Linux | RHEL 7.6 for SAP avec services de haute disponibilité et de mise à jour. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 76sapha-gen2 | LVM    | Agent Linux | RHEL 7.6 for SAP avec services de haute disponibilité et de mise à jour. Image de 2e génération. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 7,7          | LVM    | Agent Linux | RHEL 7.7 for SAP avec services de haute disponibilité et de mise à jour. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
|             | 77sapha-gen2 | LVM    | Agent Linux | RHEL 7.7 for SAP avec services de haute disponibilité et de mise à jour. Image de 2e génération. Attachement à des dépôts E4S. Facture une prime pour SAP et HA ainsi que RHEL en plus des frais de calcul de base.
rhel-byos     |rhel-lvm74| LVM    | Agent Linux | Les images BYOS RHEL 7.4, qui ne sont associées à aucune source de mises à jour, ne sont pas facturées pour RHEL Premium.
|             |rhel-lvm75| LVM    | Agent Linux | Les images BYOS RHEL 7.5, qui ne sont associées à aucune source de mises à jour, ne sont pas facturées pour RHEL Premium.
|             |rhel-lvm76| LVM    | Agent Linux | Les images BYOS RHEL 7.6, qui ne sont associées à aucune source de mises à jour, ne sont pas facturées pour RHEL Premium.
|             |rhel-lvm77| LVM    | Agent Linux | Les images BYOS RHEL 7.7, qui ne sont associées à aucune source de mises à jour, ne sont pas facturées pour RHEL Premium.
|             |rhel-lvm8 | LVM    | Agent Linux | Les images BYOS RHEL 8 (la version mineure est affichée dans la valeur de version d’image), qui ne sont associées à aucune source de mises à jour, ne sont pas facturées pour RHEL Premium.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [images Red Hat dans Azure](./redhat-images.md).
* En savoir plus sur l’[infrastructure RHUI (Red Hat Update Infrastructure)](./redhat-rhui.md).
* En savoir plus sur l’[offre BYOS RHEL](./byos.md).
* Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
