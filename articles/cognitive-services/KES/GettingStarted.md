---
title: 'Exemple : Bien démarrer avec l’API Service d’exploration des connaissances'
titlesuffix: Azure Cognitive Services
description: Utilisez l’API Service d’exploration des connaissances (KES) pour créer un moteur permettant d’effectuer des recherches interactives parmi les publications académiques.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: c9c336b9aed06095849f33060ff6969e8b7bfcaf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881704"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Prendre en main la fonctionnalité Service d’exploration des connaissances

Dans cette procédure pas à pas, vous utilisez la fonctionnalité Service d’exploration des connaissances pour créer le moteur offrant une expérience de recherche interactive pour les publications académiques. Vous pouvez installer l’outil de ligne de commande, [`kes.exe`](CommandLine.md), et tous les fichiers d’exemple à partir du [Kit de développement logiciel (SDK) de la fonctionnalité Service d’exploration des connaissances](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Les exemples de publications académiques contiennent un échantillon de 1 000 documents académiques publiés par des chercheurs de Microsoft.  Chaque document est associé à un titre, une année de publication, des auteurs et des mots clés. Chaque auteur est représenté par un ID, un nom et une affiliation au moment de la publication. Chaque mot clé peut être associé à un ensemble de synonymes (par exemple, le mot clé « machines à vecteurs de support » peut être associé au synonyme « mvs »).

## <a name="define-the-schema"></a>Définir le schéma

Le schéma décrit la structure d’attribut des objets dans le domaine. Il spécifie le nom et le type de données pour chaque attribut dans un format de fichier JSON. L’exemple suivant montre le contenu par défaut du fichier *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Ici, vous définissez *Titre*, *Année* et *Mot clé* sous forme de chaîne, d’entier et de chaîne d’attribut, respectivement. Comme les auteurs sont représentés par ID, nom et affiliation, vous définissez *Auteur* en tant qu’attribut composite avec trois sous-attributs : *Author.Id*, *Author.Name* et *Author.Affiliation*.

Par défaut, les attributs prennent en charge toutes les opérations disponibles pour leur type de données, y compris *est égal à*, *starts_with* et *is_between*. Étant donné que l’ID d’auteur est utilisé uniquement en interne en tant qu’identificateur, remplacez la valeur par défaut et spécifiez *est égal à* en tant que seule opération indexée.

Pour l’attribut *Mot clé*, autorisez les synonymes qui correspondent aux valeurs de mots clés canoniques en spécifiant le fichier de synonyme *Keyword.syn* dans la définition d’attribut. Ce fichier contient une liste de paires de valeurs canoniques et de synonymes :

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Pour plus d’informations sur la définition de schéma, consultez [Schema Format](SchemaFormat.md) (Format de schéma).

## <a name="generate-data"></a>Générer des données

Le fichier de données décrit la liste des publications à indexer, avec chaque ligne spécifiant les valeurs d’attribut d’un document au [format JSON](http://json.org/).  L’exemple suivant est une ligne unique issue du fichier de données *Academic.data*, mise en forme pour une meilleure lisibilité :

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

Dans cet extrait de code, vous spécifiez les attributs *Titre* et *Année* du document sous la forme d’une chaîne JSON et d’un nombre, respectivement. Plusieurs valeurs sont représentées à l’aide de tableaux JSON. Comme l’attribut *Auteur* est un attribut composite, chaque valeur est représentée à l’aide d’un objet JSON composé de ses sous-attributs. Les attributs avec des valeurs manquantes, tels que *Mot clé* dans ce cas, doivent être exclus de la représentation JSON.

Pour différencier la probabilité de chacun des documents, spécifiez la probabilité logarithmique relative à l’aide de l’attribut *logprob* intégré. Par exemple, pour une probabilité *p* comprise entre 0 et 1, vous calculez la probabilité logarithmique correspondante à l’aide de log(*p*), où log() représente la fonction du logarithme naturel.

Pour plus d’informations, consultez [Data Format](DataFormat.md) (Format des données).

## <a name="build-a-compressed-binary-index"></a>Créer un index binaire compressé

Une fois que vous avez un fichier de schéma et un fichier de données, vous pouvez créer un index binaire compressé des objets de données à l’aide de [`kes.exe build_index`](CommandLine.md#build_index-command). Dans cet exemple, vous générez le fichier d’index *Academic.index* à partir du fichier de schéma d’entrée *Academic.schema* et du fichier de données *Academic.data*. Utilisez la commande suivante :

`kes.exe build_index Academic.schema Academic.data Academic.index`

Pour la création rapide de prototypes en dehors d’Azure, [`kes.exe build_index`](CommandLine.md#build_index-command) peut générer de petits index localement, à partir de fichiers de données contenant jusqu'à 10 000 objets. Pour les fichiers de données plus volumineux, vous pouvez exécuter la commande à partir d’une [machine virtuelle Windows dans Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), ou effectuer une génération à distance dans Azure. Pour plus d’informations, consultez Scale-up.

## <a name="use-an-xml-grammar-specification"></a>Utiliser une spécification de grammaire XML

La grammaire spécifie le jeu de requêtes en langage naturel que le service peut interpréter, ainsi que la façon dont ces requêtes en langage naturel sont traduites en expressions de requête sémantique. Dans cet exemple, vous utilisez la grammaire spécifiée dans *academic.xml* :

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Pour plus d’informations sur la syntaxe de spécification de grammaire, consultez [Grammar Format](GrammarFormat.md) (Format de grammaire).

## <a name="compile-the-grammar"></a>Compiler la grammaire

Une fois que vous avez une spécification de grammaire XML, vous pouvez la compiler dans une grammaire binaire à l’aide de [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Notez que si la grammaire importe un schéma, le fichier de schéma doit se trouver dans le même chemin que le XML de grammaire. Dans cet exemple, vous générez le fichier de grammaire binaire *Academic.grammar* à partir du fichier de grammaire XML d’entrée *Academic.xml*. Utilisez la commande suivante :

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Héberger la grammaire et l’index dans un service web

Pour la création rapide de prototypes, vous pouvez héberger la grammaire et l’index dans un service web sur l’ordinateur local, à l’aide de [`kes.exe host_service`](CommandLine.md#host_service-command). Vous pouvez ensuite accéder au service via les [API web](WebAPI.md) pour valider la conception de la grammaire et l’exactitude des données. Dans cet exemple, vous hébergez le fichier de grammaire *Academic.grammar* et le fichier d’index *Academic.index* à l’adresse http://localhost:8000/. Utilisez la commande suivante :

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Cela initie une instance locale du service web. Vous pouvez tester de façon interactive le service en vous rendant à l’adresse `http::localhost:<port>` à partir d’un navigateur. Pour plus d’informations, consultez Tester le service.

Vous pouvez également appeler directement diverses [API web](WebAPI.md) pour tester l’interprétation du langage naturel, l’achèvement de la requête, l’évaluation de la requête structurée et le calcul de l’histogramme. Pour arrêter le service, entrez « quit » dans l’invite de commandes `kes.exe host_service`, ou appuyez sur Ctrl + C. Voici quelques exemples :

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

En dehors d’Azure, [`kes.exe host_service`](CommandLine.md#host_service-command) est limité aux index de 10 000 objets maximum. D’autres limites incluent un taux d’API de 10 requêtes par seconde et un total de 1 000 requêtes avant que le processus se termine automatiquement. Pour contourner ces restrictions, exécutez la commande à partir d’une [machine Windows dans Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) ou déployez-la sur un service cloud Azure à l’aide de la commande [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Pour plus d’informations, consultez Déployer le service.

## <a name="scale-up-to-host-larger-indices"></a>Monter en puissance pour héberger des index plus grands

Lorsque vous exécutez `kes.exe` en dehors d’Azure, l’index est limité à 10 000 objets. Vous pouvez créer et héberger des index plus grands à l’aide d’Azure. Inscrivez-vous pour obtenir un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). Par ailleurs, si vous êtes abonné à Visual Studio ou MSDN, vous pouvez [activer vos avantages d’abonné](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ceux-ci vous permettent de bénéficier de crédits Azure tous les mois.

Pour autoriser `kes.exe` à accéder à un compte Azure, [téléchargez le fichier de paramètres de publication Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) depuis le Portail Azure. Si vous y êtes invité, connectez-vous au compte Azure souhaité. Enregistrez le fichier sous *AzurePublishSettings.xml* dans le répertoire de travail à partir duquel `kes.exe` s’exécute.

Pour créer et héberger des index plus grands, vous disposez de deux méthodes. La première méthode consiste à préparer le schéma et les fichiers de données dans une machine virtuelle dans Azure. Exécutez ensuite [`kes.exe build_index`](#building-index) pour créer l’index localement sur la machine virtuelle, sans aucune restriction de taille. L’index résultant peut être hébergé localement sur la machine virtuelle à l’aide de [`kes.exe host_service`](#hosting-service) pour une création de prototypes rapide. Pour des instructions détaillées, consultez le didacticiel [Démarrage rapide : Créer une machine virtuelle Windows sur le Portail Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

La deuxième méthode consiste à effectuer une génération Azure à distance, à l’aide de [`kes.exe build_index`](CommandLine.md#build_index-command) avec le paramètre `--remote`. Cela spécifie une taille de machine virtuelle Azure. Lorsque le paramètre `--remote` est spécifié, la commande crée une machine virtuelle Azure temporaire de cette taille. Il crée ensuite l’index sur la machine virtuelle, charge l’index sur le stockage d’objets blob cible et supprime la machine virtuelle à la fin. Votre abonnement Azure est facturé pour le coût de la machine virtuelle pendant la génération de l’index.

Cette fonctionnalité de génération à distance Azure permet l’exécution de [`kes.exe build_index`](CommandLine.md#build_index-command) dans n’importe quel environnement. Lorsque vous effectuez une génération à distance, les arguments de données et de schéma d’entrée peuvent être des chemins d’accès de fichier local ou des URL de [stockage d’objets blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). L’argument d’index de sortie doit être une URL de stockage d’objets blob. Pour créer un compte de stockage Azure, consultez l’article [À propos des comptes de stockage Azure](../../storage/common/storage-create-storage-account.md). Pour réussir à copier vers et depuis un stockage d’objets blob, utilisez l’utilitaire [AzCopy](../../storage/common/storage-use-azcopy.md).

Dans cet exemple, vous pouvez partir du principe que le conteneur de stockage d’objets blob suivant a déjà été créé : http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Il contient le schéma *Academic.schema*, le fichier de synonymes référencés *Keywords.syn* et le fichier de données complet *Academic.full.data*. Vous pouvez générer l’index complet à distance à l’aide de la commande suivante :

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Notez que 5 à 10 minutes peuvent être nécessaires pour configurer une machine virtuelle temporaire pour générer l’index. Pour la création rapide de prototypes, vous pouvez :
- Procéder au développement avec un jeu de données plus petit en local, sur n’importe quelle machine.
- [Créer une machine virtuelle Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) manuellement, [vous y connecter](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) via le Bureau à distance, installer le [Kit de développement logiciel (SDK) de la fonctionnalité Service d’exploration des connaissances](https://www.microsoft.com/en-us/download/details.aspx?id=51488)et exécuter [`kes.exe`](CommandLine.md) à partir de la machine virtuelle.

La pagination ralentit le processus de génération. Pour éviter la pagination, utilisez une machine virtuelle avec une quantité de RAM multipliée par trois par rapport à la taille du fichier de données pour la génération de l’index. Utilisez une machine virtuelle avec 1 Go de RAM de plus que la taille de l’index pour l’hébergement. Pour obtenir la liste des tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Windows dans Azure](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Déployer le service

Après avoir configuré une grammaire et un index, vous êtes prêt à déployer le service sur un service cloud Azure. Pour créer un service cloud Azure, consultez [Création et déploiement d’un service cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Ne spécifiez pas de package de déploiement à ce stade.  

Lorsque vous avez créé le service cloud, vous pouvez utiliser [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) pour déployer le service. Un service cloud Azure a deux emplacements de déploiement : production et préproduction. Pour un service qui reçoit le trafic des utilisateurs en direct, vous devez effectuer un déploiement initial vers l’emplacement de préproduction. Attendez que le service démarre et s’initialise lui-même. Vous pouvez ensuite envoyer quelques requêtes pour valider le déploiement et vérifier qu’il réussit les tests de base.

[Remplacez](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) les contenus de l’emplacement de préproduction par ceux de l’emplacement de production pour que le trafic en direct soit désormais dirigé vers le service nouvellement déployé. Vous pouvez répéter ce processus lors du déploiement d’une version mise à jour du service avec de nouvelles données. Comme avec tous les autres services cloud Azure, vous pouvez éventuellement utiliser le Portail Azure pour configurer [mise à l’échelle automatique](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

Dans cet exemple, vous déployez l’index *Academic* à l’emplacement de préproduction d’un service cloud existant avec les machines virtuelles *< vm_size >*. Utilisez la commande suivante :

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Pour obtenir la liste des tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Windows dans Azure](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Une fois le service déployé, vous pouvez appeler les diverses [API web](WebAPI.md) pour tester l’interprétation du langage naturel, l’achèvement de la requête, l’évaluation de la requête structurée et le calcul de l’histogramme.  

## <a name="test-the-service"></a>Testez le service

Pour déboguer un service en direct, accédez à la machine hôte à partir d’un navigateur web. Pour un service local déployé via host_service, visitez `http://localhost:<port>/`.  Pour un service cloud Azure déployé via deploy_service, visitez `http://<serviceName>.cloudapp.net/`.

Cette page contient un lien vers des informations sur les statistiques d’appel d’API de base, ainsi que la grammaire et l’index hébergés par ce service. Cette page contient également une interface de recherche interactive qui illustre l’utilisation des API web. Entrez des requêtes dans la zone de recherche pour afficher les résultats des appels d'API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) et [calchistogram](calchistogramMethod.md). La source HTML sous-jacente de cette page sert également d’exemple pour apprendre à intégrer les API web à une application, afin de créer une expérience de recherche interactive riche.


