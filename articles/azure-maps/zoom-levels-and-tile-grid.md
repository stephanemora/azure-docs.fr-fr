---
title: Niveaux de zoom et grille mosaïque dans Azure Maps | Microsoft Docs
description: En savoir plus sur les niveaux de zoom et la grille mosaïque dans Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261261"
---
# <a name="zoom-levels-and-tile-grid"></a>Niveaux de zoom et grille mosaïque
Azure Maps utilisent le système de coordonnées de projection Spherical Mercator (EPSG : 3857).

Le monde est divisé en mosaïques carrées. Azure maps dote les vignettes raster et vectoriels pour 23 niveaux de zoom, numérotés de 0 à 22. Au niveau de zoom 0, le monde entier tient dans une seule mosaïque :

![Mosaïque du monde](./media/zoom-levels-and-tile-grid/world0.png)

Le niveau de zoom 1 utilise quatre mosaïques pour afficher le monde : un carré de 2 x 2

![Mosaïque du monde en haut à gauche](media/zoom-levels-and-tile-grid/world1a.png)     ![Mosaïque du monde en haut à droite](media/zoom-levels-and-tile-grid/world1c.png) 

![Mosaïque du monde en bas à gauche](media/zoom-levels-and-tile-grid/world1b.png)     ![Mosaïque du monde en bas à droite](media/zoom-levels-and-tile-grid/world1d.png) 

Chaque niveau de zoom supplémentaires divise en quatre les mosaïques du précédent, créant une grille de 2<sup>zoom</sup> x 2<sup>zoom</sup>. Le niveau de zoom 22 est une grille de 2<sup>22</sup> x 2<sup>22</sup> ou de 4 194 304 x 4 194 304 mosaïques (17 592 186 044 416 mosaïques au total).

Les contrôles de carte interactive Azure Maps pour le web et la prise en charge Android zoom 25 niveaux de zoom niveaux, numérotés de 0 à 24. Même si les données de route seront uniquement disponibles aux niveaux de zoom dans lorsque les vignettes sont disponibles.

Le tableau suivant fournit la liste complète de valeurs pour les niveaux de zoom :

|Niveau de zoom|Compteurs/pixel|Compteurs/côté mosaïque|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5.|4892|1252352|
|6.|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0,6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0,075|19.1|
|22|0,0375|9,55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

Les mosaïques sont appelées selon le niveau de zoom et les coordonnées x et y correspondant à la position de la mosaïque sur la grille pour ce niveau de zoom.

Lorsque vous déterminez le niveau de zoom à utiliser, n’oubliez pas de que chaque emplacement correspond à une position fixe sur sa vignette. Cela signifie que le nombre de mosaïques nécessaires pour afficher l’étendue donnée d’un territoire dépend du placement spécifique de la grille de zoom dans le monde. Par exemple, si deux points sont éloignés l’un de l’autre de 900 mètres, trois mosaïques *peuvent* suffire pour afficher un itinéraire entre eux au niveau de zoom 17. Toutefois, si le point occidental se trouve à droite de sa mosaïque et que le point oriental se trouve à gauche, on obtient quatre mosaïques :

![Échelle de démonstration du zoom](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Lorsque le niveau de zoom est déterminé, les valeurs x et y peuvent être calculées. La vignette en haut à gauche dans chaque grille de zoom est x = 0, y = 0 ; la vignette en bas à droite correspond à x = 2<sup>zoom -1</sup>, y = 2<sup>zoom-1</sup>.

La grille de zoom pour le niveau de zoom 1 est proposée ici :

![Grille de zoom pour le niveau de zoom 1](media/zoom-levels-and-tile-grid/api_x_y.png)
