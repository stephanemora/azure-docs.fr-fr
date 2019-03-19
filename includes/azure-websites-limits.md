---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: c2db5740d6373c8f6be5dabe0260fe7f99575641
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553771"
---
| Ressource | Gratuit | Partagé | De base | standard | Premium (v2) | Isolé </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Applications Web, mobiles ou API](https://azure.microsoft.com/services/app-service/) par [plan Azure App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimité<sup>2</sup> |Illimité<sup>2</sup> |Illimité<sup>2</sup> |Illimité<sup>2</sup>|
| [Plan App Service](../articles/app-service/overview-hosting-plans.md) |10 par région |10 par groupe de ressources |100 par groupe de ressources |100 par groupe de ressources |100 par groupe de ressources |100 par groupe de ressources|
| types d'instance de calcul |Partagé |Partagé |Dédié<sup>3</sup> |Dédié<sup>3</sup> |Dédié<sup>3</sup></p> |Dédié<sup>3</sup>|
| [Monter en charge](../articles/app-service/web-sites-scale.md) (nombre maximal d’instances) |1 partagée |1 partagée |3 dédiées<sup>3</sup> |10 dédiées<sup>3</sup> |20 dédiées<sup>3</sup>|100 dédiées<sup>4</sup>|
| Stockage<sup>5</sup> |1 Go<sup>5</sup> |1 Go<sup>5</sup> |10 Go<sup>5</sup> |50 Go<sup>5</sup> |250 GO<sup>5</sup></p> |1 TO<sup>5</sup>|
| Temps processeur (5 minutes)<sup>6</sup> |3 minutes |3 minutes |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Temps processeur (jour)<sup>6</sup> |60 minutes |240 minutes |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |Illimité, facturation aux [tarifs standard](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Mémoire (1 heure) |1 024 Mo par plan App Service |1 024 Mo par application |S.O. |N/A |N/A |S.O. |
| Bande passante |165 Mo |Illimitée, application du [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) |Illimitée, application du [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) |Illimitée, application du [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) |Illimitée, application du [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) |Illimitée, application du [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Architecture de l'application |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web sockets par instance<sup>7</sup> |5. |35 |350 |Illimité |Illimité |Illimité |
| [Connexions simultanées du débogueur](../articles/app-service/troubleshoot-dotnet-visual-studio.md) par application |1 |1 |1 |5. |5. |5. |
| App Service Certificates par abonnement<sup>10</sup>| Non pris en charge | Non pris en charge |10 |10 |10 |10 |
| Domaines personnalisés par application</a> |0 (sous-domaine azurewebsites.net uniquement)|500 |500 |500 |500 |500 |
| domaines personnalisés [Prise en charge SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Non pris en charge, certificat générique pour *. azurewebsites.net disponible par défaut|Non pris en charge, certificat générique pour *. azurewebsites.net disponible par défaut|Nombre illimité de connexions SNI SSL |Connexions SSL SNI illimitées et 1 connexion IP SSL incluses |Connexions SSL SNI illimitées et 1 connexion IP SSL incluses | Connexions SSL SNI illimitées et 1 connexion IP SSL incluses|
| Équilibrage de charge intégré | |X |X |X |X |X<sup>9</sup> |
| [Toujours actif](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Sauvegardes planifiées](../articles/app-service/manage-backup.md) | | | | Sauvegardes planifiées toutes les 2 heures, un maximum de 12 sauvegardes par jour (manuel + planifiée) | Sauvegardes planifiées toutes les heures, un maximum de 50 sauvegardes par jour (manuel + planifiée) | Sauvegardes planifiées toutes les heures, un maximum de 50 sauvegardes par jour (manuel + planifiée) |
| [Autoscale](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [Tâches web](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Surveillance de point de terminaison](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Emplacements intermédiaires](../articles/app-service/deploy-staging-slots.md) | | | |5. |20 |20 |
| Contrat SLA | |  |99,9 % |99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Des quotas d'applications et de stockage s'appliquent pour chaque plan App Service, sauf mention contraire.  
<sup>2</sup>Le nombre d'applications qui peuvent être hébergées sur ces ordinateurs dépend de l'activité des applications, de la taille des instances des ordinateurs et de l'utilisation de ressources correspondante.  
<sup>3</sup>Les instances dédiées peuvent être de différentes tailles. Pour en savoir plus, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>bien plus encore sont autorisés à la demande.  
<sup>5</sup>La limite de stockage est la taille totale du contenu entre toutes les applications du même plan de service d’application.  
<sup>6</sup>Ces ressources sont limitées par les ressources physiques sur les instances dédiées (taille de l'instance et nombre d'instances).  
<sup>7</sup>Si vous mettez à l'échelle une application sur deux instances dans la version de base, vous disposez de 350 connexions simultanées pour chacune des deux instances.  
<sup>8</sup>Exécution d’exécutables et/ou de scripts personnalisés à la demande, selon une planification ou en continu en tant que tâche en arrière-plan au sein de votre instance App Service. La fonctionnalité AlwaysOn est nécessaire à l'exécution de tâches web en continu. Azure Scheduler (version Gratuite ou Standard) est nécessaire aux tâches web programmées. Il n’existe aucune limite prédéfinie pour le nombre de tâches Web pouvant s’exécuter dans une instance de Service de l’application. Il existe des limites pratiques qui dépendent de ce que le code d’application tente de faire.  
<sup>9</sup>références (SKU) app Service isolé ont la capacité à être charger en interne (ILB) à charge équilibrée avec Azure Load Balancer, donc il n’existe aucune connectivité publique à partir d’internet. Aussi, certaines fonctionnalités d’une instance isolée d’App Service à charge équilibrée en interne doivent être utilisées à partir de machines qui ont un accès direct au point de terminaison réseau avec équilibrage de charge en interne.  
<sup>10</sup>limite de quota de l’App Service Certificate par abonnement peut être augmentée via une demande de support à une limite maximale de 200.  