---
title: Créer un compte de stockage pour Azure Data Lake Storage Gen2
description: Découvrez comment créer un compte de stockage à utiliser avec Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 04/27/2021
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 30be4b09c5fc85a272734468d30f11e939ded48b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125507"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Créer un compte de stockage à utiliser avec Data Lake Storage Gen2

Pour utiliser les fonctionnalités de Data Lake Storage Gen2, créez un compte de stockage disposant d’un espace de noms hiérarchique.

Pour obtenir des instructions pas à pas, consultez [Créer un compte de stockage](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json). 

Lorsque vous créez le compte, veillez à sélectionner les options décrites dans cet article.

## <a name="choose-a-storage-account-type"></a>Choisir un type de compte de stockage

Les fonctionnalités de Data Lake Storage sont prises en charge dans les types de comptes de stockage suivants :

- Usage général v2 Standard
- Objet blob de blocs Premium

Pour plus d’informations sur la façon de les choisir, consultez [Vue d’ensemble des comptes de stockage](../common/storage-account-overview.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

Vous pouvez choisir entre ces deux types de comptes dans l’onglet **Paramètres de base** de la page **Créer un compte de stockage** . 

Pour créer un compte v2 standard à usage général, sélectionnez **Standard**.

Pour créer un compte d’objet blob de blocs Premium, sélectionnez **Premium**. Ensuite, dans la liste déroulante **Type de compte Premium**, sélectionnez **Objets blob de blocs**. 

> [!div class="mx-imgBorder"]
> ![Option d’objet blob de blocs Premium](./media/create-data-lake-storage-account/premium-block-blob-option.png)

## <a name="enable-the-hierarchical-namespace"></a>Activer l’espace de noms hiérarchique

Déverrouillez les fonctionnalités Data Lake Storage en sélectionnant le paramètre **Espace de noms hiérarchique** sous l’onglet **Avancé** de la page **Créer un compte de stockage**. Vous devez activer ce paramètre lorsque vous créez le compte. Vous ne pourrez pas l’activer par la suite.

L’illustration suivante montre ce paramètre dans la page **Créer un compte de stockage**.

> [!div class="mx-imgBorder"]
> ![Paramètre d’espace de noms hiérarchique](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Si vous disposez d’un compte de stockage que vous souhaitez utiliser avec Data Lake Storage, et si le paramètre d’espace de noms hiérarchique est désactivé, vous devez migrer les données vers un nouveau compte de stockage pour lequel le paramètre est activé.

> [!NOTE]
> Vous ne pouvez pas activer simultanément la **protection des données** et un espace de noms hiérarchique.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du compte de stockage](../common/storage-account-overview.md)
- [Utilisation d’Azure Data Lake Storage Gen2 pour le Big Data](data-lake-storage-data-scenarios.md)
- [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)