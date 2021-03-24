---
title: FAQ sur les problèmes de disponibilité des applications et des services
description: Cet article répertorie les questions fréquentes sur la disponibilité des applications et des services pour Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742894"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problèmes de disponibilité des applications et des services pour Azure Cloud Services (classique) : Forum Aux Questions (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (support étendu)](../cloud-services-extended-support/overview.md) est un nouveau modèle de déploiement basé sur Azure Resource Manager pour le produit Azure Cloud Services. Du fait de ce changement, les instances Azure Cloud Services qui s’exécutent sur le modèle de déploiement basé sur Azure Service Manager ont été renommées Cloud Services (classique). Tous les nouveaux déploiements doivent passer par [Cloud Services (support étendu)](../cloud-services-extended-support/overview.md).

Cet article comprend des questions fréquentes sur les problèmes de disponibilité des applications et des services pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Mon rôle a été recyclé. Mon service cloud a-t-il fait l’objet d’une mise à jour ?
Environ une fois par mois, Microsoft publie une nouvelle version du système d’exploitation (SE) invité pour les machines virtuelles PaaS Microsoft Azure.Le SE invité n’est qu’une mise à jour parmi d’autres. Une publication peut être affectée par beaucoup d’autres facteurs. Par ailleurs, Azure s’exécute sur des centaines de milliers de machines. Il est donc impossible de prédire la date et l’heure exactes auxquelles vos rôles seront redémarrés. Nous mettrons à jour le Flux RSS de mise à jour du SE invité avec les dernières informations dont nous disposons, mais considérez l’heure indiquée comme une valeur approximative. Nous sommes conscients que cela peut être problématique pour les clients. Nous tachons d’ailleurs de limiter ou minuter avec précision ces redémarrages.

Pour plus d’informations sur les dernières mises à jour du SE invité, consultez [Versions du SE invité et matrice de compatibilité du kit SDK Azure](cloud-services-guestos-update-matrix.md).

Pour obtenir des informations utiles sur les redémarrages et des liens vers d’autres détails techniques concernant les mises à jour des SE invité et hôte, consultez le billet de blog MSDN intitulé [Role Instance Restarts Due to OS Upgrades](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades) (Redémarrage des instances de rôle en raison des mises à niveau du SE).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Pourquoi la première requête à destination de mon service cloud prend-elle plus de temps que d’habitude après que le service est resté inactivité pendant un certain temps ?
Quand le serveur web reçoit la première requête, il commence par recompiler le code avant de traiter la requête. C’est pourquoi la première requête prend plus de temps que les autres. Par défaut, le pool d’applications s’arrête en cas d’inactivité de l’utilisateur. Par ailleurs, le pool d’applications est recyclé toutes les 1 740 minutes (29 heures).

Les pools d’applications IIS (Internet Information Services) peuvent être recyclées régulièrement pour éviter des états instables susceptibles d’entraîner des blocages d’applications, une absence de réponse ou des fuites de mémoire.

Les documents suivants vous aideront à comprendre et à limiter ce problème :
* [Fixing slow initial load for IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis) (Remédier à la lenteur du chargement initial pour IIS)
* [IIS 7.5 web application first request after app-pool recycle very slow](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow) (Premier requête d’application web IIS 7.5 après une opération de recyclage de pool d’applications très lente)

Si vous voulez modifier le comportement par défaut d’IIS, vous devez avoir recours à des tâches de démarrage, car si vous appliquez manuellement des modifications aux instances de rôle web, les modifications seront perdues à la fin du processus.

Pour plus d’informations, consultez [Comment configurer et exécuter des tâches de démarrage pour un service cloud](cloud-services-startup-tasks.md).