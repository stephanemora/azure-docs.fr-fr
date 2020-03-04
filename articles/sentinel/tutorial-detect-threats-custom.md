---
title: Créer des règles d’analytique personnalisées pour détecter les menaces suspectes avec Azure Sentinel | Microsoft Docs
description: Ce tutoriel vous montre comment créer des règles d’analytique personnalisées pour détecter les menaces suspectes avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585105"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutoriel : Créer des règles d’analytique personnalisées pour détecter les menaces suspectes

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous pouvez créer des règles personnalisées qui effectuent des recherches dans votre environnement sur la base de critères spécifiques et qui génèrent des incidents lorsqu’elles trouvent des menaces correspondant aux critères. Vous pouvez ensuite examiner les incidents signalés. Ce tutoriel vous aide à créer des règles personnalisées pour détecter les menaces avec Azure Sentinel.

Ce didacticiel vous aide à détecter les menaces avec Azure Sentinel.
> [!div class="checklist"]
> * Créer des règles d’analytique
> * Automatiser les réponses aux menaces

## <a name="create-custom-analytic-rules"></a>Créer des règles d’analytique personnalisées

Vous pouvez créer des règles d’analytique personnalisées pour détecter plus facilement les types de menaces et d’anomalies suspectes dans votre environnement. En utilisant une règle, vous êtes sûr d’être immédiatement averti des menaces et ainsi de pouvoir les trier, les examiner et les corriger au plus vite.

1. Dans le portail Azure, sous Azure Sentinel, sélectionnez **Analytique**.

