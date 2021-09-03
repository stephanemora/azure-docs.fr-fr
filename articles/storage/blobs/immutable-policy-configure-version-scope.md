---
title: Configurer des stratégies d’immuabilité pour les versions d’objets BLOB (version préliminaire)
titleSuffix: Azure Storage
description: Découvrez comment configurer une stratégie d’immuabilité étendue à une version de blob (préversion). Les stratégies d’immuabilité offrent une prise en charge de WORM (Write Once, Read Many) pour le stockage d’objets blob en stockant les données dans un état non effaçable et non modifiable.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/19/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f8a1e460b471539daacc34f29c02b62678044abc
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122527998"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)

Le stockage immuable pour le Stockage Blob Azure permet aux utilisateurs de stocker des données critiques pour l’entreprise dans un état WORM (Write Once, Read Many). Dans un état WORM, les données ne peuvent pas être modifiées ou supprimées pour un intervalle spécifié par l’utilisateur. En configurant des stratégies d’immuabilité pour les données blob, vous pouvez protéger vos données contre les remplacements et les suppressions. Les stratégies d’immuabilité comprennent des stratégies de rétention limitées dans le temps et la conservation légale. Pour plus d’informations sur les stratégies de stockage immuable, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md).

Une stratégie d’immuabilité peut être étendue à une version d’objet blob individuelle (préversion) ou à un conteneur. Cet article explique comment configurer une stratégie d’immuabilité au niveau de la version. Pour savoir comment configurer des stratégies d’immuabilité au niveau du conteneur, consultez [Configurer des stratégies d’immuabilité pour les conteneurs](immutable-policy-configure-container-scope.md).

La configuration d’une stratégie d’immuabilité au niveau de la version est un processus en deux étapes :

1. Tout d’abord, activez la prise en charge de l’immuabilité au niveau de la version sur un conteneur nouveau ou existant. Consultez [Activer la prise en charge de l’immuabilité au niveau de la version sur un conteneur](#enable-support-for-version-level-immutability-on-a-container) pour plus de détails.
1. Ensuite, configurez une stratégie de rétention limitée dans le temps ou une conservation légale qui s’applique à une ou plusieurs versions d’objet blob dans ce conteneur.

> [!IMPORTANT]
> Les stratégies d’immuabilité au niveau de la version sont actuellement en **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Configuration requise

Pour configurer des stratégies de rétention limitées dans le temps au niveau de la version, le contrôle de version des objets blob doit être activé pour le compte de stockage. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md).

