---
title: Instructions pour les expériences d’ancrage efficace qui utilisent des ancres Spatial Azure | Microsoft Docs
description: Les instructions et considérations pour créer et localiser les ancres efficacement à l’aide des ancres spatiale d’Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520800"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Créer une expérience efficace d’ancrage à l’aide des ancres Spatial Azure

Cet article fournit des instructions et considérations pour vous aider à en fait créent et localiser les points d’ancrage à l’aide d’ancres spatiale.

## <a name="good-anchors"></a>Bonne ancres

Ancres spatiales vous permet de créer des ancres bon. Il est important de se consacrer à la formation ou de guider les utilisateurs dans votre expérience utilisateur (UX) pour créer des ancres bon. En investissant dans la création des ancres bonne en amont, vous aider à trouver des points d’ancrage de manière fiable l’utilisateur final :

- Sur des périphériques différents.
- À des moments différents.
- Dans les conditions d’éclairage différentes.
- Sous les angles souhaité dans l’espace.

## <a name="static-and-dynamic-locations"></a>Emplacements statiques et dynamiques

Partie de la conception de l’expérience d’ancrage consiste à choisir les emplacements. Les emplacements seront statique et être définie par un administrateur de l’espace ? Ou ils seront dynamique et définies par l’utilisateur ?

Un responsable de magasin de vente au détail souhaiterez peut-être une expérience en magasin statique pour inciter les utilisateurs à consulter. Mais le développeur d’un jeu de plateau de réalité mixte faudrait probablement permettre aux utilisateurs de choisir l’emplacement de lecture.

Pour les adresses statiques, vous pouvez apprendre aux administrateurs de passer du temps organisant l’espace avec une bonne ancres.

Pour les adresses dynamiques, tenez compte des comment que vous enseignez ou guider les utilisateurs dans votre expérience utilisateur de créer des ancres bon.

## <a name="stable-visual-features"></a>Fonctionnalités visual stables

Les systèmes de suivi visuel utilisé sur réalité mixte et appareils avec réalité augmentée s’appuient sur les fonctionnalités visuelles de l’environnement. Pour obtenir l’expérience la plus fiable :  

- *Faire* créer des ancres dans les emplacements qui ont des fonctionnalités visual stables (autrement dit, les fonctionnalités qui ne changent pas souvent).

- *Ne* créer des ancres sur les grandes surfaces vides ayant aucune caractéristiques distinctives.

- *Ne* créer des ancres sur les matériaux fortement réfléchissante.

- *Ne* créer des ancres sur des surfaces où le modèle est répété, telles que des tapis ou le papier peint.

