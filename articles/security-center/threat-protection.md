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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: f5218b2346b6ddebcee87a0e24f4924deafdb0f2
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037186"
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





## <a name="threat-protection-for-containers"></a>Protection contre les menaces pour les conteneurs <a name="azure-containers"></a>

### <a name="availability"></a>Disponibilité

- État de sortie : **Disponibilité générale**
- Rôles nécessaires : L’**administrateur de sécurité** peut ignorer les alertes. Le **Lecteur de sécurité** peut afficher les résultats.
- Clouds :<br>
    ✔ Clouds commerciaux<br>
    ✘ US Gov<br>
    ✘ Chine Gov, autres Gov

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Protection contre les menaces pour SQL Database et SQL Data Warehouse <a name="data-sql"></a>

Advanced Threat Protection pour Azure SQL Database détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données.

Vous voyez s’afficher des alertes en cas d’activités de base de données suspectes, de vulnérabilités potentielles ou d’attaques par injection de code SQL ainsi qu’en cas de détection de modèles de requêtes et d’accès anormaux à la base de données.

Advanced Threat Protection pour Azure SQL Database et SQL fait partie du package unifié [Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pour les fonctionnalités de sécurité SQL avancées, qui couvre Azure SQL Database, les instances Azure SQL Managed Instance et les bases de données Azure SQL Data Warehouse, ainsi que les serveurs SQL Server sur les Machines virtuelles Azure.

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Comment activer Advanced Threat Protection pour les serveurs SQL Server sur des machines virtuelles Azure](security-center-iaas-advanced-data.md)
* [Liste des alertes de protection contre les menaces pour SQL Database et SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Protection contre les menaces pour Stockage Azure <a name="azure-storage"></a>

### <a name="availability"></a>Disponibilité

- État de sortie :
    - [Stockage Blob](https://azure.microsoft.com/services/storage/blobs/) (disponibilité générale)
    - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (préversion)
    - [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (préversion)
- Clouds :<br>
    ✔ Clouds commerciaux<br>
    ✔ US Gov<br>
    ✘ Chine Gov, autres Gov

### <a name="whats-protected"></a>Qu’est-ce qui est protégé ?

La protection contre les menaces pour Stockage Azure détecte les activités potentiellement dangereuses sur vos comptes Stockage Azure. Vos données peuvent être protégées, qu’elles soient stockées en tant que conteneurs blob, de partages de fichiers ou de lacs de données.

Cette couche de protection vous permet de traiter efficacement les menaces *sans* pour autant être un expert en sécurité, et vous aide à gérer des systèmes de supervision de la sécurité.

Vos comptes de stockage sont protégés 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Quels types d’alertes la protection des menaces offre-t-elle pour le stockage Azure ?

Les alertes de sécurité sont déclenchées dans les cas suivants :

- **Activité suspecte** : par exemple, le compte de stockage a fait l’objet d’un accès à partir d’une adresse IP connue comme étant un nœud de sortie actif de Tor
- **Comportement anormal** : par exemple, inclut les modifications apportées au modèle d’accès à un compte de stockage
- **Programmes malveillants potentiels chargés** : analyse de réputation du code de hachage qui indique qu’un fichier chargé contient des programmes malveillants

Les alertes fournissent des détails sur l’incident qui les a déclenchées, ainsi que des suggestions pour enquêter et contrer les menaces.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Qu’est-ce que l’analyse de réputation du code de hachage pour les programmes malveillants ?

Pour déterminer si un fichier téléchargé est suspect, la protection contre les menaces pour Azure Storage utilise l’analyse de réputation du code de hachage prise en charge par [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Les outils de protection contre les menaces n’analysent pas les fichiers téléchargés, mais ils examinent les journaux de stockage et comparent le code de hachage des fichiers récemment téléchargés avec ceux des virus, chevaux de Troie, logiciels espions et ransomware connus. 

Lorsqu’un fichier est supposé contenir un logiciel malveillant, Security Center affiche une alerte et peut éventuellement envoyer un e-mail au propriétaire du stockage pour approbation afin de supprimer le fichier suspect. Pour configurer cette suppression automatique des fichiers signalée par l’analyse du code de hachage, déployez une [automatisation du flux de travail pour déclencher des alertes « Logiciel malveillant potentiel chargé sur un compte de stockage »](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Étapes suivantes 

Pour plus d’informations sur la tarification, y compris une version d’évaluation gratuite de 30 jours, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Liste des alertes de protection contre les menaces pour Stockage Azure](alerts-reference.md#alerts-azurestorage)
* [Fonctionnalités de renseignement sur les menaces de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> Vous pouvez simuler des alertes de stockage en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).







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
> 1. Dans le menu de Security Center, sélectionnez **Tarification et paramètres**.
> 1. Sélectionnez l’abonnement que vous souhaitez modifier.
> 1. Sélectionnez **Détection des menaces**.
> 1. Décochez la case **Autoriser Microsoft Cloud App Security à accéder à mes données** et sélectionnez **Enregistrer**.

>[!NOTE]
>Security Center stocke les données de client liées à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Security Center dans la zone géographique de la ressource, il stocke les données aux États-Unis. Quand Cloud App Security est activé, ces informations sont stockées selon les règles d’emplacement géographique de Cloud App Security. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).

1. Définissez l’espace de travail sur lequel vous installez l’agent. Assurez-vous que l’espace de travail est dans le même abonnement que vous utilisez dans Security Center et que vous disposez d’autorisations en lecture/écriture sur l’espace de travail.

1. Définissez le niveau tarifaire standard, puis sélectionnez **Enregistrer**.






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