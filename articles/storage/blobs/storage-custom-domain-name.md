---
title: Configurer un nom de domaine personnalisé pour votre compte de Stockage Azure | Microsoft Docs
description: Utilisez le portail Azure pour mapper votre propre nom canonique (CNAME) au point de terminaison de stockage Blob ou web dans un compte de stockage Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 4f6776a5f15cf391f3a65aceb6e9e783d87a2078
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148928"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Configurer un nom de domaine personnalisé pour votre compte de Stockage Azure

Vous pouvez configurer un domaine personnalisé pour accéder à des données d’objets blob dans votre compte de stockage Azure. Le point de terminaison par défaut pour le stockage d’objets blob Azure est *\<storage-account-name >. blob.core.windows.net*. Vous pouvez également utiliser le point de terminaison web qui est généré dans le cadre de la [fonctionnalité de sites Web statiques](storage-blob-static-website.md). Si vous mappez un domaine personnalisé et un sous-domaine, tel que *www\.contoso.com*, au point de terminaison blob ou web pour votre compte de stockage, vos utilisateurs peuvent utiliser ce domaine pour accéder aux données de blob dans votre compte de stockage.

> [!IMPORTANT]
> Stockage Azure ne prend pas encore en charge HTTPS nativement avec des domaines personnalisés. Vous pouvez [utiliser Azure CDN pour accéder aux objets blob à l’aide des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Les comptes de stockage prennent actuellement en charge un seul nom de domaine personnalisé par compte. Vous ne pouvez pas mapper un nom de domaine personnalisé aux points de terminaison de service web et d’objet blob.
> 
> [!NOTE]
> Le mappage fonctionne uniquement pour les sous-domaines (par exemple, www\.contoso.com). Si vous souhaitez avoir votre point de terminaison web disponible sur le domaine racine (par exemple, contoso.com), vous devez ensuite [utiliser Azure CDN avec des domaines personnalisés](storage-https-custom-domain-cdn.md)

Le tableau suivant contient des exemples d’URL pour les données de l’objet blob situées dans un compte de stockage nommé *mystorageaccount*. Le sous-domaine personnalisé qui est inscrit pour le compte de stockage est *www\.contoso.com*:

| Type de ressource | URL par défaut | URL du domaine personnalisé |
| --- | --- | --- |
| Compte de stockage | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Conteneur racine | http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] ou http://mystorageaccount.[zone].web.core.windows.net/[indexdoc] ou http://mystorageaccount.[zone].web.core.windows.net/$web ou http://mystorageaccount.[zone].web.core.windows.net/ | http://www.contoso.com/$web ou http://www.contoso.com/ ou http://www.contoso.com/$web/[indexdoc] ou http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Comme indiqué dans les sections suivantes, tous les exemples pour le point de terminaison du service blob s’appliquent également au point de terminaison du service web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Direct ou intermédiaire mappage CNAME

Vous pouvez faire pointer votre domaine personnalisé est préfixé avec un sous-domaine (par exemple, www\.contoso.com) au point de terminaison blob pour votre compte de stockage de deux manières : 
* Utiliser le mappage CNAME direct.
* Utiliser le sous-domaine intermédiaire *asverify*.

### <a name="direct-cname-mapping"></a>Mappage CNAME direct

La première méthode, qui est aussi la plus simple, consiste à créer un enregistrement de nom canonique (CNAME) qui mappe votre domaine personnalisé et un sous-domaine directement au point de terminaison d’objet blob. Un enregistrement CNAME est une fonctionnalité DNS (Domain Name System) qui mappe un domaine source à un domaine de destination. Dans notre exemple, le domaine source est votre propre domaine personnalisé et un sous-domaine (*www\.contoso.com*, par exemple). Le domaine de destination est votre point de terminaison de service blob (*mystorageaccount.blob.core.windows.net*, par exemple).

La méthode directe est couverte dans la section « Inscrire un domaine personnalisé ».

### <a name="intermediary-mapping-with-asverify"></a>Mappage intermédiaire avec *asverify*

La seconde méthode utilise également des enregistrements CNAME. Pour éviter les temps d’arrêt, toutefois, elle utilise d’abord un sous-domaine spécial *asverify* reconnu par Azure.

