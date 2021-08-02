---
title: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 7f2b15a57b42f87e749060f510dafff732a1d9e0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904416"
---
Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources de files d’attente Azure, en commençant par la plus petite étendue :

- **Une file d’attente individuelle.** Dans cette étendue, une attribution de rôle s’applique aux messages dans la file d’attente, ainsi qu’à ses propriétés et ses métadonnées.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à toutes les files d’attente et à leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à toutes les files d’attente dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements du groupe d’administration.

Pour plus d’informations sur l’étendue des attributions de rôle RBAC Azure, consultez [Comprendre l’étendue de RBAC Azure](../articles/role-based-access-control/scope-overview.md).
