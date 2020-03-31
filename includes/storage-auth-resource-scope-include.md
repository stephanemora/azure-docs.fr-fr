---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460506"
---
Avant d’attribuer un rôle RBAC à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources d’objets blob et de files d’attente Azure, en commençant par la plus petite étendue :

- **Un conteneur individuel.** Dans cette étendue, une attribution de rôle s’applique à tous les objets blob dans le conteneur, ainsi qu’aux propriétés et aux métadonnées du conteneur.
- **Une file d’attente individuelle.** Dans cette étendue, une attribution de rôle s’applique aux messages dans la file d’attente, ainsi qu’à ses propriétés et ses métadonnées.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs et leurs objets blob, ou à toutes les files d’attente et leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.

> [!IMPORTANT]
> Si votre abonnement comprend un espace de noms Azure DataBricks, les rôles dont l’étendue est limitée à l’abonnement n’accorderont pas l’accès aux données d’objet blob et de file d’attente. Étendre les rôles au groupe de ressources, au compte de stockage ou au conteneur ou à la file d’attente.     
