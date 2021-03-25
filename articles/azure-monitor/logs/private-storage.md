---
title: Utilisation de comptes de stockage gérés par le client dans Azure Monitor Log Analytics
description: Utilisez votre propre compte de stockage pour les scénarios de Log Analytics
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 69b5927c73dac14c76b94a4ee5bbb21449f8ec98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047450"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Utilisation de comptes de stockage gérés par le client dans Azure Monitor Log Analytics

Log Analytics s’appuie sur Stockage Azure dans différents scénarios. Cette utilisation est généralement gérée automatiquement. Toutefois, dans certains cas, vous devez fournir et gérer votre propre compte de stockage, également appelé compte de stockage géré par le client. Ce document couvre l’utilisation du stockage géré par le client pour les journaux WAD/LAD, la liaison privée, et le chiffrement à l’aide d’une clé gérée par le client (CMK). 

> [!NOTE]
> Nous vous recommandons de ne pas dépendre du contenu que Log Analytics charge sur le stockage géré par le client, étant donné que le formatage et le contenu peuvent changer.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingestion des journaux de l’extension Azure Diagnostics (WAD/LAD)
Les agents de l’extension Azure Diagnostics (également appelés WAD et LAD pour les agents Windows et Linux, respectivement) recueillent différents journaux du système d’exploitation et les stockent sur un compte de stockage géré par le client. Vous pouvez ensuite ingérer ces journaux dans Log Analytics pour les examiner et les analyser.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Procédure de collecte des journaux de l’extension Azure Diagnostics à partir de votre compte de stockage
Connectez le compte de stockage à votre espace de travail Log Analytics en tant que source de données de stockage à l’aide du [portail Azure](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) ou en appelant l’[API Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Types de données pris en charge :
* syslog
* Événements Windows
* Service Fabric
* Événements ETW
* Journaux d’activité IIS

## <a name="using-private-links"></a>Utilisation de liaisons privées
Les comptes de stockage gérés par le client servent à ingérer des journaux personnalisés ou des journaux IIS lorsque des liaisons privées sont utilisées pour se connecter à des ressources Azure Monitor. Le processus d’ingestion de ces types de données charge d’abord les journaux sur un compte Stockage Azure intermédiaire, et seulement ensuite les ingère dans un espace de travail. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Utilisation d’un compte de stockage géré par le client via une liaison privée
#### <a name="workspace-requirements"></a>Conditions requises pour l’espace de travail
Lors de la connexion à Azure Monitor via une liaison privée, les agents Log Analytics peuvent uniquement envoyer des journaux à des espaces de travail accessibles via une liaison privée. Cette exigence signifie que vous devez :
* Configurer un objet Étendue de liaison privée Azure Monitor (AMPLS) ;
* Connecter cet objet à vos espaces de travail ;
* Connecter l’objet AMPLS à votre réseau via une liaison privée ; 

Pour plus d’informations sur la procédure de configuration de l’AMPLS, consultez [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage
Pour que le compte de stockage se connecte correctement à votre liaison privée, il doit :
* Vous trouver sur votre réseau virtuel ou sur un réseau appairé et être connecté à votre réseau virtuel via une liaison privée.
* se trouver dans la même région que l’espace de travail auquel il est lié ;
* autoriser Azure Monitor à accéder au compte de stockage. Si vous avez choisi d’autoriser uniquement certains réseaux à accéder à votre compte de stockage, vous devez sélectionner l’exception : « Autoriser les services Microsoft approuvés à accéder à ce compte de stockage ».
![Image de l’approbation de compte de stockage MS services](./media/private-storage/storage-trust.png)
* si votre espace de travail traite également du trafic provenant d’autres réseaux, vous devez configurer le compte de stockage de manière à autoriser le trafic entrant provenant des réseaux concernés ou d’Internet.
* Coordonnez la version TLS entre les agents et le compte de stockage : il est recommandé d’envoyer des données à Log Analytics à l’aide du protocole TLS 1.2 ou version ultérieure. Passez en revue les [conseils spécifiques à la plateforme](https://docs.microsoft.com/azure/azure-monitor/logs/data-security#sending-data-securely-using-tls-12) et, si nécessaire, [configurez vos agents pour utiliser TLS 1.2](https://docs.microsoft.com/azure/azure-monitor/agents/agent-windows#configure-agent-to-use-tls-12). Si, pour une raison quelconque, cela n’êtes pas possible, configurez le compte de stockage pour qu’il accepte TLS 1.0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Utilisation d’un compte de stockage géré par le client pour le chiffrement de données CMK
Stockage Azure chiffre toutes les données au repos dans un compte de stockage. Par défaut, il utilise des clés gérées par Microsoft (MMK) pour chiffrer les données ; cependant, Stockage Azure vous permet également d’utiliser CMK à partir d’Azure Key Vault pour chiffrer vos données de stockage. Vous pouvez importer vos propres clés dans Azure Key Vault ou utiliser les API d’Azure Key Vault pour générer des clés.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scénarios CMK qui requièrent un compte de stockage géré par le client
* Chiffrement des requêtes d’alerte de journal avec une clé gérée par le client
* Chiffrement des requêtes enregistrées avec une clé gérée par le client

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Comment appliquer une clé gérée par le client à des comptes de stockage gérés par le client
##### <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage
Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur le chiffrement et la gestion des clés dans le stockage Azure, consultez [Chiffrement du stockage Azure pour les données au repos](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Appliquer une clé gérée par le client à vos comptes de stockage
Pour configurer votre compte Stockage Azure de façon à utiliser CMK avec Azure Key Vault, utilisez le [portail Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) ou l’[interface CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Lier des comptes de stockage à votre espace de travail Log Analytics
### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
Dans le portail Azure, ouvrez le menu de votre espace de travail, puis sélectionnez *Comptes de stockage liés*. Un panneau s’ouvre et affiche les comptes de stockage liés en utilisant les cas d’utilisation mentionnés ci-dessus (ingestion via une liaison privée, application de CMK à des requêtes enregistrées ou à des alertes).
![Image du panneau Comptes de stockage liés](./media/private-storage/all-linked-storage-accounts.png) La sélection d’un élément de la table ouvre les détails du compte de stockage, où vous pouvez définir ou mettre à jour le compte de stockage lié pour ce type. 
![Image du panneau de liaison d’un compte de stockage](./media/private-storage/link-a-storage-account-blade.png) Vous pouvez utiliser le même compte pour différents cas d’utilisation, si vous le souhaitez.

### <a name="using-the-azure-cli-or-rest-api"></a>Utilisation de l’interface de ligne de commande Azure ou de l’API REST
Vous pouvez également lier un compte de stockage à votre espace de travail via l’[interface de ligne de commande Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou l’[API REST](/rest/api/loganalytics/linkedstorageaccounts).

Les valeurs dataSourceType applicables sont les suivantes :
* CustomLogs : utilise le compte de stockage pour l’ingestion de journaux personnalisés et de journaux IIS
* Requête : utilise le compte de stockage pour stocker les requêtes enregistrées (requis pour le chiffrement CMK)
* Alertes : utilise le compte de stockage pour stocker les alertes basées sur un journal (requis pour le chiffrement CMK)


## <a name="managing-linked-storage-accounts"></a>Gestion des comptes de stockage liés

### <a name="create-or-modify-a-link"></a>Créer ou modifier une liaison
Lorsque vous liez un compte de stockage à un espace de travail, Log Analytics commence à l’utiliser au lieu du compte de stockage détenu par le service. Vous pouvez : 
* inscrire plusieurs comptes de stockage pour répartir la charge des journaux entre eux ;
* réutiliser le même compte de stockage pour plusieurs espaces de travail.

### <a name="unlink-a-storage-account"></a>Dissocier un compte de stockage
Pour cesser d’utiliser un compte de stockage, dissociez le stockage de l’espace de travail. La dissociation de tous les comptes de stockage d’un espace de travail signifie que Log Analytics tentera de se tourner vers les comptes de stockage gérés par le service. Si votre réseau dispose d’un accès limité à Internet, ces stockages peuvent être indisponibles et tout scénario qui repose sur le stockage échouera.

### <a name="replace-a-storage-account"></a>Remplacer un compte de stockage
Pour remplacer un compte de stockage utilisé pour l’ingestion :
1.  **Créez une liaison vers un nouveau compte de stockage.** Les agents de journalisation recevront la configuration mise à jour et commenceront également à envoyer des données vers le nouveau stockage. Le processus peut prendre quelques minutes.
2.  **Ensuite, dissociez l’ancien compte de stockage pour que les agents cessent d’écrire dans le compte supprimé.** Le processus d’ingestion continue de lire les données de ce compte jusqu’à ce qu’elles soient toutes ingérées. Ne supprimez pas le compte de stockage tant que tous les journaux n’ont pas été ingérés.

### <a name="maintaining-storage-accounts"></a>Conservation des comptes de stockage
#### <a name="manage-log-retention"></a>Gérer la rétention des journaux
Lorsque vous utilisez votre propre compte de stockage, la rétention est laissée à votre discrétion. Log Analytics ne supprimera pas les journaux stockés sur votre stockage privé. Au lieu de cela, vous devez configurer une stratégie pour gérer la charge en fonction de vos préférences.

#### <a name="consider-load"></a>Prendre en compte la charge
Les comptes de stockage peuvent gérer une certaine charge de requêtes de lecture et d’écriture avant de commencer à limiter les requêtes (pour plus d’informations, consultez [Cibles de scalabilité et de performances pour les comptes de stockage standard](../../storage/common/scalability-targets-standard-account.md)). La limitation a une incidence sur le temps nécessaire à l’ingestion des journaux. Si votre compte de stockage est surchargé, inscrivez un compte de stockage supplémentaire afin de répartir la charge. Pour surveiller la capacité et le niveau de performance de votre compte de stockage, consultez ses [insights dans le portail Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Frais connexes
Les comptes de stockage sont facturés en fonction du volume de données stockées, du type de stockage et du type de redondance. Pour plus d’informations, consultez [Tarification des objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs) et [Tarification de Stockage Table](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[utilisation d’Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](private-link-security.md)
- En savoir plus sur les [clés gérées par le client dans Azure Monitor](../logs/customer-managed-keys.md)
