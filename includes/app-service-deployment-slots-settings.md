---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129690"
---
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Au cours d’un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement après l’échange (éléments propres à un emplacement). Les listes suivantes représentent les paramètres qui évoluent lorsque vous échangez les emplacements.

**Paramètres échangés**:

* Paramètres généraux, par exemple versions du framework, 32/64 bits, sockets web
* Paramètres d’application (peuvent être configurés pour respecter un emplacement)
* Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
* Mappages de gestionnaires
* Certificats publics
* Contenu WebJobs
* Connexions hybrides*
* Intégration du réseau virtuel*
* Points de terminaison de service*
* Azure Content Delivery Network*

Il est prévu que les fonctionnalités marquées d’un astérisque (*) ne soient plus échangées. 

**Paramètres non échangés** :

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats non publics et paramètres TLS/SSL
* Paramètres de mise à l’échelle
* Planificateurs WebJobs
* Restrictions d’adresse IP
* Always On
* Paramètres des journaux de diagnostic
* Partage des ressources cross-origin (CORS)

> [!NOTE]
> Certains paramètres d’application qui s’appliquent à des paramètres non échangés ne sont pas non plus échangés. Par exemple, étant donné que les paramètres du journal de diagnostic ne sont pas échangés, les paramètres d’application associés comme `WEBSITE_HTTPLOGGING_RETENTION_DAYS` et `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ne sont pas non plus échangés, même s’ils n’apparaissent pas comme des paramètres d’emplacement.
>
