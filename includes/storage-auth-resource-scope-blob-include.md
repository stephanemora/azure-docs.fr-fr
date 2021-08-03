---
title: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 24bc49b82c24c560dea12c744097f643bec2b3f5
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904407"
---
Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources de blobs Azure, en commençant par la plus petite étendue :

- **Un conteneur individuel.** Dans cette étendue, une attribution de rôle s’applique à tous les objets blob dans le conteneur, ainsi qu’aux propriétés et aux métadonnées du conteneur.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs et à leurs blobs.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements du groupe d’administration.

Pour plus d’informations sur l’étendue des attributions de rôle RBAC Azure, consultez [Comprendre l’étendue de RBAC Azure](../articles/role-based-access-control/scope-overview.md).
