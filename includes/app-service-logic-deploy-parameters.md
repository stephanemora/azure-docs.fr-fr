---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66167431"
---
Avec Azure Resource Manager, vous pouvez définir des paramètres pour les valeurs à utiliser lors du déploiement du modèle. Le modèle inclut une section appelée `parameters`, qui contient toutes les valeurs des paramètres. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources que vous voulez déployer.

> [!NOTE]
> Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Définissez des paramètres seulement pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement.

Quand vous définissez des paramètres :

* Pour spécifier les valeurs autorisées qu’un utilisateur peut fournir lors du déploiement, utilisez le champ **allowedValues**.

* Pour affecter des valeurs par défaut à un paramètre quand aucune valeur n’est fournie lors du déploiement, utilisez le champ **defaultValue**. 
