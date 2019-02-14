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
ms.openlocfilehash: ceaffebde65402db385ff8b906d77c2ddadc6f97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860948"
---
# <a name="face-api-frequently-asked-questions"></a>Forum Aux Questions (FAQ) sur l’API Visage

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Si vous ne trouvez pas les réponses à vos questions dans ce Forum Aux Questions, interrogez la communauté de l’API Visage sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contactez le support technique sur [UserVoice](https://cognitive.uservoice.com/).

-----
**Question** : Quels facteurs peuvent réduire la précision des composants Reconnaissance, Vérification ou Rechercher des éléments similaires de l’API Visage ?

**Réponse** : Il s’agit généralement des mêmes facteurs qui font que l’homme éprouve des difficultés pour procéder à une identification :
* Obstacles devant les yeux
* Lumière crue, par exemple, contre-jour marqué
* Modifications d’une coupe de cheveux ou d’une moustache/barbe
* Modifications en raison de l’âge
* Très grande expressivité du visage (par exemple, la peur)

L’API Visage réussit souvent dans des situations difficiles comme celles-ci, mais sa précision peut s’en trouver réduite. Pour renforcer la reconnaissance et surmonter ces difficultés, formez votre personnel en lui soumettant des photos qui présentent différents angles et éclairages.

-----
**Question** :  Je transmets les données d’une image binaire, mais j’obtiens l’erreur « image de visage non valide ».

**Réponse** :  Cela signifie que l’algorithme a rencontré des problèmes lors de l’analyse de l’image. Causes possibles :
* Les formats d’image d’entrée pris en charge incluent JPEG, PNG, GIF (la première image), BMP.
* La taille du fichier image ne doit pas dépasser 4 Mo
* Les visages sont détectables selon une plage de tailles allant de 36 x 36 à 4 096 x 4 096 pixels. Les visages en dehors de cette plage ne sont pas détectés.
* Certains visages peuvent ne pas être détectés en raison de difficultés techniques, par exemple, les prises très rapprochées (portrait) et les occlusions importantes. Les prises frontales et quasi-frontales produisent les meilleurs résultats.

-----
