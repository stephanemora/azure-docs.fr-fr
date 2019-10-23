---
title: Utiliser des classeurs Azure Monitor pour la création de rapports Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 9bea8da4f0d694be3a39a8f5dfaca8e54ce2773d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255665"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory

Vous voulez...

- comprendre l’impact de vos [stratégies d’accès conditionnel](../conditional-access/overview.md) sur l’expérience de connexion des utilisateurs ?

- mettre un terme aux échecs de connexion pour obtenir un meilleur aperçu de l’intégration des connexions de votre organisation, et résoudre les problèmes plus rapidement ?

- savoir qui utilise l’authentification héritée pour se connecter à votre environnement ? (en [bloquant l’authentification héritée](../conditional-access/block-legacy-authentication.md), vous pouvez améliorer la protection de votre locataire.)

Pour vous aider à résoudre ces problèmes, Active Directory fournit des classeurs, à des fins de surveillance. Les [classeurs Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) regroupent du texte, des requêtes Analytics, des mesures et des paramètres sous la forme de rapports interactifs riches en contenu. 

Cet article :

- suppose que vous êtes familiarisé avec la procédure de [création de rapports interactifs à l’aide de classeurs Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) et

- explique comment utiliser les classeurs Azure Monitor pour comprendre l’impact de vos stratégies d’accès conditionnel, pour résoudre les échecs de connexion et pour identifier les authentifications héritées.
 


## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser les classeurs Azure Monitor, vous devez disposer des éléments suivants :

- Un locataire Azure Active Directory avec une licence premium (P1/P2). Découvrez comment [obtenir une licence premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Un [espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Accéder](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) à l’espace de travail Log Analytics
- Rôles suivants dans Azure Active Directory (si vous accédez à Log Analytics via le portail Azure Active Directory)
    - Administrateur de la sécurité
    - Lecteur de sécurité
    - Lecteur de rapport
    - Administrateur général

## <a name="roles"></a>contrôleur
Vous devez être dans l’un des rôles suivants et avoir [accès à l’espace de travail Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) sous-jacent pour gérer les classeurs :
-   Administrateur général
-   Administrateur de la sécurité
-   Lecteur de sécurité
-   Lecteur de rapport
-   Administrateur d’application


## <a name="workbook-access"></a>Accès aux classeurs 

Pour accéder à des classeurs, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

3. Dans la section **Surveillance**, sélectionnez **Classeurs**. 

    ![Sélectionner Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Sélectionnez un rapport ou un modèle, ou, sur la barre d’outils, sélectionnez **Ouvrir**. 

    ![Sélectionner Ouvrir](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analyse des connexions

Pour accéder au classeur d’analyse de la connexion, allez dans la section **Utilisation** et sélectionnez **Connexions**. 

Ce classeur montre les tendances suivantes en matière de connexion :

- Toutes les connexions

- Succès

- Action de l’utilisateur en attente

- Échec

Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Analyse des connexions](./media/howto-use-azure-monitor-workbooks/43.png)


Pour chaque tendance, vous obtenez une répartition en fonction des catégories suivantes :

- Location

    ![Connexions par emplacement](./media/howto-use-azure-monitor-workbooks/45.png)

- Appareil

    ![Connexions par appareil](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Connexions avec une authentification héritée 


Pour accéder au classeur afin d’identifier les connexions qui utilisent une [authentification héritée](../conditional-access/block-legacy-authentication.md), accédez à la section **Utilisation** et sélectionnez **Connexions avec une authentification existante**. 

Ce classeur montre les tendances suivantes en matière de connexion :

- Toutes les connexions

- Succès


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

- Protocoles

![Connexions avec une authentification héritée](./media/howto-use-azure-monitor-workbooks/47.png)


Pour chaque tendance, vous obtenez une répartition par application et par protocole.

![Connexions avec une authentification héritée via une application et un protocole](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Connexions par accès conditionnel 


Pour accéder au classeur afin d’identifier les connexions via des [stratégies d’accès conditionnel](../conditional-access/overview.md), accédez à la section **Accès conditionnel**, puis sélectionnez **Sign-ins by Conditional Access** (Connexions par accès conditionnel). 

Ce classeur affiche les tendances relatives aux connexions désactivées. Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Connexions avec accès conditionnel](./media/howto-use-azure-monitor-workbooks/49.png)


Pour les connexions désactivées, vous obtenez une répartition en fonction de l’état de l’accès conditionnel.

![État de l’accès conditionnel](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Connexions par contrôles d’octroi

Pour accéder au classeur afin d’identifier les connexions via des [contrôles d’octroi](../conditional-access/controls.md), accédez à la section **Accès conditionnel**, puis sélectionnez **Sign-ins by Grant Control** (Connexions par contrôle d’octroi). 

Ce classeur montre les tendances suivantes concernant les connexions désactivées :

- Exiger une authentification multifacteur
 
- Exiger des conditions d’utilisation

- Exiger une déclaration de confidentialité

- Autres


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Connexions par contrôles d’octroi](./media/howto-use-azure-monitor-workbooks/50.png)


Pour chaque tendance, vous obtenez une répartition par application et par protocole.

![Répartition des connexions récentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse des échecs de connexion

Utilisez le classeur **d’analyse des échecs de connexion** pour résoudre les erreurs avec les éléments suivants :

- Connexions
- Stratégies d’accès conditionnel
- Authentification héritée 


Pour accéder aux données relatives aux connexions via l’accès conditionnel, accédez à la session **Résoudre les problèmes**, puis sélectionnez **Sign-ins using Legacy Authentication** (Connexions à l’aide d’une authentification héritée). 

Ce classeur montre les tendances suivantes en matière de connexion :

- Toutes les connexions

- Succès

- Action en attente

- Échec


Vous pouvez filtrer chaque tendance selon les catégories suivantes :

- Période

- Applications

- Utilisateurs

![Résolution des problèmes de connexion](./media/howto-use-azure-monitor-workbooks/52.png)


Pour vous aider à résoudre les problèmes de connexion, Azure Monitor vous fournit une répartition en fonction des catégories suivantes :

- Erreurs les plus fréquentes

    ![Résumé des erreurs les plus fréquentes](./media/howto-use-azure-monitor-workbooks/53.png)

- Connexions en attente d’une action utilisateur

    ![Résumé des connexions en attente d’une action utilisateur](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Étapes suivantes

[Créer des rapports interactifs à l’aide de classeurs Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
