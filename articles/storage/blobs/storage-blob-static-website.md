---
title: Hébergement de sites web statiques dans le service Stockage Azure
description: L’hébergement de sites web statiques dans Stockage Azure constitue une solution évolutive économique pour l’hébergement d’applications web modernes.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/19/18
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 36b4fbac13e012de2fbef137c6637fd7e2daea8f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161291"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hébergement de sites web statiques dans le service Stockage Azure
Les comptes GPv2 du service Stockage Azure vous permettent de distribuer du contenu statique (fichiers HTML, CSS, JavaScript et image) directement à partir d’un conteneur de stockage nommé *$web*. La fonctionnalité d’hébergement dans Stockage Azure vous permet d’utiliser des architectures serverless, notamment [Azure Functions](/azure/azure-functions/functions-overview) et d’autres services PaaS.

Contrairement aux sites web statiques, les sites dynamiques qui dépendent du code côté serveur sont hébergés de manière plus efficace à l’aide [d’Azure Web Apps](/azure/app-service/app-service-web-overview).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Lorsque vous activez l’hébergement de sites web statiques sur votre compte de stockage, vous sélectionnez le nom de votre fichier par défaut, et si vous le souhaitez, vous fournissez un chemin d’accès à une page 404 personnalisée. Lorsque cette fonctionnalité est activée, un conteneur nommé *$web* est créé s’il n’existe pas déjà. 

Les fichiers présents dans le conteneur *$web* sont :

- traités par le biais de requêtes d’accès anonyme ;
- uniquement disponibles par l’intermédiaire d’opérations de lecture d’objet ;
- respecte la casse
- accessibles au web public suivant ce modèle : 
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- disponibles par le biais d’un point de terminaison de stockage Blob suivant ce modèle : 
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Vous utilisez le point de terminaison de stockage Blob pour charger les fichiers. Par exemple, le fichier chargé à l’emplacement suivant :

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

est accessible dans le navigateur à un emplacement tel que celui-ci :

```bash
https://contoso.z4.web.core.windows.net/image.png
```

Lorsqu’un nom de fichier n’est pas fourni, le nom de fichier par défaut sélectionné est utilisé à la racine et dans tous les sous-répertoires. Si le serveur renvoie une erreur 404 et que vous ne fournissez aucun chemin d’accès de document d’erreur, une page 404 par défaut est renvoyée à l’utilisateur.

## <a name="cdn-and-ssl-support"></a>Prise en charge du service CDN (réseau de distribution de contenu) et du protocole SSL

Pour faire en sorte que vos fichiers de site web statique soient accessibles sur HTTPS, consultez l’article [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md). Dans le cadre de ce processus, vous devez *pointer votre CDN sur le point de terminaison web* plutôt que sur le point de terminaison d’objet blob. Vous devrez peut-être patienter quelques minutes avant que votre contenu soit visible, car la configuration du CDN n’est pas exécutée immédiatement.


## <a name="custom-domain-names"></a>Noms de domaine personnalisés

