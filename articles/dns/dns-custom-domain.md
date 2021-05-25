---
title: Intégrer Azure DNS à vos ressources Azure – Azure DNS
description: Dans cet article, découvrez comment utiliser Azure DNS et fournir un DNS pour vos ressources Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/06/2021
ms.author: rohink
ms.openlocfilehash: c09c1840c9976a2ac14cb5ef73f8c4e15000c54f
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634934"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure

Azure DNS fournit une résolution de noms pour toutes vos ressources Azure qui prennent en charge les domaines personnalisés ou qui ont un nom de domaine complet (FQDN). Par exemple, vous avez une application web Azure à laquelle vous voulez que vos utilisateurs accèdent en utilisant `contoso.com` ou `www.contoso.com` comme FQDN. Cet article vous guide tout au long de la configuration de votre service Azure avec DNS Azure pour l’utilisation de domaines personnalisés.

## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser Azure DNS pour votre domaine personnalisé, vous devez tout d’abord déléguer votre domaine à Azure DNS. Pour obtenir des instructions sur la façon de configurer vos serveurs de noms pour la délégation, consultez [Déléguer un domaine à Azure DNS](./dns-delegate-domain-azure-dns.md). Une fois votre domaine délégué à votre zone Azure DNS, vous êtes en mesure de configurer les enregistrements DNS nécessaires.

Vous pouvez configurer un domaine personnalisé pour Applications Azure Function, Adresses IP publiques, App Service (Web Apps), Stockage Blob et Azure CDN.

## <a name="azure-function-app"></a>Application Azure Function

Pour configurer un domaine personnalisé pour des applications de fonction Azure, un enregistrement CNAME est créé et configuré sur l’application de fonction proprement dite.
 
1. Accédez à **Function App**, puis sélectionnez votre application de fonction. Sélectionnez **Domaines personnalisés** sous *Paramètres*. Notez l’**URL actuelle** sous *Domaines personnalisés attribués*. Cette adresse est utilisée comme alias pour l’enregistrement DNS créé.

    :::image type="content" source="./media/dns-custom-domain/function-app.png" alt-text="Capture d’écran des domaines personnalisés pour les applications de fonction.":::

1. Accédez à votre zone DNS, puis sélectionnez **+ Jeu d’enregistrements**. Entrez les informations suivantes sur la page **Ajouter un jeu d’enregistrements**, puis sélectionnez **OK** pour créer le jeu d’enregistrements.

    :::image type="content" source="./media/dns-custom-domain/function-app-record.png" alt-text="Capture d’écran de la page Ajouter un jeu d’enregistrements de l’application de fonction.":::

    |Propriété  |Valeur  |Description  |
    |---------|---------|---------|
    | Nom | myFunctionApp | Cette valeur et l’étiquette du nom du domaine constituent le FQDN pour le nom de domaine personnalisé. |
    | Type | CNAME | Utiliser un enregistrement CNAME équivaut à utiliser un alias. |
    | TTL | 1 | 1 est utilisé pour 1 heure  |
    | Unité de durée de vie | Heures | Les heures sont utilisées en tant que mesure du temps  |
    | Alias | contosofunction.azurewebsites.net | Nom DNS pour lequel vous créez l’alias. Dans cet exemple, il s’agit du nom DNS contosofunction.azurewebsites.net fourni par défaut à l’application de fonction.        |
    
1. Revenez à votre application de fonction, puis sélectionnez **Domaines personnalisés** sous *Paramètres*. Ensuite, sélectionnez **+ Ajouter un domaine personnalisé**.

    :::image type="content" source="./media/dns-custom-domain/function-app-add-domain.png" alt-text="Capture d’écran du bouton Ajouter un domaine personnalisé pour l’application de fonction.":::    

