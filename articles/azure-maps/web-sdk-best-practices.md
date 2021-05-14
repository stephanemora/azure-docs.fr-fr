---
title: Meilleures pratiques pour le kit de développement logiciel (SDK) Web Azure Maps | Microsoft Azure Maps
description: Découvrez des conseils et astuces pour optimiser l’utilisation de votre kit de développement logiciel (SDK) Web Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 31db8c4d8a371aaad939e3a85972958befb2206e
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137560"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Meilleures pratiques pour le kit de développement logiciel (SDK) Web Azure Maps

Ce document se concentre sur les meilleures pratiques pour le kit de développement logiciel (SDK) Web Azure Maps, mais la plupart des meilleures pratiques et optimisations présentées peuvent être appliquées à tous les autres kits de développement logiciel (SDK) Azure Maps.

Le kit de développement logiciel (SDK) Web Azure Maps fournit un canevas puissant pour le rendu de grands jeux de données spatiales de différentes façons. Dans certains cas, il existe plusieurs façons de restituer les données de la même manière, mais selon la taille du jeu de données et les fonctionnalités souhaitées, une méthode peut être plus performante que les autres. Cet article met en évidence les meilleures pratiques et conseils et astuces pour optimiser les performances et créer une expérience utilisateur fluide.

En général, lorsque vous cherchez à améliorer les performances de la carte, recherchez le moyen de réduire le nombre de couches et de sources, ainsi que la complexité des jeux de données et des styles de rendu utilisés.

## <a name="security-basics"></a>Concepts de base de la sécurité

La partie la plus importante de votre application est sa sécurité. Si votre application n’est pas sécurisée, un pirate peut gâcher n’importe quelle application, quelle que soit la qualité de l’expérience utilisateur. Voici quelques conseils pour assurer la sécurité de votre application Azure Maps. Quand vous utilisez Azure, veillez à vous familiariser avec les outils de sécurité mis à votre disposition. Consultez ce document pour obtenir une [présentation de la sécurité Azure](../security/fundamentals/overview.md).

