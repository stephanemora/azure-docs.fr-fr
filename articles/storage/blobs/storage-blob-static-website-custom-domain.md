---
title: 'Didacticiel : Activer un domaine personnalisé et SSL pour un site web statique dans Azure'
description: Découvrez comment configurer un domaine personnalisé pour l’hébergement de site web statique.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327492"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Didacticiel : Activer un domaine personnalisé et SSL pour un site web statique dans Azure

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

1. Recherchez votre compte de stockage dans le portail Azure et affichez la vue d’ensemble du compte.
1. Sélectionnez **Azure CDN** dans le menu **Service Blob** pour configurer Azure CDN.
1. Dans la section **Profil CDN**, spécifiez un profil CDN existant ou nouveau. Pour plus d’informations, consultez [Démarrage rapide : Créer un point de terminaison et un profil de réseau de distribution de contenu Azure](../../cdn/cdn-create-new-endpoint.md).
1. Spécifiez un niveau tarifaire pour le point de terminaison CDN. Ce tutoriel utilise le niveau tarifaire **Standard Akamai**, car il se propage rapidement, d’ordinaire en quelques minutes. D’autres niveaux tarifaires sont peut-être plus longs à se propager, mais peuvent aussi offrir d’autres avantages. Pour plus d’informations, consultez [Comparer les caractéristiques du produit Azure CDN](../../cdn/cdn-features.md).
1. Dans le champ **Nom du point de terminaison CDN**, spécifiez un nom pour votre point de terminaison CDN. Le point de terminaison CDN doit être unique dans tout Azure.
1. Spécifiez votre point de terminaison de site web statique dans le champ **Nom d’hôte d’origine**. Pour trouver votre point de terminaison de site web statique, accédez aux paramètres **Site web statique** de votre compte de stockage. Copiez le point de terminaison principal et collez-le dans la configuration CDN, en supprimant l’identificateur de protocole (*par exemple*, HTTPS).

    L’illustration suivante montre un exemple de configuration de point de terminaison :

    ![Capture d’écran montrant un exemple de configuration de point de terminaison CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Créez le point de terminaison CDN et attendez qu’il se propage.
1. Pour vérifier que le point de terminaison CDN est correctement configuré, cliquez dessus pour accéder à ses paramètres. Depuis la vue d’ensemble CDN de votre compte de stockage, recherchez le nom d’hôte du point de terminaison et accédez au point de terminaison, comme illustré dans l’image suivante. Le format de votre point de terminaison CDN est similaire à `https://staticwebsitesamples.azureedge.net`.

    ![Capture d’écran montrant la vue d’ensemble du point de terminaison CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Une fois la propagation du point de terminaison CDN terminée, l’accès au point de terminaison CDN affiche le contenu du fichier index.html que vous avez précédemment chargé sur votre site web statique.

1. Pour passer en revue les paramètres d’origine de votre point de terminaison CDN, accédez à **Origine** dans la section **Paramètres** de votre point de terminaison CDN. Vous verrez que le champ **Type d’origine** a la valeur *Origine personnalisée* et que le champ **Nom d’hôte d’origine** indique le point de terminaison de votre site web statique.

    ![Capture d’écran montrant les paramètres d’origine du point de terminaison CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Activer un domaine personnalisé et SSL

1. Créez un enregistrement CNAME avec votre fournisseur de noms de domaine pour rediriger votre domaine personnalisé vers le point de terminaison CDN. L’enregistrement CNAME pour le sous-domaine *www* doit être similaire à ce qui suit :

    ![Spécifier l’enregistrement CNAME pour le sous-domaine www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Dans le portail Azure, affichez les paramètres de votre point de terminaison CDN. Accédez à **Domaines personnalisés** sous **Paramètres** pour configurer le domaine personnalisé et le certificat SSL.
1. Sélectionnez **Ajouter un domaine personnalisé** et entrez votre nom de domaine, puis cliquez sur **Ajouter**.
1. Sélectionnez le nouveau mappage de domaine personnalisé pour provisionner un certificat SSL.
1. Affectez la valeur **ON** à **Domaine personnalisé HTTPS**, puis cliquez sur **Enregistrer**. La configuration de votre domaine personnalisé peut prendre plusieurs heures. Le portail indique la progression, comme l’illustre l’image suivante.

    ![Capture d’écran montrant la progression de la configuration du domaine personnalisé](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Testez le mappage de votre site web statique à votre domaine personnalisé en accédant à l’URL de votre domaine personnalisé.

Pour plus d’informations sur l’activation du protocole HTTPS pour les domaines personnalisés, consultez [Tutoriel : Configurer HTTPS sur un domaine personnalisé Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la deuxième partie de ce tutoriel, vous avez appris à configurer un domaine personnalisé avec SSL dans Azure CDN pour votre site web statique.

Pour plus d’informations sur la configuration et l’utilisation d’Azure CDN, consultez [Qu’est-ce qu’Azure CDN ?](../../cdn/cdn-overview.md)