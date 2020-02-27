---
title: Dimensionnement de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Dimensionnement de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616906"
---
# <a name="sizing"></a>Dimensionnement

Le redimensionnement de la grande instance HANA n’est pas différent du redimensionnement HANA en général. Pour les systèmes déployés et existants, si vous souhaitez les déplacer à partir d’autres SGBDR vers HANA, SAP fournit plusieurs rapports qui s’exécutent sur vos systèmes SAP existants. Si la base de données est déplacée vers HANA, ces rapports vérifient les données et calculent la mémoire requise pour l’instance HANA. Pour obtenir plus d’informations sur l’exécution de ces rapports et obtenir leurs mises à jour correctives ou versions les plus récentes, lisez les Remarques SAP suivantes :

- [Note de support SAP #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Dimensionnement de SAP Suite sur HANA)
- [Note de support SAP #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Rapport de dimensionnement de Suite sur HANA et S/4 HANA)
- [Note de support SAP 2121330 - FAQ : SAP BW on HANA Sizing Report (rapport de redimensionnement SAP BW sur HANA)](https://launchpad.support.sap.com/#/notes/2121330)
- [Remarque SAP n° 1736976 - Rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Remarque SAP n°2296290 - Nouveau rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/2296290)

Pour les nouvelles implémentations, SAP Quick Sizer permet de calculer les besoins en mémoire de l’implémentation du logiciel SAP sur HANA.

La mémoire requise pour HANA augmente à mesure que le volume de données augmente. Faites attention à votre consommation de mémoire actuelle afin de mieux prévoir ce qui va se passer dans l’avenir. Selon la mémoire requise, vous pouvez mapper votre demande à l’une des références SKU de la grand instance HANA.

**Étapes suivantes**
- Voir [Conditions d’intégration](hana-onboarding-requirements.md)