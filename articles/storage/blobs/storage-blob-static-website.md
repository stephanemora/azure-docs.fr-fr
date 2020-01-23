---
title: Hébergement de sites web statiques dans le service Stockage Azure
description: L’hébergement de sites web statiques dans Stockage Azure constitue une solution évolutive économique pour l’hébergement d’applications web modernes.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 8dc5599e681d9aee84f884cd4990163a2481d386
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708160"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hébergement de sites web statiques dans le service Stockage Azure

Vous pouvez servir du contenu statique (fichiers HTML, CSS, JavaScript et images) directement à partir d’un conteneur de stockage nommé *$web*. L’hébergement de votre contenu dans le stockage Azure vous permet d’utiliser des architectures serverless qui incluent [Azure Functions](/azure/azure-functions/functions-overview) et d’autres services Paas (Platform as a service).

> [!NOTE]
> Si votre site dépend du code côté serveur, utilisez [Azure App Service](/azure/app-service/overview) à la place.

## <a name="setting-up-a-static-website"></a>Configuration d’un site web statique

L’hébergement de site web statique est une fonctionnalité que vous devez activer sur le compte de stockage.

Pour activer l’hébergement de site web statique, sélectionnez le nom de votre fichier par défaut puis, le cas échéant, fournissez un chemin à une page 404 personnalisée. Si un conteneur de stockage d’objets blob nommé **$web** n’existe pas déjà dans le compte, il est créé pour vous. Ajoutez les fichiers de votre site à ce conteneur.

Pour obtenir des instructions détaillées, consultez [Héberger un site web statique dans le stockage Azure](storage-blob-static-website-how-to.md).

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Les fichiers présents dans le conteneur **$web** respectent la casse ; ils sont pris en charge par le biais de requêtes d’accès anonyme et sont disponibles uniquement par l’intermédiaire d’opérations de lecture.

## <a name="uploading-content"></a>Téléchargement de contenu

Vous pouvez utiliser un de ces outils pour charger du contenu sur le conteneur **$web** :

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Module Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extension Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Affichage du contenu

Les utilisateurs peuvent afficher le contenu du site dans un navigateur en utilisant l’URL publique du site web. Vous trouvez cette URL à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Utilisez ce tableau comme guide.

|Outil| Assistance |
|----|----|
|**Azure portal** | [Trouver l’URL du site web avec le portail Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Trouver l’URL du site web avec Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Module Azure PowerShell** | [Trouver l’URL du site web avec PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L’URL de votre site contient un code régional. Par exemple, l’URL `https://contosoblobaccount.z22.web.core.windows.net/` contient le code de région `z22`.

Même si ce code doit demeurer dans l’URL, il n’est destiné qu’à un usage interne, et vous n’aurez pas à l’utiliser de quelque autre manière que ce soit.

Le document d’index, que vous spécifiez lorsque vous activez l’hébergement de site web statique, s’affiche lorsque les utilisateurs ouvrent le site et ne spécifient aucun fichier en particulier (par exemple : `https://contosoblobaccount.z22.web.core.windows.net`).  

Si le serveur retourne une erreur 404, et que vous n’avez spécifié aucun document d’erreur lorsque vous avez activé le site web, une page 404 par défaut est retournée à l’utilisateur.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) n’est pas pris en charge avec un site web statique.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impact de la définition du niveau d’accès public du conteneur web

Vous pouvez modifier le niveau d’accès public du conteneur **$web**, mais cela n’a aucun impact sur le point de terminaison principal du site web statique, car ces fichiers sont pris en charge par le biais de requêtes d’accès anonyme. Cela signifie un accès public (en lecture seule) à tous les fichiers.

La capture d’écran suivante montre la définition du niveau d’accès public dans le portail Azure :

![Capture d’écran illustrant la façon de définir le niveau d’accès public dans le portail](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Si le point de terminaison principal du site web statique n’est pas affecté, en revanche, une modification du niveau d’accès public a bien une incidence sur le point de terminaison principal du service Blob.

Par exemple, si vous modifiez le niveau d’accès public du conteneur **$web** en le passant de **Privé (aucun accès anonyme)** à **Blob (accès en lecture anonyme pour les objets Blob uniquement)** , le niveau d’accès public au point de terminaison principal du site web statique `https://contosoblobaccount.z22.web.core.windows.net/index.html` ne change pas.

Toutefois, l’accès public au point de terminaison principal du service Blob `https://contosoblobaccount.blob.core.windows.net/$web/index.html` passe, lui, de privé à public. Désormais, les utilisateurs peuvent ouvrir ce fichier à l’aide, au choix, d’un de ces deux points de terminaison.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Prise en charge de la couche SSL (Secure Socket Layer) et du réseau de distribution de contenu (CDN)

Pour faire en sorte que vos fichiers de site web statique soient accessibles sur votre domaine personnalisé et HTTPS, consultez l’article [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md). Dans le cadre de ce processus, vous devez pointer votre CDN sur le point de terminaison principal du *site web statique* plutôt que sur le point de terminaison principal du *service Blob*. Vous devrez peut-être patienter quelques minutes avant que votre contenu soit visible, car la configuration du CDN n’est pas exécutée immédiatement.

Lorsque vous mettez à jour votre site web statique, veillez à effacer le contenu mis en cache sur les serveurs Edge du CDN en vidant le point de terminaison CDN. Pour plus d’informations, consultez [Purger un point de terminaison CDN Azure](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> Le protocole HTTPS étant pris en charge en mode natif via le point de terminaison web du compte, le point de terminaison web est accessible via les deux protocoles, HTTP et HTTPS. Toutefois, si le compte de stockage est configuré pour exiger un transfert sécurisé via HTTPS, les utilisateurs doivent utiliser le point de terminaison HTTPS. Pour plus d’informations, consultez [Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md).
>
> L’utilisation de domaines personnalisés sur HTTPS nécessite d’avoir recours à Azure CDN pour l’instant.

## <a name="custom-domain-names"></a>Noms de domaine personnalisés

Vous pouvez rendre votre site web statique disponible via un domaine personnalisé. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour votre compte de Stockage Azure](storage-custom-domain-name.md).

Pour découvrir la procédure détaillée d’hébergement de votre domaine sur Azure, consultez [Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Tarifs

L’activation de l’hébergement de site web statique est gratuite. Vous êtes facturé uniquement pour le stockage des objets blob qu’utilise votre site et pour les coûts d’exploitation. Pour plus de détails sur les prix du stockage Blob Azure, consultez la [page relative aux tarifs du stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mesures

Vous pouvez activer les métriques sur des pages de site web statique. Une fois les métriques activées, les statistiques de trafic relatives aux fichiers du conteneur **$web** sont signalées dans le tableau de bord des métriques.

Pour activer les métriques sur les pages de votre site web statique, consultez [Activer les métriques sur des pages de site web statique](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Étapes suivantes

* [Héberger un site web statique dans le stockage Azure](storage-blob-static-website-how-to.md)
* [Utiliser Azure CDN pour accéder aux objets Blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Générer votre première application web sans serveur](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutoriel : Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
