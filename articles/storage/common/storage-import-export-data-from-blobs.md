---
title: Utilisation d’Azure Import/Export pour exporter des données à partir d’objets blob Azure | Microsoft Docs
description: Découvrez comment créer des tâches d’exportation dans le Portail Azure pour transférer des données à partir d’objets blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 8ce1e7d58ba69d9f36d3b37c1e48bfeebc5d8d65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978559"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Utilisation du service Azure Import/Export pour exporter des données à partir du Stockage Blob Azure
Cet article fournit des instructions pas à pas sur l’utilisation du service Azure Import/Export pour exporter en toute sécurité de grandes quantités de données à partir du Stockage Blob Azure. Pour pouvoir utiliser ce service, vous devez expédier des lecteurs vides au centre de données Azure. Le service exporte les données de votre compte de stockage vers les lecteurs, puis vous réexpédie ces derniers.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de créer une tâche d’exportation pour transférer des données à partir du Stockage Blob Azure, passez en revue et respectez soigneusement la liste des prérequis de ce service.
Vous devez respecter les consignes suivantes :

- Avoir un abonnement Azure actif utilisable avec le service Import/Export
- Avoir au moins un compte de stockage Azure. Consultez la liste des [Comptes de stockage et types de stockage pris en charge pour le service Import/Export](storage-import-export-requirements.md). Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-account-create.md).
- Avoir un nombre suffisant de disques correspondant aux [types pris en charge](storage-import-export-requirements.md#supported-disks).
- Dotez-vous d’un compte FedEx/DHL. Si vous souhaitez faire appel à un autre transporteur que FedEx/DHL, contactez l’équipe des opérations Azure Data Box à l’adresse `adbops@microsoft.com`.
    - Le compte doit être valide, doit avoir un solde et doit offrir des fonctionnalités de réexpédition.
    - Générez un numéro de suivi pour le travail d’exportation.
    - Chaque travail doit avoir un numéro de suivi distinct. Plusieurs travaux portant le même numéro de suivi ne sont pas pris en charge.
    - Si vous n’avez pas de compte de transporteur, accédez à :
        - [Créer un compte FedEX](https://www.fedex.com/en-us/create-account.html), ou
        - [Créer un compte DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Étape 1 : Création d’une tâche d’exportation

Effectuez les étapes suivantes pour créer une tâche d’exportation dans le Portail Azure.

1. Connectez-vous sur https://portal.azure.com/.
2. Accédez à **Tous les services > Stockage > Tâches d’importation/exportation**.

    ![Accéder à Tâches d’importation/exportation](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Cliquez sur **Créer une tâche d’importation/exportation**.

    ![Cliquer sur Tâche d’importation/exportation](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Dans **De base** :

    - Sélectionnez **Exporter à partir d’Azure**.
    - Entrez un nom descriptif pour la tâche d’exportation. Utilisez le nom de votre choix pour suivre la progression de vos tâches.
        - Le nom peut contenir uniquement des lettres minuscules, des chiffres, des traits d’union et des traits de soulignement.
        - Le nom doit commencer par une lettre et ne doit pas contenir d’espaces.
    - Sélectionnez un abonnement.
    - Entrez ou sélectionnez un groupe de ressources.

        ![Concepts de base](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

3. Dans **Détails de la tâche** :

    - Sélectionnez le compte de stockage où se trouvent les données à exporter. Utilisez un compte de stockage proche de l’endroit où vous vous trouvez.
    - L’emplacement de remise est automatiquement rempli en fonction de la région du compte de stockage sélectionné.
    - Spécifiez les données blob à exporter depuis votre compte de stockage vers le ou les lecteurs vides.
    - Choisissez l’option **Exporter tout** pour exporter l’ensemble des données blob du compte de stockage.

         ![Exporter tout](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Vous pouvez spécifier les conteneurs et objets blob à exporter.
        - **Pour spécifier un objet blob à exporter** : utilisez le sélecteur **Égal à**. Spécifiez le chemin relatif de l’objet blob, en commençant par le nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
        - **Pour spécifier tous les objets blob commençant par un préfixe** : Utilisez le sélecteur **Commence par**. Spécifiez le préfixe, en commençant par une barre oblique « / ». Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob. Vous devez indiquer les chemins d’objets blob dans un format valide pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran. Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](#examples-of-valid-blob-paths).

           ![Exporter les conteneurs et objets blob sélectionnés](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Vous pouvez effectuer l’exportation à partir du fichier de liste d’objets blob.

        ![Exporter à partir d’un fichier de liste d’objets blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Si l’objet blob à exporter est utilisé pendant la copie de données, le service Azure Import/Export prend un instantané de l’objet blob, puis copie cet instantané.


4. Dans **Informations de réexpédition** :

    - Sélectionnez le transporteur dans la liste déroulante. Si vous souhaitez utiliser un autre transporteur que FedEx/DHL, choisissez une option existante dans la liste déroulante. Contactez l’équipe des opérations Azure Data Box à l’adresse `adbops@microsoft.com` pour lui indiquer le nom du transporteur auquel vous envisagez de faire appel.
    - Entrez un numéro de compte de transporteur valide que vous avez créé pour ce transporteur. Microsoft utilise ce compte pour renvoyer les lecteurs une fois la tâche d’exportation terminée.
    - Indiquez le nom d’un contact, le numéro de téléphone, l’e-mail, l’adresse, la ville, le code postal, l’état/la province et le pays/la région, puis vérifiez que ces informations sont complètes et valides.

        > [!TIP]
        > Au lieu de spécifier une adresse de messagerie pour un seul utilisateur, fournissez une adresse de groupe. Cela garantit que vous recevrez des notifications même si un administrateur s’en va.

5. Dans **Récapitulatif** :

    - Passez en revue les détails de la tâche.
    - Notez le nom de la tâche et l’adresse de livraison du centre de données Azure pour l’expédition des disques.

        > [!NOTE]
        > Envoyer toujours les disques au centre de données indiqué dans le portail Azure. Si les disques ne sont pas expédiés au centre de données approprié, le travail ne sera pas exécuté.

    - Cliquez sur **OK** pour créer la tâche d’exportation.

## <a name="step-2-ship-the-drives"></a>Étape 2 : Expédier les disques

Si vous ignorez le nombre de disques dont vous avez besoin, accédez à l’étape [Vérifier le nombre de disques](#check-the-number-of-drives). Si vous connaissez le nombre de disques nécessaires, expédiez-les.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Étape 3 : Mettre à jour la tâche avec les informations de suivi

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Étape 4 : Recevoir les disques
Quand le tableau de bord indique que la tâche a été effectuée, les disques vous sont expédiés. Le numéro de suivi de l’envoi est disponible sur le portail.

1. Une fois que vous avez reçu les disques contenant les données exportées, vous devez obtenir les clés BitLocker pour les déverrouiller. Accédez à la tâche d’exportation dans le Portail Azure. Cliquez sur l’onglet **Import/Export**.
2. Dans la liste, sélectionnez votre tâche d’exportation, puis cliquez sur celle-ci. Accédez à **Clés BitLocker**, puis copiez les clés.

   ![Afficher les clés BitLocker pour une tâche d'exportation](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Utilisez les clés BitLocker pour déverrouiller les disques.

L’exportation est effectuée. À ce stade, vous pouvez supprimer la tâche. Sinon, elle est supprimée automatiquement après 90 jours.


## <a name="check-the-number-of-drives"></a>Vérifier le nombre de disques

Cette étape *facultative* vous permet de déterminer le nombre de disques nécessaires pour la tâche d’exportation. Effectuez cette étape sur un système Windows exécutant une [version prise en charge du système d’exploitation](storage-import-export-requirements.md#supported-operating-systems).

1. [Téléchargez la version 1 de WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) sur le système Windows.
2. Décompressez le package dans le dossier par défaut : `waimportexportv1`. Par exemple : `C:\WaImportExportV1`.
3. Ouvrez une fenêtre PowerShell ou de ligne de commande avec des privilèges d’administrateur. Pour accéder au répertoire du dossier décompressé, exécutez la commande suivante :

    `cd C:\WaImportExportV1`

4. Pour vérifier le nombre de disques nécessaires pour les objets blob sélectionnés, exécutez la commande suivante :

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Les paramètres sont décrits dans le tableau suivant :

    |Paramètre de ligne de commande|Description|  
    |--------------------------|-----------------|  
    |**/logdir:**|facultatif. Répertoire du journal. Les fichiers journaux détaillés sont écrits dans ce répertoire. Si ce paramètre n’est pas spécifié, le répertoire actif est utilisé en tant que répertoire de journaux.|  
    |**/sn:**|Obligatoire. Nom du compte de stockage du travail d’exportation.|  
    |**/sk:**|Obligatoire uniquement si aucun jeton SAP de conteneur n’est spécifié. Clé du compte de stockage du travail d’exportation.|  
    |**/csas:**|Obligatoire uniquement si aucune clé de compte de stockage n’est spécifiée. SAP du conteneur pour lister les objets blob à exporter dans le travail d’exportation.|  
    |**/ExportBlobListFile:**|Obligatoire. Chemin d’accès au fichier XML contenant la liste des chemins d’accès ou des préfixes de chemin d’accès aux objets blob à exporter. Format du fichier utilisé dans l’élément `BlobListBlobPath` dans l’opération [Put Job](/rest/api/storageimportexport/jobs) de l’API REST du service Import/Export.|  
    |**/DriveSize:**|Obligatoire. Taille des disques à utiliser pour une tâche d’exportation, *par exemple*, 500 Go, 1,5 To.|  

    Consultez un [exemple de commande PreviewExport](#example-of-previewexport-command).

5. Vérifiez que vous pouvez accéder en lecture/écriture aux disques qui vont être expédiés pour la tâche d’exportation.

### <a name="example-of-previewexport-command"></a>Exemple de commande PreviewExport

L’exemple suivant illustre la commande `PreviewExport` :  

```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  

Le fichier de liste d’objets blob à exporter peut contenir des noms et des préfixes d’objets blob, comme l’illustre ce code :  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

L’outil Azure Import/Export liste tous les objets blob à exporter et calcule leur répartition sur des lecteurs de la taille spécifiée, en prenant en compte les éventuelles surcharges nécessaires, puis évalue le nombre de disques requis pour contenir les objets blob et les informations sur l’utilisation des lecteurs.  

Voici un exemple de sortie, les journaux d’activité d’information étant omis :  

```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  

Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Exemples de chemins d’objets blob valides

Le tableau suivant présente des exemples de chemins d’accès d’objet blob valides :

   | Sélecteur | Chemin d'accès d'objet blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporte tous les objets blob présents dans le compte de stockage. |
   | Starts With |/$root/ |Exporte tous les objets blob présents dans le conteneur racine. |
   | Starts With |/book |Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book** |
   | Starts With |/music/ |Exporte tous les objets blob présents dans le conteneur **music** |
   | Starts With |/music/love |Exporte tous les objets blob présents dans le conteneur **music** qui commencent par le préfixe **love**. |
   | Equal To |$root/logo.bmp |Exporte l'objet blob **logo.bmp** présent dans le conteneur racine. |
   | Equal To |videos/story.mp4 |Exporte l’objet blob **story.mp4** présent dans le conteneur **videos**. |

## <a name="next-steps"></a>Étapes suivantes

* [Voir l’état de la tâche et des disques](storage-import-export-view-drive-status.md)
* [Passer en revue les exigences d’importation/exportation](storage-import-export-requirements.md)
