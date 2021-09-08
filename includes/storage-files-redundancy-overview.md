---
title: Fichier Include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 400e8de6b8d8481cccd6d2b3bcb25620f956f823
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423381"
---
Pour protéger les données de vos partages de fichiers Azure contre la perte ou l’altération des données, tous les partages de fichiers Azure stockent plusieurs copies de chaque fichier au fur et à mesure de leur écriture. En fonction des exigences de votre charge de travail, vous pouvez sélectionner des degrés de redondance supplémentaires. Azure Files prend actuellement en charge les options de redondance des données suivantes :

- **Stockage localement redondant (LRS)**  : avec LRS, chaque fichier est stocké trois fois au sein d’un cluster de stockage Azure. Cela le protège de la perte de données due à des défaillances matérielles, telles qu’un lecteur de disque défectueux. Toutefois, si un sinistre tel qu’un incendie ou une inondation se produit à l’intérieur du centre de données, tous les réplicas d’un compte de stockage utilisant un stockage localement redondant risquent d’être perdus ou irrécupérables.
- **Stockage redondant dans une zone (ZRS)**  : avec ZRS, trois copies de chaque fichier stocké, mais ces copies sont physiquement isolées dans trois clusters de stockage distincts dans différentes *zones de disponibilité* Azure. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d'un ou de plusieurs centres de données équipés d'une alimentation, d'un système de refroidissement et d'un réseau indépendants. Une écriture sur le stockage n’est pas acceptée tant qu’elle n’est pas écrite dans les clusters de stockage dans les trois zones de disponibilité. 
- **Stockage géo-redondant (GRS)**  : avec GRS, vous avez deux régions, une région primaire et une région secondaire. Les fichiers sont stockés trois fois au sein d’un cluster de stockage Azure dans la région primaire. Les écritures sont répliquées de façon asynchrone dans une région secondaire définie par Microsoft. Le stockage GRS fournit six copies de vos données réparties entre deux régions Azure. En cas de sinistre majeur, tel que la perte permanente d’une région Azure en raison d’une catastrophe naturelle ou d’un autre événement similaire, Microsoft effectuera un basculement afin que la région secondaire en vigueur devienne la région primaire, assurant toutes les opérations. Étant donné que la réplication entre les régions primaire et secondaire est asynchrone, en cas de sinistre majeur, les données qui ne sont pas encore répliquées dans la région secondaire seront perdues. Vous pouvez également effectuer le basculement manuel d’un compte de stockage géoredondant.
- **Stockage géo-redondant dans une zone (GZRS)**  : vous pouvez considérer GZRS comme s’il s’agissait de ZRS, mais avec la géo-redondance. Avec GZRS, les fichiers sont stockés trois fois sur trois clusters de stockage distincts dans la région primaire. Toutes les écritures sont ensuite répliquées de façon asynchrone dans une région secondaire définie par Microsoft. Le processus de basculement pour GZRS fonctionne de la même façon que GRS.

Les partages de fichiers Azure standard jusqu’à 5 Tio prennent en charge les quatre types de redondance. Les partages de fichiers standard supérieurs à 5-Tio prennent uniquement en charge LRS et ZRS. Les partages de fichiers Premium Azure prennent uniquement en charge LRS et ZRS.

Les comptes de stockage v2 universels (GPv2) fournissent deux options de redondance supplémentaires qui ne sont pas prises en charge par Azure Files : le stockage géographiquement redondant avec accès en lecture, souvent appelé RA-GRS, et le stockage géographiquement redondant interzone avec accès en lecture, souvent appelé RA-GZRS. Vous pouvez approvisionner des partages de fichiers Azure dans des comptes de stockage avec ces options définies, mais Azure Files ne prend pas en charge la lecture de la région secondaire. Les partages de fichiers Azure déployés dans des comptes de stockage géographiquement redondant ou géographiquement redondant interzone avec accès en lecture sont facturés en tant que stockage géoredondant ou géoredondant interzone, respectivement.