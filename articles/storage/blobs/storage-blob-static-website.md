---
title: Hébergement de sites web statiques dans le stockage Azure (préversion) | Microsoft Docs
description: Le stockage Azure propose désormais un hébergement de sites web statiques (préversion) en fournissant une solution économique et scalable pour l’hébergement d’applications web modernes.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: df1661b5fe7a2c0e37deef5259d6b5842ed6ee5e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131607"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hébergement de sites web statiques dans le stockage Azure (préversion)
Le stockage Azure propose désormais un hébergement de sites web statiques (préversion), ce qui vous permet de déployer des applications web modernes économiques et scalables sur Azure. Sur un site web statique, les pages web contiennent du contenu statique et JavaScript ou un autre code côté client. En revanche, les sites web dynamiques dépendent du code côté serveur et peuvent être hébergés avec [Azure Web Apps](/app-service/app-service-web-overview.md).

Comment les déploiements évoluent vers des modèles économiques et élastiques, la possibilité de fournir du contenu web sans avoir à gérer de serveur est essentielle. L’introduction d’un hébergement de sites web statiques dans le stockage Azure rend tout cela possible, en activant de puissantes fonctionnalités de backend avec des architectures sans serveur qui tirent parti [d’Azure Functions](/azure-functions/functions-overview.md) et d’autres services PaaS.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Quand vous activez des sites web statiques sur votre compte de stockage, un point de terminaison de service web est créé sous la forme `<account-name>.<zone-name>.web.core.windows.net`.

Le point de terminaison de service web autorise toujours un accès en lecture anonyme, retourne des pages HTML mises en forme en réponse aux erreurs de service et autorise uniquement les opérations de lecture d’objet. Le point de terminaison de service web retourne le document d’index dans le répertoire demandé pour la racine et tous les sous-répertoires. Quand le service de stockage retourne une erreur 404, le point de terminaison web retourne un document d’erreur personnalisé si vous l’avez configuré.

Le contenu de votre site web statique est hébergé dans un conteneur spécial nommé $web. Dans le cadre du processus d’activation, $web est créé pour vous s’il n’existe pas déjà. Le contenu de $web est accessible à la racine du compte à l’aide du point de terminaison web. Par exemple `https://contoso.z4.web.core.windows.net/` retourne le document d’index que vous avez configuré pour votre site web si un document portant ce nom existe dans le répertoire racine de $web.

Lors du chargement de contenu sur votre site web, utilisez le point de terminaison de stockage Blob. Pour charger un objet blob nommé image.jpg qui est accessible à la racine du compte, utilisez l’URL `https://contoso.blob.core.windows.net/$web/image.jpg`. L’image chargée peut être affichée dans un navigateur web au point de terminaison web correspondant `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Noms de domaine personnalisés
Vous pouvez utiliser un domaine personnalisé pour héberger votre contenu web. Pour ce faire, suivez les conseils dans [Configurer un nom de domaine personnalisé pour votre compte de stockage Azure](storage-custom-domain-name.md). Pour accéder à votre site web hébergé sur un nom de domaine personnalisé via HTTPS, consultez [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Tarification et facturation
L’hébergement de sites web statiques est fourni sans coût supplémentaire. Pour plus de détails sur les prix du stockage Blob Azure, consultez la [page relative aux tarifs du stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Démarrage rapide
### <a name="azure-portal"></a>Portail Azure
Pour démarrer l’hébergement de votre application web dans le stockage Azure, vous pouvez configurer la fonctionnalité à l’aide du portail Azure et cliquer sur Site web statique (préversion) sous Paramètres dans la barre de navigation gauche. Cliquez sur Activé, puis entrez le nom du document d’index et (éventuellement) le chemin du document d’erreur personnalisé.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Chargez vos ressources web sur le conteneur $web qui a été créé dans le cadre de l’activation du site web statique. Vous pouvez effectuer cette opération directement dans le portail Azure ou tirer parti de [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des structures de répertoires entières. Veillez à inclure un document d’index avec le nom que vous avez configuré. Dans cet exemple, le nom du document est index.html.

> [!NOTE]
> Le nom du document respectant la casse, il doit correspondre exactement au nom du fichier dans le stockage.

Enfin, accédez au point de terminaison web pour tester votre site web.

## <a name="faq"></a>Forum Aux Questions
**Les sites web statiques sont-ils disponibles pour tous les types de comptes de stockage ?**  
Non, l’hébergement de sites web statiques est disponible uniquement dans les comptes de stockage standard GPv2.

**Est-ce que les règles de pare-feu et de réseau virtuel de stockage sont prises en charge sur le nouveau point de terminaison web ?**  
Oui, le nouveau point de terminaison web respecte les règles de pare-feu et de réseau virtuel configurées pour le compte de stockage.

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Générer votre première application web sans serveur](https://aka.ms/static-serverless-webapp)
