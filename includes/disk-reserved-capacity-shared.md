---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850520"
---
Économisez sur l’utilisation de vos disques SSD Premium grâce à la capacité de réserve : combinée à des instances de machines virtuelles réservées, vous pouvez réduire le coût total des machines virtuelles. La remise de réservation est appliquée automatiquement aux disques correspondants dans l’étendue de réservation sélectionnée ; vous n’avez pas besoin d’attribuer une réservation à un disque managé pour obtenir les remises. Les remises sont appliquées toutes les heures sur l’utilisation du disque et toute capacité de réserve non utilisée n’est pas reportée. La remise relative à la réservation de disque managé ne s’applique pas aux disques non managés, aux disques Ultra, ni à la consommation d’objets blobs de pages.

## <a name="determine-your-storage-needs"></a>Déterminer vos besoins en stockage

Avant d’acheter une réservation, vous devez déterminer vos besoins en stockage. Actuellement, la réservation de disque est disponible uniquement pour certaines références SKU de disques SSD Premium. La référence SKU d’un disque SSD Premium détermine la taille et les performances du disque. Lorsque vous déterminez vos besoins en stockage, nous vous recommandons de ne pas considérer les disques simplement comme une capacité totale : vous ne pouvez pas utiliser une réservation pour un disque plus grand (comme un P40) et l’utiliser pour payer deux disques de plus petite taille (P30). Lors de l’achat d’une réservation, vous achetez uniquement le nombre total de disques par référence SKU.

La réservation de disque est effectuée par référence SKU Azure Data Box Disk. Par conséquent, la consommation de réservation est basée sur l’unité des références SKU Azure Data Box Disk au lieu de la taille fournie. Par exemple, si vous avez réservé 1 disque P40 de 2 Tio de capacité approvisionnée, mais que vous n’avez alloué que 2 disques P30, les deux consommations P30 ne seront pas comptabilisées pour la réservation P40 et vous paierez le tarif du paiement à l’utilisation.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considérations relatives à l’achat

Nous vous recommandons les meilleures pratiques suivantes lorsque vous envisagez d’acheter une réservation de disque :

- Pour faciliter la détermination des réservations à acheter, vous devez analyser vos informations d’utilisation. Assurez-vous de suivre l’utilisation dans les SKU Azure Data Box Disk plutôt que la capacité de disque approvisionnée ou utilisée. 
- Examinez votre réservation de disque en même temps que votre réservation de machine virtuelle. Nous recommandons vivement de réserver à la fois pour l’utilisation des machines virtuelles et d’Azure Data Box Disk pour une économie maximale. Vous pouvez commencer par déterminer la réservation de machine virtuelle appropriée, puis évaluer la réservation de disque en conséquence. En règle générale, vous disposez d’une configuration standard pour chacune de vos charges de travail : par exemple, un serveur SQL Server peut avoir deux disques de données P40 et un disque de système d’exploitation P30. Ce type de modèle peut vous aider à déterminer la quantité de réservations que vous pouvez acheter. Cette approche peut simplifier le processus d’évaluation et garantir que vous disposez d’un plan aligné pour les machines virtuelles et les disques en matière d’abonnements, de régions et autres. 

## <a name="purchase-restrictions"></a>Restrictions d’achat

Les remises de réservation ne sont actuellement pas disponibles pour les disques suivants :
- Disques non managés/objets blobs de pages
- SSD Standard ou HDD standard
- Références SKU de SSD Premium inférieures à P30 : les réservations ne sont pas disponibles pour les références SKU de SSD Premium P1/P2/P3/P4/P6/P10/P15/P20.
- Clouds : les réservations ne sont pas disponibles à l’achat dans les régions Azure Government, Allemagne et Chine.
- Restrictions de capacité : dans de rares cas, Azure limite l’achat de nouvelles réservations pour un sous-ensemble de références SKU de disque, en raison d’une faible capacité dans une région donnée.

## <a name="buy-a-disk-reservation"></a>Acheter une réservation de disque

