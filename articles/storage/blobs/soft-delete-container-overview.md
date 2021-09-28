---
title: Suppression réversible pour les conteneurs
titleSuffix: Azure Storage
description: La fonctionnalité de suppression réversible pour les conteneurs protège vos données en vous permettant de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuse de celles-ci par une application ou un autre utilisateur du compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 51aa58bf3f3c0d672d90b70301f84306e09e06e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585701"
---
# <a name="soft-delete-for-containers"></a>Suppression réversible pour les conteneurs

La suppression réversible des conteneurs vous protège en cas de suppression accidentelle de vos données, car une fois supprimées, celles-ci sont conservées dans le système pendant un laps de temps spécifié. Pendant la période de conservation, vous pouvez restaurer un conteneur supprimé de manière réversible ainsi que son contenu, tel qu’il était au moment de sa suppression. Une fois la période de rétention terminée, le conteneur et son contenu sont définitivement supprimés.

## <a name="recommended-data-protection-configuration"></a>Configuration recommandée de la protection des données

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour une protection optimale de vos données blob, Microsoft recommande d’activer toutes les fonctionnalités de protection des données suivantes :

- Suppression réversible de conteneur, pour restaurer un conteneur supprimé. Pour savoir comment activer la suppression réversible de conteneur, consultez [Activer et gérer la suppression réversible pour les conteneurs](soft-delete-container-enable.md).
- Gestion des versions des objets blob, pour gérer automatiquement les versions précédentes d’un objet blob. Lorsque le contrôle de version est activé, vous pouvez restaurer une version antérieure d’un objet blob pour récupérer vos données si celles-ci ont été modifiées ou supprimées par erreur. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md).
- Suppression réversible de blob, pour restaurer un blob, un instantané ou une version supprimés. Pour savoir comment activer la suppression réversible de blob, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md).

Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

## <a name="how-container-soft-delete-works"></a>Fonctionnement de la suppression réversible de conteneur

Lorsque vous activez la suppression réversible de conteneur, vous pouvez spécifier une période de rétention pour les conteneurs supprimés qui est comprise entre 1 et 365 jours. La période de conservation par défaut est 7 jours. Pendant la période de rétention, vous pouvez récupérer un conteneur supprimé en appelant l’opération **Restore Container**.

Lorsque vous restaurez un conteneur, cela a également pour effet de restaurer les objet blob qu’il contient ainsi que leurs versions et instantanés. Toutefois, vous ne pouvez utiliser une suppression réversible de conteneur que pour restaurer des blobs que si le conteneur proprement dit a été supprimé. Pour restaurer un blob supprimé quand son conteneur parent n’a pas été supprimé, vous devez utiliser la suppression réversible ou le contrôle de version de blob.

> [!WARNING]
> Une suppression réversible de conteneur ne permet de restaurer que des conteneurs entiers et ce qu’ils contenaient au moment de la suppression. Une suppression réversible de conteneur ne permet pas de restaurer un blob supprimé au sein d’un conteneur. Microsoft recommande également d’activer la suppression réversible des objets blob et la gestion des versions d’objets blob pour protéger les objets blob individuels dans un conteneur.
>
> Lorsque vous restaurez un conteneur, vous devez le restaurer sous son nom d’origine. Si le nom d’origine a été utilisé pour créer un nouveau conteneur, vous ne pourrez pas restaurer le conteneur qui a été supprimé de manière réversible.

Le diagramme suivant montre comment restaurer un conteneur supprimé quand la suppression réversible de conteneur est activée :

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagramme montrant comment restaurer un conteneur supprimé de manière réversible":::

Une fois la période de rétention expirée, le conteneur est définitivement supprimé de Stockage Azure et ne peut pas être récupéré. La période de rétention commence au moment où le conteneur est supprimé. Vous pouvez modifier la période de rétention à tout moment, mais gardez à l’esprit qu’une période de rétention mise à jour s’applique uniquement aux conteneurs nouvellement supprimés. Les conteneurs supprimés avant la mise à jour seront définitivement supprimés conformément à la période de rétention qui était en vigueur au moment de leur suppression.

La désactivation de la suppression réversible de conteneur n’entraîne pas la suppression définitive des conteneurs qui ont été précédemment supprimés de manière réversible. Tous les conteneurs supprimés de manière réversible seront définitivement supprimés à l’expiration de la période de rétention qui était en vigueur au moment de leur suppression.

La suppression réversible de conteneur est disponible pour les types de comptes de stockage suivants :

- Comptes de stockage v1 et v2 universels
- Comptes de stockage d’objets blob de blocs
- Comptes de stockage d’objets blob

Les comptes de stockage avec espace de noms hiérarchique activé pour une utilisation avec Azure Data Lake Storage Gen2 sont également pris en charge.

La version 2019-12-12 ou les versions ultérieures de l’API REST Stockage Azure prennent en charge la suppression réversible de conteneur.

> [!IMPORTANT]
> La suppression réversible d’un conteneur n’a pas pour effet de protéger contre la suppression d’un compte de stockage, mais uniquement contre la suppression de conteneurs dans ce compte. Pour empêcher toute suppression d’un compte de stockage, configurez un verrou sur la ressource du compte de stockage. Pour plus d’informations sur le verrouillage des ressources Azure Resource Manager, consultez [Verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/management/lock-resources.md).

## <a name="feature-support"></a>Prise en charge des fonctionnalités

Ce tableau montre comment cette fonctionnalité est prise en charge dans votre compte ainsi que l’impact sur la prise en charge lorsque vous activez certaines fonctionnalités.

| Type de compte de stockage                | Stockage Blob (prise en charge par défaut)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Usage général v2 Standard | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)              | ![Non](../media/icons/no-icon.png) |
| Objets blob de blocs Premium          | ![Oui](../media/icons/yes-icon.png) |![Oui](../media/icons/yes-icon.png)              | ![Non](../media/icons/no-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 et le protocole NFS (Network File System) 3.0 requièrent tous deux un compte de stockage avec un espace de noms hiérarchique activé.

## <a name="pricing-and-billing"></a>Tarification et facturation

Aucuns frais supplémentaires ne sont facturés pour activer la suppression réversible de conteneur. Les données qui se trouvent dans des conteneurs supprimés de manière réversible sont facturées au même tarif que les données actives.

## <a name="next-steps"></a>Étapes suivantes

- [Configuration de la suppression réversible de conteneur](soft-delete-container-enable.md)
- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Contrôle de version des blobs](versioning-overview.md)
