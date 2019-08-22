---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623699"
---
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Au cours d’un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement après l’échange (éléments propres à un emplacement). Les listes suivantes représentent les paramètres qui évoluent lorsque vous échangez les emplacements.

**Paramètres échangés**:

* Paramètres généraux, par exemple versions du framework, 32/64 bits, sockets web
* Paramètres d’application (peuvent être configurés pour respecter un emplacement)
* Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
* Mappages de gestionnaires
* Paramètres de surveillance et de diagnostics
* Certificats publics
* Contenu WebJobs
* Connexions hybrides*
* Intégration du réseau virtuel*
* Points de terminaison de service*
* Azure Content Delivery Network*

Prochainement, les fonctionnalités marquées d’un astérisque (*) ne pourront plus être échangées. 

**Paramètres non échangés** :

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats privés et liaisons SSL
* Paramètres de mise à l'échelle
* Planificateurs WebJobs
* Restrictions d’adresse IP
* Always On
* Paramètres de protocole (HTTPS, version TLS, certificats clients)
* Paramètres des journaux de diagnostic
* Partage des ressources cross-origin (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->