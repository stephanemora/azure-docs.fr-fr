---
title: Limites et quotas du service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Découvrez les limites et les quotas du service Vision personnalisée d’Azure Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370289"
---
# <a name="limits-and-quotas"></a>Limites et quotas

Il existe trois niveaux de clés pour le service Vision personnalisée. Les ressources F0 et S0 sont disponibles sur le portail Azure. Pour plus d’informations sur la tarification et les définitions des transactions, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Les projets F0 peuvent être mis à niveau vers des projets S0.

Les ressources de projet des essais gratuits limités sont attachées à votre identifiant Vision personnalisée (autrement dit, un compte AAD ou un compte MSA). Elles sont destinées à être utilisées pour de courts essais du service.  Les comptes créés avec la préversion gratuite, avant l’introduction des préversions Azure (1er mars 2018), conserveront leurs quotas précédents pour les essais gratuits limités. 

||**Essai gratuit limité**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projets|2|2|100|
|Images d’apprentissage par projet|5 000|5 000|50 000|
|Prédictions/mois|10 000 |10 000|Illimité|
|Balises/projet|50|50|250|
|Itérations |10|10|10|
|Nombre minimal d’images étiquetées par balise, classification (recommandation : plus de 50) |5|5|5|
|Nombre minimal d’images étiquetées par balise, détection d’objets (recommandation : plus de 50)|15|15|15|
|Durée de stockage des images de prédiction|30 jours|30 jours|30 jours|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) avec stockage (transactions par seconde)|2|2|10|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) sans stockage (transactions par seconde)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (appels d’API par seconde)|2|2|10|
|[Autres appels d’API](https://go.microsoft.com/fwlink/?linkid=865446) (transactions par seconde)|10|10|10|
|Taille maximale de l’image (chargement de l’image d’apprentissage) |6 Mo|6 Mo|6 Mo|
|Taille maximale de l’image (prédiction)|4 Mo|4 Mo|4 Mo|

Les limites sur le *nombre d’images d’apprentissage par projet* et le *nombre de balises par projet* sont censées augmenter au fil du temps pour les projets S0. 
