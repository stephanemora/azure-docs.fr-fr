---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449958"
---
Avant de vous attribuez un rôle RBAC à un principal de sécurité, déterminez l’étendue d’accès que l’entité de sécurité doit avoir. Les méthodes conseillées préconisent qu’il est toujours préférable d’accorder la plus petite portée possible.

La liste suivante décrit les niveaux à laquelle vous pouvez étendre l’accès aux ressources d’objets blob et file d’attente Azure, en commençant par la plus petite portée :

- **Un conteneur spécifique.** Dans cette étendue, une entité de sécurité a accès à tous les objets BLOB dans le conteneur, ainsi que les propriétés du conteneur et les métadonnées.
- **Une file d’attente individuel.** Dans cette étendue, une entité de sécurité a accès à des messages dans la file d’attente, ainsi que les propriétés de la file d’attente et les métadonnées.
- **Le compte de stockage.** À ce niveau, un principal de sécurité a accès à tous les conteneurs et leurs objets BLOB, ou toutes les files d’attente et leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une entité de sécurité a accès à tous les conteneurs ou des files d’attente dans tous les comptes de stockage dans le groupe de ressources.
- **L’abonnement.** Dans cette étendue, une entité de sécurité a accès à tous les conteneurs ou des files d’attente dans tous les comptes de stockage dans tous les groupes de ressources dans l’abonnement.
