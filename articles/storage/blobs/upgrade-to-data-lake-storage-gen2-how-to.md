---
title: Mettre à niveau le Stockage Blob Azure avec les fonctionnalités de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Montre comment utiliser des modèles de Gestionnaire des ressources pour effectuer une mise à niveau de Stockage Blob Azure vers Data Lake Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 025aa395fa6d2fd3a8fe98f4781a6b554e2b506d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710541"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Mettre à niveau le Stockage Blob Azure avec les fonctionnalités de Azure Data Lake Storage Gen2

Cet article vous aide à déverrouiller des fonctionnalités telles que la sécurité au niveau des fichiers et des répertoires et l’accélération des opérations. Ces fonctionnalités sont largement utilisées par les charges de travail d’analyse du Big Data et sont désignées collectivement comme des Azure Data Lake Storage Gen2. 

Pour plus d’informations sur ces fonctionnalités et pour évaluer l’impact de cette mise à niveau sur les charges de travail, les applications, les coûts, les intégrations de service, les outils, les fonctionnalités et la documentation, consultez [Mise à niveau du Stockage Blob Azure avec les fonctionnalités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> La mise à niveau est irréversible. Il n’existe aucun moyen de rétablir la version antérieure de votre compte une fois que vous avez effectué la mise à niveau. Nous vous recommandons de valider votre mise à niveau dans un environnement de non-production.

## <a name="perform-the-upgrade"></a>Effectuer la mise à niveau

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.

3. Sélectionnez **Migration vers Data Lake Gen2**.

   La page de configuration **Mise à niveau vers un compte de stockage avec des fonctionnalités de Data Lake Storage Gen2** s’affiche.

   > [!div class="mx-imgBorder"]
   > ![Page Configuration](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Développez la section **Étape 1 : vérifier les modifications de compte avant la mise à niveau**, puis cliquez sur **Vérifier et accepter les modifications**.

5. Sur la page **Vérifier les modifications de compte**, cochez la case, puis cliquez sur **Accepter les modifications**.

6. Développez la section **Étape 2 : valider le compte avant la mise à niveau**, puis cliquez sur **Démarrer la validation**.

   Si la validation échoue, une erreur s’affiche dans la page. Dans certains cas, un lien **Afficher les erreurs** apparaît. Si ce lien apparaît, sélectionnez-le. 

   > [!div class="mx-imgBorder"]
   > ![Lien Afficher les erreurs](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   Ensuite, dans le menu contextuel du fichier **error.json**, sélectionnez **Télécharger**.

   > [!div class="mx-imgBorder"]
   > ![Page Erreur JSON](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   Ouvrez le fichier téléchargé pour déterminer la raison pour laquelle le compte n’a pas passé l’étape de validation. 

   Le code JSON suivant indique qu’une fonctionnalité incompatible est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. Une fois votre compte validé, développez la section **Étape 3 : Mettre à niveau le compte**, puis cliquez sur **Démarrer la mise à niveau**.

   > [!IMPORTANT]
   > Les opérations d’écriture sont désactivées lors de la mise à niveau de votre compte. Les opérations de lecture ne sont pas désactivées, mais nous vous recommandons vivement de suspendre les opérations de lecture, car elles peuvent déstabiliser le processus de mise à niveau.

   Une fois la migration terminée, un message similaire à ce qui suit s’affiche. 

   > [!div class="mx-imgBorder"]
   > ![Page La migration est terminée](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>Migrer les données, les charges de travail et les applications 

1. Configurez [des services dans vos charges de travail](data-lake-storage-integrate-with-azure-services.md) pour qu’ils pointent vers le point de terminaison du **service BLOB** ou le point de terminaison de **Data Lake Storage** .

   > [!div class="mx-imgBorder"]
   > ![Points de terminaison de compte](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Pour les charges de travail Hadoop qui utilisent le pilote Windows Azure Storage Blob ou le pilote [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html), veillez à les modifier pour utiliser le pilote [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). Contrairement au pilote WASB qui effectue des demandes au point de terminaison du **service BLOB**, le pilote ABFS envoie des demandes au point de terminaison de **Data Lake Storage** de votre compte.

2. Testez les applications personnalisées pour vous assurer qu’elles fonctionnent comme prévu avec votre compte mis à niveau. 

   L’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md) permet à la plupart des applications de continuer à utiliser les API d’objets Blob sans modification. Si vous rencontrez des problèmes ou si vous souhaitez utiliser des API pour travailler avec des ACL et des opérations d’annuaire, envisagez de déplacer une partie de votre code pour utiliser des API Data Lake Storage Gen2. Consultez les guides concernant [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) et [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Testez des scripts personnalisés pour vous assurer qu’elles fonctionnent comme prévu avec votre compte mis à niveau. 

   Comme c’est le cas avec les API d’objets BLOB, un grand nombre de vos scripts seront probablement opérationnels sans que vous ayez à les modifier. Toutefois, si nécessaire, vous pouvez mettre à jour les fichiers de scripts pour qu’ils utilisent les [applets de commande PowerShell](data-lake-storage-directory-file-acl-powershell.md) Data Lake Storage Gen2 et les [commandes Azure CLI](data-lake-storage-directory-file-acl-cli.md).
 

## <a name="see-also"></a>Voir aussi

[Présentation d'Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)