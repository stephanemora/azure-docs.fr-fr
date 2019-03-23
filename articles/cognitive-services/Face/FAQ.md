---
title: 'Forum Aux Questions (FAQ) : API Visage'
titlesuffix: Azure Cognitive Services
description: Vous trouverez ici des réponses aux questions les plus posées sur le service de l’API Visage.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351327"
---
# <a name="face-api-frequently-asked-questions"></a>Forum Aux Questions (FAQ) sur l’API Visage

> [!TIP]
> Si vous ne trouvez pas les réponses à vos questions dans ce Forum Aux Questions, interrogez la communauté de l’API Visage sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contactez le support technique sur [UserVoice](https://cognitive.uservoice.com/).

-----
**Question** : Quels facteurs peut réduire la précision de l’API visage de reconnaissance, vérification ou de rechercher similaire ?

**Réponse** : Il est généralement les mêmes cas où les êtres humains ont des difficultés à identifier une personne, y compris :
* Obstacles devant les yeux
* Éclair (par exemple, graves rétroéclairage)
* Modifications d’une coupe de cheveux ou d’une moustache/barbe
* Modifications en raison de l’âge
* Expressions du visage extrêmes (par exemple, cette situation)

API visage est souvent réussie dans les cas complexes à l’exemple ci-dessus, mais analyse de précision peut être réduit. Pour renforcer la reconnaissance et surmonter ces difficultés, formez votre personnel en lui soumettant des photos qui présentent différents angles et éclairages.

-----
**Question** :  Je transmets les données d’une image binaire, mais j’obtiens l’erreur « image de visage non valide ».

**Réponse** :  Cette erreur implique que l’algorithme a eu un problème de l’analyse de l’image. Causes possibles :
* Les formats d’image d’entrée pris en charge incluent JPEG, PNG, GIF (la première image), BMP.
* La taille du fichier image ne doit pas dépasser 4 Mo.
* Les visages sont détectables selon une plage de tailles allant de 36 x 36 à 4 096 x 4 096 pixels. Les visages en dehors de cette plage ne sont pas détectés.
* Certains visages peuvent ne pas être détectés en raison de défis techniques, par exemple grand angle (head-pose), les OCCLUSIONS importantes. Les prises frontales et quasi-frontales produisent les meilleurs résultats.

-----
