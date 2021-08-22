---
title: Résoudre des problèmes d’expérience utilisateur dans Azure Data Factory
description: Découvrez comment résoudre des problèmes d’expérience utilisateur dans Azure Data Factory.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: 99d6116a69e6aabe080d8e46bb90f90a125d7122
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641365"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Résoudre des problèmes d’expérience utilisateur dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes de résolution des problèmes couramment utilisée pour l’expérience utilisateur dans Azure Data Factory.

## <a name="adf-ux-not-loading"></a>Non-chargement de l’expérience utilisateur ADF

> [!NOTE]
> L’expérience utilisateur Azure Data Factory prend officiellement en charge Microsoft Edge et Google Chrome. L’utilisation d’autres navigateurs web peut entraîner un comportement inattendu ou non documenté.

### <a name="third-party-cookies-blocked"></a>Cookies tiers bloqués

L’expérience utilisateur ADF utilise des cookies de navigateurs pour conserver une session utilisateur et permettre des expériences de développement et de supervision interactives. Il est possible que votre navigateur bloque les cookies tiers, parce que vous utilisez une session incognito ou avez activé un bloqueur de publicités. Le blocage des cookies de tiers peut provoquer des problèmes lors du chargement du portail, comme la redirection vers une page vierge, « https://adf.azure.com/accesstoken.html  », ou l’obtention d’un message d’avertissement indiquant que les cookies de tiers sont bloqués. Pour résoudre ce problème, activez les options de cookies tiers sur votre navigateur en procédant comme suit :

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Autoriser tous les cookies

