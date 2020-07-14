---
title: Vue d’ensemble de la réplication d’objets (préversion)
titleSuffix: Azure Storage
description: La réplication d’objets (préversion) copie de façon asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Utilisez la réplication d’objets pour réduire la latence sur les demandes de lecture, pour renforcer l’efficacité des charges de travail de calcul, pour optimiser la distribution des données et pour réduire les coûts.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2d8d4c369cef8bf996628e8c89a424f04dcdbe71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888064"
---
# <a name="object-replication-for-block-blobs-preview"></a>Réplication d’objets pour des objets blob de blocs (préversion)

La réplication d’objets (préversion) copie de façon asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Voici quelques scénarios pris en charge par la réplication d’objets :

- **Minimisation de la latence.** La réplication d’objets peut réduire la latence des demandes de lecture en permettant aux clients d’utiliser des données provenant d’une région plus proche en termes de proximité physique.
- **Augmentation de l’efficacité des charges de travail de calcul.** Avec la réplication d’objets, les charges de travail de calcul peuvent traiter les mêmes ensembles d’objets blob de blocs dans différentes régions.
- **Optimisation de la distribution des données.** Vous pouvez traiter ou analyser des données incluses dans un emplacement unique, puis répliquer uniquement les résultats dans d’autres régions.
- **Optimisation des coûts.** Une fois vos données répliquées, vous pouvez réduire les coûts en les déplaçant vers le niveau archive à l’aide des stratégies de gestion du cycle de vie.

Le diagramme suivant montre comment la réplication d’objets réplique les objets blob de blocs d’un compte de stockage source dans une région vers des comptes de destination dans deux régions différentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramme montrant le fonctionnement de la réplication d’objets":::

Pour savoir comment configurer la réplication d’objets, consultez [Configurer la réplication d’objets (préversion)](object-replication-configure.md).

## <a name="object-replication-policies-and-rules"></a>Stratégies et règles de réplication d’objets

Lorsque vous configurez la réplication d’objets, vous créez une stratégie de réplication qui spécifie le compte de stockage source et le compte de destination. Une stratégie de réplication comprend une ou plusieurs règles qui spécifient un conteneur source et un conteneur de destination et indiquent quels objets blob de blocs du conteneur source seront répliqués.

Une fois que vous avez configuré la réplication d’objets, Stockage Azure vérifie régulièrement le flux de modification du compte source et réplique de manière asynchrone toutes les opérations d’écriture ou de suppression dans le compte de destination. La latence de la réplication dépend de la taille de l’objet blob de blocs en cours de réplication.

> [!IMPORTANT]
> Étant donné que les données d’objets blob de blocs sont répliquées de façon asynchrone, le compte source et le compte de destination ne sont pas immédiatement synchronisés. Il n’existe actuellement aucun contrat de niveau de service (SLA) sur le temps nécessaire à la réplication des données vers le compte de destination.

### <a name="replications-policies"></a>Stratégies de réplication

Lorsque vous configurez la réplication d’objets, une stratégie de réplication est créée à la fois sur le compte source et le compte de destination par le biais du fournisseur de ressources de Stockage Azure. La stratégie de réplication est identifiée par un ID de stratégie. La stratégie sur les comptes source et de destination doit avoir le même ID de stratégie pour que la réplication ait lieu.

Un compte de stockage peut servir de compte source pour un maximum de deux comptes de destination. Et un compte de destination peut ne pas avoir plus de deux comptes source. Les comptes source et de destination peuvent tous se trouver dans des régions différentes. Vous pouvez configurer des stratégies de réplication distinctes pour répliquer des données sur chacun des comptes de destination.

### <a name="replication-rules"></a>Règles de réplication

Les règles de réplication spécifient comment Stockage Azure va répliquer les objets blob d’un conteneur source vers un conteneur de destination. Vous pouvez spécifier jusqu’à 10 règles de réplication pour chaque stratégie de réplication. Chaque règle définit un seul conteneur source et de destination, et chaque conteneur source et de destination ne peut être utilisé que dans une seule règle.

