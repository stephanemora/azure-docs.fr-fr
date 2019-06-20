---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177337"
---
## <a name="validate-that-a-container-is-running"></a>Valider l’exécution d’un conteneur 

Il existe plusieurs façons de confirmer que le conteneur s’exécute. 

|Requête|Objectif|
|--|--|
|`http://localhost:5000/`|Le conteneur fournit une page d’accueil.|
|`http://localhost:5000/status`|Demandée avec GET, pour confirmer que le conteneur est en cours d’exécution sans provoquer de requête de point de terminaison. Cette requête peut être utilisée pour les [probes liveness et readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) de Kubernetes.|
|`http://localhost:5000/swagger`|Le conteneur fournit un ensemble complet de documentation pour les points de terminaison et une fonctionnalité `Try it now`. Avec cette fonctionnalité, vous pouvez entrer vos paramètres dans un formulaire HTML basé sur le web, et constituer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer les en-têtes HTTP, et le format du corps qui est nécessaire. |

![Page d’accueil du conteneur](./media/cognitive-services-containers-api-documentation/container-webpage.png)
