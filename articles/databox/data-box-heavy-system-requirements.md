---
title: Requise lourd de zone de données de Microsoft Azure | Microsoft Docs
description: En savoir plus sur les logiciels et la configuration réseau requise pour votre lourd de zone de données Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427743"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Azure lourd de zone de données requise (version préliminaire)

Cet article décrit la configuration système requise importante pour votre appareil lourd de zone de données Azure et pour les clients se connectant à l’appareil. Nous vous recommandons de que lire attentivement les informations avant de déployer votre lourd de zone de données, puis d’y revenir si nécessaire pendant le déploiement et les opérations suivantes.

Les conditions requises sont les suivantes :

* **Configuration logicielle requise pour les hôtes qui se connectent à données boîte lourd** -décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur web locale, les clients SMB et des exigences supplémentaires pour les hôtes qui peuvent se connecter à la zone de données.
* **Configuration réseau requise pour les lourdes de boîte de données** -fournit des informations sur la configuration réseau requise pour un fonctionnement optimal de l’appareil lourd de zone de données.

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

Votre centre de données doit avoir un réseau haut débit. Pour une vitesse de copie plus rapide, deux connexions Gigabit Ethernet 40 peuvent être utilisées en parallèle (une par nœud). Si vous n’avez pas 40-GbE disponible, nous vous recommandons d’avoir au moins deux connexions de 10 GbE (une par nœud).

## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)
