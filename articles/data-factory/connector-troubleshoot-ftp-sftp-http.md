---
title: Résoudre les problèmes liés aux connecteurs FTP, SFTP et HTTP
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs FTP, SFTP et HTTP dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390587"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés aux connecteurs FTP, SFTP et HTTP dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés aux connecteurs FTP, SFTP et HTTP dans Azure Data Factory et Azure Synapse.

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Code d’erreur : FtpFailedToConnectToFtpServer

- **Message** : `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Cause** : Un type de service lié incorrect est peut-être utilisé pour le serveur FTP, comme l’utilisation du service lié SFTP pour la connexion à un serveur FTP.

- **Recommandation** :  Vérifiez le port du serveur cible. FTP utilise le port 21.

### <a name="error-code-ftpfailedtoreadftpdata"></a>Code d’erreur : FtpFailedToReadFtpData

- **Message** : `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **Cause** : La plage de ports comprise entre 1024 et 65535 n’est pas ouverte pour le transfert de données en mode passif pris en charge par le pipeline Data Factory ou Synapse.

- **Recommandation** : Vérifiez les paramètres de pare-feu du serveur cible. Ouvrez le port 1024-65535 ou la plage de ports spécifié dans le serveur FTP à l’adresse IP SHIR/Azure IR.

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Code d’erreur : SftpOperationFail

- **Message** : `Failed to '%operation;'. Check detailed error from SFTP.`

- **Cause** : Problème avec l’opération SFTP.

- **Recommandation** :  Consultez les détails de l’erreur dans SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Code d’erreur : SftpRenameOperationFail

- **Message** : `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Cause** : Votre serveur SFTP ne prend pas en charge le changement de nom du fichier temporaire.

- **Recommandation** :  Définissez « useTempFileRename » sur false dans le récepteur de copie pour désactiver le chargement dans le fichier temporaire.


### <a name="error-code-sftpinvalidsftpcredential"></a>Code d’erreur : SftpInvalidSftpCredential

- **Message** : `Invalid SFTP credential provided for '%type;' authentication type.`

- **Cause** : Le contenu de la clé privée est extrait d’Azure Key Vault ou du SDK, mais il n’est pas correctement encodé.

- **Recommandation** :  

    Si le contenu de la clé privée provient de votre coffre de clés, le fichier de clé d’origine peut fonctionner si vous le chargez directement dans le service lié SFTP.

    Pour plus d’informations, consultez [Copier des données depuis et vers le serveur SFTP à l’aide de la fabrique de données ou des pipelines Synapse](./connector-sftp.md#use-ssh-public-key-authentication). Le contenu de la clé privée est un contenu de clé privée SSH encodé en base64.

    Encodez *l’intégralité* du contenu du fichier de clé privée d’origine avec un encodage en base64 et stockez la chaîne encodée dans votre coffre de clés. Le fichier de clé privée d’origine est celui qui peut fonctionner dans le service lié SFTP si vous sélectionnez **Charger** à partir d’un fichier.

    Voici quelques exemples que vous pouvez utiliser pour générer la chaîne :

    - Utilisation de code C# :

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Utilisation de code Python :

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Utilisez un outil de conversion en base64 tiers. Nous vous recommandons d’utiliser l’outil [Encoder au format Base64](https://www.base64encode.org/).

- **Cause** : Le format choisi pour le contenu de la clé était incorrect.

- **Recommandation** :  

    La clé privée SSH au format PKCS#8 (commençant par « -----BEGIN ENCRYPTED PRIVATE KEY----- ») n’est actuellement pas prise en charge pour l’accès au serveur SFTP. 

    Exécutez les commandes ci-dessous pour convertir la clé au format de clé SSH classique (commençant par « ------BEGIN RSA PRIVATE KEY----- ») :

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Cause** : Contenu de clé privée ou informations d’identification non valides.

- **Recommandation** :  Servez-vous d’un outil comme WinSCP pour vérifier si votre fichier de clé ou votre mot de passe est correct.

### <a name="sftp-copy-activity-failed"></a>Échec de l’activité de copie SFTP

- **Symptômes** : 
  * Code d’erreur : UserErrorInvalidColumnMappingColumnNotFound 
  * Message d’erreur : `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Cause** : La source ne comporte pas de colonne nommée « AccMngr ».

- **Résolution** : Pour déterminer si la colonne « AccMngr » existe, vérifiez la configuration de votre jeu de données en mappant la colonne du jeu de données de destination.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Code d’erreur : SftpFailedToConnectToSftpServer

- **Message** : `Failed to connect to SFTP server '%server;'.`

