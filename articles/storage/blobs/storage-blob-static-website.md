---
title: Hébergement de sites web statiques dans le service Stockage Azure
description: L’hébergement de sites web statiques dans Stockage Azure constitue une solution évolutive économique pour l’hébergement d’applications web modernes.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427960"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hébergement de sites web statiques dans le service Stockage Azure

Vous pouvez distribuer le contenu statique (HTML, CSS, JavaScript et les fichiers image) directement à partir d’un conteneur de stockage nommé *$web*. Hébergement de votre contenu dans le stockage Azure vous permet d’utiliser des architectures sans serveur qui incluent [Azure Functions](/azure/azure-functions/functions-overview) et autres services Platform as a service (PaaS).

> [!NOTE]
> Si votre site dépend du code côté serveur, utilisez [Azure App Service](/azure/app-service/overview) à la place.

## <a name="setting-up-a-static-website"></a>Configuration d’un site Web statique

Hébergement de site Web statique est une fonctionnalité que vous devez l’activer sur le compte de stockage.

Pour activer l’hébergement de sites Web statique, sélectionnez le nom de votre fichier par défaut, puis éventuellement fournir un chemin d’accès à une page 404 personnalisée. Si un conteneur de stockage d’objets blob nommé **$web** n’existe pas déjà dans le compte, un est créé pour vous. Ajoutez les fichiers de votre site pour ce conteneur.

Pour obtenir des instructions, consultez [héberger un site Web statique dans le stockage Azure](storage-blob-static-website-how-to.md).

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Fichiers dans le **$web** conteneur respectent la casse, pris en charge par le biais des demandes de l’accès anonyme et sont disponibles uniquement par le biais d’opérations de lecture.

## <a name="uploading-content"></a>Téléchargement de contenu

Vous pouvez utiliser un de ces outils à charger du contenu à la **$web** conteneur :

> [!div class="checklist"]
> * [Interface de ligne de commande Azure](storage-blob-static-website-how-to.md#cli)
> * [Module Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extension Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Affichage de contenu

Utilisateurs peuvent afficher le contenu de site à partir d’un navigateur à l’aide de l’URL publique du site Web. Vous pouvez trouver l’URL en utilisant le portail Azure, Azure CLI ou PowerShell. Utilisez ce tableau comme guide.

|Tool| Assistance |
|----|----|
|**Portail Azure** | [Rechercher l’URL du site Web à l’aide du portail Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interface de ligne de commande Azure** | [Rechercher l’URL du site Web à l’aide de l’interface CLI Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Module Azure PowerShell** | [Rechercher l’URL du site Web à l’aide de PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L’URL de votre site contient un code régional. Par exemple, l’URL `https://contosoblobaccount.z22.web.core.windows.net/` contient le code de région `z22`.

Bien que ce code doit rester à l’URL, il est uniquement à un usage interne, et vous n’aurez pas à utiliser ce code dans une autre façon.

Le document d’index que vous spécifiez lorsque vous activez l’hébergement de site Web statique, s’affiche lorsque les utilisateurs d’ouvrir le site et ne spécifient pas un fichier spécifique (par exemple : `https://contosoblobaccount.z22.web.core.windows.net`).  

Si le serveur retourne une erreur 404, et vous n’avez spécifié un document d’erreur lorsque vous avez activé le site Web, une page 404 par défaut est retourné à l’utilisateur.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impact du paramètre de niveau d’accès public du conteneur web

Vous pouvez modifier le niveau d’accès public de la **$web** conteneur, mais cela n’a aucun impact sur le point de terminaison principal site Web statique, car ces fichiers sont pris en charge par le biais des demandes de l’accès anonyme. Cela signifie qu’un accès public (en lecture seule) à tous les fichiers.

La capture d’écran suivante montre le paramètre de niveau d’accès public dans le portail Azure :

![Capture d’écran montrant comment définir le niveau d’accès public dans le portail](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Alors que le point de terminaison principal site Web statique n’est pas affectée, une modification pour le niveau d’accès public n’affecte pas le point de terminaison de service blob principal.

Par exemple, si vous modifiez le niveau d’accès public de la **$web** conteneur à partir de **privé (aucun accès anonyme)** à **Blob (accès en lecture anonyme pour les objets BLOB uniquement)** , puis le niveau d’accès public au point de terminaison principal site Web statique `https://contosoblobaccount.z22.web.core.windows.net/index.html` ne change pas.

Toutefois, l’accès public pour le serveur principal d’objets blob point de terminaison de service `https://contosoblobaccount.blob.core.windows.net/$web/index.html` passe du cloud privé à public. Désormais les utilisateurs peuvent ouvrir ce fichier à l’aide d’un de ces deux points de terminaison.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Prise en charge de la couche SSL (Secure Socket) et de réseau de distribution de contenu (CDN)

Pour rendre vos fichiers de site Web statique sur votre domaine personnalisé et le HTTPS, consultez [à l’aide d’Azure CDN pour accéder aux objets BLOB avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md). Dans le cadre de ce processus, vous devez pointer votre CDN vers le serveur principal *site Web statique* point de terminaison par opposition à principal *service blob* point de terminaison. Vous devrez peut-être attendre quelques minutes avant que votre contenu est visible, comme la configuration du CDN n’est pas exécutée immédiatement.

Lorsque vous mettez à jour votre site Web statique, veillez à effacer le contenu mis en cache sur les serveurs de périphérie CDN en supprimant le point de terminaison CDN. Pour plus d’informations, consultez [Purger un point de terminaison CDN Azure](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS est pris en charge en mode natif via le point de terminaison web de compte, le point de terminaison web est accessible via les protocoles HTTP et HTTPS. Toutefois, si le compte de stockage est configuré pour exiger un transfert sécurisé via le protocole HTTPS, les utilisateurs doivent utiliser le point de terminaison HTTPS. Pour plus d’informations, consultez [exiger un transfert sécurisé dans le stockage Azure](../common/storage-require-secure-transfer.md).
>
> L’utilisation de domaines personnalisés via HTTPS requiert l’utilisation d’Azure CDN pour l’instant.

## <a name="custom-domain-names"></a>Noms de domaine personnalisés

Vous pouvez rendre votre site Web statique disponibles via un domaine personnalisé. Pour plus d’informations, consultez [configurer un nom de domaine personnalisé pour votre compte de stockage Azure](storage-custom-domain-name.md).

Pour un examen approfondi qui héberge votre domaine sur Azure, consultez [héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Tarifs

Vous pouvez activer l’hébergement de site Web statique gratuitement. Vous êtes facturé uniquement pour le stockage d’objets blob qui utilise votre site et les coûts d’exploitation. Pour plus de détails sur les prix du stockage Blob Azure, consultez la [page relative aux tarifs du stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>metrics

Vous pouvez activer les métriques sur les pages du site Web statique. Une fois que vous avez activé les métriques, les statistiques sur les fichiers de trafic le **$web** conteneur sont signalés dans le tableau de bord des métriques.

Pour activer les mesures sur les pages de votre site Web statique, consultez [activer les métriques sur les pages du site Web statique](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Étapes suivantes

* [Héberger un site Web statique dans le stockage Azure](storage-blob-static-website-how-to.md)
* [Utiliser Azure CDN pour accéder aux objets BLOB avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Générer votre première application web sans serveur](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutoriel : Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
