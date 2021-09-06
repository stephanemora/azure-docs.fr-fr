---
title: Fichier Include
description: Fichier Include
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201107"
---
- Chaque identité managée compte pour la limite de quota d’objets dans un locataire Azure AD, comme décrit dans [Restrictions et limites du service Azure Active Directory](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   La vitesse à laquelle les identités managées peuvent être créées a les limites suivantes :

    1. Par locataire Azure AD par région Azure : 400 opérations de création toutes les 20 secondes.
    2. Par abonnement Azure par région Azure : 80 opérations de création toutes les 20 secondes.

-   Vitesse à laquelle une identité managée affectée par l’utilisateur peut être affectée avec une ressource Azure :

    1. Par locataire Azure AD par région Azure : 400 opérations d’assignation toutes les 20 secondes.
    2. Par abonnement Azure par région Azure : 300 opérations d’assignation toutes les 20 secondes.




