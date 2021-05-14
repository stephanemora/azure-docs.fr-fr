---
title: Appliquer une version minimale requise du protocole TLS (Transport Layer Security) pour des demandes entrantes
titleSuffix: Azure Storage
description: Configurez un compte de stockage afin d’exiger une version minimale du protocole TLS (Transport Layer Security) pour les clients effectuant des demandes sur le stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/29/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 218575453a8d204c59a25e145581b8934ef5790a
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317832"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Appliquer une version minimale requise du protocole TLS (Transport Layer Security) pour des demandes adressées à un compte de stockage

La communication entre une application cliente et un compte de stockage Azure est chiffrée à l’aide du protocole TLS (Transport Layer Security). Le protocole TLS est un protocole de chiffrement standard qui garantit la confidentialité et l’intégrité des données entre les clients et les services via Internet. Pour plus d’informations sur le protocole TLS , consultez [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Le stockage Azure prend en charge trois versions du protocole TLS : 1.0, 1.1 et 1.2. Le service Stockage Azure utilise le protocole TLS 1.2 sur les points de terminaison HTTP publics, mais les protocoles TLS 1.0 et TLS 1.1 sont toujours pris en charge à des fins de compatibilité descendante.

Par défaut, les comptes de stockage Azure permettent aux clients d’envoyer et de recevoir des données avec la version la plus ancienne de TLS, TLS 1.0 et les versions ultérieures. Pour appliquer des mesures de sécurité plus strictes, vous pouvez configurer votre compte de stockage afin d’exiger que les clients envoient et reçoivent des données avec une version plus récente du protocole TLS. Si un compte de stockage nécessite une version minimale du protocole TLS, les demandes effectuées avec une version antérieure échouent.

Cet article explique comment utiliser une infrastructure DRAG (détection, correction, audit, gouvernance) pour gérer en continu le protocole TLS sécurisé pour vos comptes de stockage.

Pour plus d’informations sur la façon de spécifier une version particulière de TLS lors de l’envoi d’une demande à partir d’une application cliente, consultez [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Détecter la version TLS utilisée par les applications clientes

Quand vous appliquez une version TLS minimale pour votre compte de stockage, vous risquez de rejeter des demandes de clients qui envoient des données avec une version antérieure de TLS. Pour que vous compreniez dans quelle mesure la configuration de la version minimale de TLS peut affecter les applications clientes, Microsoft vous recommande d’activer la journalisation pour votre compte de stockage Azure et d’analyser les journaux après un intervalle de temps afin de détecter les versions de TLS utilisées par les applications clientes.

Pour journaliser les demandes dans votre compte de stockage Azure et déterminer la version TLS utilisée par le client, vous pouvez utiliser la journalisation du stockage Azure dans Azure Monitor (préversion). Pour plus d’informations, consultez [Superviser le stockage Azure](../blobs/monitor-blob-storage.md).

La journalisation du stockage Azure dans Azure Monitor prend en charge l’utilisation de requêtes de journal pour analyser les données des journaux. Pour interroger les journaux, vous pouvez utiliser un espace de travail Azure Log Analytics. Pour en savoir plus sur les requêtes de journal, consultez [Tutoriel : Bien démarrer avec les requêtes Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

Pour journaliser des données de stockage Azure avec Azure Monitor et les analyser avec Azure Log Analytics, vous devez d’abord créer un paramètre de diagnostic qui indique les types de demandes et les services de stockage pour lesquels vous souhaitez journaliser les données. Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Cette préversion active les journaux pour les blobs (Azure Data Lake Storage Gen2 inclus), les fichiers, les files d’attente et les tables. Pour créer un paramètre de diagnostic dans le portail Azure, suivez ces étapes :

1. Créez un espace de travail Log Analytics dans l’abonnement qui contient votre compte de stockage Azure. Une fois que vous avez configuré la journalisation pour votre compte de stockage, les journaux sont disponibles dans l’espace de travail Log Analytics. Pour plus d’informations, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../../azure-monitor/logs/quick-create-workspace.md).
1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la section Supervision, Sélectionnez **Paramètres de diagnostic (préversion)** .
1. Sélectionnez le service de stockage Azure pour lequel vous souhaitez journaliser les demandes. Par exemple, choisissez **Blob** pour journaliser les demandes dans le stockage Blob.
1. Sélectionnez **Ajouter le paramètre de diagnostic**.
1. Fournissez un nom pour le paramètre de diagnostic.
1. Sous **Détails de la catégorie**, dans la section **Journal**, choisissez les types de demandes à journaliser. Vous pouvez journaliser les demandes de lecture, d’écriture et de suppression. Par exemple, si vous choisissez **StorageRead** et **StorageWrite**, les demandes de lecture et d’écriture sont journalisées sur le service sélectionné.
1. Sous **Détails de la destination**, sélectionnez **Envoyer à Log Analytics**. Sélectionnez votre abonnement et l’espace de travail Log Analytics que vous avez créé, comme illustré dans l’image suivante.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Capture d’écran montrant comment créer un paramètre de diagnostic pour la journalisation des demandes":::

Une fois le paramètre de diagnostic créé, les demandes adressées au compte de stockage sont journalisées conformément à ce paramètre. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter les journaux et les métriques des ressources dans Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Pour obtenir des informations de référence sur les champs disponibles dans les journaux de stockage Azure dans Azure Monitor, consultez [Journaux de ressource (préversion)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Interroger les demandes journalisées en fonction de la version de TLS

Les journaux de stockage Azure dans Azure Monitor incluent la version de TLS utilisée pour envoyer une demande à un compte de stockage. Utilisez la propriété **TlsVersion** pour vérifier la version de TLS d’une demande journalisée.

Pour déterminer le nombre de demandes effectuées sur le Stockage Blob avec différentes versions du protocole TLS au cours des sept derniers jours, ouvrez votre espace de travail Log Analytics. Collez ensuite la requête suivante dans une nouvelle requête de journal et exécutez-la. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Les résultats indiquent le nombre de demandes effectuées avec chaque version de TLS :

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Capture d’écran montrant les résultats de la requête Log Analytics pour retourner la version de TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Interroger les demandes journalisées en fonction de l’adresse IP de l’appelant et de l’en-tête de l’agent utilisateur

Les journaux de stockage Azure dans Azure Monitor incluent également l’adresse IP de l’appelant et l’en-tête de l’agent utilisateur pour vous aider à déterminer les applications clientes ayant accédé au compte de stockage. Vous pouvez analyser ces valeurs pour décider si les applications clientes doivent être mises à jour afin d’utiliser une version plus récente de TLS ou si la demande d’un client non envoyée avec la version minimale de TLS peut faire l’objet d’un rejet.

Pour déterminer les clients qui ont effectué des demandes avec une version de TLS antérieure à TLS 1.2 au cours des sept derniers jours, collez la requête suivante dans une nouvelle requête de journal et exécutez-la. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Corriger les risques de sécurité avec une version minimale de TLS

Lorsque vous êtes certain que le trafic provenant de clients utilisant des versions antérieures de TLS est minimal, ou qu’il est acceptable que des requêtes effectuées avec une version antérieure de TLS échouent, vous pouvez commencer à appliquer d’une version minimale de TLS sur votre compte de stockage. Exiger que les clients utilisent une version minimale de TLS pour effectuer des demandes sur un compte de stockage fait partie d’une stratégie visant à de réduire les risques de sécurité pour vos données.

> [!IMPORTANT]
> Si vous utilisez un service qui se connecte au Stockage Azure, vérifiez qu’il utilise la bonne version du protocole TLS pour envoyer des demandes au Stockage Azure avant de définir la version minimale requise pour un compte de stockage.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Configurer la version minimale de TLS pour un compte de stockage

Pour configurer la version minimale de TLS pour un compte de stockage, définissez la version **minimumTlsVersion** pour le compte. Cette propriété est disponible pour tous les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager. Pour plus d’informations sur le modèle de déploiement Azure Resource Manager, consultez [Vue d’ensemble du compte de stockage](storage-account-overview.md).

La propriété **MinimumTlsVersion** n’est pas définie par défaut et ne retourne pas de valeur tant que vous ne la définissez pas explicitement.  Si la valeur de la propriété est **null**, le compte de stockage autorise les demandes envoyées avec TLS version 1.0 ou ultérieure.

# <a name="portal"></a>[Portail](#tab/portal)

Lorsque vous créez un compte de stockage avec le portail Azure, la version TLS minimale est définie sur 1.2 par défaut.

Pour configurer la version TLS minimale pour un compte de stockage existant avec le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Paramètres**, sélectionnez la **Configuration**.
1. Sous **Version TLS minimale**, utilisez la liste déroulante pour sélectionner la version minimale de TLS nécessaire pour accéder aux données de ce compte de stockage.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Capture d’écran montrant comment configurer la version minimale de TLS sur le portail Azure." lightbox="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour configurer la version minimale de TLS pour un compte de stockage avec PowerShell, installez [Azure PowerShell version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) ou une version ultérieure. Ensuite, configurez la propriété **MinimumTLSVersion** pour un compte de stockage nouveau ou existant. Les valeurs valides pour **minimumTlsVersion** sont `TLS1_0`, `TLS1_1` et `TLS1_2`.

L’exemple suivant crée un compte de stockage et définit la propriété **MinimumTLSVersion** sur TLS 1.1, puis met à jour le compte et définit la propriété **MinimumTLSVersion** sur TLS 1.2. L’exemple récupère également la valeur de la propriété dans chaque cas. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer la version minimale de TLS pour un compte de stockage avec Azure CLI, installez Azure CLI version 2.9.0 ou ultérieure. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli). Ensuite, configurez la propriété **minimumTlsVersion** pour un compte de stockage nouveau ou existant. Les valeurs valides pour **minimumTlsVersion** sont `TLS1_0`, `TLS1_1` et `TLS1_2`.

L’exemple suivant crée un compte de stockage et définit la propriété **minimumTLSVersion** sur TLS 1.1. Il met ensuite à jour le compte et définit la propriété **minimumTLSVersion** sur TLS 1.2. L’exemple récupère également la valeur de la propriété dans chaque cas. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Modèle](#tab/template)

Pour configurer la version minimale de TLS pour un compte de stockage avec un modèle, créez un modèle avec la propriété **MinimumTLSVersion** définie sur `TLS1_0`, `TLS1_1`ou `TLS1_2`. Les étapes suivantes montrent comment créer un modèle dans le Portail Azure.

1. Dans le Portail Azure, choisissez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
1. Choisissez **Déploiement de modèle (déployer avec des modèles personnalisés) [préversion]** , **Créer**, puis **Créer votre propre modèle dans l’éditeur**.
1. Dans l’éditeur de modèle, collez le code JSON suivant pour créer un nouveau compte et définissez la version minimale de TLS sur TLS 1.2. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Enregistrez le modèle.
1. Spécifiez un paramètre de groupe de ressources, puis choisissez le bouton **Vérifier + créer** pour déployer le modèle et créer un compte de stockage avec la propriété **MinimumTLSVersion** configurée.

---

> [!NOTE]
> Une fois la version minimale de TLS mise à jour pour le compte de stockage, la propagation de la modification peut prendre jusqu’à 30 secondes.

La configuration de la version minimale de TLS nécessite la version 2019-04-01 ou une version ultérieure du fournisseur de ressources Stockage Azure. Pour plus d’informations, consultez [API REST du fournisseur de ressources Stockage Azure](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Vérifier la version minimale requise de TLS pour plusieurs comptes

Pour vérifier la version minimale de TLS requise sur un ensemble de comptes de stockage avec des performances optimales, vous pouvez utiliser l’Explorateur Azure Resource Graph dans le portail Azure. Pour en savoir plus sur l’utilisation de l’Explorateur Resource Graph, consultez [Démarrage rapide : Exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph](../../governance/resource-graph/first-query-portal.md).

L’exécution de la requête suivante dans l’Explorateur Resource Graph retourne une liste de comptes de stockage et affiche la version de TLS minimale pour chaque compte :

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Tester la version de TLS minimale à partir d’un client

Pour vérifier que la version minimale de TLS requise pour un compte de stockage interdit les appels effectués avec une version antérieure, vous pouvez configurer un client afin qu’il utilise une version antérieure de TLS. Pour plus d’informations sur la configuration d’un client afin qu’il utilise une version spécifique de TLS, consultez [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md).

Quand un client accède à un compte de stockage à l’aide d’une version TLS qui ne correspond pas à la version de TLS minimale configurée pour le compte, le Stockage Azure retourne le code d’erreur 400 (demande incorrecte) et un message indiquant que la version de TLS utilisée n’est pas autorisée pour effectuer des demandes sur ce compte de stockage.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Utiliser Azure Policy pour auditer la conformité

Si vous avez un grand nombre de comptes de stockage, il se peut que vous deviez effectuer un audit pour vous assurer que tous les comptes sont configurés pour la version minimale de TLS que votre organisation exige. Pour auditer la conformité d’un ensemble de comptes de stockage, utilisez Azure Policy. Azure Policy est un service que vous pouvez utiliser pour créer, attribuer et gérer des stratégies qui appliquent des règles à des ressources Azure. Lorsque vous utilisez Azure Policy, les ressources restent conformes à vos normes d’entreprise et contrats de niveau de service. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Créer une stratégie avec un effet d’audit

Azure Policy prend en charge les effets qui déterminent ce qui se produit quand une règle de stratégie est évaluée par rapport à une ressource. L’effet d’audit crée un avertissement quand une ressource n’est pas conforme, mais n’arrête pas la demande. Pour plus d’informations, consultez [Comprendre les effets d’Azure Policy](../../governance/policy/concepts/effects.md).

Pour créer une stratégie avec un effet d’audit pour la version minimale de TLS avec le portail Azure, procédez comme suit :

1. Dans le Portail Azure, accédez au service Azure Policy.
1. Dans la section **Création**, sélectionnez **Définitions**.
1. Sélectionnez **Ajouter une définition de stratégie** pour créer une nouvelle définition de stratégie.
1. Pour le champ **emplacement de la définition**, sélectionnez le bouton **Autres** pour spécifier l’emplacement de la ressource de stratégie d’audit.
1. Spécifiez un nom pour la stratégie. Vous pouvez éventuellement spécifier une description et une catégorie.
1. Sous **Règle de stratégie**, ajoutez la définition de stratégie suivante à la section **policyrule**.

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
      }
    }
    ```

1. Enregistrez la stratégie.

### <a name="assign-the-policy"></a>Affecter la stratégie

Ensuite, attribuez la stratégie à une ressource. L’étendue de la stratégie correspond à cette ressource et à toutes les ressources qu’elle contient. Pour plus d’informations sur l’attribution de stratégie, consultez [Structure d’affectation d’Azure Policy](../../governance/policy/concepts/assignment-structure.md).

Pour attribuer la stratégie avec le portail Azure, procédez comme suit :

1. Dans le portail Azure, accédez au service Azure Policy.
1. Dans la section **Création**, sélectionnez **Attributions**.
1. Sélectionnez **Attribuer une stratégie** pour créer une attribution de stratégie.
1. Pour le champ **Étendue**, sélectionnez l’étendue de l’attribution de stratégie.
1. Pour le champ **Définition de stratégie**, sélectionnez le bouton **Autres**, puis la stratégie que vous avez définie dans la section précédente dans la liste.
1. Entrez un nom pour l’attribution de stratégie. La description est facultative.
1. Laissez l’option **Application de stratégie** définie sur *Activée*. Ce paramètre n’a aucun effet sur la stratégie d’audit.
1. Sélectionnez **Vérifier + créer** pour créer l’attribution.

### <a name="view-compliance-report"></a>Afficher le rapport de conformité

Une fois que vous avez attribué la stratégie, vous pouvez afficher le rapport de conformité. Le rapport de conformité d’une stratégie d’audit fournit des informations sur les comptes de stockage qui ne sont pas conformes à la stratégie. Pour plus d’informations, consultez [Obtenir les données de conformité de la stratégie](../../governance/policy/how-to/get-compliance-data.md).

La disponibilité du rapport de conformité peut prendre plusieurs minutes après la création de l’attribution de stratégie.

Pour afficher le rapport de conformité dans le Portail Azure, procédez comme suit :

1. Dans le portail Azure, accédez au service Azure Policy.
1. Sélectionnez **Conformité**.
1. Filtrez les résultats pour le nom de l’attribution de stratégie que vous avez créée à l’étape précédente. Le rapport indique le nombre de ressources qui ne sont pas conformes à la stratégie.
1. Vous pouvez explorer le rapport au niveau du détail pour obtenir des détails supplémentaires, notamment une liste des comptes de stockage qui ne sont pas conformes.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Capture d’écran montrant le rapport de conformité de la stratégie d’audit pour la version minimale de TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Utiliser Azure Policy pour appliquer la version minimale de TLS

Azure Policy prend en charge la gouvernance cloud en s’assurant que les ressources Azure respectent les exigences et les normes. Pour appliquer une exigence de version minimale de TLS aux comptes de stockage de votre organisation, vous pouvez créer une stratégie empêchant la création de nouveau compte de stockage, qui définit l’exigence minimale de TLS sur une version antérieure de TLS que celle dictée par la stratégie. Cette stratégie empêchera également toutes les modifications de configuration apportées à un compte existant si le paramètre la version minimale de TLS pour ce compte n’est pas conforme à la stratégie.

La stratégie d’application utilise l’effet de refus pour empêcher une demande qui aurait pour effet de créer ou de modifier un compte de stockage de sorte que la version minimale de TLS ne respecte plus les normes de votre organisation. Pour plus d’informations, consultez [Comprendre les effets d’Azure Policy](../../governance/policy/concepts/effects.md).

Pour créer une stratégie avec un effet de refus pour une version minimale de TLS inférieure à la version 1.2, suivez les étapes décrites dans [Utiliser Azure Policy pour auditer la conformité](#use-azure-policy-to-audit-for-compliance), mais fournissez le fichier JSON suivant dans la section **policyrule** de la définition de stratégie :

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

Une fois que vous avez créé la stratégie avec l’effet de refus et l’avez attribuée à une étendue, un utilisateur ne peut plus créer de compte de stockage avec une version minimale de TLS antérieure à la version 1.2. Un utilisateur ne peut pas non plus apporter des changements de configuration à un compte de stockage existant qui requiert actuellement une version minimale de TLS antérieure à la version 1.2. Toute tentative en ce sens entraîne une erreur. La version minimale TLS requise pour le compte de stockage doit être définie sur 1.2 pour poursuivre la création ou la configuration du compte.

L’image suivante montre l’erreur qui se produit si vous tentez de créer un compte de stockage avec la version minimale de TLS définie sur TLS 1.0 (valeur par défaut pour un nouveau compte) quand une stratégie avec effet de refus exige que la version minimale de TLS soit définie sur TLS 1.2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Capture d’écran montrant l’erreur qui se produit lors de la création d’un compte de stockage en violation de la stratégie":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Autorisations nécessaires pour exiger une version minimale du protocole TLS

Pour pouvoir définir la propriété **MinimumTlsVersion** du compte de stockage, un utilisateur doit disposer des autorisations nécessaires pour créer et gérer des comptes de stockage. Les rôles de contrôle d’accès en fonction du rôle Azure (Azure RBAC) qui fournissent ces autorisations comprennent l’action **Microsoft.Storage/storageAccounts/write** ou l’action ***Microsoft.Storage/storageAccounts/\*** . Parmi les rôles intégrés comportant cette action figurent :

- Le rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager
- Le rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) d’Azure Resource Manager
- Le rôle [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ces rôles ne donnent pas accès aux données d’un compte de stockage par le biais d’Azure Active Directory (Azure AD). Toutefois, ils incluent l’action **Microsoft.Storage/storageAccounts/listkeys/action**, qui accorde l’accès aux clés d’accès du compte. Avec cette autorisation, un utilisateur peut utiliser les clés d’accès du compte pour accéder à toutes les données d’un compte de stockage.

Les attributions de rôles doivent être définies au niveau du compte de stockage ou à un niveau supérieur pour permettre à un utilisateur d’exiger une version minimale du protocole TLS pour le compte de stockage. Pour plus d’informations sur l’étendue des rôles, consultez [Présentation de l’étendue pour Azure RBAC](../../role-based-access-control/scope-overview.md).

Veillez à limiter l’attribution de ces rôles aux seules personnes qui ont besoin de créer un compte de stockage ou de mettre à jour ses propriétés. Appliquez le principe des privilèges minimum pour que les utilisateurs disposent des autorisations nécessaires les plus faibles possibles pour accomplir leurs tâches. Pour plus d’informations sur la gestion de l’accès avec Azure RBAC, consultez [Meilleures pratiques pour Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Les rôles d’administrateur d’abonnement classique Administrateur de service et Co-administrateur incluent l’équivalent du rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager. Le rôle **Propriétaire** comprend toutes les actions. Par conséquent, un utilisateur disposant de l’un de ces rôles d’administration peut également créer et gérer des comptes de stockage. Pour plus d’informations, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Considérations relatives au réseau

Quand un client envoie une demande au compte de stockage, il établit une connexion avec le point de terminaison public du compte de stockage, puis la demande est traitée. Le paramètre de version TLS minimale est vérifié après l’établissement de la connexion. Si la demande utilise une version de TLS antérieure à celle spécifiée par le paramètre, la connexion continue, mais la demande finit par échouer. Pour plus d’informations sur les points de terminaison publics pour le stockage Azure, consultez [Syntaxe d’URI de ressource](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le protocole TLS (Transport Layer Security) pour une application cliente](transport-layer-security-configure-client-version.md)
- [Recommandations de sécurité pour Stockage Blob](../blobs/security-recommendations.md)
