---
title: Matériaux PBR
description: Décrit le type de matériaux PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: a1fedb637bee9d98fb09d8fc3fa133b2992ce86e
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613666"
---
# <a name="pbr-materials"></a>Matériaux PBR

Les *matériaux PBR* constituent l’un des [types de matériaux](../../concepts/materials.md) pris en charge dans Azure Remote Rendering. Ils sont utilisés pour les [maillages](../../concepts/meshes.md) qui doivent recevoir un éclairage réaliste.

PBR est l'abréviation de **P**hysically **B**ased **R**endering qui signifie que le matériau décrit les propriétés visuelles d’une surface de manière physiquement plausible pour permettre des résultats réalistes dans toutes les conditions d’éclairage. La plupart des moteurs de jeux modernes et des outils de création de contenu prennent en charge les matériaux PBR considérés comme la meilleure approximation des scénarios réels en termes de rendu en temps réel.

![Exemple de modèle glTF Helmet rendu par ARR](media/helmet.png)

Cela étant, les matériaux PBR ne constituent pas une solution universelle. Certains matériaux reflètent la couleur différemment selon l’angle de vue. Par exemple, certaines structures ou peintures de voitures. Ces types de matériaux ne sont pas gérés par le modèle PBR standard et ne sont pas pris en charge par Azure Remote Rendering pour le moment. Cela comprend les extensions PBR, telles que *Thin-Film* (surfaces multicouches) et *Clear-Coat* (peintures de voitures).

## <a name="common-material-properties"></a>Propriétés des matériaux courants

Ces propriétés sont communes à tous les matériaux :

* **albedoColor :** cette couleur est multipliée par d’autres couleurs, telles que les couleurs *albedoMap* ou *:::no-loc text="vertex ":::* . Si la propriété *transparency* est activée sur un matériau, le canal alpha est utilisé pour ajuster l’opacité, avec `1` pour entièrement opaque et `0` pour entièrement transparent. Le blanc constitue la valeur par défaut.

  > [!NOTE]
  > Lorsqu’un matériau PBR est entièrement transparent, comme du verre parfaitement propre, il reflète toujours l’environnement. Les zones lumineuses telles que le soleil sont toujours visibles dans la réflexion. Cela est différent pour [les matériaux de couleur](color-materials.md).

* **albedoMap :** une [texture 2D](../../concepts/textures.md) pour les valeurs albedo par pixel.

* **alphaClipEnabled** et **alphaClipThreshold :** si la valeur de *alphaClipEnabled* est true, tous les pixels dont la valeur alpha albedo est inférieure à *alphaClipThreshold* ne seront pas représentés. Le détourage alpha peut être utilisé, même sans activation de la transparence, et s’avère nettement plus rapide à rendre. Les matériaux avec détourage alpha sont cependant plus lents à rendre que les matériaux entièrement opaques. Par défaut, le détourage alpha est désactivé.

* **textureCoordinateScale** et **textureCoordinateOffset :** l’échelle est multipliée par les coordonnées de la texture UV, le décalage y est ajouté. Peut être utilisée pour étirer et décaler les textures. La valeur par défaut de l’échelle est de (1, 1) et le décalage est de (0, 0).

* **useVertexColor :** si le maillage comprend des couleurs :::no-loc text="vertex"::: et que cette option est activée, la couleur :::no-loc text="vertex"::: des maillages est multipliée par *albedoColor* et *albedoMap*. Par défaut, *useVertexColor* est désactivé.

