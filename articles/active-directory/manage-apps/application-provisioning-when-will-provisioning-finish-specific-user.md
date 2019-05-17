---
title: Déterminer à quel moment un utilisateur spécifique pourra accéder à une application | Microsoft Docs
description: Comment déterminer à quel moment un utilisateur très important pourra accéder à une application que vous avez configurée pour l’approvisionnement des utilisateurs avec Azure AD
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
ms.openlocfilehash: 08a78a311771395f7c2113f03d5137d59999d7a7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783864"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Déterminer à quel moment un utilisateur spécifique pourra accéder à une application
Lorsque vous utilisez l’approvisionnement automatique des utilisateurs avec une application, Azure AD approvisionne et met à jour automatiquement les comptes d’utilisateur dans une application selon différents éléments comme [l’affectation d’utilisateurs et de groupes](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) et selon un intervalle régulier planifié (généralement toutes les 10 minutes).

## <a name="how-long-does-it-take"></a>Combien de temps cela prend-il ?

Le temps nécessaire à l’approvisionnement d’un utilisateur donné sera différent selon qu’une synchronisation initiale « complète » a déjà eu lieu ou non.

La première synchronisation entre Azure AD et une application peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs dans l’étendue de l’approvisionnement. 

Les synchronisations qui suivent la synchronisation initiale sont plus rapides (10 minutes, par exemple) car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale, ce qui améliore les performances des synchronisations suivantes.

## <a name="how-to-check-the-status-of-a-user"></a>Vérification de l’état d’un utilisateur

Pour consulter l’état de l’approvisionnement d’un utilisateur spécifique, consultez les journaux d’audit dans Azure AD.

Les journaux d’audit d’approvisionnement sont accessibles dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt; Journaux d’audit**. Filtrez les journaux sur la catégorie **Approvisionnement de comptes** pour afficher uniquement les événements d’approvisionnement de cette application. Vous pouvez rechercher des utilisateurs en fonction de l’ID de correspondance qui a été configuré pour eux dans les mappages d’attributs. 

Par exemple, si vous avez configuré le nom d’utilisateur principal ou l’adresse e-mail en tant qu’attribut correspondant côté Azure AD, et si l’utilisateur qui n’est pas approvisionné a la valeur « audrey@contoso.com », recherchez les journaux d’audit correspondant à « audrey@contoso.com » et passez en revue les entrées renvoyées.

Les journaux d’audit d’approvisionnement enregistrent toutes les opérations effectuées par le service d’approvisionnement, y compris :

* Interrogation d’Azure AD concernant les utilisateurs assignés qui se trouvent dans l’étendue de l’approvisionnement
* Interrogation de l’application cible concernant l’existence de ces utilisateurs
* Comparaison des objets utilisateur du système
* Ajout, mise à jour ou désactivation du compte d’utilisateur dans le système cible en fonction de la comparaison

## <a name="next-steps"></a>Étapes suivantes
[Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
