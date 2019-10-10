---
title: Routage de véhicule électrique à l’aide d’Azure Notebooks (Python) | Microsoft Docs
description: Routage de véhicule électrique à l’aide des API de routage Azure Maps et d’Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836340"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Routage de véhicule électrique à l’aide d’Azure Notebooks (Python)

Azure Maps est un portefeuille d’API de services géospatiaux intégrées en mode natif dans Azure, et qui permettent aux développeurs, aux entreprises et aux 
éditeurs de logiciels indépendants de créer des applications utilisant la géolocalisation ainsi que des solutions d’IoT, de mobilité, de logistique et de suivi des ressources. Les API REST Azure Maps peuvent être appelées à partir de langages tels que Python et R pour permettre l’analyse de données géospatiales et les scénarios de Machine Learning. Azure Maps offre un ensemble robuste d’[API de routage](https://docs.microsoft.com/rest/api/maps/route) qui permet aux utilisateurs de calculer des itinéraires entre plusieurs points de données en fonction de différentes conditions, telles que le type de véhicule ou la zone accessible. Dans ce tutoriel, nous allons examiner un scénario pour aider le conducteur d’un véhicule électrique n’ayant plus beaucoup d’autonomie à trouver la borne de recharge la plus proche en temps de conduite.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et exécuter un notebook Jupyter sur [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) dans le cloud
> * Appeler les API REST Azure Maps en Python
> * Rechercher une zone accessible en fonction du modèle de consommation du véhicule électrique
> * Rechercher des bornes de recharge de véhicule électrique dans la zone accessible (ou isochrone)
> * Afficher la limite de la zone accessible et les bornes de recharge sur une carte
> * Rechercher et visualiser l’itinéraire vers la borne de recharge de véhicule électrique la plus proche en temps


## <a name="prerequisites"></a>Prérequis 

Pour effectuer les étapes de ce tutoriel, vous devez d’abord créer un compte Azure Maps et obtenir votre clé primaire (clé d’abonnement). Suivez les instructions mentionnées dans [Gérer le compte](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) pour créer un abonnement de compte Azure Maps avec le niveau tarifaire S1 et effectuez les étapes dans [Obtenir la clé primaire](./tutorial-search-location.md#getkey) afin d’obtenir la clé primaire d’abonnement pour votre compte.

## <a name="create-an-azure-notebook"></a>Créer un notebook Azure

Pour suivre ce tutoriel, vous devez créer un projet Azure Notebook et télécharger et exécuter le fichier de notebook Jupyter. Le fichier de notebook contient du code Python qui implémente le scénario de ce tutoriel. Suivez les étapes ci-dessous pour créer un projet Azure Notebook et y charger le document du notebook Jupyter.

1. Accédez à [Azure Notebooks](https://notebooks.azure.com) et connectez-vous. Pour plus d’informations, consultez [Démarrage rapide](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. En haut de votre page de profil public, sélectionnez **Mes projets**.

    ![Mes Projets](./media/tutorial-ev-routing/myproject.png)

3. Dans la page **Mes projets**, sélectionnez **Nouveau projet**.
 
   ![Nouveau projet](./media/tutorial-ev-routing/create-project.png)

4. Dans la fenêtre contextuelle **Créer un projet** qui s’affiche, entrez les informations suivantes et cliquez sur **Créer** :
    * Nom du projet
    * ID du projet
 
    ![créer un projet](./media/tutorial-ev-routing/create-project-window.png)

5. Une fois votre projet créé, téléchargez le [fichier de document du notebook Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) à partir du [dépôt Jupyter Notebook Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Sélectionnez votre projet dans la liste de projets dans la page **Mes projets**, puis cliquez sur **Charger** pour charger le fichier de document du notebook Jupyter. Chargez le fichier à partir de votre ordinateur, puis cliquez sur **Terminé**.

    ![Charger le notebook](./media/tutorial-ev-routing/upload-notebook.png)

7. Une fois le chargement terminé, votre fichier s’affiche dans la page de votre projet. Cliquez sur le fichier du notebook pour l’ouvrir en tant que notebook Jupyter.

Pour mieux comprendre la fonctionnalité implémentée dans le fichier de notebook, nous vous recommandons d’exécuter le code dans le notebook une cellule à la fois. Vous pouvez exécuter le code de chaque cellule en cliquant sur le bouton **Run** situé en haut de l’application de notebook.

  ![Exécuter](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installer des packages au niveau du projet

Pour exécuter le code dans le notebook, vous devez installer les packages au niveau du projet. Pour installer les packages nécessaires, effectuez les étapes ci-dessous :

1. Téléchargez le fichier [« requirements.txt »](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) à partir du [dépôt Jupyter Notebook Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) et chargez-le dans votre projet.
2. Dans le tableau de bord du projet, sélectionnez **Paramètres du projet**. 
3. Dans la fenêtre contextuelle qui s’affiche, sélectionnez l’onglet **Environnement**, puis **Ajouter**.
4. Sous **Étapes de configuration de l’environnement**, 
    * Dans le premier contrôle de liste déroulante, choisissez **Requirements.txt**.
    * Dans le deuxième contrôle de liste déroulante, choisissez votre fichier « requirements.txt ».
    * Dans le troisième contrôle de liste déroulante, choisissez Python Version 3.6 comme version de Python.
7. Sélectionnez **Enregistrer**.

    ![Installer les packages](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Charger les modules et les frameworks requis

Exécutez le script suivant pour charger tous les modules et frameworks requis.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Demander les limites de la zone accessible

Dans notre scénario, une société de livraison compte quelques véhicules électriques dans sa flotte. Pendant la journée, les véhicules électriques doivent être rechargés sans avoir à revenir à l’entrepôt. Chaque fois que l’autonomie du véhicule électrique est inférieure à une heure, nous devons rechercher plusieurs bornes de recharge qui se trouvent dans la zone accessible et obtenir les limites de cette zone. Étant donné que la société préfère utiliser les itinéraires offrant un compromis économie-vitesse, le routeType demandé est « eco ». Le script suivant appelle l’[API d’obtention de zone d’itinéraire](https://docs.microsoft.com/rest/api/maps/route/getrouterange) du service de routage Azure Maps avec des paramètres pour le modèle de consommation du véhicule, et analyse la réponse afin de créer un objet polygone au format geojson représentant la zone accessible maximale du véhicule.

Exécutez le script de la cellule ci-dessous pour obtenir les limites de la zone accessible du véhicule électrique.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Rechercher des bornes de recharge de véhicule électrique dans la zone accessible

Une fois que nous disposons de la zone accessible (isochrone) pour le véhicule électrique, nous pouvons rechercher les bornes de recharge dans cette zone. Le script suivant appelle l’[API de recherche dans une géométrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) Azure Maps pour rechercher les bornes de recharge de véhicule électrique situées dans les limites de la zone d’accessibilité maximale du véhicule, puis génère la réponse dans un tableau de lieux accessibles.

Exécutez le script suivant pour rechercher les bornes de recharge de véhicule électrique dans une zone accessible.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Charger la zone accessible et les points de charge vers le service de données Azure Maps

Pour visualiser les bornes de recharge et la limite de la zone accessible maximale du véhicule électrique sur la carte, nous devons charger les données de limite et charger les données des bornes sous forme d’objets geojson sur le service de données Azure Maps à l’aide de l’[API de chargement de données](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Exécutez les deux cellules suivantes pour charger les données de limite et de points de chargement dans le service de données Azure Maps.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Afficher les bornes de recharge et la zone accessible sur la carte

Une fois les données chargées sur le service de données, nous allons maintenant exécuter le script suivant pour appeler le [service d’obtention d’image de carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage) Azure Maps afin d’afficher les points de charge et la limite accessible maximale sur l’image de carte statique.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![zone de localisations](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Rechercher la borne de recharge optimale où s’arrêter

Une fois que nous disposons de toutes les bornes de recharge potentielles dans la zone accessible, nous souhaitons savoir quelles bornes peuvent être atteintes en un minimum de temps. Le script suivant appelle l’[API de routage de matrice](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) Azure Maps, qui retourne pour le lieu du véhicule donné la durée du trajet et la distance jusqu’à chaque borne de recharge donnée. Le script de la cellule suivante analyse la réponse pour obtenir la localisation de la borne de recharge accessible la plus proche en temps.

Exécutez la cellule suivante pour trouver la borne de recharge la plus proche qui peut être atteinte en un minimum de temps.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Calculer l’itinéraire vers la borne de recharge la plus proche

Maintenant que nous avons trouvé la borne de recharge la plus proche, nous allons appeler l’[API d’obtention d’itinéraire](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) afin de demander l’itinéraire détaillé de l’endroit où se trouve le véhicule électrique jusqu’à la borne de recharge.

Exécutez le script de la cellule suivante pour obtenir l’itinéraire, puis analysez la réponse pour créer un objet geojson représentant l’itinéraire.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualiser l’itinéraire

Pour visualiser l’itinéraire, nous allons tout d’abord charger les données d’itinéraire en tant qu’objet geojson dans le service de données Azure Maps à l’aide de l’[API de chargement de données](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) Azure Maps. Ensuite, nous appellerons le service de rendu, l’[API d’obtention d’image de carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage), pour afficher l’itinéraire sur la carte et la visualiser.

Exécutez le script suivant pour obtenir une image de l’itinéraire sur la carte.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![itinéraire](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment appeler les API REST Azure Maps directement et visualiser les données Azure Maps à l’aide de Python.

Pour explorer les API Azure Maps utilisées dans ce tutoriel, consultez :

* [Obtenir une zone d’itinéraire](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Envoyer une recherche dans une géométrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Chargement de données](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Rendu - Obtenir une image de carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Envoyer une matrice d’itinéraire](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Obtenir un itinéraire](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Pour obtenir la liste complète des API REST Azure Maps, consultez :

* [API REST Azure Maps](https://docs.microsoft.com/azure/azure-maps/#reference)

Pour en savoir plus sur Azure Notebooks, consultez :

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)