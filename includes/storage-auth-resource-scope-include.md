---
title: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373746"
---
Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources d’objets blob et de files d’attente Azure, en commençant par la plus petite étendue :

- **Un conteneur individuel.** Dans cette étendue, une attribution de rôle s’applique à tous les objets blob dans le conteneur, ainsi qu’aux propriétés et aux métadonnées du conteneur.
- **Une file d’attente individuelle.** Dans cette étendue, une attribution de rôle s’applique aux messages dans la file d’attente, ainsi qu’à ses propriétés et ses métadonnées.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs et leurs objets blob, ou à toutes les files d’attente et leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements du groupe d’administration.

Pour plus d’informations sur les attributions de rôles Azure et l’étendue, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../articles/role-based-access-control/overview.md).
