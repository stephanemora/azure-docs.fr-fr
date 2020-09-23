---
title: 'Azure Security Center : Advanced Data Security pour les machines SQL (préversion)'
description: Découvrez comment activer Advanced Data Security pour les machines SQL dans Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: de5346387336acb5a4b13457d3a163f529c51b89
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438685"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Advanced Data Security pour les machines SQL (préversion)

Advanced Data Security d’Azure Security Center pour les machines SQL protège les serveurs SQL hébergés dans Azure, dans d’autres environnements cloud et même sur des machines locales. Cela étend les protections de vos serveurs SQL Azure natifs pour prendre entièrement en charge les environnements hybrides.

Cette fonctionnalité en préversion inclut des fonctions qui permettent d’identifier et d’atténuer les vulnérabilités potentielles de votre base de données, et de détecter les activités anormales susceptibles de présenter des menaces pour votre base de données : 

* **Évaluation des vulnérabilités** : service d’analyse qui vous permet de découvrir, de suivre les vulnérabilités de base de données potentielles et vous aide à les corriger. Les analyses d’évaluation fournissent une vue d’ensemble de l’état de sécurité de vos machines SQL, ainsi que des détails sur les résultats de la sécurité.

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) : service de détection qui surveille en permanence vos serveurs SQL pour détecter les menaces telles que l’injection SQL, les attaques en force brute et les abus de privilèges. Ce service fournit des alertes de sécurité orientées sur l’action dans Azure Security Center avec des détails sur l’activité suspecte, des conseils sur la façon de limiter les menaces et des options pour poursuivre vos investigations avec Azure Sentinel.

>[!TIP]
> Advanced Data Security pour les machines SQL est une extension du [package de sécurité des données avancée](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) d’Azure Security Center, disponible pour Azure SQL Database, Azure Synapse et SQL Managed Instance.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Configurer Advanced Data Security pour les machines SQL 

La configuration d’Advanced Data Security d’Azure Security Center pour les machines SQL comporte deux étapes :

* Provisionnez l’agent Log Analytics sur l’hôte du serveur SQL. Cela permet de se connecter à Azure.

* Activez le bundle facultatif dans la page de tarification et des paramètres de Security Center.

Ces deux étapes sont décrites ci-dessous.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Étape 1. Provisionnez l’agent Log Analytics sur l’hôte du serveur SQL :

