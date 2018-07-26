---
title: Configuration système exigée Microsoft Azure Data Box Disk | Microsoft Docs
description: En savoir plus sur les conditions logicielles et réseau requises pour votre solution Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
ms.openlocfilehash: 7138fa70c8b5615ad84196703f3bd76009ba5811
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011487"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Configuration système exigée Azure Data Box Disk (Préversion)

Cet article décrit la configuration système importante qui est demandée pour la solution Microsoft Azure Data Box Disk et pour les clients accédant au disque Data Box. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre disque Data Box, puis d’y revenir si nécessaire pendant le déploiement, et après pour son fonctionnement.

> [!IMPORTANT]
> Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 

La configuration système demandée comprend les plateformes prises en charge pour les clients accédant aux disques, les comptes de stockage pris en charge et les types de stockage.


## <a name="supported-operating-systems-for-clients"></a>Systèmes d’exploitation pris en charge pour les clients

Voici une liste des systèmes d’exploitation pris en charge pour le déverrouillage de disque et l’opération de copie des données par le biais des clients connectés au disque Data Box.

| **Système d’exploitation/plateforme** | **Versions** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4.0 |
| .NET Framework |4.5 |

> [!NOTE] 
> La technologie BitLocker doit être activée sur les clients exécutant l’outil de déverrouillage de disque, et qui sont utilisés pour copier les données.


## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

Voici une liste des types de stockage pris en charge pour le disque Data Box.

| **Compte de stockage** | **Remarques** |
| --- | --- |
| Classique | Standard |
| Usage général  |Standard ; V1 et V2 sont pris en charge. Les niveaux chaud et froid sont tous les deux pris en charge. |


## <a name="supported-storage-types"></a>Types de stockage pris en charge

Voici une liste des types de stockage pris en charge pour le disque Data Box.

| **Format de fichier** | **Remarques** |
| --- | --- |
| Objet blob de blocs Azure | |
| Objet blob de pages Azure  | |


## <a name="next-step"></a>Étape suivante

* [Déployer votre disque Azure Data Box](data-box-disk-deploy-ordered.md)

