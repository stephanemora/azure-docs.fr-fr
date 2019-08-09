---
title: Configuration système requise de Microsoft Azure Data Box Heavy | Microsoft Docs
description: En savoir plus sur les conditions logicielles et réseau requises pour votre solution Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427743"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Configuration système requise pour Azure Data Box Heavy (préversion)

Cet article décrit les principaux éléments de configuration système qui sont requis pour votre appareil Azure Data Box Heavy et pour les clients qui se connectent à cet appareil. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre Data Box Heavy, puis d’y revenir si nécessaire pendant le déploiement, et après pour son fonctionnement.

Les conditions requises sont les suivantes :

* **Configuration requise logicielle pour les hôtes se connectant à Data Box Heavy**: décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur web locale, les clients SMB et les exigences supplémentaires pour les hôtes qui peuvent se connecter à l’appareil Data Box.
* **Configuration requise réseau pour Data Box Heavy** : fournit des informations sur la configuration réseau nécessaire au fonctionnement optimal de l’appareil Data Box Heavy.

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

Votre centre de données doit avoir un réseau haut débit. Pour bénéficier de vitesses de copie supérieures, il est possible d’utiliser deux connexions 40 GbE (une par nœud) en parallèle. Si aucune connexion 40 GbE n’est disponible, nous vous recommandons d’avoir au moins deux connexions 10 GbE (une par nœud).

## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)
