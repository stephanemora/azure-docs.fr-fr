---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592603"
---
Form Recognizer fonctionne sur les documents d’entrée qui répondent aux critères suivants :

* Format JPG, PNG ou PDF (texte ou numérisé). Les PDF englobant du texte sont préférables, car il n’existe aucun risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* La taille du fichier doit être inférieure à 4 mégaoctets (Mo).
* Pour les images, les dimensions doivent être comprises entre 50 x 50 et 4200 x 4200 pixels.
* S’ils sont numérisés à partir de documents papier, la qualité de numérisation des formulaires doit être supérieure.
* Le texte doit utiliser l’alphabet latin (lettres de l’alphabet).
* Les données doivent être tapées au clavier (et non à la main).
* Les données doivent contenir des clés et des valeurs.
* Les clés peuvent apparaître au-dessus ou à gauche des valeurs, mais pas en dessous ni à droite.

Form Recognizer ne prend actuellement pas en charge ces types de données d’entrée :

* Tables complexes (tables imbriquées, cellules ou en-têtes fusionnés, etc.).
* Cases à cocher ou cases d’option.
* Documents PDF de plus de 50 pages.