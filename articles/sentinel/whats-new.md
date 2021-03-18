---
title: Nouveautés d’Azure Sentinel
description: Cet article décrit les nouvelles fonctionnalités d’Azure Sentinel introduites au cours des derniers mois.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 88ac8bb1bc804604b96d5c90025b8325a6ce4962
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503116"
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

## <a name="march-2021"></a>Mars 2021

- [Intégration des incidents Microsoft 365 Defender](#microsoft-365-defender-incident-integration) (préversion publique)
- [Nouveaux connecteurs de service Microsoft avec Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="microsoft-365-defender-incident-integration"></a>Intégration des incidents Microsoft 365 Defender

L’intégration d’incidents [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) d’Azure Sentinel vous permet de diffuser tous les incidents M365D dans Azure Sentinel et de les garder synchronisés entre les deux portails. Les incidents de M365D (anciennement Protection Microsoft contre les menaces) incluent l’ensemble des alertes, entités et informations pertinentes associées, ce qui vous offre suffisamment de contexte pour effectuer un triage et une investigation préliminaire dans Azure Sentinel. Une fois dans Sentinel, les incidents restent synchronisés de manière bidirectionnelle avec M365D, ce qui vous permet de tirer parti des avantages des deux portails dans votre investigation sur l’incident.

L’utilisation conjointe d’Azure Sentinel et de Microsoft 365 Defender vous offre le meilleur des deux mondes. Vous bénéficiez de tout l’éventail des insights offert par SIEM pour l’ensemble des ressources d’informations de votre organisation, ainsi que de toute la puissance d’investigation personnalisée qu’une solution XDR procure pour protéger vos ressources Microsoft 365, tout ceci coordonné et synchronisé pour des opérations SOC fluides.

Pour plus d’informations, consultez [Intégration de Microsoft 365 Defender avec Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nouveaux connecteurs de service Microsoft avec Azure Policy

[Azure Policy](../governance/policy/overview.md) est un service Azure qui vous permet d’utiliser des stratégies pour appliquer et contrôler les propriétés d’une ressource. L’utilisation de stratégies garantit que les ressources restent conformes à vos normes de gouvernance informatique.

Parmi les propriétés des ressources qui peuvent être contrôlées par les stratégies, citons la création et la gestion des journaux de diagnostic et d’audit. Azure Sentinel utilise désormais Azure Policy pour vous permettre d’appliquer un ensemble commun de paramètres de journaux de diagnostic à toutes les ressources (actuelles et futures) d’un type particulier dont vous souhaitez ingérer les journaux dans Azure Sentinel. Grâce à Azure Policy, vous n’avez plus besoin de définir les paramètres des journaux de diagnostics ressource par ressource.

Des connecteurs basés sur Azure Policy sont maintenant disponibles pour les services Azure suivants :
- [Azure Key Vault](connect-azure-key-vault.md) (préversion publique)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (préversion publique)
- Serveurs/bases de données Azure SQL (GA)

Les clients pourront toujours envoyer les journaux manuellement pour des instances spécifiques, et ne sont pas obligés d’utiliser le moteur de stratégie.

## <a name="february-2021"></a>Février 2021

- [Workbook CMMC (Cybersecurity Maturity Model Certification)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Connecteurs de données tiers](#third-party-data-connectors)
- [Insights UEBA dans la page d’entité](#ueba-insights-in-the-entity-page)
- [Amélioration de la recherche d’incidents](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Workbook CMMC (Cybersecurity Maturity Model Certification)

Le workbook CMMC Azure Sentinel fournit un mécanisme permettant d’afficher les requêtes de journal alignées sur les contrôles CMMC dans le portefeuille Microsoft, notamment les offres de sécurité Microsoft, Office 365, Teams, Intune, Windows Virtual Desktop et bien plus encore.

Le workbook CMMC permet aux architectes de sécurité, aux ingénieurs, aux analystes d’opérations de sécurité, aux responsables et aux professionnels de l’informatique de bénéficier d’une visibilité de la conscience situationnelle de la posture de sécurité des charges de travail cloud. Il existe également des recommandations pour la sélection, la conception, le déploiement et la configuration des offres Microsoft afin qu’elles soient en phase avec les pratiques et exigences CMMC respectives.

Même si vous n’êtes pas obligé de vous conformer à la certification CMMC, le workbook CMMC est utile à la création de centres d’opérations de sécurité, au développement d’alertes, à la visualisation des menaces et à la prise en charge de la conscience situationnelle des charges de travail.

Accédez au workbook CMMC dans la zone **Workbooks** d’Azure Sentinel. Sélectionnez **Modèle**, puis recherchez **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Activez et désactivez le guide du workbook CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Pour plus d’informations, consultez :

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) Workbook](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Tutoriel : Visualiser et superviser vos données](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Connecteurs de données tiers

Notre collection d’intégrations tierces continue de s’agrandir, avec 30 connecteurs ajoutés ces deux derniers mois. En voici la liste :

- [Agari Phishing Defense and Brand Protection](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Alsid for Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (anciennement G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>Insights UEBA dans la page d’entité

Les pages de détails des entités Sentinel Azure contiennent un [volet Insights](identify-threats-with-entity-behavior-analytics.md#entity-insights) qui affiche des informations sur le comportement de l’entité et aident à identifier rapidement les anomalies et les menaces de sécurité.

Si [UEBA est activé](ueba-enrichments.md) et que vous avez sélectionné un délai d’au moins quatre jours, ce volet Insights inclut aussi désormais les nouvelles sections suivantes pour les insights UEBA :

|Section  |Description  |
|---------|---------|
|**Insights UEBA**     | Récapitule les activités anormales des utilisateurs : <br>- Parmi les zones géographiques, appareils et environnements<br>- Parmi les horizons de temps et de fréquence (par rapport à l’historique de l’utilisateur) <br>- Par rapport au comportement des pairs <br>- Par rapport au comportement de l’organisation     |
|**Pairs de l’utilisateur en fonction de l’appartenance au groupe de sécurité**     |   Liste les pairs de l’utilisateur en fonction de l’appartenance à des groupes de sécurité Azure AD, ce qui permet aux équipes des opérations de sécurité de disposer d’une liste d’autres utilisateurs qui partagent des autorisations similaires.  |
|**Autorisations d’accès utilisateur à l’abonnement Azure**     |     Affiche les autorisations d’accès de l’utilisateur aux abonnements Azure accessibles directement, ou par le biais de principaux de service/groupes Azure AD.   |
|**Indicateurs de menace associés à l’utilisateur**     |  Liste une collection de menaces connues associées aux adresses IP représentées dans les activités de l’utilisateur. Les menaces sont listées par type et famille de menaces, et sont enrichies par le service de renseignement sur les menaces de Microsoft.       |
|     |         |

### <a name="improved-incident-search"></a>Amélioration de la recherche d’incidents

Nous avons amélioré l’expérience de recherche d’incidents Azure Sentinel afin de vous permettre de naviguer plus rapidement parmi les incidents lors de l’investigation d’une menace spécifique.

Lors de la recherche d’incidents dans Azure Sentinel, vous pouvez désormais effectuer une recherche d’après les détails d’incident suivants :

- ID
- Titre
- Produit
- Propriétaire
- Tag

## <a name="january-2021"></a>Janvier 2021

- [Assistant de règle d’analyse : Amélioration de l’expérience de modification de requête (préversion publique)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Module PowerShell Az.SecurityInsights (préversion publique)](#azsecurityinsights-powershell-module-public-preview)
- [Connecteur de base de données SQL](#sql-database-connector)
- [Connecteur Dynamics 365](#dynamics-365-connector)
- [Commentaires d’incident améliorés](#improved-incident-comments)
- [Clusters Log Analytics dédiés](#dedicated-log-analytics-clusters)
- [Identités managées par applications logiques](#logic-apps-managed-identities)
- [Amélioration de l’optimisation des règles avec les graphiques d’aperçu des règles d’analytique](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Assistant de règle d’analyse : Amélioration de l’expérience de modification de requête (préversion publique)

L’Assistant de règle d’analyse planifiée Azure Sentinel fournit désormais les améliorations suivantes pour l’écriture et la modification de requêtes :

-   Fenêtre d’édition extensible vous offrant plus d’espace à l’écran pour afficher votre requête.
-   Mise en surbrillance de mot clé dans le code de votre requête.
-   Prise en charge de l’autocomplétion étendue.
-   Validations de requêtes en temps réel. Les erreurs dans votre requête s’affichent désormais sous la forme d’un bloc rouge dans la barre de défilement, et d’un point rouge dans le nom d’onglet **Définir la logique de la règle**. En outre, il n’est pas possible d’enregistrer une requête comportant des erreurs.

Pour plus d’informations, consultez [Tutoriel : Créer des règles d’analytique personnalisées pour détecter des menaces](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Module PowerShell Az.SecurityInsights (préversion publique)

Azure Sentinel prend désormais en charge le nouveau module PowerShell [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Le module **Az.SecurityInsights** prend en charge les cas d’utilisation Azure Sentinel courants, tels que l’interaction avec les incidents pour changer les états, la gravité, le propriétaire, etc., l’ajout de commentaires et d’étiquettes aux incidents et la création de signets.

Même si nous vous déconseillons d’utiliser des modèles [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) pour votre pipeline CI/CD, le module **Az.SecurityInsights** est utile pour les tâches postérieures au déploiement et destiné à l’automatisation du SOC.  Par exemple, l’automatisation de votre SOC peut inclure des étapes pour configurer des connecteurs de données, créer des règles d’analyse ou ajouter des actions d’automatisation à des règles d’analytique.

Pour plus d’informations, notamment une liste complète et une description des applets de commande disponibles, des descriptions de paramètres et des exemples, consultez la [documentation PowerShell sur Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Connecteur de base de données SQL

Azure Sentinel fournit désormais un connecteur de base de données Azure SQL, qui vous permet de diffuser les journaux d’audit et de diagnostics de vos bases de données dans Azure Sentinel et de superviser en continu l’activité dans toutes vos instances.

Azure SQL est un moteur de base de données PaaS (Platform-as-a-Service) complètement managé qui prend en charge la plupart des fonctions de gestion de base de données telles que la mise à niveau, la mise à jour corrective, les sauvegardes et la surveillance, sans intervention de l’utilisateur.

Pour plus d’informations, consultez [Connecter les journaux de diagnostics et d’audit Azure SQL Database](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector"></a>Connecteur Dynamics 365

Azure Sentinel fournit désormais un connecteur pour Microsoft Dynamics 365, qui vous permet de collecter les journaux d’activité des utilisateurs, des administrateurs et du support de vos applications Dynamics 365 dans Azure Sentinel. Vous pouvez utiliser ces données pour vous aider à auditer l’intégralité des actions de traitement des données qui ont lieu, et à les analyser afin d’identifier d’éventuelles failles de sécurité.

Pour plus d’informations, consultez [Connecter des journaux d’activité Dynamics 365 à Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Commentaires d’incident améliorés

Les analystes utilisent les commentaires d’incident pour collaborer sur les incidents, en documentant les processus et les étapes manuellement ou dans le cadre d’un playbook. 

Notre expérience de commentaires d’incident améliorée vous permet de mettre en forme vos commentaires et de modifier ou de supprimer des commentaires existants.

Pour plus d’informations, consultez [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clusters Log Analytics dédiés

Azure Sentinel prend désormais en charge les clusters Log Analytics dédiés en tant qu’option de déploiement. Nous vous recommandons d’envisager un cluster dédié si vous :

- **Ingérez plus de 1 To par jour** dans votre espace de travail Azure Sentinel
- **Avez plusieurs espaces de travail Azure Sentinel** dans votre inscription Azure

Les clusters dédiés vous permettent d’utiliser des fonctionnalités telles que les clés gérées par le client, le référentiel sécurisé, le chiffrement double et des requêtes plus rapides entre les espaces de travail quand un même cluster abrite plusieurs espaces de travail.

Pour plus d’informations, consultez [Clusters dédiés pour les journaux Azure Monitor](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identités managées par applications logiques

Azure Sentinel prend désormais en charge les identités managées pour le connecteur Azure Sentinel Logic Apps, ce qui vous permet d’accorder des autorisations directement à un playbook spécifique destiné à fonctionner sur Azure Sentinel au lieu de créer des identités supplémentaires.

- **Sans identité managée**, le connecteur Logic Apps nécessite une identité distincte avec un rôle RBAC Azure Sentinel pour s’exécuter sur Azure Sentinel. L’identité distincte peut être un utilisateur Azure AD ou un principal de service, tel qu’une application Azure AD inscrite.

- L’**activation de la prise en charge des identités managées dans votre application logique** inscrit cette dernière auprès d’Azure AD et fournit un ID d’objet. Utilisez l’ID d’objet dans Azure Sentinel pour affecter l’application logique avec un rôle RBAC Azure dans votre espace de travail Azure Sentinel. 

Pour plus d'informations, consultez les pages suivantes :

- [Authentification avec une identité managée dans Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
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

Pour plus d’informations, consultez la [documentation Log Analytics](../azure-monitor/agents/log-analytics-agent.md) et les [notes de publication sur l’agent Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembre 2020

- [Superviser l’intégrité de vos playbooks dans Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Connecteur Microsoft 365 Defender (préversion publique)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Superviser l’intégrité de vos playbooks dans Azure Sentinel

Les playbooks Azure Sentinel sont basés sur des workflows intégrés à [Azure Logic Apps](../logic-apps/index.yml), service cloud qui vous permet de planifier, d’automatiser et d’orchestrer des tâches, des processus métier et des workflows. Les playbooks peuvent être appelés automatiquement lors de la création d’un incident ou lors du tri et de l’utilisation d’incidents. 

Pour fournir des insights sur l’intégrité, les performances et l’utilisation de vos playbooks, nous avons ajouté un [workbook](../azure-monitor/visualize/workbooks-overview.md) nommé **Supervision de l’intégrité des playbooks**. 

Utilisez le workbook **Supervision de l’intégrité des playbooks** pour superviser l’intégrité de vos playbooks ou rechercher des anomalies dans le nombre d’exécutions réussies ou ayant échoué. 

Le workbook **Supervision de l’intégrité des playbooks** est maintenant disponible dans la galerie des modèles Azure Sentinel :

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemple du workbook Supervision de l’intégrité des playbooks":::

Pour plus d'informations, consultez les pages suivantes :

- [Documentation Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentation Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

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
