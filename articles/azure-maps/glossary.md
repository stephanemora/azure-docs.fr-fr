---
title: Glossaire Azure Maps | Microsoft Docs
description: Un glossaire comprenant des termes couramment utilisés relatifs à Azure Maps, Location Based Services et GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: resource
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d220fea64f860ebe5b660f7ebe5ad7db7aec4534
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369856"
---
# <a name="glossary"></a>Glossaire

Voici une liste de mots couramment utilisés avec Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a> **Validation de l’adresse** : le processus de vérification de l’existence d’une adresse.

<a name="advanced-routing"></a> **Routage avancé** : une collection de services qui effectuent des opérations avancées à l’aide de données de routage, telles que le calcul de distances accessibles (isochrones), les matrices de distance et les requêtes de route de traitement par lots.

<a name="aerial-imagery"></a> **Imagerie aérienne** : consultez [Imagerie satellite](#satellite-imagery). 

<a name="along-a-route-search"></a> **Au cours d’une recherche d’itinéraire** : une requête spatiale recherchant des données et qui se trouve dans une durée ou une distance de détour spécifiées d’une route.

<a name="altitude"></a> **Altitude** : la hauteur ou l’élévation verticale d’un point au-dessus d’une surface de référence. Les mesures d’altitude sont basées sur une donnée de référence donnée, telle que le niveau moyen de la mer. Voir également Élévation.

<a name="ambiguous"></a> **Ambigu** : un état d’incertitude dans la classification des données qui existe lorsqu’un objet peut être assigné de façon appropriée à deux valeurs ou plus d’un attribut donné. Par exemple, lors du géocodage de « CA », deux résultats ambigus sont retournés, « Canada » et « Californie », car « CA » peut représenter le code d’un pays ou d’un état. 

<a name="annotation"></a> **Annotation** : texte ou graphique affichés sur la carte pour fournir des informations à l’utilisateur. Une annotation peut identifier ou décrire une entité de carte, fournir des informations générales sur une zone de la carte ou sur la carte elle-même.

<a name="antimeridian"></a> **Antiméridien** : également appelé 180<sup>e</sup> méridien. Il s’agit du point d’intersection entre -180 degrés et 180 degrés de longitude. Ce qui constitue l’opposé du méridien origine sur le globe.

<a name="application-programming-interface-api"></a> **Interface de programmation d’applications (API)** : une spécification qui permet aux développeurs de créer des applications.

<a name="api-key"></a> **Clé API** : consultez Clé Azure Maps.

<a name="area-of-interest-aoi"></a> **Zone d’intérêt (AOI)** : mesure utilisée pour définir un domaine stratégique pour la production d’une carte ou d’une base de données.

<a name="asset-tracking"></a> **Suivi des ressources** : processus de suivi de l’emplacement d’une ressource telle qu’une personne, un véhicule ou un autre objet.

<a name="asynchronous-request"></a> **Requête asynchrone** : une requête HTTP qui ouvre une connexion et effectue une requête au serveur qui retourne un identificateur pour la requête asynchrone, puis ferme la connexion. Le serveur continue de traiter la requête et l’utilisateur peut en vérifier l’état à l’aide de l’identificateur. Lorsque le traitement de la requête est terminé, l’utilisateur peut télécharger la réponse. Ce type de requête est généralement utilisé pour les longs processus d’exécution.

<a name="autocomplete"></a> **Autocomplétion** : fonctionnalité dans une application qui prédit la suite d’un mot saisi par un utilisateur. 

<a name="autosuggest"></a> **Suggestion automatique** : fonctionnalité dans une application qui prédit des possibilités logiques en fonction de ce qu’utilisateur saisit.

<a name="azure-location-based-services-lbs"></a> **Azure Location Based Services (LBS)** : service précédemment appelé Azure Maps lorsqu’il était en version préliminaire.

<a name="azure-maps-key"></a> **Clé Azure Maps**: une clé Azure Maps est une chaîne unique utilisée pour authentifier l’application Azure Maps ou la demande de service d’un utilisateur. 

## <a name="b"></a>b

<a name="base-map"></a> **Carte de base** : la partie d’une application de carte qui affiche des informations de référence en arrière-plan telles que les routes, les points de repère et les frontières politiques.

<a name="batch-request"></a> **Demande de lot** : processus de combinaison de plusieurs requêtes en une seule requête.

<a name="bearing"></a> **Repère** : direction horizontale d’un point par rapport à un autre point. Elle est exprimée par un angle relatif vers le nord, de 0 degré à 360 degrés dans le sens des aiguilles d’une montre. 

<a name="boundary"></a> **Délimitation** : ligne ou polygone séparant les entités politiques adjacentes, tels que les pays, les régions et les propriétés. Une délimitation est une ligne qui peut suivre ou non des caractéristiques physiques telles que des rivières, des montagnes ou des murs.

<a name="bounds"></a> **Limites** : consultez [Cadre englobant](#bounding-box).

<a name="bounding-box"></a> **Cadre englobant** : ensemble de coordonnées utilisé pour représenter une zone rectangulaire sur la carte. 

## <a name="c"></a>C

<a name="cadastre"></a> **Cadastre** : enregistrement de terrains et de propriétés inscrits. Voir aussi [Parcelle](#parcel).

<a name="camera"></a> **Appareil photo** : dans le contexte d’un contrôle de carte interactive, un appareil photo définit le champ de vision de la carte. La fenêtre d’affichage de l’appareil photo est déterminée en fonction de plusieurs paramètres de carte : centre, niveau de zoom, tonalité, repère. 

<a name="centroid"></a> **Centroïde** : centre géométrique d’une fonctionnalité. Le centroïde d’une ligne est le point central, tandis que le centroïde d’un polygone représente le centre de sa zone.

<a name="choropleth-map"></a> **Carte choroplèthe** : une carte thématique dans laquelle des zones sont formées proportionnellement à la mesure d’une variable statistique affichée sur la carte. Par exemple, la coloration de la frontière d’un état américain selon sa population relative comparée à tous les autres états.

<a name="concave-hull"></a> **Enveloppe concave** : une forme qui représente une géométrie concave possible englobant toutes les formes dans le jeu de données spécifié. La forme ainsi générée pourrait se décrire par un ensemble de données entouré d’une bande plastique que l’on fait chauffer, ce qui crée de grandes étendues entre les points et vers d’autres points de données.

<a name="consumption-model"></a> **Modèle de consommation** : informations qui définissent le taux de consommation en carburant ou électricité d’un véhicule. Voir aussi la [documentation sur le modèle de consommation](consumption-model.md).

<a name="control"></a> **Contrôle** : composant autonome ou réutilisable consistant en une interface graphique utilisateur qui définit un ensemble de comportements pour l’interface. Par exemple, un contrôle de carte est généralement la portion de l’interface utilisateur qui charge une carte interactive.

<a name="convex-hull"></a> **Enveloppe convexe** : il s’agit d’une forme qui représente une géométrie concave possible englobant toutes les formes dans le jeu de données spécifié. La forme ainsi générée pourrait se décrire par un ensemble de données entouré d’une bande élastique.

<a name="coordinate"></a> **Coordonnée** : comprend des valeurs de longitude et latitude utilisées pour représenter un lieu sur la carte.

<a name="coordinate-system"></a> **Système de coordonnées** : infrastructure utilisée pour définir les positions des points dans l’espace en deux ou trois dimensions.

<a name="country-code"></a> **Code pays** : identificateur unique d’un pays selon la norme ISO. ISO2 est un code à deux caractères pour un pays (par exemple, US), et ISO3 est un code à trois caractères (par exemple, USA).

<a name="country-subdivision"></a> **Sous-division de pays** : sous-division de premier niveau d’un pays, couramment appelée état ou province.

<a name="country-secondary-subdivision"></a> **Sous-division secondaire de pays** : sous-division de second niveau d’un pays, couramment appelée département.

<a name="country-tertiary-subdivision"></a> **Sous-division tertiaire de pays** : sous-division de troisième niveau d’un pays, en général une zone nommée une circonscription.

<a name="cross-street"></a> **Intersection** : point où deux rues ou plus se croisent.

<a name="cylindrical-projection"></a> **Projection cylindrique** : projection qui transforme les points d’un sphéroïde ou d’une sphère sur une tangente ou un cylindre sécante. Le cylindre est découpé de haut en bas et aplati en un plan.

## <a name="d"></a>D

<a name="datum"></a> **Donnée** : caractéristiques de référence d’un système de mesure, positions d’un système de coordonnées sur une surface (donnée horizontale) ou hauteurs sur ou sous une surface (donnée verticale).

<a name="dbf-file"></a> **Fichier DBF** : format de fichier de base de données utilisé avec des fichiers de forme (SHP).

<a name="degree-minutes-seconds-dms"></a> **Degré Minutes Secondes (DMS)** : unité de mesure pour décrire la latitude et la longitude. Un degré correspond à 1/360<sup>e</sup> d’un cercle. Un degré est en plus divisé en 60 minutes, et une minute divisée en 60 secondes.

<a name="delaunay-triangulation"></a> **Triangulation de Delaunay** : technique pour créer un maillage de triangles contigus, sans chevauchement, à partir de points de jeu de données. Chaque cercle entourant un triangle ne contient aucun point du jeu de données à l’intérieur.

<a name="demographics"></a> **Données démographiques** : caractéristiques statistiques (telles que l’âge, le taux de natalité et le revenu) d’une population humaine.

<a name="destination"></a> **Destination** : point de terminaison ou emplacement dans lequel une personne voyage.

<a name="digital-elevation-model-dem"></a> **Modèle d’élévation numérique (DEM)** : jeu de données de valeurs d’élévation associées à une surface, capturées sur une zone dans des intervalles réguliers avec une donnée commune. Les modèles d’élévation numérique sont en général utilisés pour représenter le relief d’un terrain.

<a name="dijkstra's-algorithm"></a> **Algorithme de Dijkstra** : algorithme qui examine la connectivité d’un réseau pour trouver le chemin le plus court entre deux points.

<a name="distance-matrix"></a> **Matrice des distances** : matrice qui contient des informations sur la durée et la distance de trajet entre un ensemble de départs et de destinations. 

## <a name="e"></a>E

<a name="elevation"></a> **Élévation** : distance verticale d’un point ou d’un objet sur ou sous une surface de référence ou une donnée (généralement niveau moyen de la mer). L’élévation fait en général référence à la hauteur verticale de la terre.

<a name="envelope"></a> **Enveloppe** : voir [Cadre englobant](#bounding-box).

<a name="extended-postal-code"></a> **Code postal étendu** : code postal pouvant inclure des informations supplémentaires. Par exemple, aux États-Unis, les codes postaux sont constitués de 5 chiffres mais un code postal étendu, appelé zip+4, contiendra 4 chiffres supplémentaires. Ces chiffres supplémentaires servent à identifier un segment géographique au sein de la zone de livraison à cinq chiffres, comme le quartier d’une ville, un ensemble d’appartements ou une boîte de bureau de poste, ce qui aide au tri et la livraison du courrier.

<a name="extent"></a> **Étendue** : voir [Cadre englobant](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Authentification fédérée** : méthode d’authentification qui permet l’utilisation d’un mécanisme d’ouverture de session / d’authentification unique dans plusieurs applications web et mobiles. 

<a name="feature"></a> **Fonctionnalité** : un objet qui combine une géométrie et des informations de métadonnées supplémentaires. 

<a name="feature-collection"></a> **Collection de fonctionnalités** : collection d’objets de fonctionnalités.

<a name="find-along-route"></a> **Trouver sur l’itinéraire** : requête spatiale recherchant des données et qui se trouve dans une durée ou une distance de détour spécifiées d’une route.

<a name="find-nearby"></a> **Trouver à proximité** : requête spatiale qui recherche une distance linéaire fixe (à vol d’oiseau) à partir d’un point.

<a name="fleet-management"></a> **Gestion de flotte** : gestion des véhicules commerciaux tels que voitures, camions, bateaux et avions. La gestion de flotte peut comprendre un éventail de fonctions, telles que le financement de véhicule, l’entretien, la télématique (suivi et diagnostics), ainsi que la gestion du conducteur, de la vitesse, du carburant, de l’intégrité et de la sécurité. La gestion de flotte est un processus utilisé par les entreprises dont le commerce repose sur le transport afin de limiter les risques et de réduire les coûts de personnel et de transport, tout en garantissant le respect des normes gouvernementales en vigueur.

<a name="free-flow-speed"></a> **Vitesse de libre circulation** : vitesse de libre circulation prévue dans des conditions idéales. Il s’agit généralement de la limitation de vitesse.

<a name="free-form-address"></a> **Adresse de forme libre** : adresse complète représentée en une seule ligne de texte.

<a name="fuzzy-search"></a> **Recherche vague** : recherche qui accepte une chaîne de texte pouvant être une adresse ou un point d’intérêt. 

## <a name="g"></a>G

<a name="geocode"></a> **Géocode** : adresse ou emplacement converti en une coordonnée pouvant être utilisée pour afficher cet emplacement sur une carte. 

<a name="geocoding"></a> **Géocodage** : également appelé géocodage avant. Il s’agit du processus de conversion des données d’une adresse de localisation en coordonnées. 

<a name="geodesic-path"></a> **Chemin géodésique** : chemin le plus court entre deux points sur une surface courbe. Lors du rendu sur Azure Maps, ce chemin apparaît comme ligne courbe à cause de la projection de Mercator.

<a name="geofence"></a> **Limite géographique** : région géographique définie pouvant être utilisée pour déclencher des événements lorsqu’un appareil entre ou existe dans la région.

<a name="geojson"></a> **GeoJSON** : format de fichier JSON courant utilisé pour stocker des données de vecteur géographique telles que des points, des lignes et des polygones. **Remarque** : Azure Maps utilise une version étendue de GeoJSON comme [décrit ici](extend-geojson.md).

<a name="geometry"></a> **Géométrie** : représente un objet spatial, tel qu’un point, une ligne ou un polygone.

<a name="geometrycollection"></a> **GeometryCollection** : collection d’objets géométriques.

<a name="geopol"></a> **GeoPol** : fait référence à des données géopolitiques sensibles, telles que les frontières et noms de lieux contestés.

<a name="georeference"></a> **Référence géographique** : processus d’alignement des données géographiques ou des images avec un système de coordonnées connu. Ce processus comprendre des déplacements, des rotations, des mises à l’échelle ou des inclinaisons de données.

<a name="georss"></a> **GeoRSS** : extension XML pour l’ajout de données spatiales aux flux RSS.

<a name="gis"></a> **GIS** : acronyme pour « Geographic Information System » (Système d’information géographique). Terme courant utilisé pour décrire le domaine de cartographie.

<a name="gml"></a> **GML** : également appelé Geography Markup Language. Extension de fichier XML pour le stockage de données spatiales.

<a name="gps"></a> **GPS** : également appelé Global Positioning System. Il s’agit d’un système de satellites utilisé pour déterminer la position d’un appareil sur terre. Les satellites en orbite transmettent des signaux permettant à un récepteur GPS situé n’importe où sur terre de calculer sa position par trilatération.

<a name="gpx"></a> **GPX** : également connu comme format d’échange GPS. Il s’agit d’un format de fichier XML couramment créé à partir de périphériques GPS.  

<a name="great-circle-distance"></a> **Distance orthodromique** : la distance la plus courte entre deux points sur la surface d’une sphère.

<a name="greenwich-mean-time-gmt"></a> **Heure moyenne de Greenwich (GMT)** : l’heure au méridien d’origine, indiquée à l’Observatoire royal de Greenwich, en Angleterre.

<a name="guid"></a> **GUID** : identificateur global unique. Une chaîne utilisée pour identifier uniquement une interface, une classe, une bibliothèque de types, une catégorie de composant ou un enregistrement.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Formule de Haversine** : équation courante utilisée pour calculer la distance orthodromique entre deux points sur une sphère.

<a name="hd-maps"></a> **Cartes HD** : aussi appelées cartes haute définition. Elles comprennent des informations du réseau de circulation fidèles, telles que les marquages au sol, la signalisation et clignotants, nécessaires pour une conduite autonome.

<a name="heading"></a> **Direction** : direction dans laquelle pointe un élément, ou à laquelle un élément fait face. Voir aussi [Repère](#heading).

<a name="heatmap"></a> **Carte thermique** : visualisation des données dans laquelle une gamme de couleurs représente la densité des points dans une zone particulière. Voir aussi [Carte thématique](#thermatic-map).

<a name="hybrid-imagery"></a> **Imagerie hybride** : imagerie aérienne ou satellite avec des données et des étiquettes de route superposées.

## <a name="i"></a>I

<a name="iana"></a> **IANA** : acronyme pour Internet Assigned Numbers Authority. Un groupe à but non lucratif qui supervise la gestion de l’espace d’adressage IP d’Internet.

<a name="isochrone"></a> **Isochrone** : un isochrone définit la zone dans laquelle une personne peut voyager dans un délai spécifié et pour un mode de transport particulier, dans n’importe quelle direction, à partir d’un emplacement donné. Voir aussi [Distance accessible](#reachable-range).

<a name="isodistance"></a> **Isodistance** : à partir d’un emplacement donné, un isochrone définit la zone dans laquelle une personne peut voyager dans un délai spécifié et pour un mode de transport particulier, dans n’importe quelle direction. Voir aussi [Distance accessible](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML** : également appelé Keyhole Markup Language. Il s’agit d’un format de fichier XML commun pour le stockage de données de vecteur géographique telles que les points, les lignes et les polygones. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: satellites multi-spectraux en orbite autour de la Terre, développés par la NASA, qui collectent des images terrestres utilisées par plusieurs domaines comme l’agriculture, la sylviculture et la cartographie.

<a name="latitude"></a> **Latitude** : distance angulaire mesurée en degrés à partir de l’Équateur dans une direction nord ou sud.

<a name="level-of-detail"></a> **Niveau de détail** : consultez [un Niveau de zoom](#zoom-level).

<a name="lidar"></a> **Lidar** : acronyme pour « Light detection and ranging », ou détection et localisation par la lumière. Une technique de détection à distance qui utilise des lasers pour mesurer des distances sur des surfaces réfléchissantes.

<a name="linear-interpolation"></a> **Interpolation linéaire** : estimation d’une valeur inconnue à l’aide de la distance linéaire entre des valeurs connues.

<a name="linestring"></a> **LineString** : géométrie utilisée pour représenter une ligne. Aussi appelée polyligne. 

<a name="localization"></a> **Localisation** : assistance pour différentes langues et cultures.

<a name="logistics"></a> **Logistique** : processus de déplacement des personnes, des véhicules, des fournitures ou de ressources de façon coordonnée.

<a name="longitude"></a> **Longitude** : distance angulaire mesurée en degrés à partir du méridien d’origine dans une direction est ou ouest.

## <a name="m"></a>M

<a name="map-tile"></a> **Vignette de la carte** : image rectangulaire qui représente une partition d’un canevas de carte. Pour plus d’informations, consultez la [documentation Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marqueur** : également appelé épingle ou repère. Il s’agit d’une icône qui représente un emplacement sur une carte.

<a name="mercator-projection"></a> **Projection Mercator** : projection cartographique cylindrique devenue la norme en termes de projection cartographique en mer en raison de sa capacité à représenter des lignes de cap régulières, aussi appelées lignes de rhumb, comme des segments droits qui conservent l’angle avec les méridiens. Toutes les projections cartographiques plates déforment les formes et les tailles d’une carte lorsqu’elles sont comparées à la surface de la Terre. La projection Mercator exagère les zones éloignées de l’équateur, de telle façon que des zones plus petites apparaissent grandes sur la carte au fur et à mesure que vous vous rapprochez des pôles. 

<a name="multilinestring"></a> **MultiLineString** : géométrie qui représente une collection d’objets LineString. 

<a name="multipoint"></a> **MultiPoint** : géométrie qui représente une collection d’objets Points.

<a name="multipolygon"></a> **MultiPolygon** : géométrie qui représente une collection d’éléments Polygon. Par exemple, pour afficher la délimitation d’Hawaï, chaque île serait contournée par un polygone, et la délimitation d’Hawaï serait donc MultiPolygon.

<a name="municipality"></a> **Municipalité** : ville ou village. 

<a name="municipality-subdivision"></a> **Sous-division d’une municipalité** : sous-division d’une municipalité , telle qu’un voisinage ou le nom d’un endroit tel que « centre ville ».

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barre de navigation** : ensemble des contrôles d’une carte utilisés pour ajuster le niveau de zoom, la tonalité, la rotation et le basculement de la couche de base.

<a name="nearby-search"></a> **Recherche à proximité** : requête spatiale qui recherche une distance linéaire fixe (à vol d’oiseau) à partir d’un point.

<a name="neutral-ground-truth"></a> **Véritable terrain neutre** : carte qui affiche des étiquettes dans la langue officielle de la région qu’elle représente et dans les scripts locaux si disponibles.

## <a name="o"></a>O

<a name="origin"></a> **Origine** : point ou emplacement de départ dans lequel se trouve un utilisateur.

## <a name="p"></a>P

<a name="panning"></a> **Panoramique** : processus de déplacement de la carte dans n’importe quelle direction tout en conservant un niveau de zoom constant.

<a name="parcel"></a> **Parcelle** : délimitation d’un terrain vague ou d’une propriété.

<a name="pitch"></a> **Tonalité** : l’inclinaison totale de la carte par rapport à l’axe vertical où 0 pointe tout droit vers le bas de la carte.

<a name="point"></a> **Point** : géométrie qui représente une position unique sur la carte. 

<a name="points-of-interest-poi"></a> **Points d’intérêt (POI)** : entreprise, monument ou lieu commun d’intérêt.

<a name="polygon"></a> **Polygone** : géométrie solide qui représente une zone sur une carte. 

<a name="polyline"></a> **Polyligne** : géométrie utilisée pour représenter une ligne. Également appelée LineString. 

<a name="position"></a> **Position** : longitude, latitude et altitude (coordonnées x, y, z) d’un point.

<a name="post-code"></a> **Code postal** : consultez [Code Postal](#postal-code).

<a name="postal-code"></a> **Code postal** : série de lettres et/ou de chiffres, dans un format spécifique, utilisée par le service postal d’un pays pour diviser des zones géographiques en zones afin de simplifier la distribution du courrier.

<a name="prime-meridian"></a> **Méridien d’origine** : ligne de longitude qui représente 0 degré de longitude. En règle générale, les valeurs de longitude diminuent en se déplaçant vers l’ouest jusqu’à 180 degrés, et augmentent en direction de l’est jusqu’à 180 degrés. 

<a name="prj"></a> **PRJ** : fichier texte qui accompagne souvent un fichier de forme qui contient des informations sur le système de coordonnées projetées dans lequel se trouve le jeu de données.

<a name="projection"></a> **Projection** : un système de coordonnées projetées selon une projection cartographique comme Mercator transverse, Albers et Robinson. Elles permettent la projection de cartes de la surface sphérique de la Terre sur un plan de coordonnées Cartésiennes en deux dimensions. Les systèmes de coordonnées projetées sont parfois appelés projections cartographiques.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey** : index d’adresse base-4 pour une vignette au sein d’un système de mosaïque Quadtree. Pour plus d’informations, consultez la documentation [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Quadtree** : structure de données dans laquelle chaque nœud possède exactement quatre enfants. Le système de mosaïque utilisé dans Azure Maps emploie une structure Quadtree. Ainsi, lorsqu’un utilisateur zoome sur un niveau, chaque vignette de la carte se divise en quatre vignettes secondaires.  Pour plus d’informations, consultez la documentation [Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Requêtes par seconde (RPS)** : nombre de requêtes ou de demandes qui peuvent être soumises à un service ou une plateforme en l’espace d’une seconde. 

## <a name="r"></a>R

<a name="radial-search"></a> **Recherche radiale** : requête spatiale qui recherche une distance linéaire fixe (à vol d’oiseau) à partir d’un point. 

<a name="raster-data"></a> **Données raster** : matrice de cellules (ou pixels) organisées en lignes et en colonnes (ou en grille) où chaque cellule contient une valeur représentant une information, comme la température. Les données Raster incluent des photographies aériennes numériques, des images satellites, des images numériques et des cartes scannées.

<a name="raster-layer"></a> **Couche Raster** : couche de mosaïques qui se compose d’images raster.

<a name="reachable-range"></a> **Distance accessible** : une distance accessible définit la zone dans laquelle une personne peut voyager dans un délai spécifié et pour un mode de transport particulier, dans n’importe quelle direction, à partir d’un emplacement donné. Voir aussi [Isochrone](#isochrone) et [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Détection à distance** : processus de collecte et d’interprétation des données de capteur à distance.

<a name="rest-service"></a> **Service REST** : l’acronyme REST signifie Representational State Transfer. Un service REST est un service web basé sur l’URL qui s’appuie sur la technologie web de base pour communiquer. Les méthodes les plus courantes sont les requêtes HTTP GET et POST. Ces types de services ont tendance à être plus rapides et petits que les services traditionnels SOAP.

<a name="reverse-geocode"></a> **Géocode inversé** : processus consistant à prendre une coordonnée et à déterminer l’adresse qu’elle indique sur une carte.

<a name="reproject"></a> **Reprojeter** : voir [Transformation](#transformation).

<a name="rest-service"></a> **Service REST** : acronyme pour Representational State Transfer. Une architecture pour l’échange d’informations entre pairs dans un environnement distribué et décentralisé. Le service REST permet aux programmes sur différents ordinateurs de communiquer indépendamment du système d’exploitation ou de la plateforme en envoyant une requête HTTP à une URL, et de récupérer les données.

<a name="route"></a> **Itinéraire** : chemin entre deux lieux ou plus, qui peut également inclure des informations supplémentaires telles que des instructions pour les points de cheminement le long de l’itinéraire.

<a name="requests-per-second-rps"></a> **Demandes par seconde (RPS)**: voir [Requêtes par seconde (RPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: acronyme de Really Simple Syndication, Resource Description Framework (RDF), Rich Site Summary, selon la source. Un format XML structuré et simple pour le partage de contenus sur différents sites web. Les documents RSS incluent des éléments de métadonnées clés tels que l’auteur, la date, le titre, une courte description et un lien hypertexte. Ces informations permettent à l’utilisateur (ou à un service de publication RSS) de décider des composants qui méritent d’être approfondis.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Imagerie satellite**: images prises par avions et satellites qui pointent directement vers le bas.

<a name="software-development-kit-sdk"></a> **Kit de développement logiciel (SDK)** : une collection de documentation, d’exemples de codes et d’applications pour aider un développeur à utiliser une API afin de créer des applications.

<a name="shapefile-shp"></a> **Fichier de forme (SHP)** : aussi appelé fichier de forme ESRI. Il s’agit d’un format de stockage de données de vecteur contenant l’emplacement, la forme et les attributs de caractéristiques géographiques. Un fichier de forme est stocké dans un ensemble de fichiers connexes.

<a name="spherical-mercator-projection"></a> **Projection de Mercator sphérique** : voir [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Requête spatiale** : demande adressée à un service qui effectue une opération spatiale. Par exemple, une recherche radiale ou une recherche sur la route.

<a name="spatial-reference"></a> **Référence spatiale** : un système de coordonnées local, régional ou mondial utilisé pour localiser précisément des entités géographiques. Elle définit le système de coordonnées utilisé pour transmettre les coordonnées de la carte indiquant un lieu dans le monde réel. Les références spatiales garantissent l’intégration des données spatiales de différentes couches ou sources en vue d’une analyse et d’un affichage précis. Azure Maps utilise le système de référence de coordonnées [EPSG:3857](https://epsg.io/3857) et WGS 84 pour entrer des données géométriques. 

<a name="sql-spatial"></a> **SQL spatial** : fait référence à la fonctionnalité spatiale intégrée à SQL Azure et SQL Server 2008 et versions ultérieures. Cette fonctionnalité spatiale est également disponible en tant que bibliothèque .NET qui peut être utilisée indépendamment de SQL Server. Pour plus d’informations, consultez la [Documentation sur les données spatiales (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server).

<a name="synchronous-request"></a> **Requête synchrone** :une requête HTTP ouvre une connexion et attend une réponse. Les navigateurs limitent le nombre de requêtes HTTP simultanées effectuées sur une page. Si plusieurs requêtes synchrones longues sont effectuées en même temps, cette limite peut être atteinte et les requêtes peuvent être retardées jusqu’à la fin d’une des autres requêtes.

## <a name="t"></a>T

<a name="telematics"></a> **Télématique** : envoi, réception et stockage des informations via des appareils de télécommunication conjointement un contrôle sur les objets distants. 

<a name="temporal-data"></a> **Données temporelles** : données qui font spécifiquement référence à des heures ou des dates. Les données temporelles peuvent faire référence à des événements distincts, comme les éclairs; les objets mouvants, comme les trains; ou des observations répétées, comme le compteur des capteurs de trafic.

<a name="terrain"></a> **Terrain** : zone ayant une caractéristique particulière, par exemple un terrain sablonneux ou montagneux.

<a name="thematic-maps"></a> **Cartes thématiques** : une carte thématique est une carte simple qui a pour but de refléter un thème d’une zone géographique. Il est courant pour ce type de carte de colorer les régions administratives telles que des pays, en fonction de certaines données de mesure.

<a name="tile-layer"></a> **Couche de mosaïques** : couche affichée en assemblant des mosaïques (sections rectangulaires) en une couche continue. Les vignettes peuvent être des images raster ou de vecteur. Les couches de mosaïques Raster sont généralement rendues à l’avance et stockées sous forme d’images sur un serveur. Elles peuvent occuper beaucoup d’espace de stockage. Les couches de mosaïques de vecteur sont affichées à la volée au sein de l’application cliente, par conséquent, les exigences de stockage côté serveur sont plus faibles.

<a name="time-zone"></a> **Fuseau horaire** : région du globe qui observe une heure uniforme à des fins commerciales, sociales et juridiques. Les fuseaux horaires ont tendance à respecter les délimitations des pays et de leurs sous-divisions.

<a name="transaction"></a> **Transaction** : Azure Maps utilise un modèle de licence transactionnel où :

- une transaction est créée toutes les 15 cartes ou vignettes de trafic demandées.
- une transaction est créée à chaque appel d’API vers l’un des services dans Azure Maps, tels que la recherche ou l’acheminement.

<a name="transformation"></a> **Transformation** : processus de conversion des données entre différents systèmes de coordonnées géographiques. Par exemple, vous pouvez disposer de données capturées au Royaume-Uni et basées sur le système de coordonnées géographiques OSGB 1936. Azure Maps utilise le système de référence de coordonnées [EPSG:3857](https://epsg.io/3857), variante de WGS 84. Ainsi, pour afficher les données correctement, il faudra transformer les coordonnées d’un système à un autre.

<a name="traveling-salesmen-problem-tsp"></a> **Problème voyages commerciaux** : un problème de circuit Hamiltonien dans lequel un vendeur doit trouver le moyen le plus efficace de visiter une série d’arrêts et retourner à l’emplacement de départ.  

<a name="trilateration"></a> **Trilatération** : processus de détermination de la position d’un point sur la surface de la terre en tenant compte de deux autres points, en mesurant les distances entre ces trois points.

<a name="turn-by-turn-navigation"></a> **Navigation étape par étape** : application qui fournit des instructions d’itinéraire pour chaque étape lorsque les utilisateurs approchent de l’étape suivante.

## <a name="v"></a>V

<a name="vector-data"></a> **Données de vecteur** : coordonnées basées sur les données qui sont représentées en tant que points, lignes ou polygones.

<a name="vector-tile"></a> **Vignette de vecteur** : spécification de données ouverte pour stocker des données de vecteur géospatiales avec le même système de mosaïques pour contrôler la carte. Voir aussi [Couche de mosaïques](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Problème d’acheminement du véhicule** : classe de problèmes dans lequel un ensemble d’itinéraires ordonnés d’une flotte de véhicules est calculé en prenant en tenant compte d’un ensemble de contraintes. Ces contraintes peuvent inclure des éléments tels que des fenêtres de délai de livraison, plusieurs capacités d’acheminement et des contraintes de durée de voyage.

<a name="voronoi-diagram"></a> **Diagramme Voronoi** : partition d’espace dans des zones ou des cellules qui entourent un ensemble d’objets géométriques (généralement des fonctions de point). Ces cellules, ou polygones, doivent respecter les critères des triangles Delaunay. Tous les emplacements au sein d’une zone sont plus proches de l’objet elle entoure que de n’importe quel autre objet dans l’ensemble. Les diagrammes Voronoi sont souvent utilisés pour délimiter des zones d’influence autour de caractéristiques géographiques. 

## <a name="w"></a>W

<a name="waypoint"></a> **Point de cheminement** : un point de cheminement est un emplacement géographique spécifié défini par la longitude et latitude et est utilisé à des fins de navigation. Souvent utilisé pour représenter un point par lequel une personne définit un itinéraire.

<a name="waypoint-optimization"></a> **Optimisation du point de cheminement** : processus de réorganisation d’un ensemble de points de cheminement afin de réduire la durée du trajet ou la distance nécessaire pour passer par tous les points définis. Souvent appelé [Problème voyages commerciaux](#traveling-salesmen-problem-tsp) ou [Problème d’acheminement du véhicule](#vehicle-routing-problem-vrp), en fonction de la complexité de l’optimisation.

<a name="web-map-service-wms"></a> **Web Service Map (WMS)** : WMS est une norme géographique OGC (Open Geographic Consortium) qui définit les services de cartographie basée sur l’image. Les services WMS fournissent des images de carte de zones spécifiques au sein d’une carte sur demande. Les images incluent une symbologie prérendue et peuvent être rendues dans l’un des nombreux styles nommés si définis par le service.

<a name="web-mercator"></a> **Web Mercator** : également appelé Projection Mercator sphérique. Il s’agit d’une légère variante de la projection de Mercator, utilisée principalement dans les programmes de cartographie basés sur le web. Elle utilise les mêmes formules que la projection de Mercator standard et est utilisée pour des cartes à petite échelle. Toutefois, le Web Mercator utilise les formules sphériques à toutes les échelles, tandis que les cartes Mercator à grande échelle utilisent normalement la forme ellipsoïde de la projection. La différence est imperceptible à l’échelle mondiale mais entraîne une légère déviation sur les cartes locales par rapport aux vraies cartes Mercator ellipsoïdes de la même échelle.

<a name="wgs84"></a> **WGS84** : un ensemble de constantes utilisées pour faire correspondre des coordonnées spatiales à des lieux à la surface de la carte. La donnée WGS84 est la norme utilisée par la plupart des fournisseurs de cartes en ligne et appareils GPS. Azure Maps utilise le système de référence de coordonnées [EPSG:3857](https://epsg.io/3857), variante de WGS 84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Coordonnée Z** : Voir [Altitude](#altitude). 

<a name="zip-code"></a> **Code postal** : consultez [Code Postal](#postal-code).

<a name="Zoom level"></a> **Niveau de zoom** : spécifie le niveau de détail et ce qui est visible de la carte. Avec un zoom jusqu’au niveau 0, la carte du monde sera souvent complètement visible mais n’affichera que des informations limitées comme le nom des pays et les frontières, ainsi que les noms des océans. Avec un zoom plus proche du niveau 17, la carte affiche une zone de quelques rues d’une ville avec des informations routières détaillées. Pour plus d’informations, consultez la [documentation Niveaux de zoom et grille mosaïque](zoom-levels-and-tile-grid.md).

