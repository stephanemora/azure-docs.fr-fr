---
title: Configuration système requise de Microsoft Azure Data Box | Microsoft Docs
description: En savoir plus sur la configuration logicielle et réseau requise pour votre solution Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746928"
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

Voici une liste des comptes de stockage et des types de stockage pris en charge pour l’appareil Data Box. Pour obtenir la liste complète des différents types de comptes de stockage et de leurs fonctionnalités, consultez [Types de comptes de stockage](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages*** |**Azure Files** |**Remarques**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O |
| Édition Standard v1 à usage général  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| Stockage d'objets blob Standard |O | | |Les objets chauds et froids sont pris en charge. |

\* *- Les données chargées dans les objets blob de pages doivent être de 512 octets alignés, disques durs virtuels, par exemple.*

>[!NOTE]
> Les comptes Azure Data Lake Storage Gen 2 ne sont pas pris en charge.


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

Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si une connexion 10 GbE n’est pas disponible, une liaison de données 1 GbE peut être utilisée pour copier des données, mais la vitesse de copie sera affectée.

## <a name="next-step"></a>Étape suivante

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)

