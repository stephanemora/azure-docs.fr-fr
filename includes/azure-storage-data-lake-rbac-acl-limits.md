---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131630"
---
| Mechanism | Étendue |Limites | Niveau d’autorisation pris en charge |
|---|---|---|---|
| RBAC | Comptes de stockage, conteneurs. <br>Attributions de rôles RBAC de ressources croisées au niveau de l’abonnement ou du groupe de ressources. | 2000 attributions de rôles RBAC dans un abonnement | Rôles RBAC (intégrés ou personnalisés) |
| ACL| Répertoire, fichier |32 entrées ACL (28 entrées ACL effectives) par fichier et par annuaire. Les ACL d’accès et par défaut disposent chacune de leur propre limite d’entrée de 32 ACL. |Autorisation ACL|
