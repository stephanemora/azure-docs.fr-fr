---
title: azcopy jobs remove | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503369"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Supprime tous les fichiers associés à l’ID de travail donné.

> [!NOTE] 
> Vous pouvez personnaliser l’emplacement où sont enregistrés les fichiers journaux et les fichiers de plan. Pour plus d’informations, consultez la commande [azcopy env](storage-ref-azcopy-env.md).

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](./storage-use-azcopy-v10.md#transfer-data)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)

## <a name="examples"></a>Exemples

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Options

**--help**                Aide pour la suppression.

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

**--cap-mbps float**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**--output-type** string   Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est `text`. (valeur par défaut `text`)

La chaîne **--trusted-microsoft-suffixes** spécifie des suffixes de domaine supplémentaires où des jetons de connexion Azure Active Directory peuvent être envoyés.  La valeur par défaut est «  *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net ». Tous les éléments répertoriés ici sont ajoutés à la valeur par défaut. Pour la sécurité, vous devez placer uniquement des domaines Microsoft Azure ici. Séparez plusieurs entrées par des points-virgules.

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
