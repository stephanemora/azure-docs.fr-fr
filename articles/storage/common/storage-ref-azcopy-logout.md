---
title: azcopy logout | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 18add90ace11038fbba662b13400ccc7ddd8e763
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219939"
---
# <a name="azcopy-logout"></a>azcopy logout

Déconnecte l’utilisateur et met fin à l’accès aux ressources de stockage Azure.

## <a name="synopsis"></a>Synopsis

Cette commande permet de supprimer toutes les informations de connexion mises en cache pour l’utilisateur actuel.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande logout.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|
|--trusted-microsoft-suffixes (chaîne)   |Spécifie des suffixes de domaine supplémentaires où des jetons de connexion Azure Active Directory peuvent être envoyés.  La valeur par défaut est «  *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net ». Tous les éléments répertoriés ici sont ajoutés à la valeur par défaut. Pour la sécurité, vous devez placer uniquement des domaines Microsoft Azure ici. Séparez plusieurs entrées par des points-virgules.|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
