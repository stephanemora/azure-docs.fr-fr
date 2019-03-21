---
title: Enquêter sur les utilisateurs avec analytique utilisateur Sentinel version préliminaire d’Azure | Microsoft Docs
description: En savoir plus sur l’examen des utilisateurs avec analytique utilisateur dans Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246319"
---
# <a name="investigate-users-with-user-analytics"></a>Enquêter sur les utilisateurs avec l’analytique de l’utilisateur

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les utilisateurs sont des entités que vous souhaitez surveiller de près. Pour vous aider à obtenir des informations sur vos utilisateurs, Sentinel Azure s’intègre parfaitement avec Azure-Protection avancée contre les menaces. Cette intégration vous permet d’analyser le comportement de l’utilisateur et de hiérarchiser les utilisateurs que vous devez examiner en premier, selon leurs alertes et les modèles d’activité suspecte sur Sentinel Azure et Microsoft 365.

Azure Sentinel enrichit les données de vos utilisateurs avec analytique à partir de Microsoft 365 pour activer l’analyse analytique et les risques d’utilisateur complet pour tous les utilisateurs dans Azure Active Directory. 

## <a name="how-it-works"></a>Fonctionnement

1. En fonction de la sécurité de vos règles analytiques, lorsqu’une correspondance est détectée, Sentinel Azure envoie les alertes à Azure ATP.
1. Azure ATP vérifie les entités d’utilisateur associées aux alertes et calcule la priorité de l’enquête pour ces utilisateurs.
3. Azure ATP recalcule le score des utilisateurs une fois qu’il est enrichi avec des données à partir de vos règles d’analytique pour Azure Sentinel.


## <a name="prerequisites"></a>Conditions préalables

- Une licence Azure-Protection avancée contre les menaces 
- Pour activer la fonctionnalité, vous avez besoin d’autorisations d’administrateur général sur le client dans lequel vous avez installé Azure Sentinel

> [!NOTE]
> - Pour chaque client Azure ATP, vous pouvez connecter une instance Azure Sentinel.
> - Analytique de l’utilisateur est actuellement disponible uniquement pour les utilisateurs Azure Active Directory. 

## <a name="enable-user-analytics"></a>Activer l’analytique de l’utilisateur

1. Pour activer l’analytique utilisateur dans Azure Sentinel, dans le portail, allez à la **analytique de l’utilisateur** page, puis cliquez sur **activer**. Cela envoie des informations sur l’espace de travail à Azure ATP.

1. Ensuite, il vous amène à Azure ATP. Sous **extensions de sécurité** dans le **Microsoft Azure Sentinel** , cliquez sur le **+ plus** à ajouter, puis sélectionnez l’espace de travail. 
1. Cliquez sur **Connecter**.

## <a name="investigate-a-user"></a>Rechercher un utilisateur

1. Dans le menu Azure Sentinel sous **analytique de l’utilisateur**, passez en revue la liste des utilisateurs en fonction de leur priorité de l’investigation. Le score est basé sur Azure Sentinel les alertes et les alertes de Microsoft 365.

2. Rechercher un utilisateur que vous souhaitez examiner. Cliquez sur l’utilisateur à accéder à la page de l’utilisateur. Passez en revue les activités et les alertes, l’utilisateur au fil du temps, dans la chronologie. Vous pouvez voir toutes les activités à partir de Microsoft 365 et Azure Sentinel. Vous pouvez également atteindre la page de l’utilisateur en explorant les utilisateurs au sein d’un cas.
      
    ![Utilisateurs](./media/user-analytics/user-investigation.png)

 
3. Pour que cela fonctionne bien, vous avez correctement configuré le [règle d’alerte personnalisée](tutorial-detect-threats.md) pour mapper les identificateurs d’utilisateur approprié pour le **compte** entité.

    - Pour les événements de Windows, vous devez mapper **compte** à la **SID**
    - Mapper les données AD Azure (qui se trouve dans nombreux journaux, y compris l’activité Azure) ou des données d’Office 365, le **compte** propriété le **GUID**, ou le **nom d’utilisateur Principal**. 

   ![Requête](./media/user-analytics/query-window.png)



Par exemple :  
> [!NOTE]
> Dans les journaux d’activité de l’activité d’Azure, l’entité de l’appelant inclut l’UPN.

1. Cette requête recherche pour la création d’un nombre anormale de ressources ou les activités de déploiement dans le journal d’activité Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Dans la règle d’alerte personnalisée, vous devez mapper le **compte** propriété **appelant**.
   
   ![Requête](./media/user-analytics/query-window.png)

3. Recherchez l’utilisateur dans la fenêtre d’enquête utilisateur. Pour des conseils sur l’examen des utilisateurs, consultez [didacticiel : Rechercher un utilisateur](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur de menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Pour la prise en main Azure Sentinel, vous avez besoin d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données vers Azure Sentinel](quickstart-onboard.md), et [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
