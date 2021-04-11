---
title: Optimiser les coûts d’Azure Files avec la capacité de réserve
titleSuffix: Azure Files
description: Découvrez comment réduire les coûts sur les déploiements de partage de fichiers Azure à l’aide de la capacité de réserve Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027456"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Optimiser les coûts d’Azure Files avec la capacité de réserve
Vous pouvez réaliser des économies sur les coûts de stockage pour les partages de fichiers Azure avec des réservations de capacité. La capacité de réserve Azure Files vous propose une remise sur les coûts de capacité pour le stockage lorsque vous vous engagez sur une durée de un à trois ans. Une réservation offre une quantité fixe de capacité de stockage pour la durée de la réservation.

La capacité de réserve Azure Files peut réduire de manière significative les coûts de capacité pour le stockage de données dans vos partages de fichiers Azure. Les économies réalisées dépendent de la durée de votre réservation, de la capacité totale que vous choisissez de réserver et des paramètres de niveau et de redondance que vous avez choisis pour vos partages de fichiers Azure. La capacité de réserve permet de bénéficier d’une remise, sans influencer l’état de vos partages de fichiers Azure.

Pour plus d’informations sur la tarification de la capacité de réservation pour Azure Files, consultez [Tarification Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Conditions de réservation pour Azure Files
Les sections suivantes énoncent les conditions d’une réservation de capacité Azure Files.

### <a name="reservation-capacity"></a>Capacité de réserve
Vous pouvez acheter une capacité de réserve Azure Files en unités de 10 Tio et 100 Tio par mois pour une période d’un an ou de trois ans.

### <a name="reservation-scope"></a>Étendue de la réservation
La capacité de réserve Azure Files est disponible pour un abonnement unique ou pour plusieurs abonnements (étendue partagée). En cas de limitation à un seul abonnement, la remise de réservation est appliquée à l’abonnement sélectionné uniquement. Lorsque plusieurs abonnements sont concernés, la remise de réservation est partagée entre ces abonnements dans la facturation du client. Une réservation s’applique à votre utilisation dans le cadre de l’étendue achetée et ne peut pas se limiter à un compte de stockage, un conteneur ou un objet spécifique de l’abonnement.

Une réservation de capacité pour Azure Files couvre uniquement la quantité de données stockées dans un abonnement ou un groupe de ressources partagé. Les frais relatifs aux opérations, à la bande passante et au transfert de données ne sont pas inclus dans la réservation. Dès que vous achetez une réservation, les frais de capacité qui correspondent aux attributs de la réservation sont facturés au tarif de la réduction plutôt qu’à celui du paiement à l’utilisation. Pour plus d’informations sur les réservations Azure, consultez [Qu’est-ce qu’une réservation Azure ?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Options de redondance et niveaux pris en charge
La capacité de réserve Azure Files est disponible uniquement pour les partages de fichiers standard déployés dans des comptes de stockage version 2 à usage général (GPv2). La capacité de réserve n’est pas disponible pour les partages de fichiers Azure dans les niveaux Premium ou optimisés pour les transactions.

Actuellement, seuls les partages de fichiers Azure des niveaux chaud et froid prennent en charge les réservations. Toutes les redondances de stockage prennent en charge les réservations. Pour plus d’informations sur les options de redondance, consultez [Redondance Azure Files](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Exigences de sécurité pour l’achat
Pour acheter une capacité de réserve :

- Vous devez disposer du rôle **Propriétaire** sur au moins un abonnement Entreprise ou individuel avec des tarifs de paiement à l’utilisation.
- Pour les abonnements Entreprise, **Ajouter des instances réservées** doit être activé dans le portal EA. Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
- Pour le programme Fournisseur de solutions Cloud (CSP), seuls les agents d’administration ou les agents commerciaux peuvent acheter une capacité de réserve Azure Files.

## <a name="determine-required-capacity-before-purchase"></a>Déterminer la capacité requise avant l’achat
Lorsque vous achetez une réservation Azure Files, vous devez choisir la région, le niveau et l’option de redondance pour la réservation. Votre réservation est valide uniquement pour les données stockées dans cette région, ce niveau et ce niveau de redondance. Par exemple, supposons que vous achetez une réservation de données dans la région USA Ouest pour le niveau chaud utilisant un stockage redondant interzone (ZRS). Cette réservation ne s’applique pas aux données dans l’est des États-Unis, aux données du niveau froid ou aux données du stockage géoredondant (GRS). Toutefois, vous pouvez acheter une autre réservation pour vos autres besoins.  

Les réservations sont disponibles aujourd’hui pour les blocs de 10 Tio ou 100 Tio, avec des remises plus élevées pour les blocs de 100 Tio. Lorsque vous achetez une réservation dans le Portail Azure, il se peut que Microsoft vous suggère des recommandations en fonction de votre précédente utilisation pour vous permettre de déterminer quelle réservation acheter.

## <a name="purchase-azure-files-reserved-capacity"></a>Acheter une capacité de réserve Azure Files
Vous pouvez acheter une capacité de réserve Azure Files par l’intermédiaire du [Portail Azure](https://portal.azure.com). Payez la réservation comptant ou par paiements mensuels. Pour plus d’informations sur l’achat par mensualités, consultez [Acheter des réservations Azure avec des paiements comptants ou mensuels](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Pour obtenir de l’aide sur l’identification des conditions de réservation appropriées pour votre scénario, consultez [Comprendre la remise sur la capacité de réserve Azure Files](../../cost-management-billing/reservations/understand-storage-charges.md).

Pour acheter une capacité de réserve, procédez comme suit :

1. Accédez au panneau [Acheter des réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) dans le Portail Azure.  
1. Sélectionnez **Azure Files** pour acheter une nouvelle réservation.  
1. Renseignez les champs obligatoires, comme décrit dans le tableau suivant :

    ![Capture d’écran montrant comment acheter une capacité de réserve](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Champ  |Description  |
   |---------|---------|
   |**Portée**   |  Indique le nombre d’abonnements pouvant bénéficier de l’avantage de facturation associé à la réservation. Elle contrôle également la manière dont la réservation est appliquée à des abonnements spécifiques. <br/><br/> Si vous sélectionnez **Partagé**, la remise de réservation est appliquée à la capacité Azure Files dans tous les abonnements de votre contexte de facturation. Le contexte de facturation est basé sur la façon dont vous vous êtes inscrit dans Azure. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients avec paiement à l’utilisation, l’étendue partagée comprend tous les abonnements individuels avec des tarifs de paiement à l’utilisation créés par l’administrateur de compte.  <br/><br/>  Si vous sélectionnez **Abonnement unique**, la remise de réservation est appliquée à la capacité Azure Files de l’abonnement sélectionné. <br/><br/> Si vous sélectionnez **Groupe de ressources unique**, la remise de réservation est appliquée à la capacité Azure Files de l’abonnement sélectionné et du groupe de ressources sélectionné dans cet abonnement. <br/><br/> Vous pouvez changer l’étendue de la réservation après l’achat de la réservation.  |
   |**Abonnement**  | Abonnement utilisé pour payer la réservation Azure Files. Les coûts sont facturés selon le mode de paiement défini sur l’abonnement sélectionné. L’abonnement doit être de l’un des types suivants : <br/><br/>  Contrat Entreprise (références de l’offre : MS-AZR-0017P ou MS-AZR-0148P) : Pour une souscription entreprise, les frais sont déduits du solde de Paiement anticipé Azure (précédemment appelé « engagement financier ») de l’inscription ou facturés comme dépassement. <br/><br/> Abonnement individuel avec tarifs de paiement à l’utilisation (références de l’offre : MS-AZR-0003P ou MS-AZR-0023P) : Pour un abonnement individuel avec tarifs de paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture, défini sur l’abonnement.    |
   | **Région** | Région dans laquelle la réservation est en vigueur. |
   | **Niveau** | Niveau pour lequel la réservation est en vigueur. Les options incluent les niveaux *Chaud* and *Froid*. |
   | **Redondance** | Option de redondance pour la réservation. Les options incluent *LRS*, *ZRS*, *GRS* et *GZRS*. Pour plus d’informations sur les options de redondance, consultez [Redondance Azure Files](storage-files-planning.md#redundancy). |
   | **Périodicité de facturation** | Indique la fréquence à laquelle le compte est facturé pour la réservation. Les options incluent *Mensuel* ou *Comptant*. |
   | **Taille** | Quantité de capacité à réserver. |
   |**Terme**  | Une année ou trois ans.   |

1. Une fois que vous avez sélectionné les paramètres de votre réservation, le Portail Azure affiche le coût. Le Portail affiche également le pourcentage de remise par rapport à la facturation du paiement à l’utilisation.

1. Vérifiez le coût total de la réservation dans le panneau **Acheter des réservations**. Vous pouvez également fournir un nom pour la réservation.

Une fois que vous avez acheté une réservation, elle est automatiquement appliquée aux partages de fichiers Azure existants qui correspondent aux conditions de la réservation. Si vous n’avez pas encore créé de partages de fichiers Azure, la réservation s’applique chaque fois que vous créez une ressource qui correspond aux conditions de la réservation. Dans les deux cas, la durée de la réservation démarre une fois l’achat finalisé.

## <a name="exchange-or-refund-a-reservation"></a>Échanger ou rembourser une réservation
Vous pouvez échanger ou rembourser une réservation avec certaines limitations. Ces limitations sont décrites dans les sections suivantes.

Pour échanger ou rembourser une réservation, accédez aux détails de la réservation dans le Portail Azure. Sélectionnez **Échange** ou **Remboursement**, puis suivez les instructions pour soumettre une demande de support. Une fois la demande traitée, Microsoft vous envoie un e-mail pour confirmer qu’elle a été traitée.

Pour plus d’informations sur les stratégies de réservations Azure, consultez [Échanges et remboursements en libre-service de réservations Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Échanger une réservation
L’échange d’une réservation vous permet de recevoir un remboursement calculé au prorata en fonction de la partie inutilisée de la réservation. Vous pouvez ensuite appliquer le remboursement au prix d’achat d’une nouvelle réservation Azure Files.

Il n’y a pas de limite au nombre d’échanges que vous pouvez faire. De plus, un échange n’occasionne aucuns frais. La nouvelle réservation que vous achetez doit être supérieure ou égale à la valeur du crédit calculé au prorata de la réservation d’origine. Une réservation Azure Files peut être échangée uniquement pour une autre réservation Azure Files, et non pour une réservation dédiée à un autre service Azure.

### <a name="refund-a-reservation"></a>Rembourser une réservation
Vous pouvez annuler une réservation Azure Files à tout moment. Lorsque vous annulez, vous recevrez un remboursement calculé au prorata en fonction de la durée restante de la réservation, moins des frais de résiliation anticipé de 12 %. Le remboursement maximal par année est de 50 000 $.

L’annulation d’une réservation met immédiatement fin à la réservation et retourne les mois restants à Microsoft. Le solde restant calculé au prorata, moins les frais, est crédité sur votre moyen de paiement d’origine.

## <a name="expiration-of-a-reservation"></a>Expiration d’une réservation
Lorsqu’une réservation expire, toute capacité Azure Files que vous utilisez dans le cadre de cette réservation est facturée au tarif du paiement à l’utilisation. Les réservations ne sont pas renouvelées automatiquement.

Vous recevrez une notification par e-mail 30 jours avant l’expiration de la réservation, puis à nouveau au moment de l’expiration. Pour continuer à tirer parti des économies qu’offre une réservation, renouvelez-la au plus tard à la date d’expiration.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter
Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Qu’est-ce qu’une réservation Azure ?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Comprendre comment les remises de réservation sont appliquées aux services de stockage Azure](../../cost-management-billing/reservations/understand-storage-charges.md)