- **SQL Server sur une machine virtuelle Azure** : si votre machine SQL est hébergée sur une machine virtuelle Azure, vous pouvez [provisionner automatiquement l’agent Log Analytics](security-center-enable-data-collection.md#workspace-configuration). Vous pouvez également suivre la procédure manuelle pour [ajouter une machine virtuelle Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server sur Azure Arc** : si votre serveur SQL Server est hébergé sur une machine [Azure Arc](https://docs.microsoft.com/azure/azure-arc/), vous pouvez déployer l’agent Log Analytics à l’aide de la recommandation Security Center « L’agent Log Analytics doit être installé sur vos machines Azure Arc Windows (préversion) ». Vous pouvez également suivre la procédure manuelle dans la [documentation d’Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server local** : si votre serveur SQL Server est hébergé sur une machine Windows locale sans Azure Arc, vous disposez de deux options pour la connecter à Azure :
    
    - **Déployer Azure Arc** : vous pouvez connecter n’importe quelle machine Windows à Security Center. Toutefois, Azure Arc permet une intégration plus poussée dans *tout* votre environnement Azure. Si vous configurez Azure Arc, vous verrez la page **SQL Server – Azure Arc** dans le portail et vos alertes de sécurité s’afficheront dans un onglet **Sécurité** dédié sur cette page. Donc, la première option recommandée consiste à [configurer Azure Arc sur l’hôte](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) et à suivre les instructions pour **SQL Server sur Azure Arc**, ci-dessus.
        
    - **Connecter la machine Windows sans Azure Arc** : si vous choisissez de connecter un serveur SQL Server exécuté sur une machine Windows sans utiliser Azure Arc, suivez les instructions dans [Connecter des machines Windows à Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Étape 2. Activez le bundle facultatif dans la page de tarification et des paramètres de Security Center :

1. À partir de la barre latérale de Security Center, ouvrez **Tarification et paramètres**.

    - Si vous utilisez **l’espace de travail par défaut d’Azure Security Center** (nommé « defaultworkspace-[ID de votre abonnement]-[région] »), sélectionnez **l’abonnement** approprié.

    - Si vous utilisez un **espace de travail autre que celui par défaut**, sélectionnez **l’espace de travail** approprié (entrez le nom de l’espace de travail dans le filtre, si nécessaire) :

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Activez l’option **Serveurs SQL sur des machines (préversion)** . 

    [![Page de tarification Security Center avec des bundles facultatifs](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    La fonctionnalité Advanced Data Security pour les serveurs SQL sur des machines sera activée sur tous les serveurs SQL connectés à l’espace de travail sélectionné. La protection sera complètement active après le premier redémarrage de l’instance SQL Server.

    >[!TIP] 
    > Pour créer un espace de travail, suivez les instructions fournies dans [Créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Vous pouvez également configurer la notification par e-mail pour les alertes de sécurité. 
    Vous pouvez définir une liste de destinataires devant recevoir une notification par e-mail quand des alertes Security Center sont générées. L’e-mail contient un lien direct vers l’alerte dans Azure Security Center avec tous les détails pertinents. Pour plus d'informations, consultez [Configurer des notifications par e-mail pour les alertes de sécurité](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Explorer les rapports d’évaluation des vulnérabilités

Le service d’évaluation des vulnérabilités analyse vos bases de données une fois par semaine. Les analyses s’exécutent le même jour de la semaine que celui où vous avez activé le service.

Le tableau de bord d’évaluation des vulnérabilités fournit une vue d’ensemble des résultats de votre évaluation dans toutes vos bases de données, ainsi qu’un résumé des bases de données intègres et non intègres, et un résumé global des vérifications ayant échoué en fonction de la distribution des risques.

Vous pouvez consulter les résultats de l’évaluation des vulnérabilités directement à partir de Security Center.

1. À partir de la barre latérale de Security Center, ouvrez la page **Recommendations** et sélectionnez la recommandation **Les vulnérabilités de vos serveurs SQL Server sur des machines doivent être corrigées (préversion)** . Pour plus d’informations, consultez [Recommandations de Security Center](security-center-recommendations.md). 


    [![Recommandation **Les vulnérabilités de vos serveurs SQL Server sur des machines doivent être corrigées (préversion)**](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    L’affichage détaillé de cette recommandation s’affiche.

    [![Vue détaillée de la recommandation **Les vulnérabilités de vos serveurs SQL Server sur des machines doivent être corrigées (préversion)**](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. À explorer pour plus de détails :

    * Pour obtenir une vue d’ensemble des ressources analysées (bases de données) ainsi que la liste des vérifications de sécurité qui ont été testées, sélectionnez le serveur de votre choix.

    * Pour obtenir une vue d’ensemble des vulnérabilités regroupées en fonction d’une base de données SQL spécifique, sélectionnez la base de données de votre choix.

    Dans chaque affichage, les vérifications de sécurité sont triées par **Gravité**. Cliquez sur une vérification de sécurité spécifique pour afficher un volet d’informations contenant une **Description**, une méthode de **Correction** ainsi que d’autres informations associées, telles que l’**Impact** ou les **Références**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Alertes Advanced Threat Protection pour les serveurs SQL Server sur des machines
Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des machines SQL. Ces événements peuvent déclencher des alertes affichées dans la [section des alertes pour SQL Database et Azure Synapse Analytics (anciennement SQL Data Warehouse) de la page de référence des alertes](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Explorer et examiner les alertes de sécurité

Vous pouvez examiner les alertes de sécurité dans la page des alertes de Security Center, sous l’onglet Sécurité de la ressource ou par le biais du lien direct fourni dans les e-mails d’alerte.

1. Pour afficher les alertes, sélectionnez **Alertes de sécurité** dans la barre latérale de Security Center et sélectionnez une alerte.

1. Les alertes sont conçues pour être autonomes, avec des étapes de correction détaillées et des informations d’investigation dans chacune d’elles. Vous pouvez approfondir vos investigations en utilisant d’autres fonctionnalités d’Azure Security Center et d’Azure Sentinel pour avoir une vue plus large :

    * Activez la fonctionnalité d’audit de SQL Server pour faire d’autres investigations. Si vous utilisez Azure Sentinel, chargez les journaux d’audit SQL des événements du journal de sécurité Windows pour profiter d’une expérience d’investigation enrichie. [En savoir plus sur l’audit SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Pour améliorer votre posture de sécurité, appliquez les recommandations concernant la machine hôte que Security Center indique dans chaque alerte. Vous réduirez ainsi les risques d’attaques futures. 

    [En savoir plus sur la gestion des alertes et la réponse à celles-ci](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Étapes suivantes

Pour des informations connexes, consultez l’article suivant :

- [Alertes de sécurité pour SQL Database et Azure Synapse Analytics (anciennement SQL Data Warehouse)](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurer des notifications par e-mail pour les alertes de sécurité](security-center-provide-security-contact-details.md)
- [En savoir plus sur Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Package Advanced Data Security d’Azure Security Center](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)