---
title: Stratégies de rétention limitée dans le temps pour les données d’objets blob immuables
titleSuffix: Azure Storage
description: Les stratégies de rétention limitée dans le temps stockent les données blob dans un état WORM (Écriture unique / Lecture multiple) pour un intervalle spécifié. Vous pouvez configurer une stratégie de rétention limitée dans le temps qui est étendue à une version blob (préversion) ou à un conteneur.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1022ebd9166e3e2e92a4c67cd434190c91dd09c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524856"
---
# <a name="time-based-retention-policies-for-immutable-blob-data"></a>Stratégies de rétention limitée dans le temps pour les données d’objets blob immuables

Une stratégie de rétention limitée dans le temps stocke les données blob dans un format WORM (Écriture unique / Lecture multiple) pour une période spécifiée. Quand une stratégie de rétention limitée dans le temps est définie, les clients peuvent créer et lire des objets blob, mais ils ne peuvent pas les modifier ou les supprimer. Après expiration de la période de conservation, les objets blob peuvent être effacés, mais pas remplacés.

Pour plus d’informations sur les stratégies d’immuabilité relatives au stockage d’objets blob, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md).

## <a name="retention-interval-for-a-time-based-policy"></a>Période de conservation pour une stratégie limitée dans le temps

La période de conservation minimale pour une stratégie de rétention limitée dans le temps est d’un jour et la valeur maximale est de 146 000 jours (400 ans).

Lorsque vous configurez une stratégie de rétention limitée dans le temps, les objets blob affectés resteront à l’état immuable pendant la période de conservation *effective*. La période de conservation effective pour les objets blob est égale à la différence entre l’heure de création de l’objet blob et la période de conservation spécifiée par l’utilisateur. Dans la mesure où la période de conservation d’une stratégie peut être étendue, le stockage immuable utilise la valeur la plus récente de la période de conservation spécifiée par l’utilisateur pour calculer la durée de conservation effective.

Par exemple, supposons qu’un utilisateur crée une stratégie de rétention limitée dans le temps avec une période de conservation de cinq ans. Un objet blob existant dans ce conteneur, _testblob1_, a été créé il y a un an. Autrement dit, la période de conservation effective de _testblob1_ est de quatre ans. Quand un nouvel objet blob, _testblob2_, est chargé dans le conteneur, la période de conservation effective de _testblob2_ est de cinq ans à partir du moment où il est créé.

## <a name="locked-versus-unlocked-policies"></a>Stratégies verrouillées et déverrouillées

Lorsque vous configurez pour la première fois une stratégie de rétention limitée dans le temps, la stratégie est déverrouillée à des fins de test. Une fois les tests terminés, vous pouvez verrouiller la stratégie pour qu’elle soit entièrement conforme à la norme SEC 17A-4 (f) et à toute autre conformité réglementaire.

Les stratégies verrouillées et déverrouillées protègent contre les suppressions et les remplacements. Toutefois, vous pouvez modifier une stratégie déverrouillée en réduisant ou en étendant la période de conservation. Vous pouvez également supprimer une stratégie déverrouillée.

Vous ne pouvez pas supprimer une stratégie de rétention limitée dans le temps verrouillée. Vous pouvez étendre la période de conservation, mais vous ne pouvez pas la diminuer. Un maximum de cinq augmentations de la période de conservation effective est autorisé sur la durée de vie d’une stratégie verrouillée définie au niveau du conteneur. Pour une stratégie configurée pour une version d’objet BLOB, il n’existe pas de limite au nombre d’augmentations à la période effective.

> [!IMPORTANT]
> Une stratégie de rétention limitée dans le temps doit être verrouillée pour que l’objet blob ait un état immuable conforme (protégé contre l’écriture et la suppression) conformément entre autres à la réglementation SEC 17a-4(f). Microsoft recommande de verrouiller la stratégie au bout d’un délai raisonnable, en général inférieur à 24 heures. Tant que l’état déverrouillé protège l’immuabilité, nous recommandons de ne pas utiliser l’état déverrouillé pour d’autres raisons que des évaluations de fonctionnalités à court terme.

## <a name="time-based-retention-policy-scope"></a>Étendue de la stratégie de rétention limitée dans le temps

Une stratégie de rétention limitée dans le temps peut être configurée sur l’une des étendues suivantes :

- Stratégie de niveau de version (préversion) : une stratégie de rétention limitée dans le temps peut être configurée pour s’appliquer à une version d’objet blob pour la gestion granulaire des données sensibles. Vous pouvez appliquer la stratégie à une version individuelle ou configurer une stratégie par défaut pour un conteneur qui s’applique par défaut à tous les objets blob chargés dans ce conteneur.
- Stratégie au niveau du conteneur : une stratégie de rétention limitée dans le temps qui est configurée au niveau du conteneur s’applique à tous les objets de ce conteneur. Les objets individuels ne peuvent pas être configurés avec leurs propres stratégies d’immuabilité.

