---
title: Tarification et limites – Service Vision personnalisée
titlesuffix: Azure Cognitive Services
description: Découvrez les limites et quotas applicables au service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: 8e96447f2a1b5b68ebf99ab4006fe43937f4c75f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884424"
---
# <a name="pricing-and-limits"></a>Tarification et limites

Il existe trois niveaux de clés pour le service Vision personnalisée. Les ressources de projet des essais gratuits limités sont attachées à votre identifiant Vision personnalisée (autrement dit, un compte Azure Active Directory ou un compte MSA). Elles sont destinées à être utilisées pour de courts essais du service. Vous pouvez souscrire à un abonnement F0 (gratuit) ou S0 (standard) via le portail Azure. Consultez la page [Tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondante pour en savoir plus sur la tarification et les transactions.

Les comptes créés avec la préversion gratuite, avant l’introduction des préversions Azure (1er mars 2018), conserveront leurs quotas précédents pour les essais gratuits limités. 

Le nombre d’images d’apprentissage par projet et le nombre de balises par projet sont censés augmenter au fil du temps pour les projets S0.

||**Essai gratuit limité**|**F0**|**S0**|
|-----|-----|-----|-----|
|Projets|2|2|100|
|Images d’apprentissage par projet, classification|5 000|5 000|50 000|
|Images d’apprentissage par projet, détection d’objet|5 000|5 000|10 000|
|Prédictions/mois|10 000 |10 000|Illimité|
|Balises/projet|50|50|250|
|Itérations |10|10|10|
|Nombre minimal d’images étiquetées par balise, classification (recommandation : plus de 50) |5.|5.|5.|
|Nombre minimal d’images étiquetées par balise, détection d’objets (recommandation : plus de 50)|15|15|15|
|Durée de stockage des images de prédiction|30 jours|30 jours|30 jours|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) avec stockage (transactions par seconde)|2|2|10|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) sans stockage (transactions par seconde)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (appels d’API par seconde)|2|2|10|
|[Autres appels d’API](https://go.microsoft.com/fwlink/?linkid=865446) (transactions par seconde)|10|10|10|
|Taille maximale de l’image (chargement de l’image d’apprentissage) |6 Mo|6 Mo|6 Mo|
|Taille maximale de l’image (prédiction)|4 Mo|4 Mo|4 Mo|


