---
title: Fichier Include
description: Fichier Include
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971033"
---
- Chaque identité managée compte pour la limite de quota d’objets dans un locataire Azure AD, comme décrit dans [Restrictions et limites du service Azure Active Directory](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   La vitesse à laquelle les identités managées peuvent être créées a les limites suivantes :

    1. Par locataire Azure AD par région Azure : 200 opérations de création toutes les 20 secondes.
    2. Par abonnement Azure par région Azure : 40 opérations de création toutes les 20 secondes.

- Lorsque vous créez des identités managées attribuées par l’utilisateur, seuls les caractères alphanumériques (0-9, a-z, A-Z) et le trait d’union (-) sont pris en charge. Pour que l’attribution à une machine virtuelle ou à un groupe de machines virtuelles identiques fonctionne correctement, le nom est limité à 24 caractères.
