---
title: Récupération d’urgence pour l’API Azure pour FHIR
description: Dans cet article, vous allez apprendre à activer les fonctionnalités de récupération d’urgence pour l’API Azure pour FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434381"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Récupération d’urgence pour l’API Azure pour FHIR

L’API Azure pour FHIR® est un service entièrement géré basé sur des ressources d’interopérabilité de la santé rapides (FHIR®). Pour répondre aux exigences métier et de conformité, vous pouvez utiliser la fonctionnalité de récupération d’urgence (DR) pour l’API Azure pour FHIR.

La fonctionnalité de récupération d’urgence fournit un objectif de point de récupération (RPO) de 15 minutes et un objectif de temps de récupération (RTO) de 60 minutes.

 ## <a name="how-to-enable-dr"></a>Comment activer la récupération d’urgence 
  
Pour activer la fonctionnalité de récupération d’urgence, créez un ticket de support à usage unique. Vous pouvez choisir une région Azure couplée ou une autre région dans laquelle l’API Azure pour FHIR est prise en charge. L’équipe du support technique de Microsoft activera la fonctionnalité de récupération d’urgence en fonction de la priorité de support.

## <a name="how-the-dr-process-works"></a>Fonctionnement du processus de récupération d’urgence

Le processus de récupération d’urgence implique les étapes suivantes : 
* Réplication des données
* Basculement automatique
* Récupération de la région affectée
* Restauration manuelle

### <a name="data-replication-in-the-secondary-region"></a>Réplication des données dans la région secondaire

Par défaut, l’API Azure pour FHIR offre une protection des données par le biais de la sauvegarde et de la restauration. Lorsque la fonctionnalité de récupération d’urgence est activée, la réplication des données commence. Un réplica de données est automatiquement créé et synchronisé dans la région Azure secondaire. La réplication initiale des données peut prendre quelques minutes à quelques heures, ou plus, en fonction de la quantité de données. Le réplica de données secondaire est une réplication des données principales. Elle est utilisée directement pour récupérer le service et permet d’accélérer le processus de récupération.

Il est à noter que le débit RU/s doit avoir les mêmes valeurs dans les régions primaire et secondaire.

