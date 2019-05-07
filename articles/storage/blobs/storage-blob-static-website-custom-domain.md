---
title: 'Tutoriel : Activer un domaine personnalisé avec SSL sur un site web statique à l’aide d’Azure CDN - Stockage Azure'
description: Découvrez comment configurer un domaine personnalisé pour l’hébergement de site web statique.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 4b074c9dee93ba44659b0321ae3eee7fbea1c61f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145055"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Tutoriel : Utiliser Azure CDN pour activer un domaine personnalisé avec SSL pour un site web statique

Ce tutoriel est le deuxième d’une série. Vous y découvrirez comment activer un point de terminaison de domaine personnalisé avec SSL pour votre site web statique. 

Ce tutoriel montre comment utiliser [Azure CDN](../../cdn/cdn-overview.md) pour configurer le point de terminaison de domaine personnalisé pour votre site web statique. Avec Azure CDN, vous pouvez en même temps provisionner des certificats SSL personnalisés, utiliser un domaine personnalisé et configurer des règles de réécriture personnalisées. La configuration d’Azure CDN entraîne des frais supplémentaires, mais procure une faible latence à votre site web de n’importe où dans le monde. Azure CDN fournit également le chiffrement SSL avec votre propre certificat. Pour plus d’informations sur les prix d’Azure CDN, consultez [Tarification Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Dans ce deuxième volet, vous apprenez à :

> [!div class="checklist"]
> * Créer un point de terminaison CDN sur le point de terminaison de site web statique.
> * Activer un domaine personnalisé et SSL.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, suivez la première partie, [Tutoriel : Héberger un site web statique sur le Stockage Blob](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Créer un point de terminaison CDN sur le point de terminaison de site web statique

1. Ouvrez le [portail Azure](https://portal.azure.com/) dans votre navigateur web. 
1. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.
1. Sélectionnez **Azure CDN** dans le menu **Service Blob** pour configurer Azure CDN.
1. Dans la section **Nouveau point de terminaison**, renseignez les champs pour créer un point de terminaison CDN.
1. Entrez un nom de point de terminaison, tel que *mystaticwebsiteCDN*.
1. Entrez le domaine de votre site web en tant que nom d’hôte pour votre point de terminaison CDN.
1. Comme nom d’hôte d’origine, entrez votre point de terminaison de site web statique. Pour trouver votre point de terminaison de site web statique, accédez à la section **Site web statique** correspondant à votre compte de stockage et copiez le point de terminaison. 
1. Testez votre point de terminaison CDN en accédant à *mywebsitecdn.azureedge.net* dans votre navigateur.

## <a name="enable-custom-domain-and-ssl"></a>Activer un domaine personnalisé et SSL

1. Créez un enregistrement CNAME avec votre fournisseur de noms de domaine pour rediriger votre domaine personnalisé vers le point de terminaison CDN. L’enregistrement CNAME pour le sous-domaine *www* doit être similaire à ce qui suit :

    ![Spécifier l’enregistrement CNAME pour le sous-domaine www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Dans le portail Azure, cliquez sur le point de terminaison qui vient d’être créé pour configurer le domaine personnalisé et le certificat SSL.
1. Sélectionnez **Ajouter un domaine personnalisé** et entrez votre nom de domaine, puis cliquez sur **Ajouter**.
1. Sélectionnez le mappage de domaine personnalisé qui vient d’être créé pour provisionner un certificat SSL.
1. Affectez la valeur **ON** à **Domaine personnalisé HTTPS**. Sélectionnez **CDN géré** pour faire en sorte qu’Azure CDN gère votre certificat SSL. Cliquez sur **Enregistrer**.
1. Testez votre site web en accédant à son URL.

## <a name="next-steps"></a>Étapes suivantes

Dans la deuxième partie de ce tutoriel, vous avez appris à configurer un domaine personnalisé avec SSL dans Azure CDN pour votre site web statique.

Suivez ce lien pour en savoir plus sur l’hébergement de site web statique sur Stockage Azure.

> [!div class="nextstepaction"]
> [En savoir plus sur les sites web statiques](storage-blob-static-website.md)
