---
title: Passer en revue les erreurs de copie dans les chargements à partir d’appareils Azure Data Box et Azure Data Box Heavy
description: Décrit comment passer en revue et suivre les erreurs sans possibilité de nouvelle tentative qui empêchent le chargement de fichiers à partir d’un appareil Azure Data Box ou Azure Data Box Heavy.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 05/10/2021
ms.author: alkohli
ms.openlocfilehash: 7ede9b952a27c8ed0df2c24598fc65187036e5dc
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790484"
---
# <a name="review-copy-errors-in-uploads-from-azure-data-box-and-azure-data-box-heavy-devices"></a>Passer en revue les erreurs de copie dans les chargements à partir d’appareils Azure Data Box et Azure Data Box Heavy

Cet article décrit comment passer en revue et suivre les erreurs sans possibilité de nouvelle tentative qui empêchent parfois le chargement dans le cloud de fichiers à partir d’un appareil Azure Data Box ou Azure Data Box Heavy.

> [!NOTE]
> Les informations contenues dans cet article s’appliquent uniquement aux commandes d’importation.

## <a name="upload-errors-notification"></a>Notification d’erreurs de chargement

Quand vous chargez des données de votre appareil sur Azure, il arrive que certains chargements de fichiers échouent en raison d’erreurs de configuration qui ne peuvent pas être résolues par une nouvelle tentative. Vous recevez alors une notification qui vous permet d’examiner les erreurs et de les corriger en vue d’un chargement ultérieur.