Les journaux d’audit sont disponibles sur le conteneur pour les stratégies de rétention au niveau de la version et au niveau du conteneur. Les journaux d’audit ne sont pas disponibles pour une stratégie dont l’étendue correspond à une version d’objet blob.

### <a name="version-level-policy-scope-preview"></a>Étendue de stratégie au niveau de la version (préversion)

Pour configurer des stratégies de rétention au niveau de la version, vous devez d’abord activer l’immuabilité au niveau de la version sur le conteneur parent. L’immuabilité au niveau de la version ne peut pas être désactivée une fois qu’elle est activée, bien que les stratégies déverrouillées puissent être supprimées. Pour plus d’informations, consultez [Activer la prise en charge de l’immuabilité au niveau de la version sur un conteneur](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Vous pouvez activer la prise en charge de l’immuabilité au niveau de la version au moment où vous créez un conteneur. Les conteneurs existants peuvent également prendre en charge l’immuabilité au niveau de la version, mais doivent d’abord subir un processus de migration. Ce processus peut prendre un certain temps et n’est pas réversible. Pour plus d’informations sur la migration d’un conteneur pour prendre en charge l’immuabilité au niveau de la version, consultez [Migrer un conteneur existant pour prendre en charge l’immuabilité au niveau de la version](immutable-policy-configure-version-scope.md#migrate-an-existing-container-to-support-version-level-immutability).

Concernant les stratégies de rétention limitée dans le temps au niveau de la version, le [Contrôle de version des objets blob](versioning-overview.md) doit être activé pour le compte de stockage. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md). N’oubliez pas que l’activation du contrôle de version peut avoir un impact sur la facturation. Pour plus d’informations, consultez la section **Tarification et facturation** dans [Contrôle de version des objets blob](versioning-overview.md#pricing-and-billing).

Une fois le contrôle de version activé, quand un objet blob est chargé pour la première fois, cette version de l’objet blob est la version actuelle. Chaque fois que l’objet blob est remplacé, une nouvelle version est créée, qui stocke l’état précédent de l’objet blob. Lorsque vous supprimez la version actuelle d’un objet blob, cette version actuelle devient une version antérieure et est conservée jusqu’à ce qu’elle soit explicitement supprimée. Une version d’objet blob antérieure possède la stratégie de rétention limitée dans le temps qui était en vigueur lorsque la version actuelle est devenue une version antérieure.

Si une stratégie par défaut est en vigueur pour le conteneur, quand une opération de remplacement crée une version antérieure, la nouvelle version actuelle hérite de la stratégie par défaut pour le conteneur.

Une seule stratégie de rétention limitée dans le temps peut être configurée pour chaque version. Une version peut également avoir une conservation légale configurée. Pour plus d’informations sur les configurations de stratégie d’immuabilité prises en charge en fonction de l’étendue, consultez l’[Etendue de la stratégie d’immuabilité](immutable-storage-overview.md#immutability-policy-scope).

Pour savoir comment configurer des stratégies de rétention au niveau de la version, consultez [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md).

> [!IMPORTANT]
> Les stratégies de rétention limitées dans le temps au niveau de la version sont actuellement en version **PRÉVERSION**. Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Cela peut prendre jusqu’à 30 secondes après l’activation de l’immuabilité au niveau de la version avant de pouvoir configurer des stratégies de rétention limitées dans le temps au niveau de la version.

#### <a name="configure-a-policy-on-the-current-version"></a>Configurer une stratégie sur la version actuelle

Une fois que vous avez activé la prise en charge de l’immuabilité au niveau de la version pour un conteneur, vous avez la possibilité de configurer une stratégie de rétention limitée dans le temps par défaut pour le conteneur. Quand vous configurez une stratégie de rétention limitée dans le temps par défaut pour le conteneur, puis que vous téléchargez un objet blob, celui-ci hérite de cette stratégie par défaut. Vous pouvez également choisir de remplacer la stratégie par défaut pour n’importe quel objet blob lors du chargement en configurant une stratégie personnalisée pour cet objet blob.

Si la stratégie de rétention limitée dans le temps par défaut pour le conteneur est déverrouillée, alors la version actuelle d’un objet blob qui hérite de la stratégie par défaut aura également une stratégie déverrouillée. Après le chargement d’un objet blob individuel, vous pouvez raccourcir ou étendre la période de conservation de la stratégie sur la version actuelle de l’objet blob, ou supprimer la version actuelle. Vous pouvez également verrouiller la stratégie pour la version actuelle, même si la stratégie par défaut sur le conteneur reste déverrouillée.

Si la stratégie de rétention limitée dans le temps par défaut pour le conteneur est verrouillée, alors la version actuelle d’un objet blob qui hérite de la stratégie par défaut aura également une stratégie verrouillée. Toutefois, si vous remplacez la stratégie par défaut lorsque vous téléchargez un objet blob en définissant une stratégie uniquement pour cet objet blob, alors la stratégie de cet objet blob restera déverrouillée jusqu’à ce que vous la verrouillez explicitement. Lorsque la stratégie sur la version actuelle est verrouillée, vous pouvez prolonger la période de conservation, mais vous ne pouvez pas supprimer la stratégie ou raccourcir la période de conservation.

Si aucune stratégie par défaut n’est configurée pour un conteneur, vous pouvez télécharger un objet blob à l’aide d’une stratégie personnalisée ou sans stratégie.

Si la stratégie par défaut d’un conteneur est modifiée, les stratégies sur les objets de ce conteneur restent inchangées, même si ces stratégies ont été héritées de la stratégie par défaut.

Le tableau suivant présente les différentes options disponibles pour définir une stratégie de rétention limitée dans le temps sur un objet blob lors du chargement :

| État de la stratégie par défaut sur le conteneur | Télécharger un objet blob avec la stratégie par défaut | Télécharger un objet blob avec une stratégie personnalisée | Télécharger un objet blob sans stratégie |
|--|--|--|--|
| Stratégie par défaut sur le conteneur (déverrouillée) | L’objet blob est téléchargé avec la stratégie déverrouillée par défaut | L’objet blob est téléchargé avec la stratégie déverrouillée personnalisée | L’objet blob est téléchargé sans aucune stratégie |
| Stratégie par défaut sur le conteneur (verrouillée) | L’objet blob est téléchargé avec la stratégie verrouillée par défaut | L’objet blob est téléchargé avec la stratégie déverrouillée personnalisée | L’objet blob est téléchargé sans aucune stratégie |
| Aucune stratégie par défaut sur le conteneur | N/A | L’objet blob est téléchargé avec la stratégie déverrouillée personnalisée | L’objet blob est téléchargé sans aucune stratégie |

#### <a name="configure-a-policy-on-a-previous-version"></a>Configurer une stratégie sur une version précédente

Lorsque le contrôle de version est activé, une opération d’écriture ou de suppression sur un objet blob crée une nouvelle version antérieure de cet objet blob qui enregistre l’état de l’objet blob avant l’opération. Par défaut, une version antérieure possède la stratégie de rétention limitée dans le temps qui était en vigueur pour la version actuelle, le cas échéant, lorsque la version actuelle est devenue une version antérieure. La nouvelle version actuelle hérite de la stratégie sur le conteneur, le cas échéant.

Si la stratégie héritée par une version précédente est déverrouillée, la période de conservation peut être réduite ou prolongée, ou la stratégie peut être supprimée. La stratégie sur une version précédente peut également être verrouillée pour cette version, même si la stratégie sur la version actuelle est déverrouillée.

Si la stratégie héritée par une version précédente est verrouillée, la période de conservation peut être prolongée. La stratégie ne peut pas être supprimée et la période de conservation ne peut pas être réduite.

Si aucune stratégie n’est configurée sur la version actuelle, alors la version précédente n’hérite d’aucune stratégie. Vous pouvez configurer une stratégie personnalisée pour la version.  

Si la stratégie sur une version actuelle est modifiée, les stratégies sur les versions précédentes existantes restent inchangées, même si la stratégie a été héritée d’une version actuelle.

### <a name="container-level-policy-scope"></a>Étendue de la stratégie au niveau du conteneur

Une stratégie de rétention limitée dans le temps au niveau du conteneur s’applique à tous les objets d’un conteneur, qu’ils soient nouveaux ou existants. Pour un compte avec un espace de noms hiérarchique, une stratégie au niveau du conteneur s’applique également à tous les répertoires du conteneur.

Lorsqu’une stratégie de rétention limitée dans le temps est appliquée sur un conteneur, tous les objets blob existants prennent l’état WORM immuable en moins de 30 secondes. Tous les nouveaux objets blob chargés dans ce conteneur protégé par une stratégie passent également à cet état immuable. Une fois que tous les objets blob sont à l’état immuable, aucune opération de remplacement ou de suppression n’est autorisée dans le conteneur immuable. Dans le cas d’un compte avec un espace de noms hiérarchique, les objets blob ne peuvent pas être renommés ou déplacés vers un autre répertoire.

Les limites suivantes s’appliquent aux stratégies de rétention au niveau du conteneur :

- Pour un compte de stockage, le nombre maximal de conteneurs avec stratégies immuables limitées dans le temps verrouillées est de 10 000.
- Pour un conteneur, le nombre maximal de modifications pour étendre la période de conservation pour une stratégie limitée dans le temps verrouillée est de cinq.
- Pour un conteneur, un maximum de sept journaux d’audit de stratégie de rétention limitée dans le temps est conservé pour une stratégie verrouillée.

Pour savoir comment configurer une stratégie de rétention limitée dans le temps sur un conteneur, consultez [configurer des stratégies d’immuabilité pour les conteneurs](immutable-policy-configure-container-scope.md).

## <a name="allow-protected-append-blobs-writes"></a>Autoriser les écritures protégées d’objets blob d’ajout

Les objets blob d’ajout sont constitués de blocs de données et sont optimisés pour les opérations d’ajout de données nécessaires dans les scénarios d’audit et de journalisation. Du fait de la conception des objets blob d’ajout, les nouveaux blocs ne peuvent être ajoutés qu’à la fin des objets blob. Indépendamment de l’immuabilité, la modification ou la suppression de blocs existants dans un objet blob d’ajout ne sont absolument pas autorisées. Pour en savoir plus sur les objets blob d’ajout, consultez [À propos des objets blob d’ajout](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Seules les stratégies de rétention limitées dans le temps possèdent le paramètre de propriété **AllowProtectedAppendWrites**, lequel autorise l’écriture de nouveaux blocs dans un objet blob d’ajout tout en conservant la protection et la conformité de l’immuabilité. Si ce paramètre est activé, vous êtes autorisé à créer un objet blob d’ajout directement dans le conteneur protégé par une stratégie et à continuer à ajouter de nouveaux blocs de données à la fin des objets blob d’ajout existants via l’opération Append Block. Seuls de nouveaux blocs peuvent être ajoutés et les blocs existants ne peuvent pas être modifiés ou supprimés. La protection de l’immuabilité de la durée de rétention continue de s’appliquer, ce qui empêche la suppression de l’objet blob d’ajout tant que la période de rétention effective n’est pas passée. L’activation de ce paramètre n’affecte pas le comportement d’immuabilité des objets blob de blocs ou de pages.

Sachant que ce paramètre fait partie d’une stratégie de rétention limitée dans le temps, les objets blob d’ajout restent à l’état immuable pendant la période de rétention *effective*. Comme il est possible d’ajouter de nouvelles données au-delà de la création initiale de l’objet blob d’ajout, il existe une légère différence quant à la façon dont la durée de rétention est déterminée. La rétention effective est égale à la différence entre le moment où l’objet blob d’ajout est modifié pour la dernier fois et l’intervalle de rétention spécifié par l’utilisateur. De la même manière, quand la période de conservation est allongée, le stockage immuable utilise la valeur la plus récente de la période de conservation spécifiée par l’utilisateur pour calculer la durée de rétention effective.

Par exemple, supposez qu’un utilisateur crée une stratégie de rétention limitée dans le temps avec la propriété **AllowProtectedAppendWrites** activée et une période de conservation de 90 jours. Un objet blob d’ajout, _logblob1_, est créé aujourd’hui dans le conteneur et de nouveaux journaux continuent d’y être ajoutés les 10 jours suivants. Ainsi, la période de conservation effective de _logblob1_ est de 100 jours à partir d’aujourd’hui (dernier ajout il y a + 90 jours).

Les stratégies de rétention limitées dans le temps déverrouillées permettent d’activer et de désactiver le paramètre de propriété **AllowProtectedAppendWrites** à tout moment. Une fois que la stratégie de rétention limitée dans le temps est verrouillée, le paramètre de propriété **AllowProtectedAppendWrites** ne peut pas être modifié.

## <a name="audit-logging"></a>Journalisation d’audit

Chaque conteneur avec une stratégie de rétention limitée dans le temps activée fournit un journal d’audit de stratégie. Le journal d’audit contient jusqu’à sept commandes de rétention limitées dans le temps pour les stratégies de rétention limitées dans le temps verrouillées. Les entrées de journal incluent l’ID d’utilisateur, le type de commande, les horodatages et la période de conservation. Ce journal d’audit est conservé pendant toute la durée de vie de la stratégie, conformément aux instructions réglementaires SEC 17a-4(f).

Le [Journal d’activité Azure](../../azure-monitor/essentials/platform-logs-overview.md) fournit un journal plus complet de l’ensemble des activités du service de gestion. Les [Journaux de ressources Azure](../../azure-monitor/essentials/platform-logs-overview.md) conservent des informations sur les opérations de données. Il est de la responsabilité de l’utilisateur de stocker ces journaux de manière persistante, car ceux-ci peuvent être nécessaires à des fins réglementaires ou autres.

Les modifications apportées aux stratégies de rétention limitées dans le temps au niveau de la version ne sont pas auditées.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la protection des données](data-protection-overview.md)
- [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md)
- [Conservation légale des données d’objets blob immuables](immutable-legal-hold-overview.md)
- [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md)
- [Configurer des stratégies d’immuabilité pour les conteneurs](immutable-policy-configure-container-scope.md)
