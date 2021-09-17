---
title: Comment publier des artefacts de package de configuration invité personnalisés
description: Découvrez comment publier un fichier de package de configuration invité sur le stockage Blob Azure et obtenir un jeton SAS pour un accès sécurisé.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773046"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>Comment publier des artefacts de package de configuration invité personnalisés

Avant de commencer, nous vous conseillons de lire la page de présentation de la [configuration invité](../concepts/guest-configuration.md).

Les packages .zip personnalisés de configuration invité doivent être stockés dans un emplacement accessible via HTTPS par les machines managées. Par exemple, des référentiels GitHub, un référentiel azure, un stockage azure ou un serveur web dans votre centre de donnés privé.

Les packages de configuration prenant en charge `Audit` et `AuditandSet` sont publiés de la même façon. Il n’est pas nécessaire d’effectuer une procédure spéciale pendant la publication en fonction du mode de package.

## <a name="publish-a-configuration-package"></a>Publier un package de configuration

L’emplacement privilégié pour stocker un package de configuration est Stockage Blob Azure.
Il n’existe aucune exigence particulière pour le compte de stockage, mais il est judicieux d’héberger le fichier dans une région proche de vos machines. Si vous préférez rendre le package public, vous pouvez inclure un [jeton SAS](../../../storage/common/storage-sas-overview.md) dans l’URL ou implémenter un [point de terminaison de service](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pour les machines dans un réseau privé.

Si vous n’avez pas de compte de stockage, utilisez l’exemple suivant pour en créer un.

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

La commande `Publish-GuestConfigurationPackage` charge le package de configuration dans Stockage Blob Azure et récupère un jeton SAS pour qu’il soit accessible en toute sécurité.

Paramètres de la cmdlet `Publish-GuestConfigurationPackage` :

- **Chemin d’accès** : emplacement du package à publier
- **ResourceGroupName** : nom du groupe de ressources dans lequel se trouve le compte de stockage
- **StorageAccountName** : nom du compte de stockage dans lequel le package doit être publié
- **StorageContainerName** (par défaut _guestconfiguration_) : nom du conteneur de stockage dans le compte de stockage
- **Force** : remplacer le package existant dans le compte de stockage du même nom

L’exemple suivant publie le package dans le conteneur de stockage nommé « guestconfiguration ».

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>Étapes suivantes

- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module `GuestConfiguration` afin de [créer une définition d’Azure Policy](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du Portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de la configuration invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