* **isDoubleSided :** Si le recto-verso est défini sur true, les triangles présentant ce matériau sont rendus même si l’appareil photo pointe vers les faces arrière. L’éclairage des matériaux PBR est également correctement calculé pour les faces arrière. Par défaut, cette option est désactivée. Consultez aussi le [:::no-loc text="Single-sided"::: rendu](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Propriétés des matériaux PBR

L’idée de base du rendu physique réaliste consiste à utiliser les propriétés *BaseColor*, *Metalness*, et *Roughness* pour émuler un large gamme de matériaux réels. La description détaillée du rendu physique réaliste (PBR) dépasse le cadre de cet article. Pour plus d'informations sur le rendu physique réaliste, consultez d'[autres sources](http://www.pbr-book.org). Les propriétés suivantes sont spécifiques aux matériaux PBR :

* **baseColor :** En termes de matériaux PBR, *couleur albedo* est appelée *couleur de base*. Dans Azure Remote Rendering, la propriété *albedo color* est déjà présente par le biais des propriétés des matériaux courants, de sorte qu’il n’y a aucune propriété de couleur de base supplémentaire.

* **roughness** et **roughnessMap :** La rugosité définit dans quelle mesure la surface est rugueuse ou lisse. Les surfaces rugueuses diffusent la lumière dans plus de directions que les surfaces lisses, ce qui rend les réflexions plus floues que nettes. La plage de valeurs est comprise entre `0.0` et `1.0`. Lorsque `roughness` équivaut à `0.0`, les réflexions sont nettes. Lorsque `roughness` équivaut à `0.5`, les réflexions deviennent floues.

  Si une valeur de rugosité et une cartographie de rugosité sont fournies, la valeur finale sera le produit des deux.

* **metalness** et **metalnessMap :** En physique, cette propriété correspond à la nature conductrice ou diélectrique d'une surface. Les matériaux conducteurs présentent différentes propriétés réfléchissantes et tendent à réfléchir sans couleur albedo. En termes de matériaux PBR, cette propriété détermine dans quelle mesure une surface reflète l’environnement. Les valeurs sont comprises entre `0.0` et `1.0`. Lorsque la propriété metalness est `0.0`, la couleur albedo est entièrement visible et le matériau ressemble à du plastique ou de la céramique. Lorsque la propriété metalness est `0.5`, il ressemble à du métal peint. Lorsque la propriété metalness est `1.0`, la surface perd complètement sa couleur albedo et reflète uniquement l’environnement. Par exemple, si `metalness` est `1.0` et `roughness` `0.0`, une surface ressemble à un miroir.

  Si les propriétés metalness et metalness map sont fournies, la valeur finale sera le produit des deux.

  ![Sphères rendues avec différentes valeurs pour metalness et roughness](./media/metalness-roughness.png)

  Dans l’image ci-dessus, la sphère du coin inférieur droit ressemble à un matériau en métal réel et la partie inférieure gauche à de la céramique ou du plastique. La couleur albedo change également en fonction des propriétés physiques. Si la rugosité augmente, le matériau perd la netteté de la réflexion.

* **normalMap :** Pour simuler des détails affinés, une [cartographie normale](https://en.wikipedia.org/wiki/Normal_mapping) peut être fournie.

* **occlusionMap** et **aoScale:** L'[occlusion ambiante](https://en.wikipedia.org/wiki/Ambient_occlusion) rend les objets présentant des crevasses plus réalistes en ajoutant des ombres aux zones occluses. La plage de valeurs de l'occlusion est comprise entre `0.0` et `1.0`, où `0.0` représente l'obscurité (occlusion) et `1.0` l'absence d'occlusion. Si une texture 2D est fournie en tant que cartographie d’occlusion, l’effet est activé et *aoScale* fait office de multiplicateur.

  ![Objet rendu avec et sans occlusion ambiante](./media/boom-box-ao2.gif)

* **Transparent** : En termes de matériaux PBR, il n’existe qu’un seul paramètre de transparence, activé ou non. L’opacité est définie par le canal alpha de la couleur albedo. Lorsqu’il est activé, un pipeline de rendu plus complexe est appelé pour dessiner des surfaces semi-transparentes. Azure Remote Rendering implémente [OIT (Order Independent Transparency](https://en.wikipedia.org/wiki/Order-independent_transparency)).

  La géométrie transparente est onéreuse. S'il vous faut uniquement des trous dans une surface, par exemple pour les feuilles d'un arbre, il est préférable de recourir au détourage alpha.

  ![Sphères rendues avec une transparence nulle à totale](./media/transparency.png) Dans l’image ci-dessus, remarquez la transparence totale de la sphère la plus à droite, alors que le reflet est toujours visible.

  > [!IMPORTANT]
  > Si un matériau est supposé passer de l’état opaque à transparent au moment de l’exécution, le convertisseur doit utiliser le *mode de rendu* [TileBasedComposition](../../concepts/rendering-modes.md). Cette limitation ne s’applique pas aux matériaux convertis d'emblée en matériaux transparents.

## <a name="technical-details"></a>Détails techniques

Azure Remote Rendering utilise la microfacette BRDF Cook Torrance avec GGX NDF, Schlick Fresnel et un terme de visibilité corrélée GGX Smith avec un terme diffus Lambert. Actuellement, ce modèle est la norme de facto du secteur. Pour des informations détaillées, consultez cet article : [Rendu physique réaliste (PBR) - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Le modèle PBR *Specular-Glossiness* est une alternative au modèle PBR *Metalness-Roughness* utilisé dans Azure Remote Rendering. Ce modèle peut représenter une plus grande gamme de matériaux. Toutefois, il est plus onéreux et ne fonctionne pas très bien en temps réel.
Il n’est pas toujours possible de passer de *Specular-Glossiness* à *Metalness-Roughness* car il s'agit de paires de valeurs *(Diffuse, Specular)* impossibles à convertir vers *(BaseColor, Metalness)* . La conversion dans l’autre sens est plus simple et plus précise puisque toutes les paires *(BaseColor, Metalness)* correspondent à des paires *(Diffuse, Specular)* bien définies.

## <a name="api-documentation"></a>Documentation de l’API

* [PbrMaterial, classe C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [RemoteManager.CreateMaterial(), C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [PbrMaterial, classe C++](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [RemoteManager::CreateMaterial(), C++](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Étapes suivantes

* [Matériaux couleur](color-materials.md)
* [Textures](../../concepts/textures.md)
* [Maillages](../../concepts/meshes.md)
