---
title: Dimensionnement de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez-en plus sur le dimensionnement de SAP HANA sur Azure (Grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b93e7febc478452d713d115162280f0fd2657a3
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579460"
---
# <a name="sizing"></a>Dimensionnement

Dans cet article, nous examinerons des informations utiles pour le dimensionnement de HANA - Grandes instances. En général, le dimensionnement de HANA - Grandes instances n'est pas différent du dimensionnement HANA. 

## <a name="moving-an-existing-system-to-sap-hana-large-instances"></a>Déplacer un système existant vers SAP HANA (Grandes instances)

Supposons que vous souhaitiez déplacer un système déployé existant vers HANA à partir d'un autre système de gestion de base de données relationnelle (SGBDR). SAP fournit des rapports à exécuter sur votre système SAP existant. Si la base de données est déplacée vers HANA, ces rapports vérifient les données et calculent la mémoire requise pour l’instance HANA. 

Pour obtenir plus d’informations sur l’exécution de ces rapports et obtenir leurs mises à jour correctives ou versions les plus récentes, lisez les Remarques SAP suivantes :

- [Note de support SAP #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Dimensionnement de SAP Suite sur HANA)
- [Note de support SAP #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Rapport de dimensionnement de Suite sur HANA et S/4 HANA)
- [Remarque SAP n° 2121330 - FAQ : rapport de redimensionnement SAP BW sur HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Remarque SAP n° 1736976 - Rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Remarque SAP n°2296290 - Nouveau rapport de redimensionnement de BW sur HANA](https://launchpad.support.sap.com/#/notes/2296290)

## <a name="sizing-greenfield-implementations"></a>Dimensionnement des nouvelles implémentations

Si vous démarrez votre implémentation à partir de zéro, SAP Quick Sizer calcule les besoins en mémoire de l'implémentation du logiciel SAP sur HANA.

## <a name="memory-requirements"></a>Besoins en mémoire

La mémoire requise pour HANA augmente à mesure que le volume de données augmente. Faites attention à votre consommation de mémoire actuelle afin de mieux prévoir ce qui va se passer dans l’avenir. Selon la mémoire requise, vous pouvez mapper votre demande à l’une des références SKU de la grand instance HANA.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les conditions d'intégration de HANA - Grandes instances

> [!div class="nextstepaction"]
> [Conditions d’intégration](hana-onboarding-requirements.md)
