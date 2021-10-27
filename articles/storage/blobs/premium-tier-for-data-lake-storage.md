---
title: Niveau Premium pour Azure Data Lake Storage
description: Utilisez le niveau de performance Premium avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.openlocfilehash: e9cab4b65da62bddd47cdab97c6f586b07f379d4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044443"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Niveau Premium pour Azure Data Lake Storage

Azure Data Lake Storage Gen2 prend désormais en charge les [Comptes de stockage d’objets blob de blocs Premium](storage-blob-block-blob-premium.md). Les comptes de stockage d’objets blob de blocs Premium sont idéaux pour les applications d’analytique Big Data et les charges de travail qui nécessitent une latence faible et cohérente, avec un grand nombre de transactions. Les exemples de charges de travail incluent des charges de travail interactives, IoT, l’analytique streaming, l’intelligence artificielle et le machine learning. 

>[!TIP]
> Pour en savoir plus sur les avantages en termes de performances et de coûts liés à l’utilisation d’un compte de stockage d’objets blob de blocs Premium, ainsi que pour découvrir comment d’autres clients Data Lake Storage Gen2 utilisent ce type de compte, consultez [Compte de stockage d’objets blob de blocs Premium](storage-blob-block-blob-premium.md).

## <a name="getting-started-with-premium"></a>Bien démarrer avec Premium

Tout d’abord, vérifiez que vos fonctionnalités de Stockage Blob favorites sont compatibles avec les comptes de stockage d’objets blob de blocs Premium, puis créez le compte. 

>[!NOTE]
> Vous ne pouvez pas convertir un compte de stockage standard Usage général v2 existant en compte de stockage d’objets blob de blocs Premium. Pour migrer vers un compte de stockage d’objet blob de blocs Premium, vous devez créer un compte d’objets blob de blocs et migrer les données vers le nouveau compte. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Vérifier la compatibilité des fonctionnalités de Stockage Blob

Certaines fonctionnalités de Stockage Blob ne sont pas encore prises en charge ou elles disposent d’une prise en charge partielle dans des comptes de stockage d’objets blob de blocs Premium. Avant de choisir le niveau Premium, consultez l’article [Prise en charge des fonctionnalités de Stockage Blob dans les comptes de Stockage Azure](storage-feature-support-in-storage-accounts.md) pour déterminer si les fonctionnalités que vous avez l’intention d’utiliser sont entièrement prises en charge dans votre compte. La prise en charge des fonctionnalités est en constante évolution. Veillez donc à consulter régulièrement cet article pour découvrir les mises à jour.

### <a name="create-a-new-storage-account"></a>Créer un compte de stockage

Créez un compte de stockage Azure. Pour obtenir des instructions complètes, consultez [Créer un compte de stockage](../common/storage-account-create.md). 

Quand vous créez le compte, choisissez l’option de performance **Premium** et le type de compte **Objets blob de blocs**. 

> [!div class="mx-imgBorder"]
> ![Créer un compte de stockage d’objet blob de blocs](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

Pour déverrouiller les fonctionnalités Azure Data Lake Storage Gen2, activez le paramètre **Espace de noms hiérarchique** sous l’onglet **Avancé** de la page **Créer un compte de stockage**. 

L’illustration suivante montre ce paramètre dans la page **Créer un compte de stockage**.

> [!div class="mx-imgBorder"]
> ![Paramètre d’espace de noms hiérarchique](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>Étapes suivantes

Utilisez le niveau Premium pour Azure Data Lake Storage avec votre service d’analytique favori, comme Azure Databricks, Azure HDInsight et Azure Synapse Analytics. Consultez [Tutoriels sur l’utilisation des services Azure avec Azure Data Lake Storage Gen2](data-lake-storage-integrate-with-services-tutorials.md).