Vous pouvez acheter des réservations de disque Azure par l’intermédiaire du [Portail Azure](https://portal.azure.com/). Vous pouvez payer la réservation comptant ou par paiements mensuels. Pour plus d’informations sur l’achat par mensualités, consultez [Acheter des réservations par paiements mensuels](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Pour acheter une capacité de réserve, procédez comme suit :

1. Accédez au panneau [Acheter des réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) dans le Portail Azure.
1. Sélectionnez **Disques managés Azure** pour acheter une réservation.

    ![disks-reserved-purchase-reservation.png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Renseignez les champs obligatoires, comme décrit dans le tableau suivant :

   |Champ  |Description  |
   |---------|---------|
   |**Portée**   |  Indique le nombre d’abonnements pouvant bénéficier de l’avantage de facturation associé à la réservation. Elle contrôle également la manière dont la réservation est appliquée à des abonnements spécifiques. <br/><br/> Si vous sélectionnez **Partagé**, la remise de réservation est appliquée à la capacité Stockage Azure dans tous les abonnements de votre contexte de facturation. Le contexte de facturation est basé sur la façon dont vous vous êtes inscrit dans Azure. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients avec paiement à l’utilisation, l’étendue partagée comprend tous les abonnements individuels avec des tarifs de paiement à l’utilisation créés par l’administrateur de compte.  <br/><br/>  Si vous sélectionnez **Abonnement unique**, la remise de réservation est appliquée à la capacité Stockage Azure de l’abonnement sélectionné. <br/><br/> Si vous sélectionnez **Groupe de ressources unique**, la remise de réservation est appliquée à la capacité Stockage Azure de l’abonnement sélectionné et du groupe de ressources sélectionné dans cet abonnement. <br/><br/> Vous pouvez changer l’étendue de la réservation après l’achat de la réservation.  |
   |**Abonnement**  | Abonnement utilisé pour payer la réservation de stockage Azure. Les coûts sont facturés selon le mode de paiement défini sur l’abonnement sélectionné. L’abonnement doit être de l’un des types suivants : <br/><br/>  Contrat Entreprise (références de l’offre : MS-AZR-0017P ou MS-AZR-0148P) : Pour un abonnement Entreprise, les frais sont déduits du solde de l’engagement financier de l’inscription, ou facturés comme un dépassement. <br/><br/> Abonnement individuel avec tarifs de paiement à l’utilisation (références de l’offre : MS-AZR-0003P ou MS-AZR-0023P) : Pour un abonnement individuel avec tarifs de paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture, défini sur l’abonnement.    |
   | **Disques** | Référence SKU que vous souhaitez créer. |
   | **Région** | Région dans laquelle la réservation est en vigueur. |
   | **Périodicité de facturation** | Indique la fréquence à laquelle le compte est facturé pour la réservation. Les options incluent *Mensuel* ou *Comptant*. |

    ![premium-ssd-reserved-purchase-selection.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Une fois que vous avez sélectionné les paramètres de votre réservation, le Portail Azure affiche le coût. Le Portail affiche également le pourcentage de remise par rapport à la facturation du paiement à l’utilisation. Sélectionnez **Suivant** pour passer au panneau **Acheter des réservations**.

1. Dans le volet **Acheter des réservations**, vous pouvez nommer votre réservation et sélectionner la quantité totale de réservations que vous souhaitez effectuer. Le nombre de réservations est mappé sur le nombre de disques. Par exemple, si vous souhaitez réserver une centaine de disques, vous devez remplacer **Quantité** par 100.
1. Vérifiez le coût total de la réservation.

    ![premium-ssd-reserved-selecting-sku-total-purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Une fois que vous avez acheté une réservation, elle est automatiquement appliquée aux ressources de stockage Azure Data Box Disk qui correspondent aux conditions de la réservation. Si vous n’avez pas encore créé de ressources de stockage Azure Data Box Disk, la réservation s’appliquera dès que vous créez une ressource qui correspond aux conditions de la réservation. Dans les deux cas, la durée de la réservation démarre une fois l’achat finalisé.

## <a name="exchange-or-refund-a-reservation"></a>Échanger ou rembourser une réservation

Avec certaines limitations, vous pouvez échanger ou faire rembourser une réservation.

Pour échanger ou rembourser une réservation, accédez aux détails de la réservation dans le Portail Azure. Sélectionnez **Échange ou remboursement**, puis suivez les instructions pour soumettre une demande de support. Une fois la demande traitée, Microsoft vous envoie un e-mail pour confirmer qu’elle a été traitée.

Pour plus d’informations sur les stratégies de réservations Azure, consultez [Échanges et remboursements en libre-service de réservations Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Échanger une réservation

L’échange d’une réservation vous permet de recevoir un remboursement calculé au prorata en fonction de la partie inutilisée de la réservation. Vous pouvez ensuite appliquer le remboursement au prix d’achat d’une nouvelle réservation de disque Azure.
Il n’y a pas de limite au nombre d’échanges que vous pouvez faire. De plus, un échange n’occasionne aucuns frais. La nouvelle réservation que vous achetez doit être supérieure ou égale à la valeur du crédit calculé au prorata de la réservation d’origine. Une réservation de disque Azure peut être échangée uniquement pour une autre réservation de disque Azure, et non pour une réservation dédiée à un autre service Azure.

### <a name="refund-a-reservation"></a>Rembourser une réservation

Vous pouvez annuler une réservation de disque Azure à tout moment. Si vous annulez, vous recevrez un remboursement calculé au prorata en fonction de la durée restante de la réservation, moins des frais de résiliation anticipée de 12 %. Le remboursement maximal par année est de 50 000 $.
L’annulation d’une réservation met immédiatement fin à la réservation et retourne les mois restants à Microsoft. Le solde restant calculé au prorata, moins les frais, est crédité sur votre moyen de paiement d’origine.

## <a name="expiration-of-a-reservation"></a>Expiration d’une réservation

Lorsqu’une réservation expire, toute capacité Azure Data Box Disk que vous utilisez dans le cadre de cette réservation est facturée au tarif du paiement à l’utilisation. Les réservations ne sont pas renouvelées automatiquement.
Vous recevrez une notification par e-mail 30 jours avant l’expiration de la réservation, puis à nouveau au moment de l’expiration. Pour continuer à tirer parti des économies qu’offre une réservation, renouvelez-la au plus tard à la date d’expiration.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’une réservation Azure ?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Comprendre comment la remise de réservation est appliquée à Stockage sur disque Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)