1. Visitez **chrome://settings/cookies** dans votre navigateur.
1. Sélectionner l’option **Autoriser tous les cookies** 

    ![Autoriser tous les cookies dans Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Autoriser uniquement l’expérience utilisateur ADF à utiliser des cookies
Si vous ne souhaitez pas autoriser tous les cookies, vous pouvez n’autoriser que l’expérience utilisateur ADF :
1. Visitez **chrome://settings/cookies**.
1. Sélectionner **ajouter** sous l’option **Sites qui peuvent toujours utiliser des cookies** 

    ![Ajouter l’expérience utilisateur ADF aux sites autorisés dans Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Ajoutez le site **adf.azure.com**, cochez l’option **tous les cookies**, puis enregistrez. 

    ![Autoriser tous les cookies du site d’expérience utilisateur ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Visitez **edge://settings/content/cookies** dans votre navigateur.
1. Assurez-vous que l’option **Autoriser les sites à enregistrer et à lire les données de cookie** est activée, et que l’option **Bloquer les cookies tiers** est désactivée 

    ![Autoriser tous les cookies dans Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Autoriser uniquement l’expérience utilisateur ADF à utiliser des cookies

Si vous ne souhaitez pas autoriser tous les cookies, vous pouvez n’autoriser que l’expérience utilisateur ADF :

1. Visitez **edge://settings/content/cookies**.
1. Sous la section **Autoriser**, sélectionnez **Ajouter**, puis ajoutez le site **adf.azure.com**. 

    ![Ajouter l’expérience utilisateur ADF aux sites autorisés dans Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

## <a name="connection-failed-on-adf-ux"></a>Échec de la connexion dans l’expérience utilisateur ADF

Parfois, une erreur « Échec de la connexion » s’affiche dans l’interface utilisateur d’ADF, similaire à la capture d’écran ci-dessous, lorsque vous cliquez sur **Tester la connexion**, **Aperçu**, etc. Cela signifie qu’ADF n’a pas pu effectuer l’opération parce qu’il ne peut pas se connecter au service ADF à partir de votre ordinateur.

![Échec de la connexion](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Pour résoudre le problème, vous pouvez commencer par essayer d’effectuer la même opération en activant le mode de navigation InPrivate dans votre navigateur.

Si ça ne fonctionne toujours pas, recherchez le **nom du serveur** dans le message d’erreur (**dpnortheurope.svc.datafactory.azure.com** dans cet exemple), puis saisissez le **nom du serveur** directement dans la barre d’adresses de votre navigateur. 

- Si le navigateur affiche une erreur 404, cela signifie généralement que tout est correct de votre côté (client) et que le problème se situe du côté du service ADF. Créez un ticket de support avec l’**ID d’activité** mentionné dans le message d’erreur.

    ![Ressource introuvable](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

- Si vous ne voyez pas d’erreur 404 mais une erreur similaire à celle ci-dessous dans le navigateur, cela signifie généralement qu’il y a un problème côté client. Suivez les étapes de dépannage.

    ![Erreur côté client](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Pour résoudre le problème, ouvrez l’**invite de commandes**, puis tapez `nslookup dpnortheurope.svc.datafactory.azure.com`. Une réponse normale doit ressembler à ce qui suit :

![Réponse de la commande 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

- Si vous voyez une réponse DNS normale, contactez votre support informatique local pour vérifier les paramètres de pare-feu afin de savoir si la connexion HTTPS à ce nom d’hôte est bloquée ou non. Si le problème n’est pas résolu, créez un ticket de support mentionnant l’**ID d’activité** indiqué dans le message d’erreur.

- Si autre chose s’affiche, cela signifie généralement que votre serveur DNS a rencontré un problème lors de la résolution du nom DNS. En règle générale, la modification du fournisseur de services Internet (ISP) ou du DNS (par exemple, sur Google DNS 8.8.8.8) peut être une solution de contournement. Si le problème persiste, vous pouvez essayer d’exécuter les commandes `nslookup datafactory.azure.com` et `nslookup azure.com` pour voir à quel niveau votre résolution DNS a échoué et envoyer toutes les informations à votre support informatique local ou à votre fournisseur de services Internet pour résoudre le problème. S’ils pensent que le problème se situe toujours du côté de Microsoft, créez un ticket de support mentionnant l’**ID d’activité** indiqué dans le message d’erreur.

    ![Réponse de la commande 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Modifier le type de service lié dans les jeux de données

Le jeu de données de format de fichier peut être utilisé avec tous les connecteurs basés sur des fichiers. Par exemple, vous pouvez configurer un jeu de données Parquet sur Azure Blob ou Azure Data Lake Storage Gen2. Notez que chaque connecteur prend en charge différents jeux de paramètres liés au magasin de données sur l’activité, et avec un modèle d’application différent. 

Dans l’interface utilisateur de création ADF, lorsque vous utilisez un jeu de données de format de fichier dans une activité (Copy, Lookup, GetMetadata ou Delete) et que, dans le jeu de données, vous souhaitez pointer vers un service lié d’un type différent du type actuel (par exemple, passer du système de fichiers à ADLS Gen2), le message d’avertissement suivant devrait s’afficher. Pour vous assurer qu’il s’agit d’un commutateur approprié, à la suite de votre consentement, les pipelines et les activités qui font référence à ce jeu de données sont modifiés pour utiliser également le nouveau type, et tous les paramètres de magasin de données existants incompatibles avec le nouveau type sont effacés car ils ne s’appliquent plus.

Pour en savoir plus sur les paramètres de stockage de données pris en charge pour chaque connecteur, vous pouvez accéder à l’article du connecteur correspondant-> propriétés de l’activité de copie pour afficher la liste détaillée des propriétés. Consultez [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).

![Message d'avertissement](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="could-not-load-resource-while-opening-pipeline"></a>Impossible de charger la ressource lors de l’ouverture du pipeline 

Quand l’utilisateur accède au pipeline à l’aide de l’outil de création de l’interface utilisateur graphique d’ADF, le message d’erreur ressemble à ceci : « Impossible de charger la ressource ’xxxxxx’. Assurez-vous qu’il n’existe aucune erreur dans le JSON et que des ressources référencées existent. État : TypeError : impossible de lire la propriété ’xxxxx’ de non défini, Raison possible : TypeError : impossible de lire la propriété ’xxxxxxx’ de non défini. »

La source du message d’erreur est un fichier JSON décrivant le pipeline. Cela se produit quand le client utilise une intégration git et que les fichiers JSON du pipeline sont endommagés pour une raison quelconque. Vous verrez une erreur (point rouge avec x) à gauche du nom du pipeline, comme ci-dessous.

![Erreur JSON du pipeline](media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png)

La solution consiste à corriger les fichiers JSON, puis à rouvrir le pipeline à l’aide de l’outil de création.



## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/)
* [Page de questions Microsoft Q&R](/answers/topics/azure-data-factory.html)
