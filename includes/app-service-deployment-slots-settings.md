---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/09/2021
ms.author: cephalin
ms.openlocfilehash: 0cc0758c2c4c2c0aca9f1b55ef8ee7c5e5790f31
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126057192"
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
* Points de terminaison de service*
* Azure Content Delivery Network*
* Mappages de chemin d’accès

Il est prévu que les fonctionnalités marquées d’un astérisque (*) ne soient plus échangées. 

**Paramètres non échangés** :

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats non publics et paramètres TLS/SSL
* Paramètres de mise à l’échelle
* Planificateurs WebJobs
* Restrictions d’adresse IP
* Always On
* Paramètres de diagnostic
* Partage des ressources cross-origin (CORS)
* Intégration du réseau virtuel
* Identités managées
* Paramètres se terminant par le suffixe _EXTENSION_VERSION

> [!NOTE]
> Pour rendre ces paramètres remplaçables, ajoutez le paramètre d’application `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` dans chaque emplacement de l’application, et affectez-lui la valeur `0` ou `false`. Ces paramètres sont tous remplaçables ou aucun d’entre eux ne l’est. Vous ne pouvez pas rendre certains paramètres remplaçables et d’autres, pas. Les identités managées ne sont jamais échangées et ne sont pas affectées par ce paramètre d’application de remplacement.
>
> Certains paramètres d’application qui s’appliquent à des paramètres non échangés ne sont pas non plus échangés. Par exemple, étant donné que les paramètres de diagnostic ne sont pas échangés, les paramètres d’application associés comme `WEBSITE_HTTPLOGGING_RETENTION_DAYS` et `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ne sont pas non plus échangés, même s’ils n’apparaissent pas comme des paramètres d’emplacement.
>
