---
title: Récupération d’un conteneur de compte de stockage
description: Récupération d’un conteneur de compte de stockage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693798"
---
# <a name="storage-account-container-recovery"></a>Récupération d’un conteneur de compte de stockage

Le stockage Azure fournit la résilience des données via des réplicas automatisés. Toutefois, cela n’empêche pas le code d’application ou les utilisateurs d’altérer des données de façon volontaire ou accidentelle. La conservation de l’intégrité des données sous la menace d’erreurs des utilisateurs et des applications nécessite des techniques plus avancées, comme la copie des données vers un emplacement de stockage secondaire avec un journal d’audit.

## <a name="checking-azure-storage-account-type"></a>Vérification du type de compte de stockage Azure

1. Accédez au [portail Azure](https://portal.azure.com/).

2. Recherchez votre compte de stockage.

3. Dans la section **Vue d’ensemble**, recherchez **Réplication**.

   ![Image](media/storage-account-container-recovery/1.png)

4. Si le type de réplication est **GRS/RA-GRS**, la récupération du conteneur de compte est possible, mais sans garantie. Pour tous les autres types de réplication, elle est impossible.

5. Réunissez les informations suivantes et remplissez une demande de support auprès du Support Microsoft.

   * Nom du compte de stockage :
   * Nom du conteneur :
   * Heure de la suppression :

   Le tableau suivant fournit une vue d’ensemble de l’étendue de la récupération d’un conteneur de compte de stockage en fonction de la stratégie de réplication.

   |Type de contenu|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Conteneur de stockage|Non|Non|OUI|OUI| 

   * Nous pouvons tenter de restaurer le conteneur de compte de stockage, mais sans aucune garantie. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Ce qu’il ne faut pas faire pour que la récupération réussisse

* Veuillez ne pas recréer le conteneur (avec le même nom).  
* Si vous avez déjà recréé le conteneur, vous devez le supprimer avant de remplir une demande de support pour la récupération.

## <a name="steps-to-prevent-this-in-the-future"></a>Étapes à suivre pour éviter cela à l’avenir

1. Pour éviter que cela se reproduise à l’avenir, la fonctionnalité la plus recommandée à utiliser est [Soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Nous recommandons également la fonctionnalité [Snapshot](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).
 
## <a name="next-steps"></a>Étapes suivantes

Voici deux exemples de code sur la fonctionnalité :

  * [Créer et gérer un instantané blob dans .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Utilisation d’instantanés blob avec PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

