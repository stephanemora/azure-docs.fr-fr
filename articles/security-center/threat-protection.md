---
title: Protection contre les menaces dans Azure Security Center
description: Cette rubrique décrit les ressources protégées par les fonctionnalités de protection contre les menaces d’Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 850b06153a25020f36a4c7df1863e5a576495f3b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744156"
---
# <a name="threat-protection-in-azure-security-center"></a>Protection contre les menaces dans Azure Security Center

Quand Security Center détecte une menace dans un domaines de votre environnement, il génère une alerte. Ces alertes décrivent les détails des ressources affectées, les étapes de correction suggérées et, dans certains cas, l’option permettant de déclencher une application logique en réponse.

La protection contre les menaces d’Azure Security Center offre des défenses complètes pour votre environnement :

* **Protection contre les menaces pour les ressources de calcul Azure** : Ordinateurs Windows, ordinateurs Linux, Azure App Service et conteneurs Azure

* **Protection contre les menaces pour les ressources de données Azure** : SQL Database et SQL Data Warehouse, stockage Azure et Azure Cosmos DB

* **Protection contre les menaces pour les couches de services Azure** : Couche réseau Azure, couche de gestion Azure (Azure Resource Manager) (préversion) et Azure Key Vault (préversion)

Qu’une alerte soit générée par Security Center ou reçue par Security Center à partir d’un autre produit de sécurité, vous pouvez l’exporter. Pour exporter vos alertes vers Azure Sentinel (ou un système SIEM tiers) ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers SIEM](continuous-export.md). 

> [!NOTE]
> Les alertes provenant de différentes sources peuvent prendre différentes durées d’affichage. Par exemple, l’affichage des alertes qui nécessitent une analyse du trafic réseau peut prendre plus de temps que pour les alertes liées à des processus suspects s’exécutant sur des machines virtuelles.

> [!TIP]
> Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez appliquer le niveau tarifaire Standard à l’abonnement contenant les charges de travail concernées.
>
> Vous pouvez activer la protection contre les menaces pour des **comptes Stockage Azure** au niveau de l’abonnement ou de la ressource.
> Vous pouvez activer la protection contre les menaces pour des **serveurs SQL Azure SQL Database** au niveau de l’abonnement ou de la ressource.
> Vous pouvez activer la protection contre les menaces pour **Azure Database for MariaDB/Azure Database pour MySQL/Azure Database pour PostgreSQL** au niveau de la ressource uniquement.



## <a name="threat-protection-for-windows-machines"></a>Protection contre les menaces pour les machines Windows <a name="windows-machines"></a>

Azure Security Center s’intègre aux services Azure pour superviser et protéger vos machines Windows. Security Center présente les alertes et les suggestions de correction de l’ensemble de ces services dans un format facile à utiliser.

