---
title: Configuration système requise de Microsoft Azure Data Box | Microsoft Docs
description: En savoir plus sur la configuration logicielle et réseau requise pour votre solution Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 3bce5dd1dd34c53276c5486cc255c4cd93bb6080
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242159"
---
# <a name="azure-data-box-system-requirements"></a>Conditions requises pour le système Data Box

Cet article décrit la configuration système importante qui est demandée pour votre solution Microsoft Azure Data Box et pour les clients accédant au Data Box. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre Data Box, puis d’y revenir si nécessaire pendant le déploiement, et après pour son fonctionnement.

Les conditions requises sont les suivantes :

* **Configuration logicielle pour les hôtes se connectant à Data Box** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur web locale, les clients SMB et des exigences supplémentaires pour les hôtes qui peuvent se connecter au Data Box.
* **Configuration réseau pour Data Box** : fournit des informations sur la configuration réseau nécessaire au fonctionnement optimal du Data Box.


## <a name="software-requirements"></a>Configuration logicielle requise

La configuration logicielle requise inclut les informations sur les systèmes d’exploitation pris en charge, les navigateurs pris en charge pour l’interface utilisateur web locale et les clients SMB.

### <a name="supported-operating-systems-for-clients"></a>Systèmes d’exploitation pris en charge pour les clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Systèmes de fichiers pris en charge pour les clients Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Types de stockage pris en charge

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navigateurs web pris en charge

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Configuration requise du réseau

Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si une connexion 10 GbE n’est pas disponible, une liaison de données 1 GbE peut être utilisée pour copier des données, mais la vitesse de copie sera affectée.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)
