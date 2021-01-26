---
title: Configuration système requise de Microsoft Azure Data Box | Microsoft Docs
description: Découvrez la configuration système requise pour votre Azure Data Box et pour les clients qui s’y connectent.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/23/2020
ms.author: alkohli
ms.openlocfilehash: 0190e295c2ab206242ab8a44a09ffb42746d75bd
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209546"
---
# <a name="azure-data-box-system-requirements"></a>Conditions requises pour le système Data Box

Cet article décrit les exigences système les plus importantes concernant Microsoft Azure Data Box et les clients qui se connectent à Data Box. Nous vous recommandons de lire attentivement les informations avant de déployer votre Data Box, puis de vous y référer si nécessaire pendant le déploiement et l’exploitation.

Les conditions requises sont les suivantes :

* **Exigences logicielles :** Pour les hôtes qui se connectent à Data Box, indique les systèmes d’exploitation pris en charge, les protocoles de transfert de fichiers, les comptes de stockage, les types de stockage, ainsi que les navigateurs pour l’interface utilisateur web locale.
* **Configuration réseau requise :** Pour Data Box, décrit les configuration requises pour les connexions réseau et les ports pour un fonctionnement optimal de Data Box.


## <a name="software-requirements"></a>Configuration logicielle requise

La configuration logicielle requise inclut les systèmes d’exploitation pris en charge, les protocoles de transfert de fichiers, les comptes de stockage, les types de stockage, ainsi que les navigateurs pour l’interface utilisateur web locale.

### <a name="supported-operating-systems-for-clients"></a>Systèmes d’exploitation pris en charge pour les clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocoles de transfert de fichiers pris en charge pour les clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> La connexion à des partages de Data Box n’est pas prise en charge via REST pour les ordres d’exportation. 

### <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Types de stockage pris en charge

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navigateurs web pris en charge

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Configuration requise du réseau

Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement de vous doter au minimum d’une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, vous pouvez utiliser une liaison de données 1 GbE pour copier les données. Cependant, cela affectera les vitesses de copie.

### <a name="port-requirements"></a>Exigences de port

Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic SMB ou NFS. Dans ce tableau, *Entrée* (ou *entrant*) indique que les requêtes clientes qui accèdent à votre appareil sont des requêtes entrantes. *Sortie* (ou *sortant*) indique que votre appareil Data Box envoie des données vers l’extérieur, hors déploiement. Par exemple, les données peuvent être sortantes vers Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)
