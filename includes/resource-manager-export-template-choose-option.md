---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/03/2021
ms.author: tomfitz
ms.openlocfilehash: 6a7c9b230ffa42a539dd41cc9301915d7dbe3a34
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479150"
---
## <a name="choose-the-right-export-option"></a>Choisir la bonne option d’exportation

Il existe deux façons d’exporter un modèle :

* **Exporter à partir d’un groupe de ressources ou d’une ressource**. Cette option génère un nouveau modèle à partir de ressources existantes. Le modèle exporté est un « instantané » de l’état actuel du groupe de ressources. Vous pouvez exporter un groupe de ressources complet ou des ressources spécifiques au sein de ce groupe de ressources.

* **Enregistrer à partir de l’historique**. Cette option récupère une copie exacte d’un modèle utilisé pour le déploiement. Vous spécifiez le déploiement à partir de l’historique de déploiement. 

Selon l’option choisie, les modèles exportés ont différentes qualités.

| À partir d’un groupe de ressources ou d’une ressource | À partir de l’historique |
| --------------------- | ----------------- |
| Le modèle est généré automatiquement. Vous souhaiterez probablement améliorer le code avant de le déployer. | Le modèle est une copie exacte de celui créé par l’auteur du modèle. Il est prêt à être redéployé sans aucune modification. |
| Le modèle est un instantané de l’état actuel des ressources. Il inclut toutes les modifications manuelles apportées après le déploiement. | Le modèle affiche uniquement l’état des ressources au moment du déploiement. Toutes les modifications manuelles apportées après le déploiement ne sont pas incluses. |
| Vous pouvez sélectionner les ressources à exporter à partir d’un groupe de ressources. | Toutes les ressources pour un déploiement spécifique sont incluses. Vous ne pouvez pas choisir un sous-ensemble de ces ressources ou ajouter des ressources qui ont été ajoutés à un autre moment. |
| Le modèle inclut toutes les propriétés des ressources, y compris certaines propriétés que vous ne définiriez normalement pas pendant le déploiement. Vous souhaiterez peut-être supprimer ou nettoyer ces propriétés avant de réutiliser le modèle. | Le modèle inclut uniquement les propriétés nécessaires au déploiement. Le modèle est plus éparse et plus facile à lire. |
| Le modèle probablement n’inclut probablement pas tous les paramètres dont vous avez besoin pour le réutiliser. La plupart des valeurs de propriété sont codées en dur dans le modèle. Pour redéployer le modèle dans d’autres environnements, vous devez ajouter des paramètres qui augmentent la possibilité de configurer les ressources.  Vous pouvez désélectionner **Inclure des paramètres** afin de pouvoir créer vos propres paramètres. | Le modèle inclut des paramètres qui permettent de facilement le redéployer dans différents environnements. |

Exportez le modèle à partir d’une ressource ou d’un groupe de ressources, lorsque :

* Vous devez capturer les modifications apportées aux ressources après le déploiement d’origine.
* Vous devez sélectionner les ressources à exporter.
* Les ressources n’ont pas été créées avec un modèle.

Exportez le modèle à partir de l’historique dans les cas suivants :

* Vous souhaitez un modèle facile à réutiliser.
* Vous n’avez pas besoin d’inclure les modifications apportées après le déploiement d’origine.
