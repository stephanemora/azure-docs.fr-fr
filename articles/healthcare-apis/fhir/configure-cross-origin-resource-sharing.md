---
title: Configurer le partage des ressources Cross-Origin dans le service FHIR
description: Cet article explique comment configurer le partage des ressources Cross-Origin dans le service FHIR
author: matjazl
ms.author: zxue
ms.date: 08/03/2021
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: e322b1fb848f9156b1d00d2d1ae051ef62536c4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778883"
---
# <a name="configure-cross-origin-resource-sharing-in-fhir-service"></a>Configurer le partage des ressources Cross-Origin dans le service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le service FHIR dans les API de santé Azure (par le biais du service FHIR) prend en charge le [partage des ressources Cross-Origin (cors)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). Ce dernier permet de configurer des paramètres afin que les applications d’un domaine (origine) puissent accéder aux ressources d’un domaine différent par le biais d’une requête inter-domaines.

Le partage des ressources cross-origin (CORS) est souvent utilisé dans une application à page unique qui doit appeler une API RESTful sur un autre domaine.

Pour configurer un paramètre CORS dans le service FHIR, spécifiez les paramètres suivants :

- **Origines (Access-Control-Allow-Origin)** . Liste des domaines autorisés à effectuer des demandes Cross-Origin auprès du service FHIR. Chaque domaine (origine) doit être entré dans une ligne distincte. Vous pouvez entrer un astérisque (*) pour autoriser les appels à partir de n’importe quel domaine, toutefois, cela n’est pas recommandé dans la mesure ou il existe un risque de sécurité.

- **En-têtes (Access-Control-Allow-Headers)** . Liste d’en-têtes contenus dans la requête d’origine. Pour autoriser tous les en-têtes, saisissez un astérisque (*).

- **Méthodes (Access-Control-Allow-Methods)** . Méthodes autorisées (PUT, GET, POST etc.) dans un appel d’API. Choisissez **Sélectionner tout** pour toutes les méthodes.

- **Âge maximal (Access-Control-Max-Age)** . Valeur en secondes pour mettre en cache les résultats de requête préliminaire pour Access-Control-Allow-Headers et Access-Control-Allow-Methods.

- **Autoriser les informations d’identification (Access-Control-Allow-Credentials)** . Les requêtes CORS n’incluent normalement pas de cookies permettant d’empêcher les attaques par [falsification de requête intersites](https://en.wikipedia.org/wiki/Cross-site_request_forgery). Si vous sélectionnez ce paramètre, la requête peut être effectuée de manière à inclure des informations d’identification, telles que des cookies. Vous ne pouvez pas configurer ce paramètre si vous avez déjà défini Origines sur un astérisque (*).

![Paramètres du partage des ressources cross-origin (CORS)](media/cors/cors.png)

>[!NOTE]
>Vous ne pouvez pas spécifier de paramètres différents pour différentes origines de domaine. Tous les paramètres (**En-têtes**, **Méthodes**, **Âge maximal** et **Autoriser les informations d’identification**) s’appliquent à toutes les origines spécifiées dans le paramètre Origines.