1. Dans la barre de menus du haut, sélectionne **+Créer**, puis **Règle de requête planifiée**. Cela entraîne l’ouverture de l’**Assistant de règle analytique**.

    ![Créer une requête planifiée](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Sous l’onglet **Général**, indiquez un **Nom** unique et une **Description**. Dans le champ **Tactique**, vous pouvez choisir les catégories d’attaque en fonction desquelles la règle doit être classifiée. Définissez la **Gravité** de l’alerte en fonction des besoins. Quand vous créez la règle, son **État** est **Activé** par défaut. Cela signifie qu’elle va s’exécuter dès que vous aurez fini de la créer. Si vous ne souhaitez pas qu’elle s’exécute immédiatement, sélectionnez **Désactivé**. Dans ce cas, la règle est ajoutée à votre onglet **Règles actives**, à partir duquel vous pouvez l’activer quand vous en avez besoin.

    ![Démarrer la création d’une règle d’analytique personnalisée](media/tutorial-detect-threats-custom/general-tab.png)

1. Sous l’onglet **Définir la logique de la règle**, vous pouvez soit écrire une requête directement dans le champ **Requête de règle**, soit créer la requête dans l’espace de travail Log Analytics pour effectuer ensuite un copier-coller de celle-ci.
 
   ![Créer une requête dans Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consultez la zone **Aperçu des résultats** à droite, où Azure Sentinel affiche le nombre de résultats (événements de journal) générés par la requête. Ceux-ci changent à la volée au fur et à mesure que vous écrivez et configurez votre requête. Le graphe montre le nombre de résultats sur la période définie, laquelle est déterminée par les paramètres de la section **Planification de la requête**.
    - Si vous constatez que votre requête déclenche des alertes trop nombreuses ou trop fréquentes, vous pouvez définir une base de référence dans la section **Seuil d’alerte**.

      Voici un exemple de requête qui vous avertit quand un nombre anormal de ressources est créé dans Activité Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > La requête peut comporter entre 1 et 10 000 caractères, et ne doit pas contenir « search \* » ou « union \* ».

    1. Utilisez la section **Mapper les entités** pour lier les paramètres de vos résultats de requête aux entités reconnues par Azure Sentinel. Ces entités constituent la base d’une analyse plus approfondie, notamment le regroupement des alertes en incidents sous l’onglet **Incident settings** (Paramètres d’incident).
    1. Dans la section **Planification de la requête**, définissez les paramètres suivants :

       1. Définissez le paramètre **Exécuter la requête tou(te)s les** pour contrôler la fréquence d’exécution de la requête (au maximum, toutes les 5 minutes ou au minimum, une fois par jour).

       1. Définissez le paramètre **Rechercher les données des derniers** pour déterminer la période des données couvertes par la requête. Par exemple, l’interrogation peut porter sur les 10 dernières minutes de données ou sur les 6 dernières heures de données.

       > [!NOTE]
       > Ces deux paramètres sont indépendants l’un de l’autre, jusqu’à un certain point. Vous pouvez exécuter une requête sur un court intervalle qui couvre une période plus longue que l’intervalle (en ayant en fait des requêtes qui se chevauchent), mais vous ne pouvez pas exécuter une requête sur un intervalle qui dépasse la période de couverture, sinon vous avez des écarts dans la couverture globale des requêtes.

    1. Utilisez la section **Seuil d’alerte** pour définir une base de référence. Par exemple, affectez la valeur **Est supérieur à** au paramètre **Générer une alerte quand le nombre de résultats de la requête**, puis entrez le nombre 1 000 pour que la règle génère une alerte uniquement si la requête génère plus de 1 000 résultats chaque fois qu’elle s’exécute. Dans la mesure où il s’agit d’un champ obligatoire, si vous ne souhaitez pas définir de base de référence (en d’autres termes, si vous souhaitez que votre alerte inscrive chaque événement), entrez 0 dans le champ numérique.

    1. Dans la section **Suppression**, vous pouvez activer le paramètre **Arrêter l’exécution de la requête une fois l’alerte générée** en choisissant **Activé** si, une fois que vous recevez une alerte, vous souhaitez interrompre l’exécution de cette règle pendant une période supérieure à l’intervalle d’interrogation. Si vous activez cette option, vous devez affecter au paramètre **Arrêter l’exécution de la requête pendant** la valeur correspondant à la durée d’arrêt de la requête, à savoir 24 heures au maximum.

1. Sous l’onglet **Incident Settings**, vous pouvez choisir si et comment Azure Sentinel doit transformer les alertes en incidents actionnables. Si vous ne changez pas cet onglet, Azure Sentinel crée un seul incident distinct à partir de chaque alerte. Vous pouvez choisir de ne pas créer d’incident ou de regrouper plusieurs alertes en un seul incident en changeant les paramètres de cet onglet.

    1. Dans la section **Incident Settings**, le paramètre **Create incidents from alerts triggered by this analytics rule (Créer des incidents à partir d’alertes déclenchées par cette règle analytique)** a la valeur **Activé** par défaut, ce qui signifie qu’Azure Sentinel crée un seul incident distinct à partir de chaque alerte déclenchée par la règle.<br></br>Si vous ne souhaitez pas que cette règle entraîne la création d’incidents (par exemple, si cette règle vise uniquement à collecter des informations pour une analyse future), affectez-lui la valeur **Désactivé**.

    1. Dans la section **Alert grouping** (Regroupement d’alertes), si vous souhaitez qu’un seul incident soit généré à partir d’un groupe d’alertes similaires ou récurrentes, affectez au paramètre **Regrouper les alertes déclenchées par cette règle analytique en un seul incident par** la valeur **Activé**, puis définissez les paramètres suivants.

    1. **Limiter le groupe aux alertes créées dans la période de temps sélectionnée** :<br></br> Déterminez le délai d’exécution dans lequel les alertes similaires ou récurrentes vont être regroupées. Toutes les alertes correspondantes dans ce délai d’exécution génèrent collectivement un incident ou un ensemble d’incidents (en fonction des paramètres de regroupement ci-dessous). Les alertes situées en dehors de ce délai d’exécution génèrent un incident ou un ensemble d’incidents distinct.

    2. **Regrouper les alertes déclenchées par cette règle analytique en un seul incident par** : Choisissez la base sur laquelle les alertes sont regroupées :

        - **Regrouper les alertes en un seul incident si toutes les entités correspondent** : <br></br>Les alertes sont regroupées si elles partagent des valeurs identiques pour chacune des entités mappées (définies sous l’onglet Définir la logique de la règle ci-dessus). Il s'agit du paramètre recommandé.

        - **Regrouper toutes les alertes déclenchées par cette règle en un seul incident** : <br></br>Toutes les alertes générées par cette règle sont regroupées même si elles ne partagent pas de valeurs identiques.

        - **Regrouper les alertes en un seul incident si les entités sélectionnées correspondent** : <br></br>Les alertes sont regroupées si elles partagent des valeurs identiques pour certaines des entités mappées (que vous pouvez sélectionner dans la liste déroulante). Vous pouvez utiliser ce paramètre si, par exemple, vous souhaitez créer des incidents distincts en fonction des adresses IP sources ou cibles.

    3. **Rouvrir les incidents correspondants fermés** : Si un incident a été fermé (ce qui signifie que le problème sous-jacent a été résolu), et si plus tard une autre alerte est générée et regroupée dans cet incident, affectez à ce paramètre la valeur **Activé** pour que l’incident fermé soit rouvert, ou laissez la valeur **Désactivé** pour que l’alerte crée un autre incident.

1. Sous l’onglet **Réponse automatisée**, sélectionnez les playbooks à exécuter automatiquement quand une alerte est générée par la règle personnalisée. Pour plus d’informations sur la création et l’automatisation de playbooks, consultez [Répondre aux menaces](tutorial-respond-threats-playbook.md).

1. Sélectionnez **Vérifier et créer** pour vérifier tous les paramètres de votre nouvelle règle d’alerte, puis sélectionnez **Create to initialize your alert rule** (Créer pour initialiser votre règle d’alerte).
  
1. Une fois l’alerte créée, une règle personnalisée est ajoutée au tableau sous **Règles actives**. Dans cette liste, vous pouvez activer, désactiver ou supprimer chaque règle.

1. Pour voir les résultats de vos règles d’alerte, accédez à la page **Incidents**, où vous pouvez ensuite trier,  [examiner les incidents](tutorial-investigate-cases.md) et corriger les menaces.


> [!NOTE]
> Les alertes générées dans Azure Sentinel sont consultables dans  [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Pour plus d’informations, consultez la [documentation sur les alertes Microsoft Graph Security](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à prendre en main la détection des menaces en utilisant Azure Sentinel.

Pour automatiser vos réponses aux menaces, découvrez comment [Configurer des réponses automatisées aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).

