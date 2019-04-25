---
title: Utiliser des classeurs Azure Monitor pour les rapports Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser les classeurs Azure Monitor pour les rapports d’Azure Active Directory
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
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287222"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Activation Utiliser des classeurs Azure Monitor pour les rapports d’Azure Active Directory

Voulez-vous :

- Comprendre l’impact de votre [stratégies d’accès conditionnel](../conditional-access/overview.md) sur l’expérience de connexion de vos utilisateurs ?

- Résoudre les échecs de connexion pour obtenir une meilleure vue de votre organisation dans l’authentification d’intégrité ainsi que de résoudre rapidement les problèmes ?

- Savoir qui est à l’aide des authentifications héritées se connectent à votre environnement ? Par [blocage d’authentification hérités](../conditional-access/block-legacy-authentication.md), vous pouvez améliorer la protection de votre client.


[Les classeurs moniteur Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combiner du texte, les requêtes Analytique, les métriques Azure et les paramètres dans des rapports interactifs efficaces. Azure Active Directory vous fournit pour la surveillance des classeurs qui vous aident à trouver des réponses aux questions ci-dessus.

Cet article :

- Suppose que vous êtes familiarisé avec la [créer des rapports interactifs avec des classeurs Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explique comment vous pouvez utiliser les classeurs Azure Monitor sur la surveillance pour répondre aux questions ci-dessus.
 


## <a name="prerequisites"></a>Conditions préalables

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

- Un locataire Azure Active Directory, avec une licence premium (P1/P2). Découvrez comment [obtenir une licence premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Un [espace de travail Analytique de journal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Accéder aux classeurs 

Pour accéder à des classeurs :

1. Connectez-vous à votre [Azure portal](https://portal.azure.com).

2. Dans la barre de navigation gauche, cliquez sur **Azure Active Directory**.

3. Dans le **surveillance** , cliquez sur **Insights**. 

    ![Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Cliquez sur un rapport ou un modèle, ou cliquez sur **Open** dans la barre d’outils. 

    ![Galerie](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analyse de connexion

Pour accéder au classeur de connexion analysis, cliquez sur **connexions** dans le **utilisation** section. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès

- En attente d’une action de l’utilisateur

- Échec

Vous pouvez filtrer chaque tendance par :

- Période

- Applications

- Utilisateurs

![Galerie](./media/howto-use-azure-monitor-workbooks/43.png)


Pour chaque tendance, vous obtenez une répartition :

- Lieu

    ![Galerie](./media/howto-use-azure-monitor-workbooks/45.png)

- Appareil

    ![Galerie](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Connexions à l’aide de l’authentification héritée 


Pour les connexions d’accès à l’aide [authentification hérités](../conditional-access/block-legacy-authentication.md) classeur, cliquez sur **se connecte à l’aide de l’authentification héritée** dans le **utilisation** section. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès


Vous pouvez filtrer chaque tendance par :

- Période

- Applications

- Utilisateurs

- Protocoles 

![Galerie](./media/howto-use-azure-monitor-workbooks/47.png)


Pour chaque tendance, vous obtenez une répartition par application et de protocole.

![Galerie](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Connexions par accès conditionnel 


Pour accéder aux connexions par [stratégies d’accès conditionnel](../conditional-access/overview.md) classeur, cliquez sur **connexions par accès conditionnel** dans le **accès conditionnel** section. 

Ce classeur montre la tendance pour les connexions désactivées.

Vous pouvez filtrer chaque tendance par :

- Période

- Applications

- Utilisateurs

![Galerie](./media/howto-use-azure-monitor-workbooks/49.png)


Pour les connexions désactivées, vous obtenez une répartition par l’état d’accès conditionnel.

![État de l’accès conditionnel](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Connexions par accorder les contrôles

Pour accéder aux connexions par [contrôles d’octroi](../conditional-access/controls.md) classeur, cliquez sur **connexions par accorder les contrôles** dans le **accès conditionnel** section. 

Ce classeur montre les désactivé connectez-vous tendances suivantes :

- Exiger une authentification multifacteur
 
- Exiger des conditions d’utilisation

- Exiger la déclaration de confidentialité

- Autres


Vous pouvez filtrer chaque tendance par :

- Période

- Applications

- Utilisateurs

![Galerie](./media/howto-use-azure-monitor-workbooks/50.png)


Pour chaque tendance, vous obtenez une répartition par application et de protocole.

![Galerie](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse d’échec de connexions

Utilisez le **analyse de l’échec connexions** classeur pour résoudre les erreurs avec :

- Connexions
- Stratégies d’accès conditionnel
- Authentification hérités. 


Pour accéder aux connexions par les données de l’accès conditionnel, cliquez sur **se connecte à l’aide de l’authentification héritée** dans le **dépannage** section. 

Ce classeur montre les tendances d’authentification suivantes :

- Toutes les connexions

- Succès

- Action en attente

- Échec


Vous pouvez filtrer chaque tendance par :

- Période

- Applications

- Utilisateurs

![Galerie](./media/howto-use-azure-monitor-workbooks/52.png)


Pour résoudre les problèmes de connexions, vous obtenez une répartition :

- Principales erreurs

    ![Galerie](./media/howto-use-azure-monitor-workbooks/53.png)

- Connexions en attente sur l’action de l’utilisateur

    ![Galerie](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Étapes suivantes

* [Créez des rapports interactifs avec des classeurs Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)