* **Advanced Threat Protection (ATP) de Microsoft Defender**<a name="windows-atp"></a> - Security Center étend ses plateformes de protection de charge de travail cloud en s’intégrant Advanced Threat Protection (ATP) de Microsoft Defender. Ensemble, ils offrent des fonctionnalités EDR (protection évolutive des points de terminaison) complètes.

    > [!IMPORTANT]
    > Le capteur Microsoft Defender ATP est automatiquement activé sur les serveurs Windows qui utilisent Security Center.

    Quand Microsoft Defender ATP détecte une menace, il déclenche une alerte. L’alerte s’affiche dans le tableau de bord de Security Center. À partir du tableau de bord, vous pouvez accéder à la console Microsoft Defender ATP et effectuer un examen détaillé pour découvrir l’étendue de l’attaque. Pour plus d’informations sur Microsoft Defender ATP, consultez [Intégrer des serveurs au service Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Détection des attaques sans fichier** <a name="windows-fileless"></a> - Les attaques sans fichier ciblant des points de terminaison sont courantes. Pour éviter d’être détectées, les attaques sans fichier injectent des charges utiles malveillantes en mémoire. Les charges utiles des attaquants sont conservées dans la mémoire des processus compromis et effectuent un large éventail d’activités malveillantes.

    Avec la détection des attaques sans fichier, les techniques d’investigation automatique de la mémoire identifient les comportements, les techniques et les kits de ressources des attaques sans fichier. Cette solution analyse régulièrement votre machine au moment de l’exécution et extrait des insights directement de la mémoire des processus critiques de sécurité.

    Elle trouve des preuves de l’exploitation, de l’injection de code et de l’exécution de charges utiles malveillantes. La détection des attaques sans fichier génère des alertes de sécurité détaillées qui accélèrent le tri des alertes, la corrélation et le temps de réponse en aval. Cette approche complète les solutions EDR basées sur les événements, en offrant une couverture de détection plus large.

    Pour obtenir des détails sur les alertes de détection des attaques sans fichier, consultez la [table de référence des alertes](alerts-reference.md#alerts-windows).

> [!TIP]
> Vous pouvez simuler des alertes Windows en téléchargeant le document [Azure Security Center Playbook: Alertes de sécurité](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Protection contre les menaces pour les machines Linux <a name="linux-machines"></a>

Security Center collecte les enregistrements d’audit à partir des machines Linux à l’aide d’**auditd**, l’un des frameworks d’audit Linux les plus courants. auditd se trouve dans le noyau mainline. 

* **Intégration des alertes auditd Linux et de l’agent Log Analytics** <a name="linux-auditd"></a> : le système auditd se compose d’un sous-système au niveau du noyau, qui est responsable de la supervision des appels système. Il filtre les appels par un ensemble de règles donné et écrit les messages pour ces derniers dans un socket. Security Center intègre les fonctionnalités du package auditd à l’agent Log Analytics. Cette intégration permet la collecte des événements auditd dans toutes les distributions Linux prises en charge, sans prérequis.

    Les enregistrements auditd sont collectés, enrichis et agrégés dans des événements à l’aide de l’agent Log Analytics pour Linux. Security Center enrichit continuellement l’analytique, qui utilise les signaux Linux pour détecter les comportements malveillants sur les machines Linux locales ou dans le cloud. À l’image des fonctionnalités Windows, cette analytique englobe les processus suspects, les tentatives de connexion douteuses, le chargement de modules de noyau et diverses autres activités. Ces activités peuvent signaler qu’une machine fait l’objet d’une attaque ou qu’elle a subi une violation de la sécurité.  

    Pour obtenir la liste des alertes Linux, consultez la [table de référence des alertes](alerts-reference.md#alerts-linux).

> [!TIP]
> Vous pouvez simuler des alertes Linux en téléchargeant le document [Azure Security Center Playbook : Détections Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Protection contre les menaces pour Azure App Service <a name="app-services"></a>

> [!NOTE]
> Ce service n’est pas disponible actuellement dans les régions Azure Government et de cloud souverain.

Security Center utilise l’échelle du cloud pour identifier les attaques ciblant les applications exécutées sur App Service. Les attaquants sondent les applications web pour trouver et exploiter les faiblesses. Avant d’être routées vers des environnements spécifiques, les demandes adressées aux applications s’exécutant dans Azure passent par plusieurs passerelles, où elles sont inspectées et journalisées. Ces données sont ensuite utilisées pour identifier les codes malveillants exploitant une faille de sécurité ainsi que les attaquants, et pour découvrir de nouveaux modèles qui seront utilisés ultérieurement.

En utilisant la visibilité dont Azure bénéficie en tant que fournisseur de cloud, Security Center analyse les journaux internes d’App Service pour identifier la méthodologie d’attaque sur plusieurs cibles. Les attaques distribuées et les attaques par analyse généralisée sont des exemples de méthodologie. En général, ce type d’attaque provient d’un petit sous-ensemble d’adresses IP, et présente des modèles de progression vers des points de terminaison similaires sur plusieurs hôtes. Les attaques recherchent une page ou un plug-in vulnérable et ne peuvent pas être identifiées du point de vue d’un seul hôte.

Si vous exécutez un plan App Service Windows, Security Center a également accès aux machines virtuelles et aux bacs à sable sous-jacents. Couplée aux données de journal mentionnées ci-dessus, l’infrastructure peut raconter l’histoire, depuis une nouvelle attaque lâchée dans la nature à la compromission des machines des clients. Par conséquent, même si Security Center est déployé après qu’une application web a été attaquée, il est capable de détecter les attaques en cours.

Pour obtenir la liste des alertes Azure App Service, consultez la [table de référence des alertes](alerts-reference.md#alerts-azureappserv).

Pour plus d’informations sur les plans App Service, consultez [Plans App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Protection contre les menaces pour les conteneurs Azure <a name="azure-containers"></a>

> [!NOTE]
> Ce service n’est pas disponible actuellement dans les régions Azure Government et de cloud souverain.

Security Center fournit une protection contre les menaces en temps réel pour vos environnements conteneurisés, et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Security Center offre une protection contre les menaces à différents niveaux : 

* **Au niveau de l’hôte** : l’agent de Security Center (disponible avec le niveau tarifaire Standard ; consultez les [tarifs](security-center-pricing.md) pour plus d’informations) supervise Linux pour détecter les activités suspectes. L’agent déclenche des alertes s’il détecte des activités suspectes réalisées à partir du nœud ou d’un conteneur qui y est exécuté. Parmi ces activités, citons la détection de shell web et la connexion avec des adresses IP suspectes connues.

    Pour fournir des insights plus complets sur la sécurité de votre environnement conteneurisé, l’agent supervise l’analytique des conteneurs. Il déclenche des alertes sur des événements tels que la création de conteneurs privilégiés, l’accès suspect à des serveurs d’API et à des serveurs Secure Shell (SSH) s’exécutant dans un conteneur Docker.

    >[!IMPORTANT]
    > Si vous choisissez de ne pas installer les agents sur vos machines hôtes, vous ne recevrez qu’une partie des avantages et alertes de sécurité relatifs à la protection contre les menaces. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

    Pour obtenir la liste des alertes au niveau de l’hôte, consultez la [table de référence des alertes](alerts-reference.md#alerts-containerhost).


* Au **niveau du cluster AKS**, la protection contre les menaces est basée sur l’analyse des journaux d’audit de Kubernetes. Pour activer cette supervision **sans agent**, ajoutez l’option Kubernetes à votre abonnement à partir de la page **Tarification et paramètres** (voir les [tarifs](security-center-pricing.md)). Pour générer des alertes à ce niveau, Security Center supervise vos services managés par AKS à l’aide des journaux collectés par AKS. Les tableaux de bord Kubernetes exposés, la création de rôles dotés de privilèges élevés et la création de montages sensibles sont des exemples d’événements à ce niveau.

    >[!NOTE]
    > Security Center génère des alertes de sécurité pour les actions et les déploiements Azure Kubernetes Service, qui se produisent après l’activation de l’option Kubernetes dans les paramètres de l’abonnement. 

    Pour obtenir la liste des alertes au niveau du cluster AKS, consultez la [table de référence des alertes](alerts-reference.md#alerts-akscluster).

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.

> [!TIP]
> Vous pouvez simuler des alertes de conteneur en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Protection contre les menaces pour SQL Database et SQL Data Warehouse <a name="data-sql"></a>

Advanced Threat Protection pour Azure SQL Database détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données.

Vous voyez s’afficher des alertes en cas d’activités de base de données suspectes, de vulnérabilités potentielles ou d’attaques par injection de code SQL ainsi qu’en cas de détection de modèles de requêtes et d’accès anormaux à la base de données.

Advanced Threat Protection pour Azure SQL Database et SQL font partie du package unifié [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pour les fonctionnalités de sécurité SQL avancées, couvrant les bases de données Azure SQL Database, les instances gérées d’Azure SQL Database, les bases de données Azure SQL Data Warehouse et les serveurs SQL Server sur les machines virtuelles Azure.

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Comment activer Advanced Threat Protection pour les serveurs SQL Server sur des machines virtuelles Azure](security-center-iaas-advanced-data.md)
* [Liste des alertes de protection contre les menaces pour SQL Database et SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Protection contre les menaces pour Stockage Azure <a name="azure-storage"></a>

Advanced Threat Protection pour le stockage détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité, et vous aide à gérer des systèmes de supervision de la sécurité.

La protection avancée contre les menaces pour Stockage Azure est actuellement disponible pour le [Stockage Blob](https://azure.microsoft.com/services/storage/blobs/) uniquement. 

Ce service est disponible dans tous les clouds publics et les clouds US Government, mais pas dans d’autres régions de cloud souverain ou de cloud Azure Government.

Pour plus d’informations sur la tarification, y compris une version d’évaluation gratuite de 30 jours, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Liste des alertes de protection contre les menaces pour Stockage Azure](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Vous pouvez simuler des alertes de stockage Azure en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Protection contre les menaces pour Azure Cosmos DB <a name="cosmos-db"></a>

Les alertes Azure Cosmos DB sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB.

Pour plus d'informations, consultez les pages suivantes :

* [Advanced Threat Protection pour Azure Cosmos DB (préversion)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste des alertes de protection contre les menaces pour Azure Cosmos DB (préversion)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Protection contre les menaces pour la couche réseau Azure <a name="network-layer"></a>

L’analytique de la couche réseau de Security Center est basée sur des exemples de [données IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), qui sont des en-têtes de paquets collectés par des routeurs de base Azure. En fonction de ce flux de données, les modèles Machine Learning de Security Center identifient et signalent les activités de trafic malveillantes. Security Center utilise également la base de données Microsoft Threat Intelligence pour enrichir les adresses IP.

Certaines configurations réseau peuvent empêcher Security Center de générer des alertes sur une activité réseau suspecte. Pour que Security Center génère des alertes réseau, assurez-vous que :

- Votre machine virtuelle a une adresse IP publique (ou se trouve sur un équilibreur de charge avec une adresse IP publique).

- Le trafic de sortie réseau de votre machine virtuelle n’est pas bloqué par une solution IDS externe.

- La même adresse IP a été attribuée à votre machine virtuelle pendant toute l’heure pendant laquelle la communication suspecte s’est produite. Cela s’applique également aux machines virtuelles créées dans le cadre d’un service managé (par exemple AKS, Databricks).

Pour obtenir la liste des alertes de la couche réseau Azure, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurenetlayer).

Pour plus d’informations sur la façon dont Security Center peut utiliser les signaux liés au réseau pour appliquer la protection contre les menaces, consultez [Détections de DNS heuristiques dans Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Protection contre les menaces pour la couche de gestion Azure (Azure Resource Manager) (préversion)<a name ="management-layer"></a>

La couche de protection de Security Center basée sur Azure Resource Manager est disponible en préversion.

Security Center offre une couche supplémentaire de protection en utilisant les événements Azure Resource Manager, considérée comme le plan de contrôle pour Azure. En analysant les enregistrements Azure Resource Manager, Security Center détecte les opérations inhabituelles ou potentiellement dangereuses dans l’environnement d’un abonnement Azure.

Pour obtenir la liste des alertes Azure Resource Manager (préversion), consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Plusieurs des analyses précédentes sont alimentées par Microsoft Cloud App Security. Pour tirer parti de ces analyses, vous devez activer une licence Cloud App Security. Si vous avez une licence Cloud App Security, ces alertes sont activées par défaut. Pour désactiver les alertes :
>
> 1. Dans le panneau **Security Center**, sélectionnez **Stratégie de sécurité**. Pour l’abonnement que vous souhaitez modifier, sélectionnez **Modifier les paramètres**.
> 2. Sélectionnez **Détection des menaces**.
> 3. Sous **Activer les intégrations**, décochez la case **Autoriser Microsoft Cloud App Security à accéder à mes données** et sélectionnez **Enregistrer**.

>[!NOTE]
>Security Center stocke les données de client liées à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Security Center dans la zone géographique de la ressource, il stocke les données aux États-Unis. Quand Cloud App Security est activé, ces informations sont stockées selon les règles d’emplacement géographique de Cloud App Security. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Protection contre les menaces pour Azure Key Vault (préversion)<a name="azure-keyvault"></a>

> [!NOTE]
> Ce service n’est pas disponible actuellement dans les régions Azure Government et de cloud souverain.

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

Azure Security Center inclut la protection native avancée Azure contre les menaces pour Azure Key Vault qui fournit une couche supplémentaire de renseignements de sécurité. Security Center détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes tiers de supervision de la sécurité.  

Quand des activités anormales se produisent, Security Center affiche des alertes et les envoie éventuellement par e-mail aux administrateurs d’abonnement. Ces alertes fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

Pour obtenir la liste des alertes Azure Key Vault, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Protection contre les menaces pour d’autres services Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Protection contre les menaces pour Azure WAF <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. Le pare-feu d’applications web d’Application Gateway suit l’ensemble des règles de base 3.0 ou 2.2.9 d’Open Web Application Security Project. La solution WAF est mise à jour automatiquement pour offrir une protection contre les nouvelles vulnérabilités. 

Si vous avez une licence pour Azure WAF, vos alertes WAF sont envoyées en streaming vers Security Center sans configuration supplémentaire. Pour plus d’informations sur les alertes générées par le pare-feu d’applications web, consultez [Règles et groupes de règles CRS de pare-feu d’applications web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Protection contre les menaces pour Azure DDoS Protection <a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. Elles sont devenues un problème de sécurité majeur, en particulier si vous déplacez vos applications vers le cloud. 

Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible sur Internet.

Pour vous défendre contre les attaques DDoS, achetez une licence Azure DDoS Protection, puis veillez à suivre les bonnes pratiques de conception d’applications. DDoS Protection fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Pour obtenir la liste des alertes Azure DDoS Protection, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les alertes de sécurité provenant de ces fonctionnalités de protection contre les menaces, consultez les articles suivants :

* [Tableau de référence pour toutes les alertes Azure Security Center](alerts-reference.md)
* [Alertes de sécurité dans le Centre de sécurité Azure](security-center-alerts-overview.md)
* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exporter les alertes et recommandations de sécurité (préversion)](continuous-export.md)