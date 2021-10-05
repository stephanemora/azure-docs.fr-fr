---
title: Forum aux questions
description: Forum aux questions (FAQ) sur le service Azure Object Anchors.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 18069157b4c7f38216c01649a33ed5f999dc7577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638384"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Forum aux questions sur Azure Object Anchors

Azure Object Anchors permet à une application de détecter un objet dans le monde physique à l’aide d’un modèle 3D et d’estimer sa valeur 6-DDL.

Pour plus d’informations, consultez [Vue d’ensemble d’Azure Object Anchors](overview.md).

## <a name="product-faq"></a>FAQ sur le produit
**Q : Quelles sont vos recommandations pour les objets qui doivent être utilisés ?**

**R :** Nous vous recommandons d’utiliser les propriétés suivantes pour les objets :

* 1-10 mètres pour chaque dimension
* Non symétrique, avec des variations suffisantes dans la géométrie
* Faible réflectivité (surfaces mattes) avec une couleur vive
* Objets stationnaires
* Aucune voire peu d’articulation
* Effacer les arrière-plans avec aucun voire peu d’encombrement
* L’objet analysé doit afficher une correspondance 1:1 avec le modèle que vous avez formé

**Q : Quelles sont les dimensions maximales de l’objet qui peuvent être traitées pour la conversion de modèle ?**

**R :** Chaque dimension d’un modèle CAO doit être inférieure à 10 mètres. Pour plus d’informations, consultez [Prérequis des ressources](overview.md).

**Q : Quelle est la taille maximale du modèle CAO qui peut être traitée pour la conversion ?**

**R :** La taille de fichier du modèle doit être inférieure à 150 Mo. Pour plus d’informations, consultez [Prérequis des ressources](overview.md).

**Q : Quels sont les formats CAO pris en charge ?**

**R :** Nous prenons actuellement en charge les types de fichiers `fbx`, `ply`, `obj`, `glb` et `gltf`. Pour plus d’informations, consultez [Prérequis des ressources](overview.md).

**Q : À quoi correspondent le sens de la gravité et l’unité qui sont exigés par le service de conversion de modèle ?**

