---
title: Prépayer les ressources de calcul avec une capacité de réserve - Azure Cache pour Redis
description: Prépayer les ressources de calcul Azure Cache pour Redis avec une capacité de réserve
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530206"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Prépayer les ressources de calcul Azure Cache pour Redis avec une capacité de réserve

Azure Cache pour Redis vous permet de faire des économies en prépayant les ressources de calcul au lieu d’appliquer le tarif du paiement à l’utilisation. Avec une capacité réservée Azure Cache pour Redis, vous prenez un engagement initial sur le cache pour une période d’un an ou de trois ans, afin de bénéficier d’une remise importante sur les coûts de calcul. Pour acheter une capacité réservée Azure Cache pour Redis, vous devez spécifier la région Azure, le niveau de service et la durée.

Vous n’avez pas besoin d’attribuer la réservation à des instances Azure Cache pour Redis. Les instances d’Azure Cache pour Redis en cours d’exécution, ainsi que celles qui viennent d’être déployées, bénéficient automatiquement du tarif réservé, dans la limite de la taille du cache réservé. En achetant une réservation, vous payez d’avance les coûts de calcul pendant une durée d’un an ou de trois ans. Dès que vous achetez une réservation, les charges de calcul Azure Cache pour Redis qui correspondent aux attributs de la réservation ne sont plus facturées au tarif du paiement à l’utilisation. Une réservation ne couvre pas les frais de stockage ou de réseau associés à l’utilisation du cache. À l’issue de la période de réservation, la remise sur facturation expire, et Azure Cache pour Redis est facturé au tarif du paiement à l’utilisation. Les réservations ne se renouvellent pas automatiquement. Pour plus d’informations sur les tarifs, consultez l’[offre de capacité réservée Azure Cache pour Redis](https://azure.microsoft.com/pricing/details/cache).

Vous pouvez acheter une capacité de réserve Azure Cache pour Redis sur le [portail Azure](https://portal.azure.com/). Pour acheter une capacité réservée :

* Vous devez disposer du rôle Propriétaire sur au moins un abonnement Entreprise ou individuel avec des tarifs de paiement à l’utilisation.
* Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com/). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
* Pour ce qui est du programme des fournisseurs de solutions cloud (CSP), seuls les agents d’administration ou les agents commerciaux peuvent acheter une capacité réservée Azure Cache pour Redis.

Si vous souhaitez en savoir plus sur la facturation des achats de réservation pour les clients professionnels et les clients bénéficiant du paiement à l’utilisation, consultez les articles [Comprendre l’utilisation d’une réservation Azure pour votre inscription Entreprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) et [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Déterminer la bonne taille de cache avant l’achat

La taille de la réservation doit s’appuyer sur le nombre total de calculs utilisés par le cache existant ou à déployer prochainement dans une région spécifique et qui utilise le même niveau de performance.

Par exemple, supposons que vous exécutiez un cache 32 vCore Gen5 à usage général et deux caches 16 vCore Gen5 à mémoire optimisée. Admettons également que vous envisagez de déployer au cours du mois prochain un autre serveur de base de données Gen5 32 vCore à usage général et un serveur de base de données Gen5 16 vCore à mémoire optimisée. Imaginons que vous savez que ces ressources vous seront nécessaires pendant au moins 1 an. Dans ce cas, vous devez acheter une réservation de 64 (2x32) vCores pour une durée d’un an, pour un usage général de bases de données uniques - Gen5 et une réservation de 48 (2x16 + 16) vCores pour une durée d’un an pour une base de données unique à mémoire optimisée - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Acheter une capacité réservée Azure Cache pour Redis

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter**, puis dans le volet Acheter des réservations, sélectionnez **Azure Cache pour Redis** pour acheter une nouvelle réservation pour vos caches.
4. Renseignez les champs obligatoires. Les bases de données existantes ou nouvelles, correspondant aux attributs que vous sélectionnez, peuvent prétendre à la remise de capacité réservée. Le nombre d’instances Azure Cache pour Redis qui obtiendront la remise dépend de l’étendue et de la quantité sélectionnées.


![Aperçu de la tarification réservée](media/cache-reserved-pricing/cache-reserved-price.png)


Le tableau suivant décrit les champs requis.

| Champ | Description |
| :------------ | :------- |
| Abonnement   | L’abonnement utilisé pour payer la réservation de capacité réservée Azure Cache pour Redis. Les coûts initiaux de la réservation de capacité réservée Azure Cache pour Redis sont facturés selon le mode de paiement défini dans l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148p) ou un accord individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement individuel avec paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture sur l’abonnement.
| Étendue | L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : </br></br> **Partagé** : la remise de réservation est appliquée aux instances Azure Cache pour Redis en cours d’exécution, dans tous les abonnements de votre contexte de facturation. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</br></br> **Abonnement unique** : la remise de réservation est appliquée aux instances Azure Cache pour Redis incluses dans l’abonnement. </br></br> **Groupe de ressources unique** : la remise de réservation est appliquée aux instances Azure Cache pour Redis incluses dans l’abonnement sélectionné et dans le groupe de ressources sélectionné dans cet abonnement.
| Région | La région Azure couverte par la réservation de capacité réservée Azure Cache pour Redis.
| Niveau tarifaire | Le niveau de service des serveurs Azure Cache pour Redis.
| Terme | Un an ou trois ans
| Quantité | La quantité de ressources de calcul achetée au sein de la réservation de capacité réservée Azure Cache pour Redis. La quantité correspond au nombre de caches de la région Azure et du niveau de service sélectionnés qui est réservé et qui bénéficie de la remise de facturation. Par exemple, si vous exécutez ou envisagez d’exécuter des serveurs Azure Cache pour Redis avec une capacité de cache totale de 26 Go dans la région USA Est, vous devez spécifier une quantité de 26 pour optimiser le bénéfice de tous les caches.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilité de la taille du cache

La flexibilité de la taille du cache vous permet d’effectuer un scale-up ou un scale-down dans un niveau de service et dans une région, sans perdre le bénéfice de la capacité réservée.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

La remise sur la réservation est appliquée automatiquement aux instances Azure Cache pour Redis qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du portail Azure, de PowerShell, d’Azure CLI ou de l’API.

*  Pour découvrir comment les remises sur capacité réservée sont appliquées à Azure Cache pour Redis, consultez [Comprendre la remise sur réservation Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md).

* Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

    * [Qu’est-ce qu’une réservation Azure ?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gérer les réservations Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Comprendre la remise sur réservation Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

