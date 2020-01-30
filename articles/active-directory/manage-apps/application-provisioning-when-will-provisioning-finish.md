---
title: L’attribution d’utilisateurs dans une application de la galerie Azure AD prend des heures
description: Comment savoir si l’approvisionnement de votre application risque de prendre plus longtemps que prévu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0c2586b129935043ae7b2eccd11cc3d65a385c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712094"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>L’approvisionnement d’un utilisateur pour une application de la galerie Azure AD prend plusieurs heures

Lorsque vous activez le provisionnement automatique d’une application pour la première fois, le cycle initial peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs dans l’étendue du provisionnement. 

Les synchronisations qui suivent le cycle initial sont plus rapides, car le service de provisionnement stocke les filigranes qui représentent l’état des deux systèmes après le cycle initial, ce qui améliore les performances des synchronisations suivantes.

## <a name="how-to-improve-provisioning-performance"></a>Comment améliorer les performances de l’approvisionnement

Si la durée du cycle initial excède quelques heures, une action est possible pour améliorer les performances :

-   **Filtres d’étendue utilisateur**. Les filtres d’étendue vous permettent d’affiner les données que le service d’approvisionnement extrait d’Azure AD en filtrant les utilisateurs en fonction d’attributs spécifiques. Pour plus d’informations sur les filtres d’étendue, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Étapes suivantes
[Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](user-provisioning.md)

