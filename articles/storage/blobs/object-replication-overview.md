---
title: Vue d'ensemble de la réplication d'objets
titleSuffix: Azure Storage
description: La réplication d'objets copie de manière asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Utilisez la réplication d’objets pour réduire la latence sur les demandes de lecture, pour renforcer l’efficacité des charges de travail de calcul, pour optimiser la distribution des données et pour réduire les coûts.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ee76c1692049d5b25e85b6780fbcf78f7ebfdd2f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987058"
---
# <a name="object-replication-for-block-blobs"></a>Réplication d'objets blob de blocs

La réplication d'objets copie de manière asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Voici quelques scénarios pris en charge par la réplication d’objets :

- **Minimisation de la latence.** La réplication d’objets peut réduire la latence des demandes de lecture en permettant aux clients d’utiliser des données provenant d’une région plus proche en termes de proximité physique.
- **Augmentation de l’efficacité des charges de travail de calcul.** Avec la réplication d’objets, les charges de travail de calcul peuvent traiter les mêmes ensembles d’objets blob de blocs dans différentes régions.
- **Optimisation de la distribution des données.** Vous pouvez traiter ou analyser des données incluses dans un emplacement unique, puis répliquer uniquement les résultats dans d’autres régions.
- **Optimisation des coûts.** Une fois vos données répliquées, vous pouvez réduire les coûts en les déplaçant vers le niveau archive à l’aide des stratégies de gestion du cycle de vie.

Le diagramme suivant montre comment la réplication d’objets réplique les objets blob de blocs d’un compte de stockage source dans une région vers des comptes de destination dans deux régions différentes.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramme montrant le fonctionnement de la réplication d’objets":::

Pour plus d'informations sur la configuration de la réplication d'objets, consultez [Configurer la réplication d'objets](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Prérequis pour la réplication d’objets

La réplication d'objets exige que les fonctionnalités de stockage Azure suivantes soient également activées :

- [Flux de modification](storage-blob-change-feed.md) : doit être activé sur le compte source. Pour plus d'informations sur l'activation du flux de modifications, consultez [Activer et désactiver le flux de modifications](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Contrôle de version des objets blob](versioning-overview.md) : doit être activé sur les comptes source et de destination. Pour plus d'informations sur l'activation du contrôle de version des objets blob, consultez [Activer et gérer le contrôle de version des objets blob](versioning-enable.md).

L’activation de ces fonctionnalités peut entraîner des coûts supplémentaires. Pour plus d’informations, consultez la [page des tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="object-replication-policies-and-rules"></a>Stratégies et règles de réplication d’objets

Lorsque vous configurez la réplication d’objets, vous créez une stratégie de réplication qui spécifie le compte de stockage source et le compte de destination. Une stratégie de réplication comprend une ou plusieurs règles qui spécifient un conteneur source et un conteneur de destination et indiquent quels objets blob de blocs du conteneur source seront répliqués.

Une fois que vous avez configuré la réplication d’objets, Stockage Azure vérifie régulièrement le flux de modification du compte source et réplique de manière asynchrone toutes les opérations d’écriture ou de suppression dans le compte de destination. La latence de la réplication dépend de la taille de l’objet blob de blocs en cours de réplication.

> [!IMPORTANT]
> Étant donné que les données d’objets blob de blocs sont répliquées de façon asynchrone, le compte source et le compte de destination ne sont pas immédiatement synchronisés. Il n’existe actuellement aucun contrat de niveau de service (SLA) sur le temps nécessaire à la réplication des données vers le compte de destination.

### <a name="replication-policies"></a>Stratégies de réplication

Lorsque vous configurez la réplication d’objets, une stratégie de réplication est créée à la fois sur le compte source et le compte de destination par le biais du fournisseur de ressources de Stockage Azure. La stratégie de réplication est identifiée par un ID de stratégie. La stratégie sur les comptes source et de destination doit avoir le même ID de stratégie pour que la réplication ait lieu.

Un compte de stockage peut servir de compte source pour un maximum de deux comptes de destination. Les comptes source et de destination peuvent se trouver dans la même région ou dans des régions différentes. Ils peuvent également être associés à des abonnements et à des locataires Azure Active Directory (Azure AD) différents. Une seule stratégie de réplication peut être créée pour chaque paire compte source/compte de destination.

### <a name="replication-rules"></a>Règles de réplication

Les règles de réplication spécifient comment Stockage Azure va répliquer les objets blob d’un conteneur source vers un conteneur de destination. Vous pouvez spécifier jusqu’à 10 règles de réplication pour chaque stratégie de réplication. Chaque règle de réplication définit un seul conteneur source et de destination, et chaque conteneur ne peut être utilisé que dans une seule règle.

Quand vous créez une règle de réplication, par défaut, seuls les nouveaux objets blob de blocs ajoutés par la suite au conteneur source sont copiés. Vous pouvez spécifier que les objets blob de blocs, qu'ils soient nouveaux et existants, soient copiés, ou bien vous pouvez définir une étendue de copie personnalisée qui copie les objets blob de blocs créés à partir d'un moment donné.

Vous pouvez aussi spécifier un ou plusieurs filtres dans le cadre d’une règle de réplication pour filtrer les objets blob de blocs par préfixe. Lorsque vous spécifiez un préfixe, seuls les objets blob correspondant à ce préfixe dans le conteneur source sont copiés dans le conteneur de destination.

Les conteneurs source et de destination doivent tous les deux exister pour que vous puissiez les spécifier dans une règle. Une fois que vous avez créé la stratégie de réplication, le conteneur de destination bascule en lecture seule. Toute tentative d’écriture dans le conteneur de destination échoue avec le code d’erreur 409 (Conflit). En revanche, vous pouvez appeler l’opération [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) sur un objet blob dans le conteneur de destination pour le déplacer vers le niveau d’archive. Pour plus d’informations sur le niveau d’archive, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Facturation

La réplication d'objets entraîne des coûts supplémentaires pour les transactions de lecture et d'écriture sur les comptes source et de destination, ainsi que des frais de sortie pour la réplication des données du compte source vers le compte de destination, et des frais de lecture pour le traitement des flux de modification.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la réplication d’objets](object-replication-configure.md)
- [Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md)
- [Activer et gérer le contrôle de version des objets blob](versioning-enable.md)
