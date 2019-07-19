---
title: Règles d’alerte dans Azure Security Center | Microsoft Docs
description: Ce document est conçu pour vous aider à créer des règles d’alerte dans Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 984bd4d5db210679884655721be0cbcdac8c1705
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485280"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Règles d’alerte personnalisées dans Azure Security Center (préversion)
Ce document est conçu pour vous aider à créer des règles d’alerte dans Azure Security Center.

> [!NOTE]
> Les alertes personnalisées ont été retirées de Security Center. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Retrait des règles d’alerte personnalisées dans Azure Security Center

Les alertes personnalisées ont été retirées le 30 juin 2019 en raison de la suppression de l’infrastructure sous-jacente sur laquelle elles étaient basées. Toutes les alertes personnalisées configurées avant le retraitement ne sont pas prises en compte et les alertes de sécurité basées sur ces règles d’alerte personnalisées ne sont pas générées. Vous pouvez toujours afficher les requêtes des règles d’alerte personnalisées dans Security Center afin de les recréer dans les alternatives présentées ci-dessous :

Nous vous recommandons aux utilisateurs de procéder de l’une des façons suivantes :
- Activez [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) et utilisez sa fonctionnalité [Analytics](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) intégrée pour recréer leurs règles d’alerte.
- Recréer leurs alertes avec les alertes de journal Azure Monitor
                                     
Pour conserver vos alertes existantes et les créer à nouveau dans Azure Sentinel, veuillez [lancer Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Lors d’une première étape, sélectionnez l’espace de travail où sont stockées vos alertes personnalisées, puis sélectionnez l’élément de menu « Analytics » pour configurer vos règles d’alerte personnalisées. Pour plus d’informations, consultez la [documentation](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats).

> [!NOTE]
> Les alertes personnalisées utilisant les requêtes d’instructions [Recherche](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) ou [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) ne sont pas prises en charge dans Azure Sentinel. Modifiez ces alertes avant d’effectuer la migration.

Pour recréer vos alertes à l’aide d’alertes de journal Azure Monitor, consultez : [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) pour obtenir des instructions sur la façon de créer des alertes de journal. Pour une vue d’ensemble générale des alertes de journal dans Azure Monitor, cliquez [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Qu’est-ce que les règles d’alerte personnalisées dans Azure Security Center ?

Security Center possède un ensemble d’[alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), qui sont déclenchées en cas de menace ou d’activité suspecte. Dans certains scénarios, vous souhaiterez peut-être créer une alerte personnalisée pour gérer les besoins spécifiques de votre environnement.

Les règles d’alerte personnalisés de Security Center permettent de définir de nouvelles alertes de sécurité basées sur les données déjà collectées à partir de votre environnement. Vous pouvez créer des requêtes, dont le résultat peut être utilisé comme critère pour la règle personnalisée et une fois ce critère mis en correspondance, la règle est exécutée. Vous pouvez utiliser les événements de sécurité des ordinateurs, les journaux d’activité des solutions de sécurité du partenaire ou les données ingérées à l’aide d’API pour créer des requêtes personnalisées.

> [!NOTE]
> Les Alertes personnalisées ne sont pas prises en charge dans la [fonctionnalité d’investigation](security-center-investigation.md) de Security Center.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Comment créer une règle d’alerte personnalisée dans Security Center ?

Ouvrez le tableau de bord **Security Center** et procédez comme suit pour créer une règle d’alerte personnalisée :

1.  Dans le volet gauche, sous **Détection** cliquez sur **Règles d’alerte personnalisées (préversion)** .
2.  Sur la page **Security Center - Règles d’alerte personnalisés (préversion)** , cliquez sur **Nouvelle règle d’alerte personnalisée**.

    ![Alerte personnalisée](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  La page Créer une règle d’alerte personnalisée s’affiche avec les options suivantes :

    ![Créer](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Dans le champ **Nom** , tapez le nom de cette règle personnalisée.
5.  Entrez une brève description qui reflète l’objectif de cette règle dans le champ **Description**.
6.  Sélectionnez le niveau de gravité (haute, moyenne, faible) en fonction de vos besoins dans le champ **Gravité**.
7.  Sélectionnez l’abonnement dans lequel cette règle s’applique dans le champ **Abonnement**.
8.  Sélectionnez l’espace de travail que vous souhaitez analyser à l’aide de cette règle dans le champ **Espace de travail** et la requête que vous voulez utiliser pour obtenir les résultats dans le champ **Rechercher**.

    > [!NOTE]
    > Vous devez disposer d’une autorisation en écriture dans l’espace de travail sélectionné pour stocker votre alerte personnalisée.
    >
    >

    Le résultat de la requête déclenche l’alerte. Notez que lorsque vous tapez une requête valide, la coche verte s’affiche sur le côté droit de ce champ :

    ![Requête](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Sélectionnez l’intervalle de temps lors duquel la requête ci-dessus sera exécutée dans le champ **Période**. Notez que le résultat de la recherche dans la partie inférieure de ce champ change selon l’intervalle de temps sélectionné.

    ![Période](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. Dans le champ **Évaluation** sélectionnez la fréquence à laquelle cette règle doit être évaluée et exécutée.
12. Dans le champ **Nombre de résultats**, sélectionnez l’opérateur (supérieur ou inférieur à).
13. Dans le champ **Seuil**, entrez un nombre qui sera utilisé comme référence pour l’opérateur sélectionné précédemment.
14. Activez l’option **Supprimer les alertes** si vous souhaitez définir un délai d’attente avant que Security Center envoie une autre alerte pour cette règle.
15. Cliquez sur **OK** pour terminer.

Après avoir terminé la création de la nouvelle règle d’alerte, celle-ci s’affiche dans la liste des règles d’alerte personnalisées. Une fois les conditions de cette règle réunies, une nouvelle alerte est déclenchée, que vous pouvez voir dans le tableau de bord **Alertes de sécurité**.

![Alerte](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Notez que les paramètres (requête de recherche, seuil, etc.) qui ont été établis lors de la création de la règle sont disponibles dans l’alerte pour cette règle personnalisée.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à créer une règle d’alerte personnalisée dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