Vous pouvez [configurer un nom de domaine personnalisé pour votre compte Stockage Azure](storage-custom-domain-name.md) afin que votre site web statique soit accessible par le biais d’un domaine personnalisé. Pour découvrir la procédure détaillée d’hébergement de votre domaine sur Azure, consultez l’article [Héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Tarifs
L’hébergement de sites web statiques est fourni sans coût supplémentaire. Pour plus de détails sur les prix du stockage Blob Azure, consultez la [page relative aux tarifs du stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Démarrage rapide

### <a name="azure-portal"></a>Portail Azure
Commencez par ouvrir le Portail Azure à l’adresse https://portal.azure.com, puis exécutez la procédure ci-après sur votre compte de stockage GPv2 :

1. Cliquez sur **Paramètres**.
2. Cliquez sur **Site web statique**.
3. Entrez un *nom de document d’index*. (Un nom courant est *index.html*.)
4. Si vous le souhaitez, entrez un *chemin d’accès de document d’erreur* pointant vers une page 404 personnalisée. (Une valeur courante est *404.html*.)

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Ensuite, chargez vos ressources dans le conteneur *$web* par le biais du Portail Azure ou de l’application [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des répertoires entiers. Veillez à inclure un fichier correspondant au *nom de document d’index* que vous avez sélectionné lors de l’activation de la fonctionnalité.

Enfin, accédez au point de terminaison web pour tester votre site web.

### <a name="azure-cli"></a>Azure CLI
Installez l’extension stockage en préversion :

```azurecli-interactive
az extension add --name storage-preview
```
Si vous disposez de plusieurs abonnements, définissez votre interface de ligne de commande sur l’abonnement du compte de stockage GPv2 que vous souhaitez activer :

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Activez la fonctionnalité. Veillez à remplacer toutes les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs :

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Interrogez l’URL de point de terminaison web :

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Chargez des objets dans le conteneur *$web* à partir d’un répertoire source :

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d $web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Déploiement

Les méthodes disponibles pour le déploiement de contenu dans un conteneur de stockage sont les suivantes :

- [AZCopy](../common/storage-use-azcopy.md)
- [Explorateur Stockage](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://code.visualstudio.com/tutorials/static-website/deploy-VSTS)
- [Extension Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

Dans tous les cas, prenez soin de copier les fichiers dans le conteneur *$web*.

## <a name="metrics"></a>Mesures

Pour activer les métriques sur vos pages de site web statique, cliquez sur **Paramètres** > **Surveillance** > **Métriques**.

Les données de métriques sont générées par raccordement aux différentes API de métriques. Le portail affiche uniquement les membres d’API utilisés dans un délai d’exécution donné afin de se focaliser exclusivement sur les membres qui renvoient des données. Pour garantir votre capacité à sélectionner le membre d’API nécessaire, la première étape consiste à étendre le délai d’exécution.

Cliquez sur le bouton de délai d’exécution, sélectionnez **Dernières 24 heures**, puis cliquez sur **Appliquer**. 

![Intervalle de temps des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Ensuite, sélectionnez **Blob** dans la liste déroulante *Espace de noms*.

![Espace de noms des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Ensuite, sélectionnez la métrique **Sortie**.

![Métrique des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Dans le sélecteur **Agrégation**, sélectionnez *Somme*.

![Agrégation des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Ensuite, cliquez sur le bouton **Ajouter un filtre**, puis choisissez **Nom API** dans le sélecteur *Propriété*.

![Nom d’API des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Enfin, cochez la case en regard de **GetWebContent** dans le sélecteur *Valeurs* pour remplir le rapport des métriques.

![Valeur GetWebContent des métriques de sites web statiques dans Stockage Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Une fois activées, les statistiques de trafic relatives aux fichiers du conteneur *$web* sont signalées dans le tableau de bord des métriques.

## <a name="faq"></a>Forum Aux Questions

**La fonctionnalité de sites web statiques est-elle disponible pour tous les types de comptes de stockage ?**  
Non, l’hébergement de sites web statiques est disponible uniquement dans les comptes de stockage standard GPv2.

**Est-ce que les règles de pare-feu et de réseau virtuel de stockage sont prises en charge sur le nouveau point de terminaison web ?**  
Oui, le nouveau point de terminaison web respecte les règles de pare-feu et de réseau virtuel configurées pour le compte de stockage.

**Le point de terminaison web respecte-t-il la casse ?**  
Oui, le point de terminaison web respecte la casse, tout comme le point de terminaison d’objet blob. 

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation d’Azure CDN pour accéder aux objets blob avec des domaines personnalisés via HTTPS](storage-https-custom-domain-cdn.md)
* [Configurer un nom de domaine personnalisé pour le point de terminaison de votre objet blob ou web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure Web Apps](/azure/app-service/app-service-web-overview)
* [Générer votre première application web sans serveur](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Didacticiel : héberger votre domaine dans Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
