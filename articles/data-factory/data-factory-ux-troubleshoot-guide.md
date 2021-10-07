---
title: Résoudre les problèmes liés à Azure Data Factory Studio
description: Découvrez comment résoudre les problèmes liés à Azure Data Factory Studio.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663133"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>Résoudre les problèmes liés à Azure Data Factory Studio

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes de résolution des problèmes couramment utilisées pour [Azure Data Factory Studio](https://adf.azure.com), l’interface utilisateur du service.

## <a name="azure-data-factory-studio-fails-to-load"></a>Échec du chargement d’Azure Data Factory Studio

> [!NOTE]
> Azure Data Factory Studio prend officiellement en charge Microsoft Edge et Google Chrome. L’utilisation d’autres navigateurs web peut entraîner un comportement inattendu ou non documenté.

### <a name="third-party-cookies-blocked"></a>Cookies tiers bloqués

Azure Data Factory Studio utilise les cookies de navigateur pour conserver l’état d’une session utilisateur et obtenir des expériences de développement et de supervision interactives. Il est possible que votre navigateur bloque des cookies de tiers parce que vous utilisez une session incognito ou que vous avez activé un bloqueur de publicités. Le blocage des cookies de tiers peut provoquer des problèmes lors du chargement du portail.  Vous pouvez être redirigé vers une page blanche ou vers « https://adf.azure.com/accesstoken.html  », ou vous pouvez recevoir un message d’avertissement indiquant que les cookies de tiers sont bloqués. Pour résoudre ce problème, activez les options de cookies tiers sur votre navigateur en procédant comme suit :

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>Autoriser tous les cookies

1. Visitez **edge://settings/content/cookies** dans votre navigateur.
1. Assurez-vous que l’option **Autoriser les sites à enregistrer et à lire les données de cookie** est activée, et que l’option **Bloquer les cookies tiers** est désactivée 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="Autoriser tous les cookies dans Edge":::
1. Actualisez Azure Data Factory Studio et réessayez.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Autoriser seulement Azure Data Factory Studio à utiliser des cookies

Si vous ne voulez pas autoriser tous les cookies, vous pouvez autoriser seulement Azure Data Factory Studio :

1. Visitez **edge://settings/content/cookies**.
1. Sous la section **Autoriser**, sélectionnez **Ajouter**, puis ajoutez le site **adf.azure.com**. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="Ajouter l’expérience utilisateur ADF aux sites autorisés dans Edge":::
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>Autoriser tous les cookies

1. Visitez **chrome://settings/cookies** dans votre navigateur.
1. Sélectionner l’option **Autoriser tous les cookies** 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="Autoriser tous les cookies dans Chrome":::
1. Actualisez Azure Data Factory Studio et réessayez.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Autoriser seulement Azure Data Factory Studio à utiliser des cookies

Si vous ne voulez pas autoriser tous les cookies, vous pouvez autoriser seulement Azure Data Factory Studio :
1. Visitez **chrome://settings/cookies**.
1. Sélectionner **ajouter** sous l’option **Sites qui peuvent toujours utiliser des cookies** 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="Ajouter l’expérience utilisateur ADF aux sites autorisés dans Chrome":::
1. Ajoutez le site **adf.azure.com**, cochez l’option **tous les cookies**, puis enregistrez. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="Autoriser tous les cookies du site d’expérience utilisateur ADF":::
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Erreur d’échec de la connexion dans Azure Data Factory Studio

Vous pouvez parfois voir une erreur « Échec de la connexion » dans Azure Data Factory Studio similaire à la capture d’écran ci-dessous, par exemple après avoir cliqué sur **Tester la connexion** ou **Afficher un aperçu**. Il signifie que l’opération a échoué parce que votre ordinateur local n’a pas pu se connecter au service ADF.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="Échec de la connexion":::

Pour résoudre le problème, vous pouvez commencer par essayer d’effectuer la même opération en activant le mode de navigation InPrivate dans votre navigateur.

Si ça ne fonctionne toujours pas, recherchez le **nom du serveur** dans le message d’erreur (**dpnortheurope.svc.datafactory.azure.com** dans cet exemple), puis saisissez le **nom du serveur** directement dans la barre d’adresses de votre navigateur. 

- Si le navigateur affiche une erreur 404, cela signifie généralement que tout est correct de votre côté (client) et que le problème se situe du côté du service ADF. Créez un ticket de support avec l’**ID d’activité** mentionné dans le message d’erreur.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="Ressource introuvable":::

- Si vous ne voyez pas d’erreur 404 mais une erreur similaire à celle ci-dessous dans le navigateur, cela signifie généralement qu’il y a un problème côté client. Suivez les étapes de dépannage.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="Erreur côté client":::

Pour résoudre le problème, ouvrez l’**invite de commandes**, puis tapez `nslookup dpnortheurope.svc.datafactory.azure.com`. Une réponse normale doit ressembler à ce qui suit :

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="Réponse de la commande 1":::

- Si vous voyez une réponse DNS (Domain Name Service) normale, contactez votre support informatique local pour vérifier les paramètres du pare-feu.  Vérifiez que les connexions HTTPS à ce nom d’hôte ne sont pas bloquées. Si le problème persiste, créez un ticket de support pour ADF en mentionnant l’**ID d’activité** indiqué dans le message d’erreur.

- Une réponse DNS qui diffère de la réponse normale ci-dessus peut également signifier qu’il existe un problème avec votre serveur DNS lors de la résolution du nom DNS. Changer votre serveur DNS (par exemple pour le DNS Google 8.8.8.8) peut contourner le problème dans ce cas. 

- Si le problème persiste, vous pouvez essayer d’exécuter les commandes `nslookup datafactory.azure.com` et `nslookup azure.com` pour voir à quel niveau votre résolution DNS a échoué et envoyer toutes les informations à votre support informatique local ou à votre fournisseur de services Internet pour résoudre le problème. S’ils pensent que le problème se situe toujours du côté de Microsoft, créez un ticket de support mentionnant l’**ID d’activité** indiqué dans le message d’erreur.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="Réponse de la commande 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>Message d’avertissement de changement du type de service lié dans les jeux de données

Vous pouvez rencontrer le message d’avertissement ci-dessous quand vous utilisez un jeu de données avec un format de fichier dans une activité, et que vous voulez ensuite pointer vers un service lié d’un type différent de celui que vous avez utilisé avant dans l’activité (par exemple de Système de fichiers à Azure Data Lake Storage Gen2).

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="Message d’avertissement":::

Les jeux de données avec format de fichier peuvent être utilisés avec tous les connecteurs basés sur des fichiers. Par exemple, vous pouvez configurer un jeu de données Parquet sur Azure Blob ou Azure Data Lake Storage Gen2. Notez que chaque connecteur prend en charge différents jeux de paramètres liés au magasin de données sur l’activité, et avec un modèle d’application différent. 

Dans l’interface utilisateur de création d’ADF, quand vous utilisez un jeu de données avec format de fichier dans une activité (notamment Copy, Lookup, GetMetadata ou Delete) et que, dans le jeu de données, vous voulez pointer vers un service lié d’un type différent du type actuel (par exemple, passer de Système de fichiers à ADLS Gen2), vous allez voir ce message d’avertissement. Pour garantir que ce changement est correct, après votre consentement, les pipelines et les activités qui référencent ce jeu de données sont modifiés pour utiliser également le nouveau type, et tous les paramètres existants du magasin de données qui sont incompatibles avec le nouveau type sont effacés parce qu’ils ne s’appliquent plus.

Pour en savoir plus sur les paramètres de stockage de données pris en charge pour chaque connecteur, vous pouvez accéder à l’article du connecteur correspondant-> propriétés de l’activité de copie pour afficher la liste détaillée des propriétés. Consultez [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [Système de fichiers](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) et [SFTP](connector-sftp.md).


## <a name="could-not-load-resource-while-opening-pipeline"></a>Impossible de charger la ressource lors de l’ouverture du pipeline 

Quand l’utilisateur accède à un pipeline en utilisant Azure Data Factory Studio, un message d’erreur indique « Impossible de charger la ressource 'xxxxxx' ».  Vérifiez qu’il n’existe aucune erreur dans le JSON et que les ressources référencées existent. État : TypeError : impossible de lire la propriété ’xxxxx’ de non défini, Raison possible : TypeError : impossible de lire la propriété ’xxxxxxx’ de non défini. »

La source du message d’erreur est un fichier JSON décrivant le pipeline. Cela se produit quand le client utilise une intégration git et que les fichiers JSON du pipeline sont endommagés pour une raison quelconque. Vous verrez une erreur (point rouge avec x) à gauche du nom du pipeline, comme ci-dessous.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="Erreur JSON du pipeline":::

La solution consiste à d’abord corriger les fichiers JSON, puis à rouvrir le pipeline en utilisant l’outil de création.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
* [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/)
* [Page de questions Microsoft Q&R](/answers/topics/azure-data-factory.html)