---
title: Configurer la version minimale requise du protocole TLS (Transport Layer Security) pour un compte de stockage
titleSuffix: Azure Storage
description: Configurez un compte de stockage afin d’exiger une version minimale du protocole TLS (Transport Layer Security) pour les clients effectuant des demandes sur le stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208969"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Configurer la version minimale requise du protocole TLS (Transport Layer Security) pour un compte de stockage

La communication entre une application cliente et un compte de stockage Azure est chiffrée à l’aide du protocole TLS (Transport Layer Security). Le protocole TLS est un protocole de chiffrement standard qui garantit la confidentialité et l’intégrité des données entre les clients et les services via Internet. Pour plus d’informations sur le protocole TLS , consultez [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Le stockage Azure prend en charge trois versions du protocole TLS : 1.0, 1.1 et 1.2. TLS 1.2 est la version la plus sécurisée du protocole TLS. Le stockage Azure utilise TLS 1.2 sur les points de terminaison HTTP publics, mais TLS 1.0 et TLS 1.1 sont toujours pris en charge à des fins de compatibilité descendante.

Par défaut, les comptes de stockage Azure permettent aux clients d’envoyer et de recevoir des données avec la version la plus ancienne de TLS, TLS 1.0 et les versions ultérieures. Pour appliquer des mesures de sécurité plus strictes, vous pouvez configurer votre compte de stockage afin d’exiger que les clients envoient et reçoivent des données avec une version plus récente du protocole TLS. Si un compte de stockage nécessite une version minimale de TLS, les demandes effectuées avec une version antérieure échouent.

Cet article explique comment configurer un compte de stockage pour exiger que les clients envoient des demandes avec une version minimale de TLS. Pour plus d’informations sur la façon de spécifier une version particulière de TLS lors de l’envoi d’une demande à partir d’une application cliente, consultez [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Détecter la version TLS utilisée par les applications clientes

Quand vous appliquez une version TLS minimale pour votre compte de stockage, vous risquez de rejeter les demandes des clients qui envoient des données avec une version antérieure de TLS. Pour que vous compreniez dans quelle mesure la configuration de la version minimale de TLS peut affecter les applications clientes, Microsoft vous recommande d’activer la journalisation pour votre compte de stockage Azure et d’analyser les journaux après un intervalle de temps afin de déterminer les versions de TLS utilisées par les applications clientes.

Pour journaliser les demandes dans votre compte de stockage Azure et déterminer la version TLS utilisée par le client, vous pouvez utiliser la journalisation du stockage Azure dans Azure Monitor (préversion). Pour plus d’informations, consultez [Superviser le stockage Azure](monitor-storage.md).

La journalisation du stockage Azure dans Azure Monitor prend en charge l’utilisation de requêtes de journal pour analyser les données des journaux. Pour interroger les journaux, vous pouvez utiliser un espace de travail Azure Log Analytics. Pour en savoir plus sur les requêtes de journal, consultez [Tutoriel : Bien démarrer avec les requêtes Log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Pour journaliser des données de stockage Azure avec Azure Monitor et les analyser avec Azure Log Analytics, vous devez d’abord créer un paramètre de diagnostic qui indique les types de demandes et les services de stockage pour lesquels vous souhaitez journaliser les données. Pour créer un paramètre de diagnostic dans le portail Azure, suivez ces étapes :

1. Inscrivez-vous à la [journalisation de stockage Azure dans Azure Monitor (préversion)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Créez un espace de travail Log Analytics dans l’abonnement qui contient votre compte de stockage Azure. Une fois que vous avez configuré la journalisation pour votre compte de stockage, les journaux sont disponibles dans l’espace de travail Log Analytics. Pour plus d’informations, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la section Supervision, Sélectionnez **Paramètres de diagnostic (préversion)** .
1. Sélectionnez le service de stockage Azure pour lequel vous souhaitez journaliser les demandes. Par exemple, choisissez **Blob** pour journaliser les demandes dans le stockage Blob.
1. Sélectionnez **Ajouter le paramètre de diagnostic**.
1. Fournissez un nom pour le paramètre de diagnostic.
1. Sous **Détails de la catégorie**, dans la section **Journal**, choisissez les types de demandes à journaliser. Vous pouvez journaliser les demandes de lecture, d’écriture et de suppression. Par exemple, si vous choisissez **StorageRead** et **StorageWrite**, les demandes de lecture et d’écriture sont journalisées sur le service sélectionné.
1. Sous **Détails de la destination**, sélectionnez **Envoyer à Log Analytics**. Sélectionnez votre abonnement et l’espace de travail Log Analytics que vous avez créé, comme illustré dans l’image suivante.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Capture d’écran montrant comment créer un paramètre de diagnostic pour la journalisation des demandes":::

Une fois le paramètre de diagnostic créé, les demandes adressées au compte de stockage sont journalisées conformément à ce paramètre. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter les journaux et les métriques des ressources dans Azure](../../azure-monitor/platform/diagnostic-settings.md).

Pour obtenir des informations de référence sur les champs disponibles dans les journaux de stockage Azure dans Azure Monitor, consultez [Journaux de ressource (préversion)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Interroger les demandes journalisées en fonction de la version de TLS

Les journaux de stockage Azure dans Azure Monitor incluent la version de TLS utilisée pour envoyer une demande à un compte de stockage. Utilisez la propriété **TlsVersion** pour vérifier la version de TLS d’une demande journalisée.

Pour récupérer les journaux des 7 derniers jours et déterminer le nombre de demandes effectuées sur le stockage Blob avec chaque version de TLS, ouvrez votre espace de travail Log Analytics. Collez ensuite la requête suivante dans une nouvelle requête de journal et exécutez-la. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Les résultats indiquent le nombre de demandes effectuées avec chaque version de TLS :

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Capture d’écran montrant les résultats de la requête Log Analytics pour retourner la version de TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Interroger les demandes journalisées en fonction de l’adresse IP de l’appelant et de l’en-tête de l’agent utilisateur

Les journaux de stockage Azure dans Azure Monitor incluent également l’adresse IP de l’appelant et l’en-tête de l’agent utilisateur pour vous aider à déterminer les applications clientes ayant accédé au compte de stockage. Vous pouvez analyser ces valeurs pour décider si les applications clientes doivent être mises à jour afin d’utiliser une version plus récente de TLS ou si la demande d’un client non envoyée avec la version minimale de TLS peut faire l’objet d’un rejet.

Pour récupérer les journaux des 7 derniers jours et déterminer les clients qui ont effectué des demandes avec une version de TLS antérieure à TLS 1.2, collez la requête suivante dans une nouvelle requête de journal et exécutez-la. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Configurer la version minimale de TLS pour un compte

Pour configurer la version minimale de TLS pour un compte de stockage, utilisez le portail Azure ou Azure CLI afin de définir la version **minimumTlsVersion** pour le compte. Cette propriété est disponible pour tous les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage](storage-account-overview.md).

# <a name="portal"></a>[Portail](#tab/portal)

Pour configurer la version minimale de TLS pour un compte de stockage avec le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sélectionnez le paramètre **Configuration**.
1. Sous **Version TLS minimale**, utilisez la liste déroulante afin de sélectionner la version minimale de TLS nécessaire pour accéder aux données dans ce compte de stockage, comme illustré dans l’image suivante.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Capture d’écran montrant comment configurer la version minimale de TLS dans le portail Azure":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer la version minimale de TLS pour un compte de stockage avec Azure CLI, commencez par obtenir l’ID de ressource de votre compte de stockage en appelant la commande [az resource show](/cli/azure/resource#az-resource-show). Ensuite, appelez la commande [az resource update](/cli/azure/resource#az-resource-update) pour définir la propriété **minimumTlsVersion** pour le compte de stockage. Les valeurs valides pour **minimumTlsVersion** sont `TLS1_0`, `TLS1_1` et `TLS1_2`.

L’exemple suivant définit la version minimale de TLS sur 1.2. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Une fois la version minimale de TLS mise à jour pour le compte de stockage, la propagation de la modification peut prendre jusqu’à 30 secondes.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Vérifier la version minimale de TLS requise pour un compte

Pour déterminer la version minimale requise de TLS configurée pour un compte de stockage, vérifiez la propriété Azure Resource Manager **minimumTlsVersion**. Pour vérifier cette propriété pour un grand nombre de comptes de stockage à la fois, utilisez l’Explorateur Azure Resource Graph.

La propriété **minimumTlsVersion** n’est pas définie par défaut et ne retourne pas de valeur tant que vous ne la définissez pas explicitement. Par défaut, le compte de stockage autorise les demandes envoyées avec la version de TLS 1.0 ou ultérieure si la valeur de la propriété est Null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Vérifier la version minimale requise de TLS pour un compte de stockage spécifique

Pour vérifier la version minimale de TLS requise pour un compte de stockage spécifique à l’aide d’Azure CLI, appelez la commande [az resource show](/cli/azure/resource#az-resource-show) et interrogez la propriété **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Vérifier la version minimale de TLS requise pour un ensemble de comptes de stockage

Pour vérifier la version minimale de TLS requise sur un ensemble de comptes de stockage avec des performances optimales, vous pouvez utiliser l’Explorateur Azure Resource Graph dans le portail Azure. Pour en savoir plus sur l’utilisation de l’Explorateur Resource Graph, consultez [Démarrage rapide : Exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph](/azure/governance/resource-graph/first-query-portal).

L’exécution de la requête suivante dans l’Explorateur Resource Graph retourne une liste de comptes de stockage et affiche la version de TLS minimale pour chaque compte :

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Tester la version de TLS minimale à partir d’un client

Pour vérifier que la version minimale de TLS requise pour un compte de stockage interdit les appels effectués avec une version antérieure, vous pouvez configurer un client afin qu’il utilise une version antérieure de TLS. Pour plus d’informations sur la configuration d’un client afin qu’il utilise une version spécifique de TLS, consultez [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md).

Quand un client accède à un compte de stockage à l’aide d’une version TLS qui ne correspond pas à la version de TLS minimale configurée pour le compte, le Stockage Azure retourne le code d’erreur 400 (demande incorrecte) et un message indiquant que la version de TLS utilisée n’est pas autorisée pour effectuer des demandes sur ce compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md)
- [Recommandations de sécurité pour Stockage Blob](../blobs/security-recommendations.md)
