---
title: L’utilisation et des informations de rapport dans le portail Azure Active Directory | Microsoft Docs
description: Introduction à l’utilisation et les insights rapport dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806355"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Rapport d’utilisation et des informations dans le portail Azure Active Directory

Avec le rapport d’utilisation et insights, vous pouvez obtenir une vue centrée sur l’application de vos données de connexion. Vous pouvez trouver des réponses aux questions suivantes :

*   Ce que la partie supérieure servent des applications de mon organisation ?
*   Quelles sont les applications ont des connexions plus ayant échouées ? 
*   Quelles sont les principales erreurs de connexion pour chaque application ?

## <a name="prerequisites"></a>Conditions préalables 

Pour accéder aux données à partir du rapport d’utilisation et insights, vous devez :

* un locataire Azure AD ;
* Une licence Azure AD premium (P1/P2) pour afficher les données de connexion
* Un utilisateur administrateur général, administrateur de sécurité, lecteur de sécurité ou les rôles de lecteur de rapport. En outre, n’importe quel utilisateur (non administrateurs) permettre accéder à leurs propres connexions. 

## <a name="access-the-usage-and-insights-report"></a>Accéder au rapport d’utilisation et insights

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez l’annuaire, puis sélectionnez **Azure Active Directory** et choisissez **applications d’entreprise**.
3. À partir de la **activité** section, sélectionnez **l’utilisation et les insights** pour ouvrir le rapport. 

![Rapport d’utilisation et Insights](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Utilisez le rapport

Le rapport d’utilisation et insights affiche la liste des applications avec le signe d’un ou plusieurs de tentative et vous permet de trier par le nombre de connexions réussies, les connexions ayant échouées et le taux de réussite.

En cliquant sur charger plus bas de la liste vous permet d’afficher des applications supplémentaires sur la page. Vous pouvez sélectionner la plage de dates pour afficher toutes les applications qui ont été utilisées dans la plage.

Vous pouvez également définir le focus sur une application spécifique. Sélectionnez **afficher l’activité de connexion** pour afficher le signe dans l’activité au fil du temps pour l’application, ainsi que les principales erreurs.  

Lorsque vous sélectionnez un jour dans le graphique d’utilisation de l’application, vous obtenez une liste détaillée des activités de connexion pour l’application.  

![Rapport d’utilisation et Insights](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Étapes suivantes

* [Rapport des connexions](concept-sign-ins.md)