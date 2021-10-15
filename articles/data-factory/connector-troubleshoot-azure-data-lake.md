---
title: Résoudre les problèmes liés aux connecteurs Azure Data Lake Storage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs Azure Data Lake Storage Gen1 et Gen2 dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 1c36fa5295acafb96e57484cf34429091dd634e9
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390576"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés aux connecteurs Azure Data Lake Storage dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés aux connecteurs Azure Data Lake Storage Gen1 et Gen2 dans Azure Data Factory et Azure Synapse.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Message d’erreur : Le serveur a clos la connexion sous-jacente : Impossible d’établir une relation de confiance pour le canal sécurisé SSL/TLS.

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Cause** : La validation du certificat a échoué lors de l’établissement d’une liaison TLS.

- **Résolution** : Pour contourner ce problème, utilisez la copie intermédiaire pour ignorer la validation TLS (Transport Layer Security) pour Azure Data Lake Storage Gen1. Vous devez reproduire ce problème et collecter la trace du moniteur réseau (netmon), puis demander à votre équipe réseau de vérifier la configuration du réseau local.

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="Diagramme des connexions Azure Data Lake Storage Gen1 pour la résolution des problèmes.":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Message d’erreur : Le serveur distant a retourné une erreur : (403) Interdit

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Cause** : L’une des causes possibles est que le principal du service ou l’identité managée que vous utilisez n’a pas l’autorisation d’accéder à certains dossiers ou fichiers.

- **Résolution** : Accordez les autorisations appropriées sur tous les dossiers et sous-dossiers que vous devez copier. Pour en savoir plus, consultez [Copier des données vers ou depuis Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Message d’erreur : Échec d’obtention du jeton d’accès à l’aide du principal du service. Erreur ADAL : service_unavailable

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante :

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Cause** : Lorsque le serveur de jeton de service (STS) détenu par Azure Active Directory n’est pas disponible, c.-à-d. qu’il est trop occupé pour gérer les requêtes, il renvoie une erreur HTTP 503. 

- **Résolution** : Réexécutez l’activité de copie au bout de quelques minutes.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Code d’erreur : AdlsGen2OperationFailed

- **Message** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

  | Analyse de la cause                                               | Recommandation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Si Azure Data Lake Storage Gen2 lève une erreur, une opération a échoué.| Consultez le message d’erreur détaillé généré par Azure Data Lake Storage Gen2. Si l’erreur est un échec temporaire, recommencez l’opération. Si vous avez besoin d’aide supplémentaire, contactez le support Stockage Azure et fournissez l’ID de la requête indiqué dans le message d’erreur. |
  | Si le message d’erreur contient la chaîne « Forbidden », le principal du service ou l’identité managée que vous utilisez ne dispose peut-être pas des autorisations suffisantes pour accéder à Azure Data Lake Storage Gen2. | Pour résoudre cette erreur, consultez [Copier et transformer des données dans Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Si le message d’erreur contient la chaîne « InternalServerError », l’erreur est retournée par Azure Data Lake Storage Gen2. | L’erreur peut être due à un échec temporaire. Si c’est le cas, recommencez l’opération. Si le problème persiste, contactez le support Stockage Azure et fournissez l’ID de la requête indiqué dans le message d’erreur. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>La requête au compte Azure Data Lake Storage Gen2 a provoqué une erreur de dépassement de délai

- **Message** : 
  * Code d’erreur = `UserErrorFailedBlobFSOperation`
  * Message d’erreur = `BlobFS operation failed for: A task was canceled.`

- **Cause** : Le problème est dû à une erreur de dépassement de délai du récepteur Azure Data Lake Storage Gen2, qui se produit généralement sur l’ordinateur du runtime d'intégration auto-hébergé.

- **Recommandation** : 

    - Placez votre machine du runtime d’intégration auto-hébergé et le compte Azure Data Lake Storage Gen2 cible dans la même région, si possible. Cela peut éviter une erreur de dépassement de délai aléatoire et améliorer les performances.

    - Vérifiez s’il existe un paramètre de réseau spécial, comme ExpressRoute, et assurez-vous que le réseau dispose d’une bande passante suffisante. Nous vous suggérons de diminuer le paramètre des tâches simultanées du runtime d’intégration auto-hébergé lorsque la bande passante globale est faible. Cela permet d’éviter que plusieurs tâches simultanées ne se disputent les ressources réseau.

    - Utilisez une taille de bloc plus petite pour la copie non binaire afin d’atténuer les effets d’une telle erreur de dépassement de délai si la taille de fichier est modérée ou petite. Pour plus d’informations, consultez [Bloc Put de stockage Blob](/rest/api/storageservices/put-block).

       Pour spécifier une taille de bloc personnalisée, modifiez la propriété dans l’éditeur de fichier JSON comme indiqué ici :
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
