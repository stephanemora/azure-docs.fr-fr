---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657776"
---
Form Recognizer fonctionne sur les documents d’entrée qui répondent aux critères suivants :

* Format JPG, PNG ou PDF (texte ou numérisé). Les PDF englobant du texte sont préférables, car il n’existe aucun risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* Si vos fichiers PDF sont verrouillés par mot de passe, vous devez supprimer le verrou avant de les soumettre.
* La taille du fichier doit être inférieure à 4 Mo.
* Pour les images, les dimensions doivent être comprises entre 600 x 100 et 4 200 x 4 200 pixels.
* S’ils sont numérisés à partir de documents papier, la qualité de numérisation des formulaires doit être supérieure.
* Le texte doit utiliser l’alphabet latin (lettres de l’alphabet).
* Les données doivent contenir des clés et des valeurs.
* Les clés peuvent apparaître au-dessus ou à gauche des valeurs, mais pas en dessous ni à droite.

Form Recognizer ne prend actuellement pas en charge ces types de données d’entrée :

* Tables complexes (tables imbriquées, cellules ou en-têtes fusionnés, etc.).
* Cases à cocher ou cases d’option.
* Documents PDF de plus de 50 pages.
