---
title: Utiliser Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS
description: Découvrez comment intégrer le CDN Azure avec le stockage d’objets blob pour accéder aux objets blob avec des domaines personnalisés via HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392521"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Utiliser Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS

Le réseau de distribution de contenu (Azure CDN) Azure prend désormais en charge le protocole HTTPS pour les noms de domaine personnalisés. Azure CDN vous permet d’accéder aux objets blob à l’aide de votre nom de domaine personnalisé via le protocole HTTPS. Pour ce faire, activez Azure CDN sur le point de terminaison de votre objet blob ou web, puis mappez le CDN à un nom de domaine personnalisé. Une fois que vous avez terminé, Azure simplifie l’activation HTTPS pour votre domaine personnalisé via un accès en un clic et une gestion complète des certificats. Il n’y a aucune augmentation dans la tarification normale Azure CDN.

Azure CDN protège la confidentialité et l’intégrité des données de vos applications web lors de leur transit. En utilisant le protocole SSL pour assurer le trafic via HTTPS, Azure CDN garantit que vos données sont chiffrées lorsqu’elles sont envoyées sur Internet. Grâce au protocole HTTPS, Azure CDN protège vos applications web contre les attaques.

> [!NOTE]  
> Outre la prise en charge SSL pour les noms de domaine personnalisés, Azure CDN peut vous aider à faire évoluer votre application pour fournir du contenu de bande passante élevée dans le monde entier. Pour en savoir plus, consultez [Vue d’ensemble d’Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Démarrage rapide

Pour activer le protocole HTTPS pour votre point de terminaison du stockage d’objets blob personnalisé, procédez comme suit :

1.  [Intégrer un compte de stockage Azure au CDN Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Cet article vous guide dans la création d’un compte de stockage dans le portail Azure si vous ne l’avez pas déjà fait.

    > [!NOTE]  
    > Pour ajouter votre point de terminaison web de stockage pendant la préversion de la prise en charge des sites web statiques dans le stockage Azure, sélectionnez **Origine personnalisée** dans le menu déroulant **Type d’origine**. Dans le portail Azure, vous devez effectuer cette opération à partir de votre profil Azure CDN et non directement dans votre compte de stockage.

2.  [Mapper du contenu CDN Azure avec un domaine personnalisé](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Activer le protocole HTTPS sur un domaine personnalisé CDN Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signatures d’accès partagé

Par défaut, les points de terminaison de stockage d’objets blob interdisent l’accès en lecture anonyme. Si le point de terminaison de votre stockage d’objets blob est configuré de sorte que l’accès en lecture anonyme est interdit, fournissez un jeton [Signature d’accès partagé (SAP)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour chaque requête effectuée auprès de votre domaine personnalisé. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md).

Azure CDN ne respecte pas les restrictions ajoutées au jeton Signature d’accès partagé. Par exemple, tous les jetons Signature d’accès partagé expirent. Vous pouvez toujours accéder au contenu avec une signature d’accès partagé expirée jusqu'à ce que ce contenu soit supprimé des nœuds de périphérie Azure CDN. Vous pouvez contrôler la durée de mise en cache sur Azure CDN en définissant l’en-tête de réponse du cache. Pour en savoir plus, voir [Gérer l’expiration des objets blob d’Azure Storage dans Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Si vous créez deux ou plusieurs URL avec signature d’accès partagé pour le même point de terminaison d’objets blob, nous vous recommandons d’activer la chaîne de requête mise en cache pour votre CDN Azure. Cette action garantit que Azure traite chaque URL comme une entité unique. Pour plus d’informations, consultez [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirection HTTP vers HTTPS

Vous pouvez rediriger le trafic HTTP vers HTTPS. Cette opération nécessite l’utilisation de l’offre CDN Azure premium de Verizon. [Remplacez le comportement HTTP à l’aide du moteur de règles CDN Azure](../../cdn/cdn-rules-engine.md) en appliquant la règle suivante :

![Règle de redirection HTTP vers HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

Le paramètre *Cdn-endpoint-name*, que vous sélectionnez dans la liste déroulante, fait référence au nom que vous avez configuré pour votre point de terminaison du CDN. *Origin-path* désigne le chemin au sein de votre compte de stockage d’origine dans lequel est stocké votre contenu statique. Si vous hébergez tout le contenu statique dans un seul conteneur, remplacez *origin-path* par le nom de ce conteneur.

Pour approfondir vos connaissances des règles, consultez les [fonctionnalités du moteur de règles de CDN Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Tarification et facturation

Lorsque vous accédez à des objets blob via un CDN Azure, vous payez [le prix du stockage d’objets Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pour le trafic entre les nœuds de périphérie et l’origine (stockage d’objets blob). Vous payez également [le prix Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) pour l’accès aux données à partir des nœuds de périphérie.

Par exemple, supposons que vous avez un compte de stockage dans la région USA Ouest, auquel vous accédez via Azure CDN. Si une personne au Royaume-Uni tente d’accéder à un objet blob de ce compte de stockage via Azure CDN, Azure vérifie d’abord l’objet blob dans le nœud de périphérie le plus proche du Royaume-Uni. Si Azure détecte l’objet blob, il accède à une copie et utilise la tarification Azure CDN, car Azure CDN y accède. Si Azure ne trouve pas l’objet blob, il copie l’objet blob dans le nœud de périphérie. Cette action entraîne des frais de sortie et de transaction, tel que spécifié dans la tarification du stockage d’objets blob. Azure accède ensuite au fichier sur le nœud de périphérie, ce qui entraîne la facturation Azure CDN.

Sur la [page de tarification Azure CDN](https://azure.microsoft.com/pricing/details/cdn/), notez que la prise en charge HTTPS pour les noms de domaine personnalisés est uniquement disponible pour Azure CDN dans les produits Verizon Standard et Premium.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer un nom de domaine personnalisé pour un point de terminaison de stockage Blob](storage-custom-domain-name.md)
* [Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)
