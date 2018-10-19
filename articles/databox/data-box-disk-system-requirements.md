---
title: Configuration système exigée Microsoft Azure Data Box Disk | Microsoft Docs
description: En savoir plus sur les conditions logicielles et réseau requises pour votre solution Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: d10ca5d704892ae0a1494d729b46abf0fc06aa64
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092250"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Configuration système exigée Azure Data Box Disk (Préversion)

Cet article décrit la configuration système importante qui est demandée pour la solution Microsoft Azure Data Box Disk et pour les clients accédant au disque Data Box. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre disque Data Box, puis d’y revenir si nécessaire pendant le déploiement, et après pour son fonctionnement.

> [!IMPORTANT]
> Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 

La configuration système demandée comprend les plateformes prises en charge pour les clients accédant aux disques, les comptes de stockage pris en charge et les types de stockage.


## <a name="supported-operating-systems-for-clients"></a>Systèmes d’exploitation pris en charge pour les clients

Voici une liste des systèmes d’exploitation pris en charge pour le déverrouillage de disque et l’opération de copie des données par le biais des clients connectés au disque Data Box.

| **Système d’exploitation** | **Versions testées** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Autres logiciels requis pour les clients Windows

Pour un client Windows, les éléments suivants doivent également être installés.

| **Logiciel**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Autres logiciels requis pour les clients Linux

Pour les clients Linux, l’ensemble d’outils Data Box Disk installe les logiciels requis suivants :

- dislocker
- OpenSSL

## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

Voici une liste des types de stockage pris en charge pour le disque Data Box.

| **Compte de stockage** | **Remarques** |
| --- | --- |
| Classique | standard |
| Usage général  |Standard ; V1 et V2 sont pris en charge. Les niveaux chaud et froid sont tous les deux pris en charge. |


## <a name="supported-storage-types"></a>Types de stockage pris en charge

Voici une liste des types de stockage pris en charge pour le disque Data Box.

| **Format de fichier** | **Remarques** |
| --- | --- |
| Objet blob de blocs Azure | |
| Objet blob de pages Azure  | |


## <a name="next-step"></a>Étape suivante

* [Déployer votre disque Azure Data Box](data-box-disk-deploy-ordered.md)

