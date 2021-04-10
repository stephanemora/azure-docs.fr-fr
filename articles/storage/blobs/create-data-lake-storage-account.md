---
title: Créer un compte de stockage pour Azure Data Lake Storage Gen2
description: Découvrez comment créer un compte de stockage à utiliser avec Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624335"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Créer un compte de stockage à utiliser avec Data Lake Storage Gen2

Pour utiliser les fonctionnalités de Data Lake Storage Gen2, créez un compte de stockage disposant d’un espace de noms hiérarchique.

## <a name="choose-a-storage-account-type"></a>Choisir un type de compte de stockage

Les fonctionnalités de Data Lake Storage sont prises en charge dans les types de comptes de stockage suivants :

- Usage général v2
- BlockBlobStorage

Pour plus d’informations sur la façon de les choisir, consultez [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Créer un compte de stockage avec un espace de noms hiérarchique

Créez un [compte v2 à usage général](../common/storage-account-create.md) ou un compte [BlockBlobStorage](storage-blob-create-account-block-blob.md) avec le paramètre **Espace de noms hiérarchique** activé.

Déverrouillez les fonctionnalités de Data Lake Storage lorsque vous créez le compte en activant le paramètre **Espace de noms hiérarchique** sous l’onglet **Avancé** de la page **Créer un compte de stockage**. Vous devez activer ce paramètre lorsque vous créez le compte. Vous ne pourrez pas l’activer par la suite.

L’illustration suivante montre ce paramètre dans la page **Créer un compte de stockage**.

> [!div class="mx-imgBorder"]
> ![Paramètre d’espace de noms hiérarchique](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Si vous disposez d’un compte de stockage que vous souhaitez utiliser avec Data Lake Storage, et si le paramètre d’espace de noms hiérarchique est désactivé, vous devez migrer les données vers un nouveau compte de stockage pour lequel le paramètre est activé.

> [!NOTE]
> Vous ne pouvez pas activer simultanément la **protection des données** et un **espace de noms hiérarchique**.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du compte de stockage](../common/storage-account-overview.md)
- [Utilisation d’Azure Data Lake Storage Gen2 pour le Big Data](data-lake-storage-data-scenarios.md)
- [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
