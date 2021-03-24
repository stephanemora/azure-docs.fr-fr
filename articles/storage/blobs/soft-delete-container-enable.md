---
title: Activer et gérer la suppression réversible pour les conteneurs (préversion)
titleSuffix: Azure Storage
description: Activez la suppression réversible de conteneur (préversion) afin de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216341"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Activer et gérer la suppression réversible pour les conteneurs (préversion)

La suppression réversible de conteneur (préversion) protège vos données contre les modifications ou suppressions accidentelles ou erronées. Lorsque la suppression réversible de conteneur est activée pour un compte de stockage, un conteneur et son contenu peuvent être récupérés après suppression pendant la période de rétention que vous spécifiez.

S’il existe une possibilité de modification ou de suppression accidentelles de vos données par une application ou un autre utilisateur du compte de stockage, Microsoft vous recommande d’activer la suppression réversible de conteneur. Cet article explique comment activer la suppression réversible pour les conteneurs. Pour plus d’informations sur la suppression réversible de conteneur, notamment sur la façon de s’inscrire à la préversion, consultez [Suppression réversible pour les conteneurs (préversion)](soft-delete-container-overview.md).

Pour la protection des données de bout en bout, Microsoft vous recommande également d’activer la suppression réversible pour les blobs et le contrôle de version des blobs. Pour savoir comment activer la suppression réversible pour les blobs, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md). Pour savoir comment activer le contrôle de version des blobs, consultez [Contrôle de version des blobs](versioning-overview.md).

> [!IMPORTANT]
>
> La suppression réversible de conteneur est actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure disponibles en version bêta, en préversion ou qui ne sont pas encore en phase de disponibilité générale.

## <a name="enable-container-soft-delete"></a>Activer la suppression réversible de conteneur

Vous pouvez activer ou désactiver la suppression réversible de conteneur (préversion) pour le compte de stockage à tout moment à l’aide du portail Azure ou d’un modèle Resource Manager.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour activer la suppression réversible de conteneur pour votre compte de stockage à l’aide du portail Azure, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre compte de stockage.
1. Localisez les paramètres **Protection des données** sous **Service Blob**.
1. Définissez la propriété **Suppression réversible de conteneur** sur *Activé*.
1. Dans **Stratégies de rétention**, spécifiez la durée pendant laquelle les conteneurs supprimés de manière réversible sont conservés par Stockage Azure.
1. Enregistrez vos modifications.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Capture d’écran représentant l’activation de la suppression réversible de conteneur dans le portail Azure":::

# <a name="template"></a>[Modèle](#tab/template)

Pour activer la suppression réversible de conteneur avec un modèle Resource Manager, créez un modèle qui définit la propriété **containerDeleteRetentionPolicy**. Les étapes suivantes montrent comment créer un modèle dans le Portail Azure.

1. Dans le Portail Azure, choisissez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
1. Choisissez **Template deployment**, **Créer**, puis **Créer votre propre modèle dans l’éditeur**.
1. Dans l’éditeur de modèle, collez le code JSON suivant. Remplacez la valeur d’espace réservé `<account-name>` par le nom de votre compte de stockage.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Spécifiez la période de rétention. La valeur par défaut est 7.
1. Enregistrez le modèle.
1. Spécifiez le groupe de ressources du compte, puis choisissez le bouton **Vérifier + créer** pour déployer le modèle et activer la suppression réversible de conteneur.

## <a name="view-soft-deleted-containers"></a>Afficher les conteneurs supprimés de manière réversible

Lorsque la suppression réversible est activée, vous pouvez afficher les conteneurs supprimés de manière réversible dans le portail Azure. Les conteneurs supprimés de manière réversible sont visibles pendant la période de rétention spécifiée. Après l’expiration de la période de rétention, un conteneur supprimé de manière réversible est définitivement supprimé et n’est plus visible.

Pour afficher les conteneurs supprimés de manière réversible dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure et affichez la liste de vos conteneurs.
1. Activez le bouton bascule Afficher les conteneurs supprimés pour inclure les conteneurs supprimés dans la liste.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Capture d’écran montrant comment afficher les conteneurs supprimés de manière réversible dans le portail Azure":::

## <a name="restore-a-soft-deleted-container"></a>Restaurer un conteneur supprimé de manière réversible

Vous pouvez restaurer un conteneur supprimé de manière réversible et son contenu pendant la période de rétention. Pour restaurer un conteneur supprimé de manière réversible dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure et affichez la liste de vos conteneurs.
1. Affichez le menu contextuel du conteneur que vous souhaitez restaurer, puis choisissez **Annuler la suppression** dans le menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Capture d’écran montrant comment restaurer un conteneur supprimé de manière réversible dans le portail Azure":::

## <a name="next-steps"></a>Étapes suivantes

- [Suppression réversible pour les conteneurs (préversion)](soft-delete-container-overview.md)
- [Suppression réversible pour les objets blob](soft-delete-blob-overview.md)
- [Contrôle de version des blobs](versioning-overview.md)
