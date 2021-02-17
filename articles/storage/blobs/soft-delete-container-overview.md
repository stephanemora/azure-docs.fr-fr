---
title: Suppression réversible pour les conteneurs (préversion)
titleSuffix: Azure Storage
description: La fonctionnalité de suppression réversible pour les conteneurs (préversion) protège vos données, ce qui vous permet de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses de celles-ci par une application ou un autre utilisateur du compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 0c15be86c282451440f9b81d57f17e835559b5ae
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979099"
---
# <a name="soft-delete-for-containers-preview"></a>Suppression réversible pour les conteneurs (préversion)

La suppression réversible pour les conteneurs (préversion) protège vos données contre les suppressions accidentelles ou malveillantes. Lorsque la suppression réversible de conteneur est activée pour un compte de stockage, tout conteneur supprimé et son contenu sont conservés dans Stockage Azure pour la période que vous spécifiez. Pendant la période de rétention, vous pouvez restaurer des conteneurs précédemment supprimés. La restauration d’un conteneur a pour effet de restaurer tous les blobs figurant dans celui-ci au moment de sa suppression.

Pour la protection de bout en bout de vos données de blobs, Microsoft recommande d’activer les fonctionnalités de protection des données suivantes :

- Suppression réversible de conteneur, pour restaurer un conteneur supprimé. Pour savoir comment activer la suppression réversible de conteneur, consultez [Activer et gérer la suppression réversible pour les conteneurs](soft-delete-container-enable.md).
- Gestion des versions des objets blob, pour gérer automatiquement les versions précédentes d’un objet blob. Lorsque le contrôle de version est activé, vous pouvez restaurer une version antérieure d’un objet blob pour récupérer vos données si celles-ci ont été modifiées ou supprimées par erreur. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md).
- Suppression réversible de blob, pour restaurer un blob ou une version supprimés. Pour savoir comment activer la suppression réversible de blob, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md).

> [!WARNING]
> La suppression d’un compte de stockage est irréversible. La suppression réversible n’a pas pour effet de protéger contre la suppression d’un compte de stockage, mais uniquement contre la suppression d’objets de données dans ce compte. Pour empêcher toute suppression d’un compte de stockage, configurez un verrou **CannotDelete** sur la ressource du compte de stockage. Pour plus d’informations sur le verrouillage des ressources Azure Resource Manager, consultez [Verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Fonctionnement de la suppression réversible de conteneur

Lorsque vous activez la suppression réversible de conteneur, vous pouvez spécifier une période de rétention pour les conteneurs supprimés qui est comprise entre 1 et 365 jours. La période de conservation par défaut est 7 jours. Pendant la période de rétention, vous pouvez récupérer un conteneur supprimé en appelant l’opération **Undelete Container**.

Lorsque vous restaurez un conteneur, cela a également pour effet de restaurer les blobs qu’il contient ainsi que leurs versions. Toutefois, vous ne pouvez utiliser une suppression réversible de conteneur que pour restaurer des blobs que si le conteneur proprement dit a été supprimé. Pour restaurer un blob supprimé quand son conteneur parent n’a pas été supprimé, vous devez utiliser la suppression réversible ou le contrôle de version de blob.

Le diagramme suivant montre comment restaurer un conteneur supprimé quand la suppression réversible de conteneur est activée :

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagramme montrant comment restaurer un conteneur supprimé de manière réversible":::

Lorsque vous restaurez un conteneur, vous pouvez le restaurer avec son nom d’origine si celui-ci n’a pas été réutilisé. Si le nom du conteneur d’origine a été utilisé, vous pouvez restaurer le conteneur avec un nouveau nom.

Une fois la période de rétention expirée, le conteneur est définitivement supprimé de Stockage Azure et ne peut pas être récupéré. La période de rétention commence au moment où le conteneur est supprimé. Vous pouvez modifier la période de rétention à tout moment, mais gardez à l’esprit qu’une période de rétention mise à jour s’applique uniquement aux conteneurs nouvellement supprimés. Les conteneurs supprimés avant la mise à jour seront définitivement supprimés conformément à la période de rétention qui était en vigueur au moment de leur suppression.

La désactivation de la suppression réversible de conteneur n’entraîne pas la suppression définitive des conteneurs qui ont été précédemment supprimés de manière réversible. Tous les conteneurs supprimés de manière réversible seront définitivement supprimés à l’expiration de la période de rétention qui était en vigueur au moment de leur suppression.

## <a name="about-the-preview"></a>À propos de la préversion

La suppression réversible de conteneur est disponible en préversion dans toutes les régions Azure.

> [!IMPORTANT]
> La préversion de la suppression réversible de conteneur est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles.

La version 2019-12-12 et les versions ultérieures de l’API REST de Stockage Azure prennent en charge la suppression réversible de conteneur.

### <a name="storage-account-support"></a>Prise en charge du compte de stockage

La suppression réversible de conteneur est disponible pour les types de comptes de stockage suivants :

- Comptes de stockage v1 et v2 universels
- Comptes de stockage d’objets blob de blocs
- Comptes de stockage d’objets blob

Les comptes de stockage avec espace de noms hiérarchique activé pour une utilisation avec Azure Data Lake Storage Gen2 sont également pris en charge.

### <a name="register-for-the-preview"></a>S’inscrire pour la préversion

Pour vous inscrire à la préversion de la suppression réversible de conteneur, utilisez PowerShell ou Azure CLI pour envoyer une demande d’inscription de la fonctionnalité avec votre abonnement. Une fois votre demande approuvée, vous pouvez activer la suppression réversible de conteneur sur tout compte de stockage v2 universel, compte de stockage de blobs ou compte de stockage d’objets blob de blocs Premium.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vous inscrire à l’aide de PowerShell, appelez la commande [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature).

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vous inscrire avec Azure CLI, appelez la commande [az feature register](/cli/azure/feature#az-feature-register).

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Vérifier l’état de votre inscription

Pour vérifier l’état de votre inscription, utilisez PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vérifier l’état de votre inscription à l’aide de PowerShell, appelez la commande [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature).

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier l’état de votre inscription à l’aide d’Azure CLI, appelez la commande [az feature](/cli/azure/feature#az-feature-show).

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Tarification et facturation

Aucuns frais supplémentaires ne sont facturés pour activer la suppression réversible de conteneur. Les données qui se trouvent dans des conteneurs supprimés de manière réversible sont facturées au même tarif que les données actives.

## <a name="next-steps"></a>Étapes suivantes

- [Configuration de la suppression réversible de conteneur](soft-delete-container-enable.md)
- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Contrôle de version des blobs](versioning-overview.md)
