---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024885"
---
Avant d’attribuer un rôle RBAC à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources d’objets blob et de files d’attente Azure, en commençant par la plus petite étendue :

- **Un conteneur individuel.** Dans cette étendue, une attribution de rôle s’applique à tous les objets blob dans le conteneur, ainsi qu’aux propriétés et aux métadonnées du conteneur.
- **Une file d’attente individuelle.** Dans cette étendue, une attribution de rôle s’applique aux messages dans la file d’attente, ainsi qu’à ses propriétés et ses métadonnées.
- **Le compte de stockage.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs et leurs objets blob, ou à toutes les files d’attente et leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage du groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de l’abonnement.