[![Azure Traffic Manager. ](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>Basculement automatique

En cas de panne de la région primaire, l’API Azure pour FHIR bascule automatiquement vers la région secondaire et le même point de terminaison de service est utilisé. Le service est supposé reprendre en une heure ou moins, et la perte de données potentielle peut atteindre jusqu’à 15 minutes. D’autres modifications de configuration peuvent être nécessaires. Pour plus d’informations, consultez [modifications de configuration dans Dr](#configuration-changes-in-dr).

[![Basculement en cas de récupération d’urgence. ](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>Récupération de la région affectée

Une fois que la région affectée est récupérée, elle est automatiquement disponible en tant que région secondaire et redémarrage de la réplication des données. Vous pouvez démarrer le processus de récupération des données ou attendre la fin de l’étape de restauration automatique.

[![Réplication en cas de récupération d’urgence. ](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

Lorsque le calcul a restauré la région récupérée et que les données n’ont pas été restaurées, il peut y avoir des latences réseau potentielles. La raison principale est que le calcul et les données se trouvent dans deux régions différentes. Les latences du réseau doivent disparaître automatiquement dès que les données sont restaurées dans la région récupérée par le biais d’un déclencheur manuel.

[![Latence du réseau. ](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>Restauration manuelle

Le calcul bascule automatiquement vers la région récupérée. Les données sont restaurées manuellement vers la région récupérée par l’équipe du support technique de Microsoft à l’aide du script. 

[Restauration ![ automatique en cas de récupération d’urgence. ](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>Modifications de configuration dans DR

D’autres modifications de configuration peuvent être nécessaires lorsque vous utilisez un lien privé, une clé gérée par le client (CMK), un connecteur FHIR IoMT (Internet des éléments médicaux) et des $export.

### <a name="private-link"></a>Liaison privée

Vous pouvez activer la fonctionnalité de liaison privée avant ou après l’approvisionnement de l’API Azure pour FHIR. Vous pouvez également approvisionner un lien privé avant ou après l’activation de la fonctionnalité de récupération d’urgence. Reportez-vous à la liste ci-dessous lorsque vous êtes prêt à configurer un lien privé pour la récupération d’urgence.

* Configurez le lien privé Azure dans la région primaire. Cette étape n’est pas requise dans la région secondaire. Pour plus d’informations, consultez [configurer un lien privé](/azure/healthcare-apis/fhir/configure-private-link) .

* Créez un réseau virtuel Azure dans la région primaire et un autre réseau virtuel dans la région secondaire. Pour plus d’informations, consultez [créer un réseau virtuel à l’aide de l’portail Azure](../../virtual-network/quick-create-portal.md).

* Dans la région primaire, VNet crée une homologation de réseaux virtuels sur le réseau virtuel de la région secondaire. Pour en savoir plus, consultez [Peering de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

* Utilisez les paramètres par défaut, ou vous pouvez adapter la configuration en fonction des besoins. L’importance est que le trafic peut circuler entre les deux réseaux virtuels.

* Lorsque le DNS privé est configuré, le réseau virtuel dans la région secondaire doit être configuré manuellement comme « liaisons de réseau virtuel ». Le réseau virtuel principal doit déjà avoir été ajouté dans le cadre du processus de création de point de terminaison de liaison privée. Pour plus d’informations, consultez [liaisons de réseau virtuel](../../dns/private-dns-virtual-network-links.md).

* Si vous le souhaitez, configurez une machine virtuelle dans le réseau virtuel de la région primaire et une autre dans le réseau virtuel de la région secondaire. Vous pouvez accéder à l’API Azure pour FHIR à partir des deux machines virtuelles.

La fonctionnalité de liaison privée doit continuer à fonctionner pendant une panne régionale et une fois la restauration terminée. Pour plus d’informations, consultez [configurer un lien privé](/azure/healthcare-apis/fhir/configure-private-link).

> [!NOTE]
> La configuration des réseaux virtuels et de l’homologation de réseaux virtuels n’affecte pas la réplication des données.

### <a name="cmk"></a>CMK

Votre accès à l’API Azure pour FHIR est conservé si le coffre de clés qui héberge la clé gérée dans votre abonnement est accessible. Il y a un temps d’arrêt temporaire possible, car Key Vault peut prendre jusqu’à 20 minutes pour rétablir sa connexion. Pour plus d’informations, consultez [Disponibilité et redondance d’Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md).  

### <a name="export"></a>$export

Le travail d’exportation est récupéré à partir d’une autre région au bout de 10 minutes sans mise à jour de l’état du travail. Suivez les conseils relatifs au stockage Azure pour récupérer votre compte de stockage en cas de panne régionale. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage](../../storage/common/storage-disaster-recovery-guidance.md). 

Veillez à accorder les mêmes autorisations à l’identité système de l’API Azure pour FHIR. En outre, si le compte de stockage est configuré avec des réseaux sélectionnés, consultez [Comment exporter des données FHIR](/azure/healthcare-apis/fhir/export-data).

### <a name="iomt-fhir-connector"></a>Connecteur IoMT FHIR

Toute connexion existante ne fonctionnera pas tant que la région n’a pas été restaurée. Vous pouvez créer une nouvelle connexion une fois le basculement terminé et votre serveur FHIR accessible. Cette nouvelle connexion continue à fonctionner lorsque la restauration automatique se produit.

> [!NOTE]
> Le connecteur IoMT est une fonctionnalité en version préliminaire qui ne prend pas en charge la récupération d’urgence. 

## <a name="how-to-test-dr"></a>Test de la récupération d’urgence

Bien que cela ne soit pas obligatoire, vous pouvez tester la fonctionnalité de récupération d’urgence dans un environnement de non-production. Pour le test de récupération d’urgence, seules les données sont incluses et le calcul n’est pas inclus. 

Envisagez les étapes suivantes pour le test de récupération d’urgence.

* Préparer un environnement de test avec des données de test. Il est recommandé d’utiliser une instance de service avec de petites quantités de données pour réduire le temps nécessaire à la réplication des données.
 
* Créez un ticket de support et fournissez votre abonnement Azure et le nom de service de l’API Azure pour FHIR pour votre environnement de test.

* Trouvez un plan de test, comme vous le feriez avec n’importe quel test de récupération d’urgence.
 
* L’équipe du support technique de Microsoft Active la fonctionnalité de récupération d’urgence et confirme que le basculement a eu lieu.

* Exécutez votre test de récupération d’urgence et enregistrez les résultats des tests, ce qui devrait inclure les pertes de données et les problèmes de latence du réseau. 

* Pour la restauration automatique, avertissez l’équipe du support technique de Microsoft que l’étape de restauration automatique est terminée.
 
* Facultatif Partagez vos commentaires avec l’équipe du support technique de Microsoft.


> [!NOTE]
> Le test de récupération d’urgence doublera le coût de votre environnement de test pendant le test. Aucun coût supplémentaire n’est nécessaire après la fin du test de récupération d’urgence et la désactivation de la fonctionnalité de récupération d’urgence.

## <a name="cost-of-disaster-recovery"></a>Coût de la récupération d’urgence

La fonctionnalité de récupération d’urgence entraîne des coûts supplémentaires, car les données du réplica de calcul et de données s’exécutent dans l’environnement de la région secondaire. Pour plus d’informations sur la tarification, reportez-vous à la page Web [Azure API for FHIR]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir) .

> [!NOTE]
> L’offre de récupération d’urgence est soumise au [contrat SLA pour l’API Azure pour FHIR](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/), 1,0.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris le fonctionnement de la récupération d’urgence pour Azure API pour FHIR et comment l’activer. Pour en savoir plus sur les autres fonctionnalités prises en charge de l’API Azure pour FHIR, consultez :

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge par FHIR](fhir-features-supported.md)