**R :** Le sens de la gravité est le vecteur qui est dirigé vers la terre, et l’unité de mesure représente l’échelle du modèle. Lors de la conversion d’un modèle, il est important de [vérifier que le sens de la gravité et l’unité de dimension sont corrects](./troubleshoot/object-detection.md#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct).

**Q : Combien de temps faut-il pour convertir un modèle CAO ?**

**R :** Pour un modèle `ply`, 3 à 15 minutes en général. Si vous soumettez des modèles dans d’autres formats, comptez entre 15 et 60 minutes en fonction de la taille du fichier.

**Q : Comment reprendre suite à un échec de conversion de modèle ?**

**R :** Pour plus d’informations sur les différents codes d’erreur résultant d’un échec d’un travail de conversion de modèle et sur la façon de les gérer, consultez la [page des codes d’erreur de conversion](.\model-conversion-error-codes.md).

**Q : Quels appareils Object Anchors prend-il en charge ?**

**R :** HoloLens 2.

**Q : quel build de système d’exploitation dois-je exécuter avec HoloLens ?**

**R :** le build 18363.720 ou une version ultérieure du système d’exploitation, publié après le 12 mars 2020.

  Pour plus d’informations, consultez la [mise à jour 2020 de Windows 10, en date du 12 mars](https://support.microsoft.com/help/4551762).

**Q : Combien de temps faut-il pour détecter un objet sur HoloLens ?**

**R :** Cela dépend de la taille de l’objet et du processus d’analyse. Pour accélérer la détection, suivez les meilleures pratiques pour une analyse approfondie.
Pour les objets plus petits mesurant moins de 2 mètres dans chaque dimension, la détection peut prendre quelques secondes. Pour les objets plus grands, comme une voiture, l’utilisateur doit parcourir une boucle complète autour de l’objet pour obtenir une détection fiable, ce qui signifie que la détection peut prendre plusieurs dizaines de secondes.

**Q : Quelles sont les meilleures pratiques pour utiliser Object Anchors dans une application HoloLens ?**

**R :**

 1. Effectuez un étalonnage visuel pour obtenir un rendu précis.
 2. Assurez-vous que la pièce offre une texture visuelle riche et un bon éclairage.
 3. Maintenez l’objet immobile, à l’écart de tout encombrement, si possible.
 4. Si vous le souhaitez, effacez le cache [Mappage spatial](/windows/mixed-reality/spatial-mapping) sur votre appareil HoloLens.
 5. Numérisez l’objet en tournant autour. Vérifiez que la majeure partie de l’objet est capturée.
 6. Définissez une zone de recherche suffisamment grande pour couvrir l’objet.
 7. L’objet doit rester immobile pendant la détection.
 8. Démarrez la détection d’objets et visualisez le rendu en fonction de la pose estimée.
 9. Verrouillez l’objet détecté ou arrêtez le suivi une fois que la pose est stable et précise afin d’économiser la batterie.

**Q : Quelle version de la boîte à outils Mixed Reality mon application HoloLens Unity doit-elle utiliser pour pouvoir se servir du kit SDK Unity Object Anchors ?**

**R :** Le kit SDK Unity Azure Object Anchors ne dépend en aucun cas de la boîte à outils Mixed Reality. Vous êtes donc libre d’utiliser la version de votre choix. Pour plus d’informations, consultez [Présentation de la boîte à outils Mixed Reality pour Unity](/windows/mixed-reality/develop/unity/mrtk-getting-started).

**Q : Quelle est la précision d’une pose estimée ?**

**R :** Cela dépend de la taille de l’objet, de sa matière, de son environnement, etc. Pour les petits objets, la pose estimée peut avoir une marge d’erreur de 2 cm. Pour les objets volumineux, comme un véhicule, l’erreur peut se situer entre 2 et 8 cm.

**Q : Object Anchors peut-il gérer le déplacement d’objets ?**

**R :** Nous ne prenons pas en charge le **déplacement continu** ni les objets **dynamiques**. Nous prenons en charge les objets dans une position entièrement nouvelle dans l’espace une fois qu’ils y ont été physiquement déplacés, mais nous ne pouvons pas les suivre pendant leur déplacement.

**Q : Object Anchors peut-il gérer la déformation ou les articulations ?**

**R :** Partiellement, en fonction du degré de changement de la forme ou de la géométrie en raison d’une déformation ou articulation. Si la géométrie de l’objet change beaucoup, l’utilisateur peut créer un autre modèle pour cette configuration et l’utiliser pour la détection.

**Q : Combien de modèles différents Object Anchors peut-il détecter à la fois ?**

**R :** Nous garantissons actuellement une expérience utilisateur optimale pour la détection de trois modèles à la fois. Toutefois, nous n’appliquons pas de limite.

**Q : Object Anchors peut-il détecter plusieurs instances d’un même modèle d’objet ?**

**R :** Oui, nous prenons en charge la détection de trois instances d’un même type de modèle tout en garantissant une expérience utilisateur optimale. Cependant, nous n’appliquons pas de limite. Vous pouvez détecter une instance d’objet par zone de recherche. En appelant `ObjectQuery.SearchAreas.Add`, vous pouvez ajouter d’autres zones de recherche à une requête afin de détecter d’autres instances. Vous pouvez appeler `ObjectObserver.DetectAsync` avec plusieurs requêtes afin de détecter plusieurs modèles.

**Q : Que dois-je faire si le runtime Object Anchors ne détecte pas mon objet ?**

**R :** Il existe de nombreux facteurs qui peuvent empêcher un objet d’être détecté correctement : l’environnement, la configuration de la conversion de modèle, les paramètres de requête, etc. En savoir plus sur la [résolution des problèmes liés à la détection d’objet](./troubleshoot/object-detection.md).

**Q : Comment choisir les paramètres de requête d’objet ?**

**R :** Pour cela, vous pouvez consulter des [conseils d’ordre général](./troubleshoot/object-detection.md#adjust-object-query-values) ainsi qu’un guide plus détaillé sur les [objets difficiles à détecter](./detect-difficult-object.md).

**Q : Comment obtenir des données de diagnostic Object Anchors à partir de HoloLens ?**

**R :** L’application peut spécifier l’emplacement des archives de diagnostics. L’exemple d’application Object Anchors consigne les diagnostics dans le dossier **TempState**.

**Q : Pourquoi le modèle source n’est-il pas aligné avec l’objet physique lors de l’utilisation de la pose retournée par le Kit de développement logiciel (SDK) Object Anchors ?**

**R :** Unity peut modifier le système de coordonnées lors de l’importation d’un modèle d’objet. Par exemple, le Kit de développement logiciel (SDK) Unity Object Anchors inverse l’axe Z lors de la conversion d’un système de coordonnées droitier vers un système de coordonnées gaucher, mais Unity peut appliquer une rotation supplémentaire au niveau de l’axe X ou Y. Un développeur peut définir cette rotation supplémentaire en visualisant et en comparant les systèmes de coordonnées.

**Q : Prenez-vous en charge la 2D ?**

**R :** Dans la mesure où nous nous basons sur la géométrie, nous ne prenons en charge que la 3D.

**Q : Pouvez-vous identifier les différences dans un modèle de différentes couleurs ?**

**R :** Étant donné que nos algorithmes sont basés sur la géométrie, les différentes couleurs du même modèle ne se comporteront pas de la même façon.

**Q : Puis-je utiliser Object Anchors sans connectivité Internet ?**

**R :**
* Pour la conversion et l’entraînement du modèle, une connectivité est nécessaire car ces opérations s’effectuent dans le cloud.
* Les sessions d’exécution se déroulent entièrement sur l’appareil et ne nécessitent pas de connectivité, car tous les calculs se produisent sur HoloLens 2.

## <a name="privacy-faq"></a>Questions fréquentes sur la confidentialité
**Q : Comment Azure Object Anchors stocke-t-il les données ?**

**R :** Nous stockons uniquement les métadonnées système, chiffrées au repos avec une clé de chiffrement de données gérée par Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu la réponse à certaines questions courantes sur l’obtention des meilleurs résultats avec Azure Object Anchors.
Voici quelques articles connexes :

> [!div class="nextstepaction"]
> [Bonnes pratiques](./best-practices.md)

> [!div class="nextstepaction"]
> [Résolution des problèmes de détection d’objets](./troubleshoot/object-detection.md)