Quand vous créez une règle de réplication, par défaut, seuls les nouveaux objets blob de blocs ajoutés par la suite au conteneur source sont copiés. Vous pouvez également spécifier que les objets blob de blocs, qu’ils soient nouveaux et existants, soient copiés, ou bien vous pouvez définir une étendue de copie personnalisée qui copie les objets blob de blocs créés à partir d’un moment donné.

Vous pouvez aussi spécifier un ou plusieurs filtres dans le cadre d’une règle de réplication pour filtrer les objets blob de blocs par préfixe. Lorsque vous spécifiez un préfixe, seuls les objets blob correspondant à ce préfixe dans le conteneur source sont copiés dans le conteneur de destination.

Les conteneurs source et de destination doivent tous les deux exister pour que vous puissiez les spécifier dans une règle. Une fois que vous avez créé la stratégie de réplication, le conteneur de destination bascule en lecture seule. Toute tentative d’écriture dans le conteneur de destination échoue avec le code d’erreur 409 (Conflit). En revanche, vous pouvez appeler l’opération [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) sur un objet blob dans le conteneur de destination pour le déplacer vers le niveau d’archive. Pour plus d’informations sur le niveau d’archive, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>À propos de la préversion

La réplication est prise en charge pour les comptes de stockage v2 universels uniquement. La réplication d’objets est disponible dans les régions suivantes en préversion :

- France Centre
- Est du Canada
- Centre du Canada

Les comptes source et de destination doivent se trouver dans l’une de ces régions pour pouvoir utiliser la réplication d’objets. Les comptes peuvent se trouver dans deux régions différentes.

Pendant la préversion, aucun coût supplémentaire n’est associé à la réplication des données entre les comptes de stockage.

> [!IMPORTANT]
> La préversion de la réplication d’objets est destiné uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles.

### <a name="prerequisites-for-object-replication"></a>Prérequis pour la réplication d’objets

La réplication d’objets nécessite que les fonctionnalités de stockage Azure suivantes soient activées : 
- [Flux de modification](storage-blob-change-feed.md)
- [Contrôle de version](versioning-overview.md)

Avant de configurer la réplication d’objets, activez ses prérequis. Le flux de modification doit être activé sur le compte source, et le versioning des objets blob doit être activé à la fois sur le compte source et le compte de destination. Pour plus d’informations sur l’activation de chacune de ces fonctionnalités, consultez ces articles :

- [Activer et désactiver le flux de modification](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Activer et gérer le versioning des objets blob](versioning-enable.md)

Veillez à vous inscrire pour les préversions du flux de modification et du versioning des objets blob avant de les activer.

L’activation de ces fonctionnalités peut entraîner des coûts supplémentaires. Pour plus d’informations, consultez la [page des tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>S’inscrire pour la préversion

Vous pouvez vous inscrire à la préversion de la réplication d’objets à l’aide de PowerShell ou Azure CLI. Si vous ne l’avez pas déjà fait, vérifiez que vous vous inscrivez également aux préversions du flux de modification et du versioning des objets blob.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vous inscrire à la préversion avec PowerShell, exécutez les commandes suivantes :

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vous inscrire à la préversion avec Azure CLI, exécutez les commandes suivantes :

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Vérifier l’état de l’inscription

Vous pouvez vérifier l’état de vos demandes d’inscription à l’aide de PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vérifier l’état de vos demandes d’inscription à l’aide de PowerShell, exécutez les commandes suivantes :

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier l’état de vos demandes d’inscription avec Azure CLI, exécutez les commandes suivantes :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Poser des questions ou envoyer des commentaires

Pour poser des questions sur la préversion de la réplication d’objets ou fournir des commentaires, contactez Microsoft à l’adresse AzureStorageFeedback@microsoft.com. Les idées et suggestions relatives au stockage Azure sont toujours les bienvenues sur le [forum dédié aux commentaires sur le stockage Azure](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la réplication d’objets (préversion)](object-replication-configure.md)
- [Prise en charge du flux de modification dans Stockage Blob Azure (préversion)](storage-blob-change-feed.md)
- [Activer et gérer le versioning des objets blob](versioning-enable.md)