![Exemples d’un environnement de bons points d’ancrage et un environnement incorrect pour les points d’ancrage](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Différentes façons d’affichage

Lorsque vous créez un point d’ancrage, pensez aux personnes qui seront ultérieurement tentent de localiser le point d’ancrage.

Considérons, par exemple, un point d’ancrage au milieu d’une salle qui a deux portes. Probablement voulez-vous autoriser les utilisateurs à accéder à la salle de soit porte. Lorsque vous créez le point d’ancrage, vous devez analyser sa position dans les deux bureaux. Vous modifiez des perspectives pour capturer des données d’environnement autour de l’ancre pour que les utilisateurs puissent localiser le point d’ancrage à partir d’une porte.

En règle générale, lorsque vous créez un point d’ancrage, analysez-la sous les angles des personnes qui tentent de localiser. Par conséquent, si vous placez contenu virtuel sur un sculpture extérieur, il est judicieux de porter la sculpture, lors de l’analyse, lorsque vous créez le point d’ancrage. Si votre point d’ancrage est dans le coin d’un espace, il n’existe qu’un seul sens à tenter une approche à partir de. Lorsque vous créez cette ancre, vous pouvez le numériser uniquement à partir de cette perspective.

## <a name="multiple-anchors"></a>Plusieurs points d’ancrage

Éclairage peut faire une différence dans les fonctionnalités visual détecte une application. Ancres créés dans une lumière naturelle forte peuvent être difficiles à localiser dans la lumière artificielle et vice versa.  

Si vous rencontrez ce problème, il peut aider à créer deux points d’ancrage. Au même endroit, créer un point d’ancrage dans l’heure d’été et l’autre dans la lumière artificielle. Votre application peut ensuite interroger pour les deux points d’ancrage. Lorsqu’un point d’ancrage est situé, l’application aura une pose pour le point d’ancrage. 

De même, dans les environnements où les fonctionnalités visual modifier, car le déplacement de la plupart des objets, plusieurs points d’ancrage peuvent aider à. Lorsqu’un point d’ancrage devient trop difficile à trouver en raison de modifications importantes dans l’environnement, vous pouvez remplacer le point d’ancrage avec un autre. Vous êtes susceptible d’effectuer, par exemple, dans un magasin de vente au détail où la mise en page est actualisé à intervalles de quelques mois.

## <a name="targets-and-rooms"></a>Cibles et salles

Dans de nombreux cas, un point d’ancrage est un point d’entrée à l’expérience votre application. Vous voudrez effectuer cette étape rapidement et fiable pour permettre aux utilisateurs pouvez entrer votre expérience. Passer du temps sur la façon dont les utilisateurs trouveront les points d’ancrage est une étape de conception importantes. Il est utile de réfléchir à la recherche de points d’ancrage en termes de deux scénarios étendus : *cibles* et *salles*.

### <a name="targets"></a>Cibles

Dans le scénario cible, l’emplacement d’un point d’ancrage est bien connu. Par exemple, dans une application fictive peinture de réalité mixte, un utilisateur place un canevas virtuel sur le mur. Elle indique les autres aux utilisateurs dans la salle de point de leurs appareils au même endroit sur le mur à localiser le point d’ancrage et à commencer l’expérience.  

Un autre exemple de scénario cible peut être une connexion dans un café qui lit, « Analyse d’affaires ». Café a placé un point d’ancrage ici. Comme le signe d’analyse les utilisateurs, ils localiser le point d’ancrage et entrez l’expérience de réalité augmentée pour trouver des offres de café.

Dans le scénario cible, les photos peuvent aider. Si vous affichez les utilisateurs d’une photo de la cible prévue sur leur appareil, ils peuvent rapidement identifier les éléments à analyser dans le monde réel. Par exemple, vous pouvez aider vos utilisateurs arrivent dans la zone générale d’une cible à l’aide de GPS. Quand l’utilisateur arrive, votre application affiche une photo de la cible. L’utilisateur recherche autour de l’espace, recherche la cible et l’analyse pour le point d’ancrage.

![Illustration d’une ancre, montrant une photo de la cible sur un appareil mobile](./media/start-here-edit.png)

### <a name="rooms"></a>Salles de conversation

Dans le scénario de la salle, les utilisateurs entrent un espace simplement savoir qu'est une ancre ici quelque part. Utilisateurs analyser l’espace avec leur appareil et de repérer rapidement le point d’ancrage.

Cette expérience généralement vous oblige à créer des ancres bien organisées, comme indiqué dans les différentes façons d’affichage. Si vous avez analysé le faire à partir de nombreuses perspectives lorsque vous avez créé le point d’ancrage, les utilisateurs peuvent analyser presque n’importe où lorsqu’ils tentent de localiser.

![Illustration de la façon dont un utilisateur peut analyser une salle pour trouver un point d’ancrage](./media/scan-room.png)

Essentiellement, vous consacrer plus de temps d’analyse l’espace lorsque vous créez le point d’ancrage afin que les utilisateurs ultérieure peuvent analyser et de localiser rapidement le point d’ancrage. Lorsque vous créez votre expérience, vous devrez envisager ce compromis important.

L’exemple de l’application de peinture de réalité mixte que nous avons abordée précédemment ne fonctionne pas bien comme un scénario de la salle. Ici, l’utilisateur place le point d’ancrage souhaite d’autres personnes à rejoindre l’expérience rapidement. Les utilisateurs ne souhaitent pas attendre avant de démarrer l’expérience jusqu'à ce que l’espace est également analysé. Étant donné que tous les utilisateurs savent exactement pour qu’il pointe leur appareil pour localiser les ancres, cet exemple fonctionne mieux en tant qu’un scénario de cible.

## <a name="anchor-location"></a>Emplacement d’ancrage

Les systèmes de suivi visuel s’appuient sur les fonctionnalités visual dans un environnement. Les fonctionnalités plus visuelle qui inclut une analyse, plus la probabilité de trouver un point d’ancrage.

Suivez les recommandations générales de cette section pour créer une expérience utilisateur qui encourage une analyse utile de l’environnement.

Tout d’abord, si l’utilisateur ne peut pas localiser un point d’ancrage après quelques secondes, l’application doit encourager les utilisateurs à déplacer l’appareil pour capturer des autres perspectives. L’application peut encourageons également aux utilisateurs de se déplacer autour de l’environnement pour rechercher le point d’ancrage à partir d’autres perspectives. Les perspectives de fonctionnalité plus que l’appareil voit, mieux c’est.

Pour les scénarios cibles, demandez à l’utilisateur pour vous déplacer dans la cible pour l’afficher à partir de différentes perspectives. En d’autres termes, demandez à l’utilisateur de capturer la cible à partir de nouvelles perspectives jusqu'à ce que le point d’ancrage se trouve.

Pour les scénarios de salle, demandez-lui d’analyse lente de la salle. Par exemple, demandez à l’utilisateur à activer pour capturer des 180 degrés ou même à 360 degrés de la salle. Ou demandez à l’utilisateur d’afficher la salle à partir d’une nouvelle perspective. 

La méthode la plus significative consiste à analyser à travers la pièce. Une analyse à travers la pièce capture des fonctions visuelles de l’environnement qu’une analyse d’un mur à proximité, par exemple. Une analyse d’un mur proche ne capture autant de fonctionnalités visual utile de l’environnement.

Il n’est pas utile à plusieurs reprises déplacer l’appareil de gauche à droite lorsque vous recherchez un point d’ancrage. Capture simplement les mêmes points à partir de la même perspective.

## <a name="experience-tests"></a>Tests de l’expérience

Dans cet article, nous avons vu des recommandations générales. Avec ancres spatiale, vous écrivez des applications qui interagissent avec le monde réel. Pour cette raison, vous devez consacrer temps pour tester des scénarios du point d’ancrage de votre application en environnement réel. Cela est particulièrement vrai pour les environnements qui représentent où vous prévoyez que vos utilisateurs à utiliser l’application.
