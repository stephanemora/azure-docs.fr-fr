---
title: Utiliser des classeurs Azure Monitor pour les rapports Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser les classeurs de Azure Monitor pour les rapports d’Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6ae14ec152975717af5d55780bcc39aa87c4b01a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406594"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Comment utiliser des classeurs Azure Monitor pour les rapports Azure Active Directory

Voulez-vous :

- Comprendre l’impact de votre [stratégies d’accès conditionnel](../conditional-access/overview.md) sur l’expérience de connexion des utilisateurs ?

- Résoudre les échecs de connexion pour obtenir un meilleur aperçu de connectez-vous l’intégrité votre organisation et à résoudre rapidement les problèmes ?

- Savoir qui utilise des authentifications héritées pour vous connecter à votre environnement ? (Par [blocage d’authentification hérités](../conditional-access/block-legacy-authentication.md), vous pouvez améliorer la protection de votre client.)

Pour vous aider à résoudre ces problèmes, Active Directory fournit des classeurs pour la surveillance. [Les classeurs moniteur Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combiner du texte, des requêtes d’analytique, des mesures et des paramètres dans des rapports interactifs efficaces. 

Cet article :

- Suppose que vous êtes familiarisé avec la [créer des rapports interactifs à l’aide de classeurs de moniteur](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explique comment utiliser les classeurs de moniteur pour comprendre l’impact de vos stratégies d’accès conditionnel, pour résoudre les échecs de connexion et pour identifier les authentifications héritées.
 


## <a name="prerequisites"></a>Conditions préalables

Pour utiliser les classeurs de moniteur, vous devez :

- Un client Active Directory avec une licence de premium (P1 ou P2). Découvrez comment [obtenir une licence premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Un [espace de travail Analytique de journal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Accès aux classeurs 

Pour accéder à des classeurs :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

3. Dans le **surveillance** section, sélectionnez **Insights**. 

    ![Sélectionnez les Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Sélectionnez un rapport ou un modèle, ou sur la barre d’outils, sélectionnez **Open**. 

    ![Sélectionnez Ouvrir](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analyse de connexion

Pour accéder à la connexion analysis classeur, dans le **utilisation** section, sélectionnez **connexions**. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès

- En attente d’une action de l’utilisateur

- Échec

Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Analyse de connexion](./media/howto-use-azure-monitor-workbooks/43.png)


Pour chaque tendance, vous obtenez une répartition par les catégories suivantes :

- Lieu

    ![Connexions par emplacement](./media/howto-use-azure-monitor-workbooks/45.png)

- Appareil

    ![Connexions par appareil](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Connexions à l’aide de l’authentification héritée 


Pour accéder au classeur pour les connexions qui utilisent [authentification hérités](../conditional-access/block-legacy-authentication.md), dans le **utilisation** section, sélectionnez **se connecte à l’aide de l’authentification héritée**. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

- Protocoles

![Se connecte par authentification hérités](./media/howto-use-azure-monitor-workbooks/47.png)


Pour chaque tendance, vous obtenez une répartition par application et de protocole.

![L’authentification héritée de connexions par application et de protocole](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Connexions par accès conditionnel 


Pour accéder au classeur pour les connexions par [stratégies d’accès conditionnel](../conditional-access/overview.md), dans le **accès conditionnel** section, sélectionnez **connexions par accès conditionnel**. 

Ce classeur montre les tendances pour les connexions désactivées. Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Connexions à l’aide de l’accès conditionnel](./media/howto-use-azure-monitor-workbooks/49.png)


Pour les connexions désactivées, vous obtenez une répartition par l’état d’accès conditionnel.

![État de l’accès conditionnel](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Connexions par accorder les contrôles

Pour accéder au classeur pour les connexions par [contrôles d’octroi](../conditional-access/controls.md), dans le **accès conditionnel** section, sélectionnez **connexions par accorder les contrôles**. 

Ce classeur montre les désactivé connectez-vous tendances suivantes :

- Exiger une authentification multifacteur
 
- Exiger des conditions d’utilisation

- Exiger la déclaration de confidentialité

- Autres


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Connexions par accorder les contrôles](./media/howto-use-azure-monitor-workbooks/50.png)


Pour chaque tendance, vous obtenez une répartition par application et de protocole.

![Répartition des connexions récentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse d’échec de connexions

Utilisez le **analyse de l’échec connexions** classeur pour résoudre les erreurs avec les éléments suivants :

- Connexions
- Stratégies d'accès conditionnel
- Authentification héritée 


Pour les connexions d’accès par des données de l’accès conditionnel, dans le **dépannage** section, sélectionnez **se connecte à l’aide de l’authentification héritée**. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès

- Action en attente

- Échec


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Résolution des problèmes de connexions](./media/howto-use-azure-monitor-workbooks/52.png)


Pour vous aider à résoudre les problèmes de connexions, Azure Monitor vous donne une répartition par les catégories suivantes :

- Principales erreurs

    ![Résumé des principales erreurs](./media/howto-use-azure-monitor-workbooks/53.png)

- Connexions en attente sur l’action de l’utilisateur

    ![Résumé des connexions en attente sur l’action de l’utilisateur](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Étapes suivantes

[Créez des rapports interactifs à l’aide de classeurs de moniteur](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).