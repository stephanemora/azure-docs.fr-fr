---
title: L’approvisionnement d’un utilisateur pour une application de la galerie Azure AD prend plusieurs heures | Microsoft Docs
description: Comment savoir si l’approvisionnement de votre application risque de prendre plus longtemps que prévu
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: dc582ff3dac8f128972f070309d5c8a4ce21fb70
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335381"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>L’approvisionnement d’un utilisateur pour une application de la galerie Azure AD prend plusieurs heures

Lorsque vous activez l’approvisionnement automatique d’une application pour la première fois, la synchronisation initiale peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs dans la portée de l’approvisionnement. 

Les synchronisations qui suivent la synchronisation initiale sont plus rapides, car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale, ce qui améliore les performances des synchronisations suivantes.

## <a name="how-to-improve-provisioning-performance"></a>Comment améliorer les performances de l’approvisionnement

Si la synchronisation initiale prend plus de quelques heures, une action est possible pour améliorer les performances :

-   **Filtres d’étendue utilisateur**. Les filtres d’étendue vous permettent d’affiner les données que le service d’approvisionnement extrait d’Azure AD en filtrant les utilisateurs en fonction d’attributs spécifiques. Pour plus d’informations sur les filtres d’étendue, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Étapes suivantes
[Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)