1. Dans la page **Ajouter un domaine personnalisé**, entrez l’enregistrement CNAME dans le champ de texte **Domaine personnalisé** et sélectionnez **Valider**. Si l’enregistrement a été trouvé, le bouton **Ajouter un domaine personnalisé** s’affiche. Sélectionnez **Ajouter un domaine personnalisé** pour ajouter l’alias.

    :::image type="content" source="./media/dns-custom-domain/function-app-cname.png" alt-text="Capture d’écran de la page Ajouter un domaine personnalisé pour l’application de fonction.":::

## <a name="public-ip-address"></a>Adresse IP publique

Pour configurer un domaine personnalisé pour des services qui utilisent une ressource d'adresse IP publique, comme Application Gateway, Load Balancer, le service cloud, les machines virtuelles Resource Manager et Classic, un enregistrement A est utilisé.

1. Accédez à la ressource IP publique et sélectionnez **Configuration**. Notez l’adresse IP affichée.

    :::image type="content" source="./media/dns-custom-domain/public-ip.png" alt-text="Capture d’écran de la page de configuration de l’IP publique.":::

1. Accédez à votre zone DNS, puis sélectionnez **+ Jeu d’enregistrements**. Entrez les informations suivantes sur la page **Ajouter un jeu d’enregistrements**, puis sélectionnez **OK** pour créer le jeu d’enregistrements.

    :::image type="content" source="./media/dns-custom-domain/public-ip-record.png" alt-text="Capture d’écran de la page Jeu d’enregistrements de l’IP publique.":::

    | Propriété | Valeur | Description |
    | -------- | ----- | ------------|
    | Nom | webserver1 | Cette valeur et l’étiquette du nom du domaine constituent le FQDN pour le nom de domaine personnalisé. |
    | Type | Un | Utiliser un enregistrement A si la ressource est une adresse IP. |
    | TTL | 1 | 1 est utilisé pour 1 heure |
    | Unité de durée de vie | Heures | Les heures sont utilisées en tant que mesure du temps |
    | Adresse IP | `<your ip address>` | Adresse IP publique. |

1. Une fois l’enregistrement A créé, exécutez `nslookup` pour valider les résolutions de l’enregistrement.

    :::image type="content" source="./media/dns-custom-domain/public-ip-nslookup.png" alt-text="Capture d’écran de nslookup dans cmd pour l’IP publique.":::

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Les étapes suivantes vous guident tout au long de la configuration d’un domaine personnalisé pour une application web de service d’applications.

1. Accédez à **App Service**, sélectionnez la ressource pour laquelle vous configurez un nom de domaine personnalisé, puis sélectionnez **Domaines personnalisés** sous *Paramètres*. Notez l’**URL actuelle** sous *Domaines personnalisés attribués*. Cette adresse est utilisée comme alias pour l’enregistrement DNS créé.

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Capture d’écran des domaines personnalisés pour l’application web.":::

1. Accédez à votre zone DNS, puis sélectionnez **+ Jeu d’enregistrements**. Entrez les informations suivantes sur la page **Ajouter un jeu d’enregistrements**, puis sélectionnez **OK** pour créer le jeu d’enregistrements.

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Capture d’écran de la page Jeu d’enregistrements de l’application web.":::

    | Propriété  | Valeur | Description |
    |---------- | ----- | ----------- |
    | Nom | mywebserver | Cette valeur et l’étiquette du nom du domaine constituent le FQDN pour le nom de domaine personnalisé. |
    | Type | CNAME | Utiliser un enregistrement CNAME équivaut à utiliser un alias. Si la ressource utilisait une adresse IP, un enregistrement A serait utilisé. |
    | TTL | 1 | 1 est utilisé pour 1 heure |
    | Unité de durée de vie | Heures | Les heures sont utilisées en tant que mesure du temps |
    | Alias | contoso.azurewebsites.net | Nom DNS pour lequel vous créez l’alias. Dans cet exemple, il s’agit du nom DNS contoso.azurewebsites.net fourni par défaut à l’application web. |