Pour plus d’informations sur les configurations de compte de stockage prises en charge pour les stratégies d’immuabilité au niveau de la version, consultez [Configurations de compte prises en charge](immutable-storage-overview.md#supported-account-configurations).

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>Activer la prise en charge de l’immuabilité au niveau de la version sur un conteneur

Avant de pouvoir appliquer une stratégie de rétention limitée dans le temps à une version d’objet blob, vous devez activer la prise en charge de l’immuabilité au niveau de la version. Les conteneurs nouveaux et existants peuvent être configurés pour prendre en charge l’immuabilité au niveau de la version. Toutefois, un conteneur existant doit subir un processus de migration afin d’activer la prise en charge.

N’oubliez pas que l’activation de la prise en charge de l’immuabilité au niveau de la version pour un conteneur ne rend pas les données de ce conteneur immuables. Vous devez également configurer soit une stratégie d’immuabilité par défaut pour le conteneur, soit une stratégie d’immuabilité sur une version d’objet blob spécifique.

### <a name="enable-version-level-immutability-for-a-new-container"></a>Activer l’immuabilité au niveau de la version pour un nouveau conteneur

Pour utiliser une stratégie d’immuabilité au niveau de la version, vous devez d’abord activer explicitement la prise en charge WORM au niveau de la version sur le conteneur. Vous pouvez activer la prise en charge de WORM au niveau de la version soit lorsque vous créez le conteneur, soit lorsque vous ajoutez une stratégie d’immuabilité de niveau version à un conteneur existant.

Pour créer un conteneur prenant en charge l’immuabilité au niveau de la version dans le Portail Azure, procédez comme suit :

1. Accédez à la page **Conteneurs** pour votre compte de stockage dans le Portail Azure, puis sélectionnez **Ajouter**.
1. Dans la boîte de dialogue **Nouveau conteneur**, indiquez un nom pour votre conteneur, puis développez la section **Avancé**.
1. Sélectionnez **Activer la prise en charge de l’immuabilité au niveau de la version** pour activer l’immuabilité au niveau de la version pour le conteneur.

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="Capture d’écran montrant comment créer un conteneur avec l’immuabilité activée au niveau de la version":::

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>Migrer un conteneur existant pour prendre en charge l’immuabilité au niveau de la version

Pour configurer des stratégies d’immuabilité au niveau de la version pour un conteneur existant, vous devez migrer le conteneur pour prendre en charge le stockage immuable au niveau de la version. La migration du conteneur peut prendre un certain temps et ne peut pas être inversée.

Un conteneur existant doit être migré qu’une stratégie de rétention limitée dans le temps au niveau du conteneur soit configurée ou non. Si le conteneur a une conservation légale au niveau du conteneur, il ne peut pas être migré tant que la conservation légale n’est pas supprimée.

Pour migrer un conteneur afin de prendre en charge le stockage immuable au niveau de la version dans le Portail Azure, procédez comme suit :

1. Accédez au conteneur souhaité.
1. Sélectionnez le bouton **Plus** à droite et sélectionnez **Stratégie d’accès**.
1. Sélectionnez **Ajouter une stratégie** sous **Stockage blob non modifiable**.
1. Dans le champ **Type de stratégie**, sélectionnez *Rétention limitée dans le temps*, puis spécifiez l’intervalle de rétention.
1. Sélectionnez **Activer l’immuabilité au niveau de la version**.
1. Sélectionnez **OK** pour commencer la migration.

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="Capture d’écran montrant comment migrer un conteneur existant pour prendre en charge l’immuabilité au niveau de la version":::

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>Configurer une stratégie de rétention limitée dans le temps sur un conteneur

Une fois qu’un conteneur est activé pour l’immuabilité au niveau de la version, vous pouvez spécifier une stratégie de rétention limitée dans le temps au niveau de la version par défaut pour le conteneur. Une fois que vous avez spécifié une stratégie par défaut pour un conteneur, cette stratégie s’applique par défaut à toutes les nouvelles versions d’objets blob créées dans le conteneur. Vous pouvez remplacer la stratégie par défaut pour toute version de l’objet blob dans le conteneur.

La stratégie par défaut n’est pas automatiquement appliquée aux versions d’objets blob qui existaient avant la configuration de la stratégie par défaut.

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>Configurer une stratégie de rétention limitée dans le temps par défaut sur un conteneur

Pour appliquer une stratégie d’immuabilité par défaut au niveau de la version à un conteneur dans le Portail Azure, procédez comme suit :

1. Dans le Portail Azure, accédez à la page **Conteneurs**, puis localisez le conteneur auquel vous souhaitez appliquer la stratégie.
1. Sélectionnez le bouton **Plus** à droite du nom de conteneur et choisissez **Stratégie d’accès**.
1. Sous la section **Stockage blob non modifiable** dans la boîte de dialogue **Stratégie d’accès**, sélectionnez **Ajouter une stratégie**.
1. Sélectionnez **Stratégie de rétention limitée dans le temps** et spécifiez l’intervalle de rétention.
1. Si vous le souhaitez, sélectionnez **Autoriser les ajouts protégés supplémentaires** pour permettre aux écritures d’ajouter des objets blob protégés par une stratégie d’immuabilité. Pour plus d’informations, consultez [Autoriser les écritures protégées de blobs d’ajout](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
1. Sélectionnez **OK** pour appliquer la stratégie par défaut au conteneur.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="Capture d’écran montrant comment configurer une stratégie de rétention par défaut au niveau de la version pour un conteneur":::

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>Déterminer l’étendue d’une stratégie de rétention sur un conteneur

Pour déterminer l’étendue d’une stratégie de rétention limitée dans le temps dans le Portail Azure, procédez comme suit :

1. Accédez au conteneur souhaité.
1. Sélectionnez le bouton **Plus** à droite et sélectionnez **Stratégie d’accès**.
1. Cherchez le champ **Étendue** sous **Stockage blob non modifiable**. Si le conteneur est configuré avec une stratégie de rétention par défaut au niveau de la version, l’étendue est définie sur *Version*, comme illustré dans l’image suivante :

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="Capture d’écran montrant la stratégie de rétention par défaut au niveau de la version configurée pour le conteneur":::

1. Si le conteneur est configuré avec une stratégie de rétention au niveau du conteneur, l’étendue est définie sur *Conteneur*, comme illustré dans l’image suivante :

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="Capture d’écran montrant la stratégie de rétention au niveau du conteneur configurée pour le conteneur":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>Configurer une stratégie de rétention limitée dans le temps sur une version existante

Les stratégies de rétention limitées dans le temps maintiennent les données blob dans un état WORM pendant un intervalle spécifié. Pour plus d’informations sur les stratégies de rétention limitées dans le temps, consultez [Stratégies de rétention limitée dans le temps pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md).

Vous avez trois options pour configurer une stratégie de rétention limitée dans le temps pour une version d’objet blob :

- Option 1 : vous pouvez configurer une stratégie par défaut étendue au conteneur et qui s’applique à tous les objets du conteneur par défaut. Les objets du conteneur hériteront de la stratégie par défaut, sauf si vous la substituez explicitement en configurant une stratégie sur une version d’objet blob individuelle. Pour plus d’informations, consultez [Configurer une stratégie de rétention limitée dans le temps par défaut sur un conteneur](#configure-a-default-time-based-retention-policy-on-a-container).
- Option 2 : vous pouvez configurer une stratégie sur la version actuelle de l’objet blob. Cette stratégie peut remplacer une stratégie par défaut configurée sur le conteneur si celle-ci existe et qu’elle est déverrouillée. Par défaut, toutes les versions précédentes créées après la configuration de la stratégie héritent de la stratégie sur la version actuelle de l’objet blob. Pour plus d’informations, consultez [Configurer une stratégie de rétention sur la version actuelle d’un objet blob](#configure-a-retention-policy-on-the-current-version-of-a-blob).
- Option 3 : vous pouvez configurer une stratégie sur une version antérieure d’un objet blob. Cette stratégie peut remplacer une stratégie par défaut configurée sur la version actuelle si celle-ci existe et qu’elle est déverrouillée. Pour plus d’informations, consultez [Configurer une stratégie de rétention sur une version antérieure d’un objet blob](#configure-a-retention-policy-on-a-previous-version-of-a-blob).

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>Configurer une stratégie de rétention sur la version actuelle d’un objet blob

Le portail Azure affiche la liste des objets blob lorsque vous accédez à un conteneur. Chaque objet blob affiché représente sa version actuelle. Pour plus d’informations sur la gestion de versions des objets blob, consultez [Gestion de versions des objets blob](versioning-overview.md).

Pour configurer une stratégie de rétention limitée dans le temps sur la version actuelle d’un objet blob, procédez comme suit :

1. Accédez au conteneur qui contient l’objet BLOB cible.
1. Sélectionnez le bouton **Plus** à droite du nom d’objet blob et choisissez **Stratégie d’accès**. Si une stratégie de rétention limitée dans le temps a déjà été configurée pour la version précédente, elle apparaît dans la boîte de dialogue **Stratégie d’accès**.
1. Sous la section **Versions d’objets blob non modifiables** dans la boîte de dialogue **Stratégie d’accès**, sélectionnez **Ajouter une stratégie**.
1. Sélectionnez **Stratégie de rétention limitée dans le temps** et spécifiez l’intervalle de rétention.
1. Sélectionnez **OK** pour appliquer la stratégie à la version actuelle de l’objet blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="Capture d’écran montrant comment configurer une stratégie de rétention pour la version actuelle d’un objet blob":::

Vous pouvez afficher les propriétés d’un objet blob pour voir si une stratégie est activée sur la version actuelle. Sélectionnez l’objet blob, puis accédez à l’onglet **Vue d’ensemble** et recherchez la propriété de la **stratégie d’immuabilité au niveau de la version**. Si une stratégie est activée, la propriété **Période de rétention** affiche la date et l’heure d’expiration de la stratégie. Gardez à l’esprit qu’une stratégie peut être configurée pour la version actuelle ou qu’elle peut être héritée du conteneur parent de l’objet blob si une stratégie par défaut est en vigueur.

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Capture d’écran montrant les propriétés de stratégie d’immuabilité sur la version d’objet blob dans le Portail Azure":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>Configurer une stratégie de rétention sur une version précédente d’un objet blob

Vous pouvez également configurer une stratégie de rétention limitée dans le temps sur une version antérieure d’un objet blob. Une version précédente est toujours immuable et ne peut donc pas être modifiée. Toutefois, une version précédente peut être supprimée. Une stratégie de rétention limitée dans le temps protège contre la suppression alors qu’elle est en vigueur.

Pour configurer une stratégie de rétention limitée dans le temps sur une version précédente d’un objet blob, procédez comme suit :

1. Accédez au conteneur qui contient l’objet BLOB cible.
1. Sélectionnez l’objet blob, puis accédez à l’onglet **Versions**.
1. Trouvez la version cible, sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**. Si une stratégie de rétention limitée dans le temps a déjà été configurée pour la version précédente, elle apparaît dans la boîte de dialogue **Stratégie d’accès**.
1. Sous la section **Versions d’objets blob non modifiables** dans la boîte de dialogue **Stratégie d’accès**, sélectionnez **Ajouter une stratégie**.
1. Sélectionnez **Stratégie de rétention limitée dans le temps** et spécifiez l’intervalle de rétention.
1. Sélectionnez **OK** pour appliquer la stratégie à la version actuelle de l’objet blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Capture d’écran montrant comment configurer une stratégie de rétention pour une version précédente de l’objet blob dans le Portail Azure":::

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>Configurer une stratégie de rétention limitée dans le temps lors du chargement d’un objet blob

Lorsque vous utilisez le portail Azure pour charger un objet blob dans un conteneur prenant en charge l’immuabilité au niveau de la version, vous disposez de plusieurs options pour configurer une stratégie de rétention limitée dans le temps pour le nouvel objet blob :

- Option 1 : si une stratégie de rétention par défaut est configurée pour le conteneur, vous pouvez charger l’objet blob avec la stratégie du conteneur. Cette option est sélectionnée par défaut lorsqu’il existe une stratégie de rétention sur le conteneur.
- Option 2 : si une stratégie de rétention par défaut est configurée pour le conteneur, vous pouvez choisir de remplacer la stratégie par défaut, soit en définissant une stratégie de rétention personnalisée pour le nouvel objet blob, soit en chargeant l’objet blob sans stratégie.
- Option 3 : si aucune stratégie par défaut n’est configurée pour le conteneur, vous pouvez charger l’objet blob avec une stratégie personnalisée ou sans stratégie.

Pour configurer une stratégie de rétention limitée dans le temps lorsque vous téléchargez un objet blob, procédez comme suit :

1. Accédez au conteneur souhaité, puis sélectionnez **Charger**.
1. Dans la boîte de dialogue **Charger blob**, développez la section **Avancé**.
1. Configurez la stratégie de rétention limitée dans le temps pour le nouvel objet blob dans le champ **Stratégie de rétention**. Si une stratégie par défaut est configurée pour le conteneur, cette stratégie est sélectionnée par défaut. Vous pouvez également spécifier une stratégie personnalisée pour l’objet blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Capture d’écran montrant les options de configuration de la stratégie de rétention pour le chargement d’objets blob dans le Portail Azure":::

## <a name="modify-an-unlocked-retention-policy"></a>Modifier une stratégie de rétention déverrouillée

Vous pouvez modifier une stratégie de rétention limitée dans le temps déverrouillée pour raccourcir ou allonger l’intervalle de rétention. Vous pouvez également supprimer une stratégie déverrouillée. La modification ou la suppression d’une stratégie de rétention limitée dans le temps et qui est déverrouillée pour une version d’objet blob n’affecte pas les stratégies en vigueur pour les autres versions. Si une stratégie de rétention limitée dans le temps par défaut est en vigueur pour le conteneur, la version de l’objet blob avec la stratégie modifiée ou supprimée n’héritera plus du conteneur.

Pour modifier une stratégie de rétention déverrouillée limitée dans le temps, procédez comme suit :

1. Recherchez la version cible, qui peut être la version actuelle ou une version antérieure d’un objet blob. Sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**.
1. Sous la section **Version d’objets blob immuables**, recherchez la stratégie actuellement déverrouillée. Sélectionnez le bouton **Plus**, puis sélectionnez **Modifier** dans le menu.

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Capture d’écran montrant comment modifier une stratégie de rétention limitée dans le temps au niveau de la version existante dans le Portail Azure":::

1. Indiquez la nouvelle date et l’heure de l’expiration de la stratégie.

Pour supprimer une stratégie déverrouillée, suivez les étapes 1 à 4, puis sélectionnez **Supprimer** dans le menu.

## <a name="lock-a-time-based-retention-policy"></a>Verrouiller une stratégie de rétention limitée dans le temps

Une fois que vous avez fini de tester une stratégie de rétention limitée dans le temps, vous pouvez verrouiller la stratégie. Une stratégie verrouillée est conforme à la norme SEC 17A-4 (f) et à toute autre conformité réglementaire. Vous pouvez augmenter l’intervalle de rétention pour une stratégie verrouillée jusqu’à cinq fois, mais vous ne pouvez pas raccourcir cet intervalle.

Une fois qu’une stratégie est verrouillée, vous ne pouvez pas la supprimer. Toutefois, vous pouvez supprimer l’objet blob après l’expiration de l’intervalle de rétention.

Pour verrouiller une stratégie, effectuez les étapes suivantes :

1. Recherchez la version cible, qui peut être la version actuelle ou une version antérieure d’un objet blob. Sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**.
1. Sous la section **Version d’objets blob immuables**, recherchez la stratégie actuellement déverrouillée. Sélectionnez le bouton **Plus**, puis sélectionnez **Verrouiller la stratégie** dans le menu.
1. Confirmez que vous souhaitez verrouiller la stratégie.

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Capture d’écran montrant comment verrouiller une stratégie de rétention limitée dans le temps dans le portail Azure":::

## <a name="configure-or-clear-a-legal-hold"></a>Configurer ou effacer une conservation légale

La conservation légale stocke des données immuables jusqu’à ce qu’elle soit explicitement désactivée. Pour en savoir plus sur les stratégies de conservation légale, consultez [Conservation légale pour les données d’objets blob immuables](immutable-legal-hold-overview.md).

Pour configurer une conservation légale sur une version d’objet blob, effectuez ces étapes :

1. Recherchez la version cible, qui peut être la version actuelle ou une version antérieure d’un objet blob. Sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**.
1. Sous la section **Version d’objets blob immuables**, sélectionnez **Ajouter une stratégie**.
1. Choisissez le type de stratégie **Conservation légale**, puis sélectionnez **OK** pour l’appliquer.

L’illustration suivante montre une version actuelle d’un objet blob avec une stratégie de rétention limitée dans le temps et une conservation légale configurée.

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="Capture d’écran montrant la conservation légale configurée pour la version de l’objet blob":::

Pour annuler la conservation légale, accédez à la boîte de dialogue **Stratégie d’accès**, sélectionnez le bouton **Plus** et choisissez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md)
- [Stratégies de rétention limitées dans le temps pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md)
- [Conservation légale des données d’objets blob immuables](immutable-legal-hold-overview.md)
