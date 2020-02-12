---
title: Alertes de sécurité concernant les environnements dans Azure DevTest Labs
description: Cet article explique comment afficher des alertes de sécurité pour un environnement dans DevTest Labs, et prendre les mesures nécessaires.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992020"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertes de sécurité concernant les environnements dans Azure DevTest Labs
Cet article explique comment afficher des alertes de sécurité concernant les environnements dans Azure DevTest Labs. 

## <a name="prerequisites"></a>Conditions préalables requises
Vous pouvez afficher des alertes de sécurité uniquement pour les environnements qui sont déployés dans votre laboratoire. Pour tester ou utiliser cette fonctionnalité, déployez un environnement dans votre laboratoire. 

## <a name="view-security-alerts-for-an-environment"></a>Voir les alertes de sécurité d’un environnement

1. Dans la page d’accueil de votre laboratoire, sélectionnez **Alertes de sécurité** dans le menu de gauche. Le nombre d’alertes de sécurité (de niveau élevé, moyen ou faible) doit s’afficher.

    ![Alertes de sécurité - Vue d’ensemble](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Cliquez avec le bouton droit sur les trois points (...) de la dernière colonne, puis sélectionnez **Voir les alertes de sécurité**. 

    ![Afficher les alertes de sécurité](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Vous verrez des informations détaillées sur les alertes et les recommandations Advisor. 

    ![Afficher les alertes de sécurité](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les environnements, consultez les articles suivants :

- [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurer et utiliser des environnements publics](devtest-lab-configure-use-public-environments.md)
