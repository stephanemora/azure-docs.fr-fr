---
title: Nouveautés d’Azure Sentinel
description: Cet article décrit les nouvelles fonctionnalités d’Azure Sentinel introduites au cours des derniers mois.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 8154e1adff8d8c2bdfe7fedc9309f95e5c5880bd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99499457"
---
# <a name="whats-new-in-azure-sentinel"></a>Nouveautés d’Azure Sentinel

Cet article liste les fonctionnalités récentes ajoutées à Azure Sentinel et les nouvelles fonctionnalités des services associés qui offrent une expérience utilisateur améliorée dans Azure Sentinel.

Pour plus d’informations sur les fonctionnalités précédentes fournies, consultez nos [blogs Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.


> [!TIP]
> Nos équipes de chasse des menaces Microsoft mettent à la disposition de la [communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) des requêtes, playbooks, workbooks et notebooks, notamment des [requêtes de chasse](https://github.com/Azure/Azure-Sentinel) spécifiques que vos équipes peuvent adapter et utiliser. 
>
> Vous pouvez également contribuer ! Rejoignez-nous dans la [communauté GitHub des chasseurs de menaces Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Janvier 2021

- [Module PowerShell Az.SecurityInsights (préversion publique)](#azsecurityinsights-powershell-module-public-preview)
- [Connecteur de base de données SQL](#sql-database-connector)
- [Commentaires d’incident améliorés](#improved-incident-comments)
- [Clusters Log Analytics dédiés](#dedicated-log-analytics-clusters)
- [Identités managées par applications logiques](#logic-apps-managed-identities)
- [Amélioration de l’optimisation des règles avec les graphiques d’aperçu des règles d’analytique](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>Module PowerShell Az.SecurityInsights (préversion publique)

Azure Sentinel prend désormais en charge le nouveau module PowerShell [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Le module **Az.SecurityInsights** prend en charge les cas d’utilisation Azure Sentinel courants, tels que l’interaction avec les incidents pour changer les états, la gravité, le propriétaire, etc., l’ajout de commentaires et d’étiquettes aux incidents et la création de signets.

Même si nous vous déconseillons d’utiliser des modèles [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) pour votre pipeline CI/CD, le module **Az.SecurityInsights** est utile pour les tâches postérieures au déploiement et est spécifiquement destiné à l’automatisation du SOC.  Par exemple, l’automatisation de votre SOC peut inclure des étapes pour configurer des connecteurs de données, créer des règles d’analyse ou ajouter des actions d’automatisation à des règles d’analytique.

Pour plus d’informations, notamment une liste complète et une description des applets de commande disponibles, des descriptions de paramètres et des exemples, consultez la [documentation PowerShell sur Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Connecteur de base de données SQL

Azure Sentinel fournit désormais un connecteur de base de données Azure SQL, qui vous permet de diffuser les journaux d’audit et de diagnostics de vos bases de données dans Azure Sentinel et de superviser en continu l’activité dans toutes vos instances.

Azure SQL est un moteur de base de données PaaS (Platform-as-a-Service) complètement managé qui prend en charge la plupart des fonctions de gestion de base de données telles que la mise à niveau, la mise à jour corrective, les sauvegardes et la surveillance, sans intervention de l’utilisateur.

Pour plus d’informations, consultez [Connecter les journaux de diagnostics et d’audit Azure SQL Database](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Commentaires d’incident améliorés

Les analystes utilisent les commentaires d’incident pour collaborer sur les incidents, en documentant les processus et les étapes manuellement ou dans le cadre d’un playbook. 

Notre expérience de commentaires d’incident améliorée vous permet de mettre en forme vos commentaires et de modifier ou de supprimer des commentaires existants.

Pour plus d’informations, consultez [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clusters Log Analytics dédiés

Azure Sentinel prend désormais en charge les clusters Log Analytics dédiés en tant qu’option de déploiement. Nous vous recommandons d’envisager un cluster dédié si vous :

- **Ingérez plus de 1 To par jour** dans votre espace de travail Azure Sentinel
- **Avez plusieurs espaces de travail Azure Sentinel** dans votre inscription Azure

Les clusters dédiés vous permettent d’utiliser des fonctionnalités telles que les clés gérées par le client, le référentiel sécurisé, le chiffrement double et des requêtes plus rapides entre les espaces de travail quand un même cluster abrite plusieurs espaces de travail.

Pour plus d’informations, consultez [Clusters dédiés pour les journaux Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Identités managées par applications logiques

Azure Sentinel prend désormais en charge les identités managées pour le connecteur Azure Sentinel Logic Apps, ce qui vous permet d’accorder des autorisations directement à un playbook spécifique destiné à fonctionner sur Azure Sentinel au lieu de créer des identités supplémentaires.

- **Sans identité managée**, le connecteur Logic Apps nécessite une identité distincte avec un rôle RBAC Azure Sentinel pour s’exécuter sur Azure Sentinel. L’identité distincte peut être un utilisateur Azure AD ou un principal de service, tel qu’une application Azure AD inscrite.

- L’**activation de la prise en charge des identités managées dans votre application logique** inscrit cette dernière auprès d’Azure AD et fournit un ID d’objet. Utilisez l’ID d’objet dans Azure Sentinel pour affecter l’application logique avec un rôle RBAC Azure dans votre espace de travail Azure Sentinel. 

Pour plus d'informations, consultez les pages suivantes :

- [Authentification avec une identité managée dans Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
- [Documentation du connecteur Azure Sentinel Logic Apps](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Amélioration de l’optimisation des règles avec les graphiques d’aperçu des règles d’analytique (préversion publique)

Azure Sentinel vous aide désormais à optimiser vos règles d’analytique, ce qui vous permet d’accroître leur précision et de réduire le bruit.

Après avoir modifié une règle d’analytique sous l’onglet **Définir la logique de la règle**, recherchez la zone **Simulation des résultats** à droite. 

Sélectionnez **Tester avec les données actuelles** pour qu’Azure Sentinel exécute une simulation des 50 dernières exécutions de votre règle d’analytique. Un graphique est créé pour indiquer le nombre moyen d’alertes que la règle aurait générées, en fonction des données d’événement brutes évaluées. 

Pour plus d’informations, consultez [Définir la logique de requête de règle et configurer les paramètres](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Décembre 2020

- [80 nouvelles requêtes de chasse intégrées](#80-new-built-in-hunting-queries)
- [Améliorations de l’agent Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nouvelles requêtes de chasse intégrées
 
Les requêtes de chasse intégrées d’Azure Sentinel permettent aux analystes du SOC de réduire les lacunes dans la couverture actuelle de la détection et de se lancer sur de nouvelles pistes de chasse.

Cette mise à jour pour Azure Sentinel comprend de nouvelles requêtes de chasse qui fournissent une couverture au sein de la matrice de framework MITRE ATT&CK :

- **Collection**
- **Commande et contrôle**
- **Accès aux informations d’identification**
- **Découverte**
- **Exécution**
- **Exfiltration**
- **Impact**
- **Accès initial**
- **Persistance**
- **Élévation des privilèges**

Les requêtes de chasse ajoutées sont conçues pour vous aider à détecter les activités suspectes dans votre environnement. Même si elles peuvent retourner des activités légitimes et des activités potentiellement malveillantes, elles peuvent être utiles pour guider votre chasse. 

Si, après avoir exécuté ces requêtes, vous êtes convaincu des résultats, vous souhaiterez peut-être les convertir en règles d’analytique ou ajouter les résultats de la chasse à des incidents existants ou nouveaux.

Toutes les requêtes ajoutées sont disponibles par le biais de la page Chasse avec Azure Sentinel. Pour plus d’informations, consultez [Repérer les menaces avec Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Améliorations de l’agent Log Analytics

Les utilisateurs Azure Sentinel bénéficient des améliorations de l’agent Log Analytics suivantes :

- **Prise en charge d’un plus grand nombre de systèmes d’exploitation**, notamment CentOS 8, RedHat 8 et SUSE Linux 15
- **Prise en charge de Python 3** en plus de Python 2

Azure Sentinel utilise l’agent Log Analytics pour envoyer des événements à votre espace de travail, tels que les événements de sécurité Windows, les événements Syslog et les journaux CEF.

> [!NOTE]
> L’agent Log Analytics est parfois appelé agent OMS ou MMA (Microsoft Monitoring Agent). 
> 

Pour plus d’informations, consultez la [documentation Log Analytics](/azure/azure-monitor/platform/log-analytics-agent) et les [notes de publication sur l’agent Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembre 2020

- [Superviser vos playbooks Logic Apps dans Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Connecteur Microsoft 365 Defender (préversion publique)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Superviser vos playbooks Logic Apps dans Azure Sentinel

Azure Sentinel s’intègre désormais à [Azure Logic Apps](/azure/logic-apps/), service cloud qui vous permet de planifier, automatiser et orchestrer des tâches, des processus métier et des workflows.

Utilisez une application logique Azure dans Azure Sentinel comme un playbook, qui peut être appelé automatiquement lors de la création d’un incident ou lors du tri et de l’utilisation d’incidents. 

Pour fournir des insights sur l’intégrité, les performances et l’utilisation de vos playbooks, y compris ceux que vous ajoutez avec Azure Logic Apps, nous avons ajouté un [workbook Azure](/azure/azure-monitor/platform/workbooks-overview) nommé **Supervision de l’intégrité des playbooks**. 

Utilisez le workbook **Supervision de l’intégrité des playbooks** pour superviser l’intégrité de vos playbooks ou rechercher des anomalies dans le nombre d’exécutions réussies ou ayant échoué. 

Le workbook **Supervision de l’intégrité des playbooks** est maintenant disponible dans la galerie des modèles Azure Sentinel :

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemple du workbook Supervision de l’intégrité des playbooks":::

Pour plus d'informations, consultez les pages suivantes :

- [Documentation Logic Apps](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Documentation Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Connecteur Microsoft 365 Defender (préversion publique)
 
Le connecteur Microsoft 365 Defender pour Azure Sentinel vous permet d’envoyer en streaming des journaux de chasse avancée (un type de données d’événement brutes) de Microsoft 365 Defender vers Azure Sentinel. 

Avec l’intégration de [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) dans le cadre de la sécurité [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection), vous pouvez désormais collecter vos événements de chasse avancée Microsoft Defender for Endpoint à l’aide du connecteur Microsoft 365 Defender et les envoyer en streaming directement dans de nouvelles tables dédiées de votre espace de travail Azure Sentinel. 

Les tables Azure Sentinel sont générées sur le même schéma que celui utilisé dans le portail Microsoft 365 Defender et vous fournissent un accès complet à l’ensemble des journaux de chasse avancée. 

Pour plus d’informations, consultez [Connecter des données de Microsoft 365 Defender à Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender était anciennement connu sous le nom de Protection Microsoft contre les menaces ou MTP. Microsoft Defender for Endpoint était précédemment appelé Microsoft Defender Advanced Threat Protection, ou MDATP.
> 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Intégrer Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](quickstart-get-visibility.md)