> [!IMPORTANT]
> Azure Maps fournit deux méthodes d’authentification.
> * Authentification par clé d’abonnement
> * L’authentification Azure Active Directory utilise Azure Active Directory dans toutes les applications de production.
> L’authentification par clé d’abonnement est simple et est utilisée par la plupart des plateformes de mappage comme méthode légère pour mesurer l’utilisation de la plateforme à des fins de facturation. Toutefois, il ne s’agit pas d’une forme sécurisée d’authentification. Elle ne doit donc être utilisée que localement lors du développement d’applications. Certaines plateformes permettent de limiter les adresses IP et/ou les référents HTTP dans les requêtes. Toutefois, ces informations peuvent facilement être usurpées. Si vous utilisez des clés d’abonnement, veillez à [les faire pivoter régulièrement](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory est un service d’identité d’entreprise qui offre une large sélection de fonctionnalités et de paramètres de sécurité pour toutes sortes de scénarios d’application. Microsoft recommande que toutes les applications de production utilisant Azure Maps utilisent Azure Active Directory pour l’authentification.
> En savoir plus sur la [gestion de l’authentification dans Azure Maps](how-to-manage-authentication.md) dans ce document.

### <a name="secure-your-private-data"></a>Sécuriser vos données privées

Lorsque des données sont ajoutées dans des kits de développement logiciel (SDK) interactifs Azure Maps, elles sont affichées localement sur l’appareil de l’utilisateur final et ne sont jamais renvoyées sur Internet.

Si votre application charge des données qui ne doivent pas être accessibles publiquement, assurez-vous que les données sont stockées dans un emplacement sécurisé, qu’elles sont accessibles de manière sécurisée et que l’application elle-même est verrouillée et n’est disponible que pour les utilisateurs de votre choix. Si l’une de ces étapes est ignorée, une personne non autorisée peut potentiellement accéder à ces données. Azure Active Directory peut vous aider à verrouiller cette défaillance.

Consultez ce tutoriel sur [ajouter l’authentification à votre application web s’exécutant sur Azure App Service](../app-service/scenario-secure-app-authentication-app-service.md)

### <a name="use-the-latest-versions-of-azure-maps"></a>Utiliser les versions les plus récentes d’Azure Maps

Les kits de développement logiciel (SDK) Azure Maps passent par des tests de sécurité réguliers, ainsi que toutes les bibliothèques de dépendances externes qui peuvent être utilisées par les kits de développement logiciel (SDK). Tout problème de sécurité connu est résolu en temps opportun et mis en production. Si votre application pointe vers la dernière version majeure de la version hébergée du kit de développement logiciel (SDK) Web Azure Maps, elle recevra automatiquement toutes les mises à jour de version mineure qui incluront des correctifs de sécurité.

Si vous auto-hébergez le kit de développement logiciel (SDK) Web Azure Maps via le module NPM, veillez à utiliser le symbole du signe insertion (^) avec le numéro de version du package Azure Maps NPM dans votre fichier `package.json`, afin qu’il pointe toujours vers la dernière version mineure.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Optimiser le chargement de mappage initial

Lors du chargement d’une page web, l’une des premières choses que vous souhaitez faire est de commencer à afficher quelque chose dès que possible afin que l’utilisateur ne s’affiche pas sur un écran vide.

### <a name="watch-the-maps-ready-event"></a>Regarder l’événement ready des mappages

De même, lorsque le mappage se charge initialement souvent, il est souhaitable de charger les données le plus rapidement possible, de sorte que l’utilisateur n’examine pas un mappage vide. Étant donné que le mappage charge les ressources de façon asynchrone, vous devez attendre qu’il soit prêt à interagir avec avant d’essayer de générer vos propres données sur celui-ci. Il existe deux événements que vous pouvez attendre, un événement `load` et un événement `ready`. L’événement de chargement est déclenché une fois que le mappage a fini de charger complètement l’affichage de la carte initiale et que chaque mosaïque a été chargée. L’événement ready se déclenche quand les ressources de mappage minimales nécessaires au démarrage de l’interaction avec le mappage. L’événement ready peut souvent se déclencher dans la moitié de l’événement de charge et vous permettre ainsi de commencer à charger vos données dans le mappage plus tôt.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Chargement différé dans le kit de développement logiciel (SDK) Web Azure Maps

Si le mappage n’est pas nécessaire immédiatement, chargez le kit de développement logiciel (SDK) Web Azure Maps en différé jusqu’à ce qu’il soit nécessaire. Cela retardera le chargement des fichiers JavaScript et CSS utilisés par le kit de développement logiciel (SDK) Web Azure Maps jusqu’à ce que cela soit nécessaire. Cela se produit généralement lorsque le mappage est chargé dans un onglet ou un panneau de menu volant qui n’est pas affiché lors du chargement de la page.
L’exemple de code suivant montre comment retarder le chargement du kit de développement logiciel (SDK) Web Azure Maps jusqu’à ce qu’un bouton soit enfoncé.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Chargement différé du mappage" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>Chargement différé de la carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Ajouter un espace réservé pour le mappage

Si le mappage prend un certain temps pour se charger en raison de limitations du réseau ou d’autres priorités au sein de votre application, envisagez d’ajouter une petite image d’arrière-plan au mappage `div` en tant qu’espace réservé pour le mappage. Cela remplit l’annulation du mappage `div` pendant le chargement.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Définir le style de mappage initial et les options de caméra lors de l’initialisation

Souvent, les applications veulent charger le mappage à un emplacement ou un style spécifique. Parfois, les développeurs attendent que le mappage soit chargé (ou attendent l'événement `ready`), puis utilisent les fonctions `setCemer` ou `setStyle` du mappage. Cela prend souvent plus de temps pour atteindre l’affichage de la carte initiale souhaitée, car de nombreuses ressources finissent par être chargées par défaut avant le chargement des ressources nécessaires pour l’affichage de la carte souhaitée. Une meilleure approche consiste à passer la caméra de mappage souhaitée et les options de style dans le mappage lors de son initialisation.

## <a name="optimize-data-sources"></a>Optimiser les sources des données

Le kit de développement logiciel (SDK) Web a deux sources de données,

* **Source GeoJSON** : connue sous le nom de classe `DataSource`, gère les données d’emplacement brut au format GeoJSON localement. Convient aux petits et moyens jeux de données (jusqu’à des centaines de milliers de fonctionnalités).
* **Source de mosaïque vectorielle** : connue sous la classe `VectorTileSource`, charge des données formatées sous forme de mosaïques vectorielles pour l’affichage actuelle de la carte, en fonction du système de mosaïque des cartes. Convient aux jeux de données volumineux ou énormes (des millions ou milliards de fonctionnalités).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Utiliser des solutions basées sur des mosaïques pour les jeux de données volumineux

Si vous utilisez des jeux de données plus volumineux contenant des millions de fonctionnalités, la méthode recommandée pour obtenir des performances optimales consiste à exposer les données à l’aide d’une solution côté serveur, telle que le service de mosaïques d’images vectorielles ou raster.  
Les mosaïques de vecteurs sont optimisées pour charger uniquement les données qui sont en affichage avec les géométries découpées vers la zone de focus de la mosaïque et généralisées pour correspondre à la résolution du mappage pour le niveau de zoom de la mosaïque.

La [plateforme Azure Maps Creator](creator-indoor-maps.md) permet de récupérer des données au format de mosaïque vectorielle. D’autres formats de données peuvent utiliser des outils tels que [Tippecanoe](https://github.com/mapbox/tippecanoe) ou l’une des nombreuses [ressources répertoriées sur cette page](https://github.com/mapbox/awesome-vector-tiles).

Il est également possible de créer un service personnalisé qui restitue des jeux de données comme des mosaïques d’images raster côté serveur et de charger les données à l’aide de la classe TileLayer dans le kit de développement logiciel (SDK) du mappage. Cela offre des performances exceptionnelles, car le mappage ne doit charger et gérer que quelques dizaines d’images au maximum. Toutefois, il existe certaines limitations relatives à l’utilisation des mosaïques raster dans la mesure où les données brutes ne sont pas disponibles localement. Un service secondaire est souvent requis pour alimenter tout type d’expérience d’interaction, par exemple, rechercher la forme sur laquelle l’utilisateur a cliqué. En outre, la taille de fichier d’une mosaïque raster est souvent supérieure à celle d’une mosaïque de vecteur compressé qui contient des géométries optimisées au niveau du zoom et généralisées.

En savoir plus sur les sources de données dans le document [Créer une source de données](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Combiner plusieurs jeux de données en une seule source de mosaïques de vecteur

Moins le mappage a à gérer de sources de données, plus il peut traiter toutes les fonctionnalités à afficher. En particulier, lorsqu’il s’agit de sources de mosaïque, la combinaison de deux sources de mosaïque vectorielle réduit le nombre de requêtes HTTP pour récupérer les mosaïques en deux parties et la quantité totale de données est légèrement inférieure, car il n’y a qu’un seul en-tête de fichier.

La combinaison de plusieurs jeux de données dans une seule source de mosaïque vectorielle peut être obtenue à l’aide d’un outil tel que [Tippecanoe](https://github.com/mapbox/tippecanoe). Les jeux de données peuvent être combinés dans une collection de fonctionnalités unique ou séparés en couches distinctes dans la mosaïque vectorielle appelée couches source. Lors de la connexion d’une source de mosaïque vectorielle à une couche de rendu, vous devez spécifier la couche source qui contient les données que vous souhaitez afficher avec la couche.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Réduire le nombre d’actualisations de canevas en raison de mises à jour de données

Il existe plusieurs façons d'ajouter ou de mettre à jour des données dans une classe `DataSource`. La liste ci-dessous répertorie les différentes méthodes et certains éléments à prendre en compte pour garantir de bonnes performances.

* La fonction de sources de données `add` peut être utilisée pour ajouter une ou plusieurs fonctionnalités à une source de données. Chaque fois que cette fonction est appelée, elle déclenche une actualisation de canevas de mappage. Si vous ajoutez de nombreuses fonctionnalités, associez-les dans un tableau ou une collection de fonctionnalités et transmettez-les une seule fois à cette fonction, au lieu d’effectuer une boucle sur un jeu de données et d’appeler cette fonction pour chaque fonctionnalité.
* La fonction de sources de données `setShapes` peut être utilisée pour remplacer toutes les formes d’une source de données. En coulisses, elle combine les fonctions de sources de données `clear` et `add` ensemble et effectue une actualisation de canevas de mappage unique au lieu de deux, ce qui est beaucoup plus rapide. Veillez à utiliser cette valeur lorsque vous souhaitez mettre à jour toutes les données d’une source de données.
* La fonction de sources de données `importDataFromUrl` peut être utilisée pour charger un fichier GeoJSON via une URL dans une source de données. Une fois que les données ont été téléchargées, elles sont transmises à la fonction de sources de données `add`. Si le fichier GeoJSON est hébergé sur un autre domaine, assurez-vous que l’autre domaine prend en charge les requêtes inter-domaines (CORs). S’il n’est pas possible de copier les données dans un fichier local sur votre domaine ou de créer un service proxy pour lequel les COR sont activés. Si le fichier est volumineux, envisagez de le convertir en une source de mosaïque vectorielle.
* Si les fonctionnalités sont encapsulées avec la classe `Shape`, les fonctions `addProperty`, `setCoordinates` et `setProperties` de la forme déclenchent toutes une mise à jour dans la source de données et une actualisation du canevas de mappage. Toutes les fonctionnalités retournées par les fonctions de sources de données `getShapes` et `getShapeById` sont automatiquement encapsulées avec la classe `Shape`. Si vous souhaitez mettre à jour plusieurs formes, il est plus rapide de les convertir au format JSON à l’aide de la fonction de sources de données `toJson`, de modifier le GeoJSON, puis de transmettre ces données à la fonction de sources de données `setShapes`.

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Évitez d’appeler la fonction Clear des sources de données inutilement

L’appel de la fonction Clear de la classe `DataSource` entraîne l’actualisation d’un canevas de mappage. Si la fonction `clear` est appelée plusieurs fois dans une ligne, un retard peut se produire lorsque le mappage attend que chaque actualisation se produise.

Cela se produit souvent dans les applications quand une application efface la source de données, télécharge de nouvelles données, efface à nouveau la source de données, puis ajoute les nouvelles données à la source de données. En fonction de l’expérience utilisateur souhaitée, les alternatives suivantes seraient préférables.

* Effacez les données avant de télécharger les nouvelles données, puis transmettez les nouvelles données dans la fonction `add` ou `setShapes` des sources de données. S’il s’agit du seul jeu de données sur le mappage, le mappage est vide pendant le téléchargement des nouvelles données.
* Téléchargez les nouvelles données, puis transmettez-les à la fonction `setShapes` de sources de données. Toutes les données du mappage sont alors remplacées.

### <a name="remove-unused-features-and-properties"></a>Supprimer les propriétés et les fonctionnalités inutilisées

Si votre jeu de données contient des fonctionnalités qui ne vont pas être utilisées dans votre application, supprimez-les. De même, supprimez toutes les propriétés des fonctionnalités qui ne sont pas nécessaires. Cela a plusieurs avantages :

* Réduit la quantité de données qui doivent être téléchargées.
* Réduit le nombre de fonctionnalités qui doivent être bouclées lors du rendu des données.
* Peut parfois aider à simplifier ou supprimer les filtres et les expressions pilotées par les données, ce qui signifie moins de traitement nécessaire au moment de l’affichage.

Lorsque les fonctionnalités possèdent un grand nombre de propriétés ou de contenu, il est beaucoup plus performant de limiter ce qui est ajouté à la source de données à celles nécessaires au rendu et de disposer d’une méthode ou d’un service distinct pour récupérer la propriété ou le contenu supplémentaire si nécessaire. Par exemple, si vous avez un mappage simple affichant des emplacements sur un mappage lorsque vous cliquez, un ensemble de contenu détaillé s’affiche. Si vous souhaitez utiliser le style piloté par les données pour personnaliser la façon dont les emplacements sont affichés sur le mappage, chargez uniquement les propriétés nécessaires dans la source de données. Lorsque vous souhaitez afficher le contenu détaillé, utilisez l’ID de la fonctionnalité pour récupérer le contenu supplémentaire séparément. Si le contenu est stocké côté serveur, un service peut être utilisé pour le récupérer de manière asynchrone, ce qui réduirait considérablement la quantité de données qui doit être téléchargée lors du chargement initial du mappage.

En outre, la réduction du nombre de chiffres significatifs dans les coordonnées des fonctionnalités peut également réduire considérablement la taille des données. Il n’est pas rare que les coordonnées contiennent au moins 12 décimales ; toutefois, six décimales ont une précision d’environ 0,1, ce qui est souvent plus précis que l’emplacement représenté par la coordonnée (six décimales sont recommandées lors de l’utilisation de petites données d’emplacement, telles que les dispositions de construction intérieures). Le fait d’avoir plus de six décimales ne fera probablement aucune différence dans la façon dont les données sont affichées et ne nécessitera que l’utilisateur pour télécharger plus de données sans aucun avantage supplémentaire.

Voici une liste d'[outils utiles pour travailler avec des données GeoJSON](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Utiliser une source de données distincte pour modifier rapidement les données

Parfois, il est nécessaire de mettre rapidement à jour les données sur la carte pour obtenir des informations sur les mises à jour en direct des données de diffusion en continu ou d’animation des fonctionnalités. Quand une source de données est mise à jour, le moteur de rendu effectue une boucle et affiche toutes les fonctionnalités dans la source de données. La séparation entre les données statiques et les données modifiées rapidement dans différentes sources de données peut réduire de manière significative le nombre de fonctionnalités qui sont à nouveau affichées à chaque mise à jour de la source de données et améliore les performances globales.

Si vous utilisez des mosaïques de vecteur avec des données actives, un moyen simple de prendre en charge les mises à jour consiste à utiliser l'en-tête de réponse `expires`. Par défaut, toute couche de mosaïques vectorielles ou de mosaïques raster recharge automatiquement les mosaïques lorsque la date est `expires`. Les mosaïques de flux de trafic et d’incident du mappage utilisent cette fonctionnalité pour garantir que les données de trafic en temps réel actualisées s’affichent sur le mappage. Cette fonctionnalité peut être définissante l’option de service `refreshExpiredTiles` du mappage sur `false`.

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Ajuster les options de mémoire tampon et de tolérance dans les sources de données GeoJSON

La classe `DataSource` convertit les données d’emplacement brutes en mosaïques vectorielles locales pour le rendu à la volée. Ces mosaïques de vecteurs locaux découpent les données brutes dans les limites de la zone de mosaïques avec un peu de mémoire tampon pour garantir un rendu lissé entre les mosaïques. Plus l’option `buffer` est faible, plus les données qui se chevauchent sont stockées dans les mosaïques de vecteur local et les performances sont meilleures, mais, plus la modification des artefacts de rendu qui se produit est importante. Essayez de peaufiner cette option pour tirer le bon mélange des performances avec des artefacts de rendu minimaux.

La classe `DataSource` a également une option `tolerance` qui est utilisée avec l’algorithme de simplification de la Douglas-Peucker lors de la réduction de la résolution des géométries à des fins de rendu. L’augmentation de cette valeur de tolérance réduira la résolution des géométries et améliorera les performances. Ajustez cette option pour obtenir la combinaison appropriée de performances et de résolution de la géométriepour votre jeu de données.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Définir l’option de zoom maximale des sources de données GeoJSON

La classe `DataSource` convertit les données d’emplacement brutes en mosaïques vectorielles locales pour le rendu à la volée. Par défaut, il effectue cette opération jusqu’au niveau de zoom 18, à partir duquel, lorsqu’il s’agit d’un zoom plus proche, il échantillonne les données des mosaïques générées pour le niveau de zoom 18. Cela fonctionne bien pour la plupart des jeux de données nécessitant une haute résolution lorsqu’ils sont zoomés à ces niveaux. Toutefois, lorsque vous travaillez avec des jeux de données qui sont plus susceptibles d’être affichés quand vous faites un zoom arrière, par exemple lors de l’affichage de polygones d’État ou de province, la définition de l’option `minZoom` de la source de données sur une valeur inférieure, `12`, réduit le calcul de la quantité, la génération de mosaïques locales et la mémoire utilisée par la source de données.

### <a name="minimize-geojson-response"></a>Réduire la réponse GeoJSON

Lorsque vous chargez des données GeoJSON à partir d’un serveur par le biais d’un service ou en chargeant un fichier plat, veillez à ce que les données soient réduites pour supprimer les espaces superflus, ce qui rend la taille du téléchargement supérieure à celle nécessaire.

### <a name="access-raw-geojson-using-a-url"></a>Accéder à un GeoJSON brut à l’aide d’une URL

Il est possible de stocker des objets GeoJSON en ligne à l’intérieur de JavaScript, mais cela utilisera beaucoup de mémoire, car les copies de ce dernier seront stockées dans la variable que vous avez créée pour cet objet et l’instance de source de données, qui le gère dans un rôle de travail séparé. Exposez le GeoJSON à votre application à l’aide d’une URL et la source de données chargera une copie unique des données directement dans le rôle de travail des sources de données.  

## <a name="optimize-rendering-layers"></a>Optimiser les couches de rendu

Les mappages Azure fournissent plusieurs couches différentes pour le rendu des données sur un mappage. Il existe plusieurs optimisations desquelles vous pouvez tirer profit pour adapter ces couches en fonction de votre scénario, des niveaux de performances accrues et de l’expérience utilisateur générale.

### <a name="create-layers-once-and-reuse-them"></a>Créer des couches une seule fois et les réutiliser

Le kit de développement logiciel (SDK) Azure Maps Web est considéré comme piloté par les données. Les données sont placées dans des sources de données qui sont ensuite connectées à des couches de rendu. Si vous souhaitez modifier les données du mappage, mettez à jour les données dans la source de données ou modifiez les options de style sur une couche. C’est souvent beaucoup plus rapide que de supprimer et de recréer des couches chaque fois qu’une modification est apportée.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Envisager la couche de bulles sur la couche de symboles

La couche de bulles affiche des points sous forme de cercles sur le mappage et peut facilement avoir leur rayon et leur style de couleur à l’aide d’une expression pilotée par les données. Étant donné que le cercle est une forme simple pour dessiner WebGL, le moteur de rendu est en mesure de le rendre plus rapide qu’une couche de symboles, qui doit charger et restituer une image. La différence de niveaux de performance de ces deux couches de rendu est perceptible lors du rendu de dizaines de milliers de points.

### <a name="use-html-markers-and-popups-sparingly"></a>Utiliser des marqueurs HTML et des fenêtres contextuelles avec modération

Contrairement à la plupart des couches des contrôles Azure Maps Web qui utilisent WebGL pour le rendu, les marqueurs HTML et les fenêtres contextuelles utilisent des éléments DOM traditionnels pour le rendu. Par conséquent, plus le nombre de marqueurs HTML et les fenêtres contextuelles ajoutés à la page est important, plus il y aura d’éléments DOM. Le niveau de performance peut se dégrader si vous ajoutez plusieurs centaines de marqueurs HTML ou de fenêtres contextuelles. Pour les jeux de données volumineux, envisagez le clustering de vos données ou l’utilisation d’une couche de symboles ou de bulles. Pour les fenêtres contextuelles, une stratégie commune consiste à créer une fenêtre contextuelle unique et à le réutiliser en mettant à jour son contenu et sa position, comme indiqué dans l’exemple ci-dessous :

<br/>

<iframe height='500' scrolling='no' title='Réutilisation d’une fenêtre contextuelle avec plusieurs épingles' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pin</a> (Réutilisation d’une fenêtre contextuelle avec plusieurs épingles) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Cela dit, si vous avez seulement quelques points à afficher sur le mappage, la simplicité des marqueurs HTML peut être préférable. En outre, les marqueurs HTML peuvent être facilement glissés si nécessaire.

### <a name="combine-layers"></a>Combiner des couches

Le mappage est capable d’afficher des centaines de couches, mais plus le nombre de couches est grand, plus il faut de temps pour afficher une scène. Une stratégie pour réduire le nombre de couches consiste à combiner des couches ayant des styles similaires ou pouvant être stylisées à l’aide de [styles pilotés par les données](data-driven-style-expressions-web-sdk.md).

Par exemple, considérez un jeu de données dans lequel toutes les fonctionnalités ont une propriété `isHealthy` qui peut avoir la valeur `true` ou `false`. Si vous créez une couche de bulles qui restitue différentes bulles de couleur en fonction de cette propriété, il existe plusieurs façons d’effectuer cette opération, comme indiqué ci-dessous, de la moins à la plus performante.

* Fractionner les données en deux sources de données en fonction de la valeur `isHealthy` et attacher une couche de bulles avec une option de couleur codée en dur à chaque source de données.
* Placez toutes les données dans une source de données unique et créez deux couches de bulles avec une option de couleur codée en dur et un filtre basé sur la propriété `isHealthy`. 
* Placez toutes les données dans une source de données unique, créez une couche de bulles unique avec une expression de style `case` pour l’option de couleur en fonction de la propriété `isHealthy`. Voici un exemple de code qui illustre cela.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Créer des animations de couche de symboles lisses

La détection de collision est activée par défaut pour les couches de symboles. Cette détection de collision vise à garantir que deux symboles ne se chevauchent pas. Les options d’icône et de texte d’une couche de symboles ont deux options,

* `allowOverlap` : spécifie que le symbole sera visible s’il entre en conflit avec d’autres symboles.
* `ignorePlacement` : spécifie que les autres symboles sont autorisés à entrer en conflit avec le symbole.

Ces deux options ont la valeur `false` par défaut. Lors de l’animation d’un symbole, les calculs de détection de collision s’exécutent sur chaque image de l’animation, ce qui peut ralentir l’animation et lui donner un aspect moins fluide. Pour lisser l’animation, définissez ces options sur `true`.

L’exemple de code suivant est un moyen simple d’animer une couche de symboles.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animation de la couche de symboles" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’<a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animation de couche de symboles</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Spécifier la plage du niveau de zoom

Si vos données répondent à l’un des critères suivants, veillez à spécifier le niveau de zoom minimal et maximal de la couche afin que le moteur de rendu puisse l’ignorer lorsqu’il est en dehors de la plage du niveau de zoom.

* Si les données proviennent d’une source de mosaïque vectorielle, les couches sources des différents types de données sont souvent disponibles uniquement par le biais d’une plage de niveaux de zoom.
* Si vous utilisez une couche de mosaïques qui n’a pas de mosaïque pour tous les niveaux de zoom de 0 à 24 et que vous souhaitez qu’elle effectue le rendu uniquement aux niveaux où elle a des mosaïques et ne tente pas de remplir les mosaïques manquantes avec des mosaïques d’autres niveaux de zoom.
* Si vous souhaitez uniquement afficher une couche à certains niveaux de zoom.
Toutes les couches comportent une option `minZoom` et `maxZoom` dans laquelle la couche sera restituée lorsqu’elle sera comprise entre ces niveaux de zoom basés sur cette logique ` maxZoom > zoom >= minZoom`.

**Exemple**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Spécifier les limites de la couche de mosaïques et la plage de zoom source

Par défaut, les couches de mosaïque chargent les mosaïques dans le monde entier. Toutefois, si le service de mosaïques en a uniquement pour une certaine zone, le mappage essaiera de les charger en dehors de cette zone. Dans ce cas, une demande pour chaque mosaïque est effectuée et attend une réponse qui peut bloquer les autres requêtes effectuées par le mappage et ralentir ainsi le rendu des autres couches. Si vous spécifiez les limites d’une couche de mosaïques, le mappage ne demande que les mosaïques qui se trouvent dans ce cadre englobant. En outre, si la couche de mosaïques est uniquement disponible entre certains niveaux de zoom, spécifiez le zoom source minimal et maximal pour la même raison.

**Exemple**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Utiliser un style de carte vide lorsque le mappage de base n’est pas visible

Si une couche est superposée sur le mappage qui couvre entièrement le mappage de base, envisagez de définir le style de mappage sur `blank` ou `blank_accessible` pour que le mappage de base ne soit pas affiché. Un scénario courant pour ce faire consiste à superposer une mosaïque de globe complet à l’absence d’opacité ou de zone transparente au-dessus du mappage de base.

### <a name="smoothly-animate-image-or-tile-layers"></a>Animer en douceur les couches d’images ou de mosaïques

Si vous souhaitez animer une série de couches d’images ou de mosaïques sur le mappage. Il est souvent plus rapide de créer une couche pour chaque image ou couche de mosaïques et de modifier l’opacité que de mettre à jour la source d’une seule couche sur chaque image d’animation. Le masquage d’une couche en affectant à l’opacité la valeur zéro et en présentant une nouvelle couche en définissant son opacité sur une valeur supérieure à zéro est beaucoup plus rapide que la mise à jour de la source dans la couche. Vous pouvez également activer/désactiver la visibilité des couches, mais veillez à définir la durée de fondu de la couche sur zéro. sinon, elle animera la couche lors de son affichage, ce qui entraînera un effet de scintillement puisque la couche précédente aura été masquée avant que la nouvelle couche ne soit visible.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Modifier la logique de détection de collision de la couche de symboles

La couche de symboles a deux options qui existent pour l’icône et le texte nommées `allowOverlap` et `ignorePlacement`. Ces deux options spécifient si l’icône ou le texte d’un symbole peut se chevaucher ou être chevauché. Quand la valeur est `false`, la couche de symboles effectue des calculs lors de l’affichage de chaque point pour voir si elle entre en conflit avec un autre symbole déjà affiché dans la couche et, le cas échéant, n’affiche pas le symbole de collision. C’est parfait pour réduire l’encombrement sur le mappage et réduire le nombre d’objets affichés. En définissant ces options sur `false`, cette logique de détection de collision est ignorée et tous les symboles sont affichés sur le mappage. Ajustez cette option pour obtenir la meilleure combinaison du niveau de performance et d’expérience utilisateur.

### <a name="cluster-large-point-data-sets"></a>Jeux de données de grands points de cluster

Lorsque vous travaillez avec de grands ensembles de points de données, vous pouvez constater que, lorsqu’ils sont rendus à certains niveaux de zoom, un grand nombre des points se chevauchent et ne sont visibles que partiellement, ou pas du tout. Le clustering consiste à regrouper des points rapprochés et de les représenter comme un point en cluster. À mesure que l’utilisateur effectue un zoom avant sur le mappage, les clusters sont scindés en leurs points individuels. Cela peut réduire de manière significative la quantité de données qui doivent être affichées, rendre le mappage moins encombré et améliorer le niveau de performance. La classe `DataSource` a des options pour le clustering local des données. En outre, de nombreux outils qui génèrent des mosaïques vectorielles ont également des options de clustering.

En outre, augmentez la taille du rayon du cluster pour améliorer le niveau de performance. Plus le rayon de cluster est grand, moins il y a de points en cluster pour assurer le suivi et l’affichage.
En savoir plus dans le [document de données du point de clustering](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Utiliser des cartes thermiques en cluster pondérées

La couche de carte thermique peut rendre facilement des dizaines de milliers de points de données. Pour les jeux de données plus volumineux, envisagez d’activer le clustering sur la source de données et d’utiliser un petit rayon de cluster et utilisez la propriété clusters `point_count` comme poids pour le mappage de la hauteur. Lorsque le rayon du cluster est limité à quelques pixels, il y aura peu de différence visuelle dans la carte thermique affichée. L’utilisation d’un rayon de cluster plus important permet d’améliorer le niveau de performance, mais peut réduire la résolution de la carte thermique affichée.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

En savoir plus sur le [Clustering et les cartes thermiques dans ce document](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Réduire la taille des ressources d’image

Des images peuvent être ajoutées au sprite d’images des mappages pour afficher des icônes dans une couche de symboles ou des modèles dans une couche de polygones. Diminuez la taille de ces images pour réduire la quantité de données qui doivent être téléchargées et la quantité d’espace qu’ils occupent dans le sprite d’images des mappages. Lorsque vous utilisez une couche de symboles qui met à l’échelle l’icône à l’aide de l’option `size`, utilisez une image qui correspond à la taille maximale d’affichage de votre plan sur le mappage et pas plus. Cela permet de s’assurer que l’icône est affichée avec une haute résolution tout en minimisant les ressources qu’elle utilise. De plus, les SVG peuvent également être utilisés sous un format de fichier plus petit pour les images d’icône simples.

## <a name="optimize-expressions"></a>Optimiser les expressions

Les [expressions de style pilotées par les données](data-driven-style-expressions-web-sdk.md) offrent beaucoup de flexibilité et de puissance pour le filtrage et le style des données sur le mappage. Les expressions peuvent être optimisées de plusieurs façons. Voici quelques astuces.

### <a name="reduce-the-complexity-of-filters"></a>Simplifier les filtres

Filtre une boucle sur toutes les données d’une source de données et vérifie si chaque filtre correspond à la logique du filtre. Si les filtres deviennent complexes, cela peut entraîner des problèmes de performances. Voici quelques stratégies possibles pour y remédier.

* Si vous utilisez des mosaïques vectorielles, divisez les données en différentes couches sources.
* Si vous utilisez la classe `DataSource`, arrêtez ces données dans des sources de données distinctes. Essayez d’équilibrer le nombre de sources de données avec la complexité du filtre. Un trop grand nombre de sources de données peut entraîner des problèmes de performances. Par conséquent, vous devrez peut-être effectuer des tests pour savoir ce qui convient le mieux à votre scénario.
* Lorsque vous utilisez un filtre complexe sur une couche, envisagez d’utiliser plusieurs couches avec des expressions de style pour réduire la complexité du filtre. Évitez de créer une série de couches avec des styles codés en dur lorsque les expressions de style peuvent être utilisées comme un grand nombre de couches, ce qui peut également entraîner des problèmes de performances.

### <a name="make-sure-expressions-dont-produce-errors"></a>S’assurer que les expressions ne produisent pas d’erreurs

Les expressions sont souvent utilisées pour générer du code afin d’effectuer des calculs ou des opérations logiques au moment du rendu. Tout comme le code dans le reste de votre application, assurez-vous que les calculs et le sens logique ne sont pas susceptibles d’engendrer des erreurs. Les erreurs dans les expressions entraînent des problèmes lors de l’évaluation de l’expression, ce qui peut entraîner des problèmes de réduction du niveau de performance et de rendu.

L’une des erreurs courantes à prendre en compte consiste à avoir une expression qui s’appuie sur une propriété de fonctionnalité qui peut ne pas exister sur toutes les fonctionnalités. Par exemple, le code suivant utilise une expression pour définir la propriété de couleur d’une couche de bulles sur la propriété `myColor` d’une fonctionnalité.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Le code ci-dessus fonctionnera bien si toutes les fonctionnalités de la source de données ont une propriété `myColor` et que la valeur de cette propriété est une couleur. Cela peut ne pas être un problème si vous disposez d’un contrôle total sur les données de la source de données et que vous savez que toutes les fonctionnalités auront une couleur valide dans une propriété `myColor`. Cela dit, pour rendre ce code protégé contre les erreurs, une expression `case` peut être utilisée avec l'expression `has` pour vérifier que la fonctionnalité possède la propriété `myColor`. Si c’est le cas, l'expression de type `to-color` peut ensuite être utilisée pour essayer de convertir la valeur de cette propriété en couleur. Si la couleur n’est pas valide, une couleur de secours peut être utilisée. Le code suivant montre comment effectuer cette opération et définit la couleur de secours sur le vert.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Ordonner des expressions booléennes de la plus à la moins spécifique

Lorsque vous utilisez des expressions booléennes qui contiennent plusieurs tests conditionnels, classez les tests conditionnels du plus au moins spécifique. En procédant ainsi, la première condition doit réduire la quantité de données pour lesquelles la deuxième condition doit être testée, réduisant ainsi le nombre total de tests conditionnels devant être effectués.

### <a name="simplify-expressions"></a>Simplifier les expressions

Les expressions peuvent être puissantes et parfois complexes. Plus l’expression est simple, plus son évaluation est rapide. Par exemple, si une comparaison simple est nécessaire, une expression comme `['==', ['get', 'category'], 'restaurant']` serait préférable à l’utilisation d’une expression de correspondance comme `['match', ['get', 'category'], 'restaurant', true, false]`. Dans ce cas, si la propriété en cours de vérification est une valeur booléenne, une expression `get` est encore plus simple `['get','isRestaurant']`.

## <a name="web-sdk-troubleshooting"></a>Résolution des problèmes du kit de développement logiciel (SDK) Web

Voici quelques conseils pour déboguer certains des problèmes courants rencontrés lors du développement avec le kit de développement logiciel (SDK) Azure Maps Web.

**Pourquoi le mappage ne s’affiche-t-il pas lorsque je charge le contrôle web ?**

Effectuez les actions suivantes :

* Vérifiez que vous avez ajouté les options d’authentification ajoutées au mappage. Si ce n’est pas le cas, le mappage est chargé avec un canevas vide, car il ne peut pas accéder aux données du mappage de base sans authentification et les erreurs 401 apparaissent dans l’onglet Réseau des outils de développement du navigateur.
* Vérifiez que vous disposez d’une connexion Internet.
* Consultez la console pour rechercher les erreurs des outils de développement du navigateur. Certaines erreurs peuvent empêcher l’affichage du mappage. Déboguez votre application.
* Assurez-vous d’utiliser un [navigateur pris en charge](supported-browsers.md).

**Toutes mes données sont affichées de l’autre côté du monde, que se passe-t-il ?**
Les coordonnées, également appelées positions, des kits de développement logiciel (SDK) Azure Maps s’alignent sur le format de la norme industrielle `[longitude, latitude]`. Ce même format est également la manière dont les coordonnées sont définies dans le schéma GeoJSON ; données principales mises en forme utilisées dans les kits de développement logiciel (SDK) Azure Maps. Si vos données s’affichent de l’autre côté du monde, il est probable que les valeurs de longitude et de latitude soient inversées dans vos informations de coordonnées/positions.

**Pourquoi les marqueurs HTML apparaissent-ils à un emplacement incorrect dans le contrôle web ?**

Points à vérifier :

* Si vous utilisez du contenu personnalisé pour le marqueur, assurez-vous que les options `anchor` et `pixelOffset` sont correctes. Par défaut, le centre en bas du contenu est aligné sur la position sur le mappage.
* Assurez-vous que le fichier CSS pour Azure Maps a été chargé.
* Inspectez l’élément DOM du marqueur HTML pour voir si une CSS de votre application s’est ajoutée au marqueur et affecte sa position.

**Pourquoi les icônes ou le texte de la couche de symboles apparaissent-ils au mauvais endroit ?**
Vérifiez que les options `anchor` et `offset` sont correctement configurées pour s’aligner sur la partie de l’image ou du texte que vous souhaitez aligner avec la coordonnée du mappage.
Si le symbole n’est simplement pas à sa place lors de la rotation du mappage, activez l'option `rotationAlignment`. Par défaut, les symboles que nous allons faire pivoter avec le point de vue des cartes s’affichent à la verticale pour l’utilisateur. Toutefois, selon votre scénario, il peut être souhaitable de verrouiller le symbole à l’orientation du mappage. Affectez à l’option `rotationAlignment` la valeur `’map’` pour effectuer cette opération.
Si le symbole n’est simplement pas à sa place lors de l’inclinaison du mappage, vérifiez l'option `pitchAlignment`. Par défaut, les symboles restent à la hauteur du point de vue de la carte, car le mappage est incliné. Toutefois, selon votre scénario, il peut être souhaitable de verrouiller le symbole à l’inclinaison du mappage. Affectez à l’option `pitchAlignment` la valeur `’map’` pour effectuer cette opération.

**Pourquoi certaines de mes données ne s’affichent-elles pas sur le mappage ?**

Points à vérifier :

* Consultez la console pour rechercher les erreurs des outils de développement du navigateur.
* Assurez-vous qu’une source de données a été créée et ajoutée au mappage et que la source de données a été connectée à une couche de rendu qui a également été ajoutée au mappage.
* Ajoutez des points d’arrêt dans votre code et parcourez-le pour vous assurer que les données sont ajoutées à la source de données et que la source de données et les couches sont ajoutées au mappage sans qu’aucune erreur ne se produise.
* Essayez de supprimer des expressions pilotées par les données de votre couche de rendu. Il est possible que l’une d’entre elles ait une erreur qui est à l’origine du problème.

**Puis-je utiliser le kit de développement logiciel (SDK) Azure Maps Web dans un iframe de bac à sable ?**

Oui. Notez que [Safari présente un bogue](https://bugs.webkit.org/show_bug.cgi?id=170075) qui empêche les iframes de bac à sable d’exécuter des rôles de travail, ce qui est requis par le kit de développement logiciel (SDK) Azure Maps Web. La solution consiste à ajouter la balise `"allow-same-origin"` à la propriété de bac à sable de l’iframe.

## <a name="get-support"></a>Obtenir de l’aide

Voici les différentes façons d’obtenir un support technique pour Azure Maps en fonction de votre problème.

**Comment faire signaler un problème de données ou un problème avec une adresse ?**

Azure Maps dispose d’un outil de commentaires sur les données dans lequel les problèmes de données peuvent être signalés et suivis. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Chaque problème soumis génère une URL unique que vous pouvez utiliser pour suivre la progression du problème de données. Le temps nécessaire à la résolution d’un problème de données varie en fonction du type de problème et de la facilité de vérification de la modification. Une fois résolues, le service de rendu verra la mise à jour dans la mise à jour hebdomadaire, tandis que d’autres services tels que le géocodage et le routage verront la mise à jour dans la mise à jour mensuelle. Des instructions détaillées sur la façon de signaler un problème de données sont fournies dans ce [document](how-to-use-feedback-tool.md).

**Comment signaler un bogue dans un service ou une API ?**

https://azure.com/support

**Où puis-je obtenir de l’aide technique pour Azure Maps ?**

En cas de lien avec le visuel Azure Maps dans Power BI : https://powerbi.microsoft.com/support/ pour tous les autres services Azure Maps : https://azure.com/support ou les forums des développeurs : https://docs.microsoft.com/answers/topics/azure-maps.html

**Comment effectuer une demande de fonctionnalité ?**

Effectuer une demande de fonctionnalité sur notre site vocal utilisateur : https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour obtenir des conseils supplémentaires sur l’amélioration de l’expérience utilisateur dans votre application.

> [!div class="nextstepaction"]
> [Rendre votre application accessible](map-accessibility.md)

En savoir plus sur la terminologie utilisée par Azure Maps et le secteur géospatial.

> [!div class="nextstepaction"]
> [Glossaire Azure Maps](glossary.md)