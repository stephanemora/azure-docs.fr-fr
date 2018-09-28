---
title: Configuration système requise de Microsoft Azure Data Box | Microsoft Docs
description: En savoir plus sur la configuration logicielle et réseau requise pour votre solution Azure Data Box
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
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f97c6174adf454a031e94942843075c457236575
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982971"
---
# <a name="azure-data-box-system-requirements"></a>Conditions requises pour le système Data Box

Cet article décrit la configuration système importante qui est demandée pour votre solution Microsoft Azure Data Box et pour les clients accédant au Data Box. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre Data Box, puis d’y revenir si nécessaire pendant le déploiement, et après pour son fonctionnement.

Les conditions requises sont les suivantes :

* **Configuration logicielle pour les hôtes se connectant à Data Box** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur web locale, les clients SMB et des exigences supplémentaires pour les hôtes qui peuvent se connecter au Data Box.
* **Configuration réseau pour Data Box** : fournit des informations sur la configuration réseau nécessaire au fonctionnement optimal du Data Box.


## <a name="software-requirements"></a>Configuration logicielle requise

La configuration logicielle requise inclut les informations sur les systèmes d’exploitation pris en charge, les navigateurs pris en charge pour l’interface utilisateur web locale et les clients SMB.

### <a name="supported-operating-systems-for-clients"></a>Systèmes d’exploitation pris en charge pour les clients

Voici une liste des systèmes d’exploitation pris en charge pour l’opération de copie des données par le biais des clients connectés à l’appareil Data Box.

| **Système d’exploitation** | **Versions** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Systèmes de fichiers pris en charge pour les clients Linux

| **Protocoles** | **Versions** | 
| --- | --- | 
| SMB |2.X et versions ultérieures |
| NFS | Toutes les versions jusqu’à la version 4.1 incluse|

### <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

Voici une liste des types de stockage pris en charge pour l’appareil Data Box.

| **Compte de stockage** | **Remarques** |
| --- | --- |
| Classique | standard |
| Usage général  |Standard ; V1 et V2 sont pris en charge. |
| Blob |Les objets chauds et froids sont pris en charge. |


### <a name="supported-storage-types"></a>Types de stockage pris en charge

Voici une liste des types de stockage pris en charge pour l’appareil Data Box.

| **Format de fichier** | **Remarques** |
| --- | --- |
| Objet blob de blocs Azure | |
| Objet blob de pages Azure  | Les données doivent être des données alignées de 512 octets.|
| Azure Files | |


### <a name="supported-web-browsers"></a>Navigateurs web pris en charge

Voici une liste des navigateurs web pris en charge pour l’interface utilisateur web locale.

| **Browser** | **Versions** | **Conditions/remarques supplémentaires** |
| --- | --- | --- |
| Google Chrome |Version la plus récente |Testé avec Chrome|
| Microsoft Edge |Version la plus récente | |
| FireFox | Version la plus récente | Testé avec FireFox|
| Internet Explorer |Version la plus récente |Si vous ne pouvez pas vous connecter, vérifiez si les cookies et Javascript sont activés. Pour activer l’accès de l’interface utilisateur, ajoutez l’adresse IP de l’appareil à **Actions de confidentialité** afin que l’appareil puisse accéder aux cookies. |


## <a name="networking-requirements"></a>Configuration requise du réseau

Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement une connexion 10 GbE minimum. Si une connexion 10 GbE n’est pas disponible, une liaison de données 1 GbE peut être utilisée pour copier des données, mais la vitesse de copie sera affectée.

## <a name="next-step"></a>Étape suivante

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)