La notification suivante apparaît dans le portail Azure. Les erreurs sont listées dans le journal de copie des données que vous pouvez ouvrir avec **DATA COPY PATH**. Pour obtenir de l’aide sur la résolution des erreurs, consultez le [récapitulatif des erreurs de chargement sans possibilité de nouvelle tentative](#summary-of-non-retryable-upload-errors).

![Notification d’erreurs durant le chargement](media/data-box-troubleshoot-data-upload/copy-completed-with-errors-notification-01.png)

Vous ne pouvez pas corriger ces erreurs. Le chargement s’est terminé avec des erreurs. La notification vous informe des problèmes de configuration que vous devez résoudre avant d’essayer un autre chargement par le biais d’un transfert réseau ou d’une nouvelle commande d’importation.

Une fois que vous avez vérifié les erreurs et confirmé que vous êtes prêt à continuer, les données sont effacées de manière sécurisée de l’appareil. Si vous ne répondez pas à la notification, la commande est exécutée automatiquement après 14 jours. Pour obtenir des instructions étape par étape, consultez [Passer en revue les erreurs et continuer](#review-errors-and-proceed).


## <a name="review-errors-and-proceed"></a>Passer en revue les erreurs et continuer

La commande est exécutée automatiquement après 14 jours. En agissant sur la notification, vous pouvez déplacer des éléments plus rapidement.

[!INCLUDE [data-box-review-nonretryable-errors](../../includes/data-box-review-nonretryable-errors.md)]


## <a name="summary-of-non-retryable-upload-errors"></a>Récapitulatif des erreurs de chargement sans possibilité de nouvelle tentative

Les erreurs sans possibilité de nouvelle tentative suivantes génèrent une notification :

|Catégorie d’erreur                    |Code d'erreur |Message d’erreur                                                                             |
|----------------------------------|-----------|------------------------------------------------------------------------------------------|
|UploadErrorCloudHttp              |400        |Requête incorrecte (nom de fichier non valide) [Plus d’informations](#bad-request-file-name-not-valid).|
|UploadErrorCloudHttp              |400        |The value for one of the HTTP headers is not in the correct format. (Le format de la valeur d’un des en-têtes HTTP est incorrect.) [Plus d’informations](#the-value-for-one-of-the-http-headers-is-not-in-the-correct-format)|
|UploadErrorCloudHttp              |409        |Cette opération n’est pas autorisée car l’objet blob est immuable en raison d’une stratégie. [Plus d’informations](#this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy)|
|UploadErrorCloudHttp              |409        |La capacité provisionnée totale des partages ne peut pas dépasser la taille maximale du compte. [Plus d’informations](#the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit)|
|UploadErrorCloudHttp              |409        |Le type d'objet blob n'est pas valide pour cette opération. [Plus d’informations](#the-blob-type-is-invalid-for-this-operation)|
|UploadErrorCloudHttp              |409        |Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande. [Plus d’informations](#there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request)|
|UploadErrorManagedConversionError |409        |La taille de l’objet blob importé n’est pas valide. La taille de l’objet blob est de `<blob-size>` octets. Les tailles prises en charge sont comprises entre 20971520 octets et 8192 Gio. [En savoir plus](#the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib)|
<!--Temporarily removed from table: Bad Request (file property failure for Azure Files)-->

Pour plus d’informations sur le contenu du journal de copie des données, consultez [Suivi et journalisation des événements pour votre commande d’importation Azure Data Box et Azure Data Box Heavy](data-box-logs.md).

D’autres erreurs d’API REST peuvent se produire pendant les chargements de données. Pour plus d’informations, consultez les [code d’erreur courants de l’API REST](/rest/api/storageservices/common-rest-api-error-codes).

> [!NOTE]
> Les sections de **suivi** dans les descriptions d’erreur expliquent comment mettre à jour la configuration de vos données avant de passer une nouvelle commande d’importation ou d’effectuer un transfert réseau. Vous ne pouvez pas corriger ces erreurs dans le chargement actuel.


### <a name="bad-request-file-name-not-valid"></a>Requête incorrecte (nom de fichier non valide)

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 400

**Description de l’erreur :** La plupart des problèmes de nommage de fichiers sont détectés au cours de la phase de **préparation de l’expédition** ou corrigés automatiquement pendant le chargement (ce qui entraîne l’état **Copie avec des avertissements**). Quand un nom de fichier non valide n’est pas intercepté, le chargement du fichier sur Azure échoue.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande, renommez les fichiers listés pour qu’ils répondent aux exigences de nommage d’Azure Files. Pour connaître la configuration requise, consultez les [noms de répertoires et de fichiers](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


<!--TEMPORARILY REMOVED. Product team may restore later. ### Bad Request (File property failure for Azure Files)

**Error category:** UploadErrorCloudHttp 

**Error code:** 400

**Error description:** Data import will fail if the upload of file properties fails for Azure Files.  

**Follow-up:** You can't fix this error in the current upload. The upload will complete with errors. Before you do a network transfer or start a new import order, *GET TROUBLESHOOTING*.-->


### <a name="the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"></a>Le format de la valeur d’un des en-têtes HTTP est incorrect

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 400

**Description de l’erreur :** Impossible de charger les objets blob listés, car ils ne répondent pas aux exigences de format ou de taille des objets blob dans Stockage Azure.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez ce qui suit :

- Les objets blob de pages listés respectent les limites de page de 512 octets.

- Les objets blob de blocs listés ne dépassent pas la taille maximale de 4,75 Tio.


### <a name="this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy"></a>Cette opération n’est pas autorisée car l’objet blob est immuable en raison d’une stratégie

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 409

**Description de l’erreur :** Si un conteneur de stockage d’objets blob est configuré au format WORM (Write Once, Read Many), le chargement des objets blob déjà stockés dans le conteneur échoue.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez que les objets blob listés ne font pas partie d’un conteneur de stockage immuable. Pour plus d’informations, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](/azure/storage/blobs/storage-blob-immutable-storage).


### <a name="the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit"></a>La capacité provisionnée totale des partages ne peut pas dépasser la taille maximale du compte

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 409

**Description de l’erreur :** Le chargement a échoué car la taille totale des données dépasse la taille limite du compte de stockage. Par exemple, la capacité maximale d’un compte FileStorage est de 100 Tio. Si la taille totale des données dépasse 100 Tio, le chargement échoue.  

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez que la capacité totale de tous les partages dans le compte de stockage ne dépasse pas la taille limite du compte de stockage. Pour plus d’informations, consultez [Limites de la taille du compte de stockage Azure](data-box-limits.md#azure-storage-account-size-limits).


### <a name="the-blob-type-is-invalid-for-this-operation"></a>Le type d’objet blob n’est pas valide pour cette opération

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 409

**Description de l’erreur :** L’importation de données vers un objet blob dans le cloud échoue si les données ou les propriétés de l’objet blob de destination sont modifiées.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez qu’il n’y a aucune modification simultanée des objets blob listés ou de leurs propriétés pendant le chargement.

### <a name="there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request"></a>Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande

**Catégorie d’erreur :** UploadErrorCloudHttp 

**Code d’erreur :** 409

**Description de l’erreur :** L’importation de données vers un objet blob dans le cloud échoue si l’objet blob de destination a un bail actif.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez que les objets blob listés n’ont pas de bail actif. Pour plus d’informations, consultez [Accès concurrentiel pessimiste pour les objets blob](/azure/storage/blobs/concurrency-manage?tabs=dotnet#pessimistic-concurrency-for-blobs).


### <a name="the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib"></a>La taille de l’objet blob importé n’est pas valide. La taille de l’objet blob est de `<blob-size>` octets. Les tailles prises en charge sont comprises entre 20971520 octets et 8192 Gio.

**Catégorie d’erreur :** UploadErrorManagedConversionError

**Code d’erreur :** 409

**Description de l’erreur :** Les objets blob de pages listés n’ont pas pu être chargés, car leur taille ne permet pas leur conversion en disque managé. Pour être converti en disque managé, un objet blob de pages doit avoir une taille comprise entre 20 Mo (20 971 520 octets) et 8 192 Gio.

**Suivi :** Vous ne pouvez pas corriger cette erreur dans le chargement actuel. Le chargement s’est terminé avec des erreurs. Avant de procéder à un transfert réseau ou de démarrer une nouvelle commande d’importation, vérifiez que la taille de chaque objet blob listé est comprise entre 20 Mo et 8 192 Gio.


## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue les erreurs courantes de l’API REST](/rest/api/storageservices/common-rest-api-error-codes).
- [Vérifier le chargement des données dans Azure](data-box-deploy-picked-up.md?tabs=in-us-canada-europe#verify-data-upload-to-azure-8)