- **Cause** : Si le message d’erreur contient la chaîne « L’opération de lecture de socket a expiré après 30 000 millisecondes », il est possible que le type de service lié soit incorrect pour le serveur SFTP. Par exemple, vous utilisez peut-être le service lié FTP pour vous connecter au serveur SFTP.

- **Recommandation** :  Vérifiez le port du serveur cible. Par défaut, SFTP utilise le port 22.

- **Cause** : Si le message d’erreur contient la chaîne « La réponse du serveur ne contient pas d’identification de protocole SSH », il est possible que le serveur SFTP limite la connexion. Plusieurs connexions sont créées pour opérer des téléchargements parallèles à partir du serveur SFTP, lequel atteint parfois la limitation de serveur SFTP. En règle générale, les différents serveurs retournent des erreurs différentes lorsqu’ils rencontrent une limitation.

- **Recommandation** :  

    Définissez le nombre maximal de connexions simultanées du jeu de données SFTP sur 1, puis réexécutez l’activité de copie. Si elle aboutit, cela est le signe que la limitation est en cause.

    Si vous souhaitez promouvoir le faible débit, contactez votre administrateur SFTP pour augmenter le nombre limite de connexions simultanées ou vous pouvez procéder comme suit :

    * Si vous utilisez un IR auto-hébergé, ajoutez l’adresse IP de l’ordinateur IR auto-hébergé à la liste d’autorisation.
    * Lorsque vous utilisez Azure IR, consultez [Adresses IP Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md). Si vous ne souhaitez pas ajouter une plage d’adresses IP à la liste d’autorisation du serveur SFTP, utilisez plutôt le runtime d’intégration auto-hébergé.


### <a name="error-code-sftppermissiondenied"></a>Code d’erreur : SftpPermissionDenied

- **Message** : `Permission denied to access '%path;'`

- **Cause** : L’utilisateur indiqué n’a pas d’autorisation de lecture ou d’écriture sur le dossier ou le fichier lors de son fonctionnement.

- **Recommandation** : Accordez à l’utilisateur l’autorisation de lire ou d’écrire dans le dossier ou les fichiers sur le serveur SFTP.
 
### <a name="error-code-sftpauthenticationfailure"></a>Code d’erreur : SftpAuthenticationFailure

- **Message** : `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **Cause** : Les informations d’identification spécifiées (votre mot de passe ou votre clé privée) ne sont pas valides.

- **Recommandation** : Vérifiez vos informations d’identification.

- **Cause** : Le type d’authentification spécifié n’est pas autorisé ou n’est pas suffisant pour terminer l’authentification sur votre serveur SFTP.

- **Recommandation** : Appliquez les options suivantes pour utiliser le type d’authentification correct :
    - Si votre serveur requiert un mot de passe, utilisez « Basic ».
    - Si votre serveur requiert une clé privée, utilisez l’authentification par clé publique SSH.
    - Si votre serveur nécessite à la fois « password » (un mot de passe) et « private key » (une clé privée), utilisez « Authentification multifacteur ».

- **Cause** : votre serveur SFTP requiert « keyboard-interactive » (interaction au clavier) pour l’authentification alors que vous avez indiqué « password » (mot de passe).

- **Recommandation** : 

    « keyboard-interactive » est une méthode d’authentification spéciale différente de « password ». Cela signifie que, lors de la connexion à un serveur, vous devez entrer le mot de passe manuellement et vous ne pouvez pas utiliser le mot de passe enregistré précédemment. Toutefois Azure Data Factory (ADF) est un service de transfert de données planifié et aucune zone d’entrée contextuelle ne vous permet de fournir le mot de passe au moment de l’exécution. <br/> 
    
    En guise de compromis, une option est fournie pour simuler la saisie en arrière-plan à la place de votre saisie manuelle réelle, ce qui équivaut à changer le paramètre « keyboard-interactive » en « password ». Si vous pouvez accepter ce problème de sécurité, suivez les étapes ci-dessous pour l’activer :<br/> 
    1. Sur le portail ADF, pointez sur le service lié SFTP, puis ouvrez sa charge utile en sélectionnant le bouton code.
    1. Ajoutez `"allowKeyboardInteractiveAuth": true` dans la section « typeProperties ».

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Code d’erreur : HttpFileFailedToRead

- **Message** : `Failed to read data from http server. Check the error from http server：%message;`

- **Cause** : Cette erreur se produit quand une fabrique de données ou un pipeline Synapse communique avec le serveur HTTP, mais que l’opération de requête HTTP échoue.

- **Recommandation** :  Vérifiez le code d’état HTTP dans le message d’erreur, puis corrigez le problème du serveur distant.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
