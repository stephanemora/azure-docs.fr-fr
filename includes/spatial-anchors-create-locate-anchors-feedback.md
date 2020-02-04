---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694401"
---
## <a name="provide-feedback-to-the-user"></a>Envoyer vos commentaires à l’utilisateur

Vous pouvez écrire du code pour gérer l’événement de session de mise à jour. Cet événement se déclenche chaque fois que la session améliore sa compréhension de votre environnement. Vous pouvez ainsi :

- Utilisez la classe `UserFeedback` pour apporter vos commentaires à l’utilisateur pendant que l’appareil est déplacé et que la session met à jour sa compréhension de l’environnement. Pour ce faire, effectuez la procédure suivante :
- Déterminez s’il y a suffisamment de données spatiales suivies pour créer ou localiser des ancres spatiales. Vous pouvez le déterminer avec `ReadyForCreateProgress` ou `RecommendedForCreateProgress`. Une fois que `ReadyForCreateProgress` est supérieur à 1, nous disposons de suffisamment de données pour enregistrer une ancre spatiale cloud. Toutefois, nous vous recommandons d’attendre que `RecommendedForCreateProgress` soit supérieur à 1 pour le faire.
