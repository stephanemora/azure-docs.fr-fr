---
title: Conservation légale des données d’objets blob immuables
titleSuffix: Azure Storage
description: Une conservation légale stocke les données blob dans un format WORM (Write-Once, Read-Many) jusqu’à ce qu’elles soient explicitement effacées. Utilisez une conservation légale lorsque la période pendant laquelle les données doivent être conservées à l’état WORM est inconnue.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 39108c5c5fd823e313ce616fbb4f6d6135e7d81e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645756"
---
# <a name="legal-holds-for-immutable-blob-data"></a>Conservation légale des données d’objets blob immuables

Une conservation légale est une stratégie d’immuabilité temporaire qui peut être appliquée à des fins d’investigation légale ou de protection générale. Une conservation légale stocke les données blob dans un format WORM (Write-Once, Read-Many) jusqu’à ce qu’elles soient explicitement effacées. Lorsqu’une conservation légale est définie, des objets blob peuvent être créés et lus, mais ils ne peuvent pas être modifiés ni supprimés. Utilisez une conservation légale lorsque la période pendant laquelle les données doivent être conservées à l’état WORM est inconnue.

Pour plus d’informations sur les stratégies de stockage immuable, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md).

## <a name="legal-hold-scope"></a>Étendue de conservation légale

Une stratégie de conservation légale peut être configurée sur l’une des étendues suivantes :

- Stratégie au niveau de la version (préversion) : une conservation légale peut être configurée sur un niveau de version de l’objet blob individuel pour la gestion granulaire des données sensibles.
- Stratégie au niveau du conteneur : une conservation légale configurée au niveau du conteneur s’applique à tous les objets blob de ce conteneur. Les objets individuels ne peuvent pas être configurés avec leurs propres stratégies d’immuabilité.

### <a name="version-level-policy-scope-preview"></a>Étendue de stratégie au niveau de la version (préversion)

Pour configurer une conservation légale sur une version d’objet blob, vous devez d’abord activer l’immuabilité au niveau de la version sur le conteneur parent. L’immuabilité au niveau de la version ne peut pas être désactivée une fois qu’elle est activée. Pour plus d’informations, consultez [Activer la prise en charge de l’immuabilité au niveau de la version sur un conteneur](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container).

Une fois que l’immuabilité au niveau de la version est activée pour un conteneur, une conservation légale ne peut plus être définie au niveau du conteneur. Les dépassements légaux doivent être appliqués aux versions individuelles des objets blob. Une conservation légale peut être configurée pour la version actuelle ou une version antérieure d’un objet blob.

Concernant les stratégies de conservation légale au niveau de la version, le contrôle de version des objets blob doit être activé pour le compte de stockage. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md). N’oubliez pas que l’activation du contrôle de version peut avoir un impact sur la facturation. Pour plus d’informations, consultez la section **Tarification et facturation** dans [Contrôle de version des objets blob](versioning-overview.md#pricing-and-billing).

Pour en savoir plus sur l’activation d’une conservation légale au niveau de la version, consultez [Configurer ou effacer une conservation légale](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold).

### <a name="container-level-scope"></a>Étendue au niveau du conteneur

Lorsque vous configurez une conservation légale pour un conteneur, celle-ci s’applique à tous les objets du conteneur. Lorsque la conservation légale est désactivée, les clients peuvent à nouveau écrire et supprimer des objets dans le conteneur sauf si une stratégie de rétention limitée dans le temps est également en vigueur pour le conteneur.

Lorsqu’une conservation légale est appliquée sur un conteneur, tous les objets blob existants prennent l’état WORM immuable en moins de 30 secondes. Tous les nouveaux objets blob chargés dans ce conteneur protégé par une stratégie ont également cet état. Une fois que tous les objets blob sont à l’état immuable, aucune opération de remplacement ou de suppression n’est autorisée dans le conteneur immuable. Dans le cas d’un compte avec un espace de noms hiérarchique, les objets blob ne peuvent pas être renommés ou déplacés vers un autre répertoire.

Pour savoir comment configurer une conservation légale avec l’étendue au niveau du conteneur, consultez [configurer ou effacer un conservation légale](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold).

#### <a name="legal-hold-tags"></a>Étiquettes de conservation légale

Une conservation légale au niveau du conteneur doit être associée à une ou plusieurs balises alphanumériques définies par l’utilisateur qui servent de chaînes d’identificateurs. Par exemple, une balise peut inclure un ID de cas ou un nom d’événement.

#### <a name="audit-logging"></a>Journalisation d’audit

Chaque conteneur avec une conservation légale en vigueur fournit un journal d’audit de stratégie. Le journal comporte l’identifiant utilisateur, le type de commande, les timestamps et les balises d’archivage juridique. Ce journal d’audit est conservé pendant toute la durée de vie de la stratégie, conformément aux instructions réglementaires SEC 17a-4(f).

Le [Journal d’activité Azure](../../azure-monitor/essentials/platform-logs-overview.md) fournit un journal plus complet de l’ensemble des activités du service de gestion. Les [Journaux de ressources Azure](../../azure-monitor/essentials/platform-logs-overview.md) conservent des informations sur les opérations de données. Il est de la responsabilité de l’utilisateur de stocker ces journaux de manière persistante, car ceux-ci peuvent être nécessaires à des fins réglementaires ou autres.

#### <a name="limits"></a>Limites

Les limites suivantes s’appliquent aux conservations légales au niveau du conteneur :

- Pour un compte de stockage, le nombre maximal de conteneurs avec le paramètre de conservation légale est de 10 000.
- Le nombre maximal par conteneur de balises d’archivage juridique est de dix.
- La longueur minimale d’une balise de conservation légale est de trois caractères alphanumériques. La longueur maximale est de 23 caractères alphanumériques.
- Pour un conteneur, dix journaux d’activité de stratégie de rétention légale au maximum sont conservés pour la durée de la stratégie.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la protection des données](data-protection-overview.md)
- [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md)
- [Stratégies de rétention limitées dans le temps pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md)
- [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md)
- [Configurer des stratégies d’immuabilité pour les conteneurs](immutable-policy-configure-container-scope.md)