1. Revenez à votre application web, puis sélectionnez **Domaines personnalisés** sous *Paramètres*. Ensuite, sélectionnez **+ Ajouter un domaine personnalisé**.

    :::image type="content" source="./media/dns-custom-domain/web-app-add-domain.png" alt-text="Capture d’écran du bouton Ajouter un domaine personnalisé pour l’application web.":::  

1. Dans la page **Ajouter un domaine personnalisé**, entrez l’enregistrement CNAME dans le champ de texte **Domaine personnalisé** et sélectionnez **Valider**. Si l’enregistrement a été trouvé, le bouton **Ajouter un domaine personnalisé** s’affiche. Sélectionnez **Ajouter un domaine personnalisé** pour ajouter l’alias.

    :::image type="content" source="./media/dns-custom-domain/web-app-cname.png" alt-text="Capture d’écran de la page Ajouter un domaine personnalisé pour l’application web.":::

1. Une fois que le processus est terminé, exécutez **nslookup** pour vérifier que la résolution de nom fonctionne.

    :::image type="content" source="./media/dns-custom-domain/app-service-nslookup.png" alt-text="Capture d’écran de nslookup pour l’application web."::: 

Pour en savoir plus sur le mappage d’un domaine personnalisé à App Service, consultez [Mapper un nom DNS personnalisé existant à des applications web Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Pour savoir comment migrer un nom DNS actif, consultez [Migrer un nom DNS actif vers Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Si vous avez besoin d’acheter un domaine personnalisé pour votre compte App Service, consultez [Acheter un nom de domaine personnalisé pour Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md).

## <a name="blob-storage"></a>Stockage d'objets blob

Les étapes suivantes vous guident tout au long de la configuration d’un enregistrement CNAME pour un compte Stockage Blob à l’aide de la méthode asverify. Cette méthode garantit l’absence de temps d’arrêt.

1. Accédez à **Comptes de stockage**, sélectionnez votre compte de stockage, puis sélectionnez **Mise en réseau** sous *Paramètres*. Sélectionnez ensuite l’onglet **Domaine personnalisé**. Notez le nom de domaine complet à l’étape 2, ce nom est utilisé pour créer le premier enregistrement CNAME.

    :::image type="content" source="./media/dns-custom-domain/blob-storage.png" alt-text="Capture d’écran des domaines personnalisés pour le compte de stockage.":::

1. Accédez à votre zone DNS, puis sélectionnez **+ Jeu d’enregistrements**. Entrez les informations suivantes sur la page **Ajouter un jeu d’enregistrements**, puis sélectionnez **OK** pour créer le jeu d’enregistrements.

    :::image type="content" source="./media/dns-custom-domain/storage-account-record.png" alt-text="Capture d’écran de la page Jeu d’enregistrements du compte de stockage.":::

    | Propriété | Valeur | Description |
    | -------- | ----- | ----------- |
    | Nom | asverify.mystorageaccount | Cette valeur et l’étiquette du nom du domaine constituent le FQDN pour le nom de domaine personnalisé. |
    | Type | CNAME | Utiliser un enregistrement CNAME équivaut à utiliser un alias. |
    | TTL | 1 | 1 est utilisé pour 1 heure |
    | Unité de durée de vie | Heures | Les heures sont utilisées en tant que mesure du temps |
    | Alias | asverify.contoso.blob.core.windows.net | Nom DNS pour lequel vous créez l’alias. Dans cet exemple, il s’agit du nom DNS asverify.contoso.blob.core.windows.net fourni par défaut au compte de stockage. |

1. Revenez à votre compte de stockage et sélectionnez **Mise en réseau**, puis l’onglet **Domaine personnalisé**. Saisissez l’alias que vous avez créé sans le préfixe asverify dans la zone de texte, cochez la case **Utiliser la validation CNAME indirecte**, puis sélectionnez **Enregistrer**. 

    :::image type="content" source="./media/dns-custom-domain/blob-storage-add-domain.png" alt-text="Capture d’écran de la page Ajouter un domaine personnalisé du compte de stockage.":::

1. Revenez à votre zone DNS, puis créez un enregistrement CNAME sans le préfixe asverify.  Après cela, vous pouvez supprimer en toute sécurité l’enregistrement CNAME avec le préfixe asverify.

    :::image type="content" source="./media/dns-custom-domain/storage-account-record-set.png" alt-text="Capture d’écran de l’enregistrement du compte de stockage sans préfixe asverify.":::

1. Validez la résolution DNS en exécutant `nslookup`.

Pour en savoir plus sur le mappage d’un domaine personnalisé à un point de terminaison Stockage Blob, voir [Configurer un nom de domaine personnalisé pour un point de terminaison de stockage Blob](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json).

## <a name="azure-cdn"></a>Azure CDN

Les étapes suivantes vous guident tout au long de la configuration d’un enregistrement CNAME pour un point de terminaison CDN à l’aide de la méthode cdnverify. Cette méthode garantit l’absence de temps d’arrêt.

1. Accédez à votre profil CDN et sélectionnez le point de terminaison que vous utilisez. Sélectionnez **+ Domaine personnalisé**. Notez la valeur de **Nom d’hôte du point de terminaison**, car il s’agit de l’enregistrement vers lequel pointe l’enregistrement CNAME.

    :::image type="content" source="./media/dns-custom-domain/cdn.png" alt-text="Capture d’écran montrant la page Domaine personnalisé de CDN.":::

1. Accédez à votre zone DNS, puis sélectionnez **+ Jeu d’enregistrements**. Entrez les informations suivantes sur la page **Ajouter un jeu d’enregistrements**, puis sélectionnez **OK** pour créer le jeu d’enregistrements.

    :::image type="content" source="./media/dns-custom-domain/cdn-record.png" alt-text="Capture d’écran de la page Jeu d’enregistrements de CDN.":::

    | Propriété | Valeur | Description |
    | -------- | ----- | ----------- |
    | Nom | cdnverify.mycdnendpoint | Cette valeur et l’étiquette du nom du domaine constituent le FQDN pour le nom de domaine personnalisé. |
    | Type | CNAME | Utiliser un enregistrement CNAME équivaut à utiliser un alias. |
    | TTL | 1 | 1 est utilisé pour 1 heure |
    | Unité de durée de vie | Heures | Les heures sont utilisées en tant que mesure du temps |
    | Alias | cdnverify.contoso.azureedge.NET | Nom DNS pour lequel vous créez l’alias. Dans cet exemple, il s’agit du nom DNS cdnverify.contoso.azureedge.net fourni par défaut au point de terminaison CDN. |

1. Revenez à votre point de terminaison CDN et sélectionnez **+ Domaine personnalisé**. Entrez votre alias d’enregistrement CNAME sans le préfixe cdnverify, puis sélectionnez **Ajouter**.

    :::image type="content" source="./media/dns-custom-domain/cdn-add.png" alt-text="Capture d’écran de la page Ajouter un domaine personnalisé pour un point de terminaison CDN.":::

1. Revenez à votre zone DNS, puis créez un enregistrement CNAME sans le préfixe cdnverify.  Après cela, vous pouvez supprimer en toute sécurité l’enregistrement CNAME avec le préfixe cdnverify.

    :::image type="content" source="./media/dns-custom-domain/cdn-record-set.png" alt-text="Capture d’écran de l’enregistrement CDN sans préfixe cdnverify.":::

Pour plus d’informations sur le CDN et la façon de configurer un domaine personnalisé sans l’étape d’inscription intermédiaire, voir [Mapper du contenu Azure CDN à un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

Découvrir comment [Configurer des DNS inversés dans les services hébergés par Azure](dns-reverse-dns-for-azure-services.md).