Le mappage de votre domaine personnalisé à un point de terminaison d’objet blob peut entraîner une courte indisponibilité du domaine au moment où vous l’inscrivez dans le [portail Azure](https://portal.azure.com). Si ce domaine prend actuellement en charge une application visée par un contrat de niveau de service (SLA) interdisant toute interruption de service, utilisez le sous-domaine Azure *asverify* en tant qu’étape d’inscription intermédiaire. Cette étape garantit aux utilisateurs l’accès à votre domaine pendant que vous procédez au mappage DNS.

La méthode intermédiaire est décrite dans Inscrire un domaine personnalisé à l'aide du sous-domaine *asverify*.

## <a name="register-a-custom-domain"></a>Inscrire un domaine personnalisé
Enregistrez le domaine à l’aide de la procédure de cette section si les instructions suivantes s’appliquent :
* Vous acceptez que le domaine soit temporairement indisponible à vos utilisateurs.
* Votre domaine personnalisé n’héberge actuellement aucune application. 

Avec Azure DNS, vous pouvez configurer un nom DNS personnalisé pour votre magasin d’objets blob. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

Si votre domaine personnalisé prend actuellement en charge une application qui ne peut pas subir de temps d'arrêt, utilisez la procédure décrite à la rubrique Inscrire un domaine personnalisé à l'aide du sous-domaine *asverify*.

Pour configurer un nom de domaine personnalisé, créez un enregistrement CNAME dans DNS. L’enregistrement CNAME spécifie un alias pour un nom de domaine. Dans notre exemple, il mappe l’adresse de votre domaine personnalisé au point de terminaison de stockage blob de votre compte de stockage.

Vous pouvez généralement gérer les paramètres DNS de votre domaine sur le site web du Registre de celui-ci. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire. Comme certaines formules de base pour l’inscription de domaine n’offrent pas la configuration DNS. Avant de pouvoir créer l’enregistrement CNAME, vous devrez peut-être mettre à niveau votre formule d’inscription de domaine.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.

1. Dans le volet de menu, sous **Service blob**, sélectionnez **Domaine personnalisé**.  
   Le volet **Domaine personnalisé** s’affiche.

1. Connectez-vous au site web du Registre de votre domaine, puis accédez à la page de gestion DNS.  
   Vous pourrez trouver cette page dans une section intitulée **Domain Name**, **DNS** ou **Name Server Management**.

1. Recherchez la section relative à la gestion des enregistrements CNAME.  
   Pour cela, accédez à une page de paramètres avancés et recherchez **CNAME**, **Alias** ou **Sous-domaines**.

1. Créer un enregistrement CNAME, entrez un alias de sous-domaine tel que **www** ou **photos** (sous-domaine est obligatoire, les domaines racines ne sont pas pris en charge), puis indiquez un nom d’hôte.  
   Le nom d’hôte est votre point de terminaison de service blob. Le format est *\<mystorageaccount>.blob.core.windows.net*, où *mystorageaccount* correspond au nom de votre compte de stockage. Le nom d’hôte à utiliser s’affiche dans l’élément #1 du volet **Domaine personnalisé** dans le [portail Azure](https://portal.azure.com). 

1. Dans la zone de texte du volet **Domaine personnalisé**, entrez le nom de votre domaine personnalisé, avec le sous-domaine.  
   Par exemple, si votre domaine est *contoso.com* et votre alias de sous-domaine est *www*, entrez **www\.contoso.com**. Si votre sous-domaine est *photos*, entrez **photos.contoso.com**.

1. Pour inscrire votre domaine personnalisé, sélectionnez **Enregistrer**.  
   Si l’inscription réussit, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour.

Une fois votre nouvel enregistrement CNAME propagé via DNS, vos utilisateurs peuvent afficher les données de l’objet blob à l’aide de votre domaine personnalisé, à condition de disposer des autorisations appropriées.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Inscrire un domaine personnalisé à l’aide du sous-domaine *asverify*
Si votre domaine personnalisé prend actuellement en charge une application visée par un contrat SLA qui interdit toute interruption de service, inscrivez votre domaine personnalisé à l’aide de la procédure présentée dans cette section. En créant un enregistrement CNAME qui pointe du sous-domaine *asverify.\<subdomain>.\<customdomain>* vers le compte de stockage *asverify.\<storageaccount>.blob.core.windows.net*, vous pouvez pré-enregistrer votre domaine auprès d’Azure. Vous pouvez ensuite créer un second enregistrement CNAME qui pointe du sous-domaine *\<subdomain>.\<customdomain>* vers *\<storageaccount>.blob.core.windows.net*, afin de diriger le trafic allant vers votre domaine personnalisé vers votre point de terminaison de service blob.

Le sous-domaine *asverify* est un sous-domaine spécial reconnu par Azure. En ajoutant le sous-domaine *asverify* à votre propre sous-domaine, vous permettez à Azure de reconnaître votre domaine personnalisé sans avoir à modifier l'enregistrement DNS du domaine. Quand l’enregistrement DNS pour le domaine est modifié, il est mappé au point de terminaison d’objet blob sans aucune interruption de service.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.

1. Dans le volet de menu, sous **Service blob**, sélectionnez **Domaine personnalisé**.  
   Le volet **Domaine personnalisé** s’affiche.

1. Connectez-vous au site web de votre fournisseur DNS, puis accédez à la page de gestion DNS.  
   Vous pourrez trouver cette page dans une section intitulée **Domain Name**, **DNS** ou **Name Server Management**.

1. Recherchez la section relative à la gestion des enregistrements CNAME.  
   Pour cela, accédez à une page de paramètres avancés et recherchez **CNAME**, **Alias** ou **Sous-domaines**.

1. Créez un enregistrement CNAME, indiquez un alias de sous-domaine qui inclut le sous-domaine *asverify*, par exemple **asverify.www** ou **asverify.photos**, puis spécifiez un nom d’hôte.  
   Le nom d’hôte est votre point de terminaison de service blob. Le format est *asverify.\<mystorageaccount>.blob.core.windows.net*, où *mystorageaccount* correspond au nom de votre compte de stockage. Le nom d’hôte à utiliser s’affiche dans l’élément #2 du volet *Domaine personnalisé* dans le [portail Azure](https://portal.azure.com).

1. Dans la zone de texte du volet **Domaine personnalisé**, entrez le nom de votre domaine personnalisé, avec le sous-domaine.  
   N’incluez pas *asverify*. Par exemple, si votre domaine est *contoso.com* et votre alias de sous-domaine est *www*, entrez **www\.contoso.com**. Si votre sous-domaine est *photos*, entrez **photos.contoso.com**.

1. Cochez la case **Utiliser la validation CNAME indirecte**.

1. Pour inscrire votre domaine personnalisé, sélectionnez **Enregistrer**.  
   Si l’inscription réussit, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour. Votre domaine personnalisé a été vérifié par Azure, mais le trafic en direction de votre domaine n'est pas encore dirigé vers votre compte de stockage.

1. Retournez sur le site web du fournisseur DNS, puis créez un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison de service blob.  
   Par exemple, spécifiez le sous-domaine sous la forme *www* ou *photos* (sans *asverify*) et spécifiez le nom d’hôte sous la forme *\<mystorageaccount>.blob.core.windows.net*, où *mystorageaccount* correspond au nom de votre compte de stockage. Cette étape marque la fin de l'enregistrement de votre domaine personnalisé.

1. Pour finir, vous pouvez supprimer l’enregistrement CNAME que vous avez créé et qui contient le sous-domaine *asverify*, car il n’a plus d’utilité.

Une fois votre nouvel enregistrement CNAME propagé via DNS, vos utilisateurs peuvent afficher les données de l’objet blob à l’aide de votre domaine personnalisé, à condition de disposer des autorisations appropriées.

## <a name="test-your-custom-domain"></a>Tester votre domaine personnalisé

Pour confirmer que votre domaine personnalisé est mappé à votre point de terminaison de service blob, créez un objet blob dans un conteneur public au sein de votre compte de stockage. Dans un navigateur web, utilisez un URI au format suivant pour accéder à l’objet blob : `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Pour accéder à un formulaire web dans le conteneur *myforms* dans le sous-domaine personnalisé *photos.contoso.com*, vous pouvez par exemple utiliser l’URI suivant : `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Annuler l’inscription d’un domaine personnalisé

Pour annuler l’inscription d’un domaine personnalisé pour votre point de terminaison de stockage Blob, utilisez l’une des procédures suivantes.

### <a name="azure-portal"></a>Portail Azure

Pour supprimer le paramètre de domaine personnalisé, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage.

1. Dans le volet de menu, sous **Service blob**, sélectionnez **Domaine personnalisé**.  
   Le volet **Domaine personnalisé** s’affiche.

1. Effacez le contenu de la zone de texte contenant le nom de votre domaine personnalisé.

1. Sélectionnez le bouton **Enregistrer**.

Une fois le domaine personnalisé supprimé, une notification de portail apparaît, indiquant que votre compte de stockage a été correctement mis à jour.

### <a name="azure-cli"></a>Azure CLI

Pour supprimer une inscription de domaine personnalisé, utilisez la commande d’interface de ligne de commande [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) et spécifiez une chaîne vide (`""`) pour la valeur d’argument `--custom-domain`.

* Format de commande :

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemple de commande :

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour supprimer une inscription de domaine personnalisé, utilisez l’applet de commande PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), puis spécifiez une chaîne vide (`""`) pour la valeur d’argument `-CustomDomainName`.

* Format de commande :

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemple de commande :

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Étapes suivantes
* [Mapper un domaine personnalisé à un point de terminaison Azure CDN (Content Delivery Network)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Utiliser Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Hébergement de sites web statiques dans le stockage Blob Azure (préversion)](storage-blob-static-website.md)
