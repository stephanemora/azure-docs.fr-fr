---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017670"
---
| Mechanism | Étendue |Limites | Niveau d’autorisation pris en charge |
|---|---|---|---|
| Azure RBAC | Comptes de stockage, conteneurs. <br>Attributions de rôles Azure de ressources croisées au niveau de l’abonnement ou du groupe de ressources. | 2000 attributions de rôles Azure dans un abonnement | Rôles Azure (intégrés ou personnalisés) |
| ACL| Répertoire, fichier |32 entrées ACL (28 entrées ACL effectives) par fichier et par annuaire. Les ACL d’accès et par défaut disposent chacune de leur propre limite d’entrée de 32 ACL. |Autorisation ACL|
