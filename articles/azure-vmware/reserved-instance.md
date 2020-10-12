---
title: Économiser grâce à l’instance réservée de Azure VMware Solution
description: Découvrez comment acheter une instance réservée pour Azure VMware Solution.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91491662"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Économiser grâce à Azure VMware Solution

Lorsque vous validez une instance réservée d’Azure VMware Solution, vous pouvez économiser de l’argent. La remise de réservation est appliquée automatiquement au nombre d’hôtes Azure VMware Solution en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous n’avez pas besoin d’attribuer une réservation à un hôte dédié pour obtenir les remises. Un achat d’instance réservée couvre uniquement la partie calcul de votre utilisation et inclut les coûts liés aux licences logicielles. Consultez la  [vue d’ensemble d’Azure VMware Solution](introduction.md).

## <a name="purchase-restriction-considerations"></a>Considérations en matière de restriction d’achat

Les instances réservées sont disponibles avec quelques exceptions près :

-   **Clouds** : les réservations sont disponibles uniquement dans les régions répertoriées sur la page [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware).

-   **Quota insuffisant** : une réservation qui est limitée à un abonnement unique/partagé doit avoir un quota d’hôtes disponible dans l’abonnement pour la nouvelle instance réservée. Pour résoudre ce problème, vous pouvez  [créer une demande d’augmentation de quota](enable-azure-vmware-solution.md).

-   **Éligibilité de l’offre** : vous aurez besoin d’un  [Contrat Entreprise (EA) Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) avec Microsoft.

-   **Restrictions de capacité** : dans de rares cas, Azure limite l’achat de nouvelles réservations pour les SKU d’hôtes Azure VMware Solution, en raison d’une faible capacité dans une région donnée.

## <a name="buy-a-reservation"></a>Acheter une réservation

Vous pouvez acheter une instance réservée pour une instance d’hôte Azure VMware Solution dans le  [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Payez la réservation  [comptant ou par paiements mensuels](../cost-management-billing/reservations/prepare-buy-reservation.md).

Ces conditions s’appliquent à l’achat d’une instance réservée d’hôte dédié :

-   Vous devez avoir le rôle Propriétaire pour au moins un abonnement EA ou un abonnement assorti d’un paiement à l’utilisation.

-   Pour les abonnements EA, l’option  **Ajouter des instances réservées** doit être activée dans le  [portail EA](https://ea.azure.com/). Ou, si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.

Pour acheter une instance :

1. Connectez-vous au  [portail Azure](https://portal.azure.com/).

2. Sélectionnez  **Tous les services** > **Réservations**.

3. Sélectionnez  **Ajouter** pour acheter une nouvelle réservation, puis sélectionnez **Azure VMware Solution**.

4. Complétez les champs obligatoires. Les hôtes Azure VMware Solution en cours d’exécution qui correspondent aux attributs que vous avez sélectionnés peuvent bénéficier de la remise de réservation. Le nombre réel d’hôtes Azure VMware Solution qui obtiendront la remise dépend de l’étendue et de la quantité sélectionnées.

   Si vous avez un Accord Entreprise, vous pouvez utiliser l’option  **Ajouter** pour ajouter rapidement des instances supplémentaires. L’option n’est pas disponible pour les autres types d’abonnements.

   | Champ        |  Description |
   | ------------ | ------------ |
   | Abonnement | Abonnement utilisé pour payer la réservation. Les coûts de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou Microsoft Customer Agreement (MCA) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Les frais sont déduits du solde de l’engagement financier, si disponibles, ou facturés comme un dépassement. Pour un abonnement payable à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou facture défini sur l’abonnement. |
   | Étendue        | L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez :<br><ul><li><b>Étendue de groupe de ressources unique : applique la remise sur réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.</li><li><b>Étendue d’abonnement unique : applique la remise sur réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.</li><li><b>Étendue partagée : applique la remise sur réservation aux ressources correspondantes des abonnements éligibles inclus dans le contexte de facturation. Pour les clients EA, le contexte de facturation est défini par l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.</li></ul>       |
   | Région       | Région Azure couverte par la réservation.   |
   | Taille de l’hôte    | AV36    |
   | Terme         | Une année ou trois ans.  |
   | Quantité     | Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’hôtes Azure VMware Solution en cours d’exécution pouvant bénéficier de la remise.    |

## <a name="usage-data-and-reservation-utilization"></a>Données d’utilisation et utilisation de la réservation

Vos données d’utilisation ont un prix effectif de zéro pour l’utilisation qui bénéficie d’une remise de réservation. Vous pouvez voir quelle instance Azure VMware Solution a reçu la remise de réservation pour chaque réservation.

Pour plus d’informations sur l’affichage des remises de réservation dans les données d’utilisation, et si vous êtes un client EA, consultez  [Comprendre l’utilisation de la réservation Azure pour l’inscription de votre entreprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Si vous avez un abonnement individuel, consultez  [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec paiement à l’utilisation](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Modifier une réservation après achat

Vous pouvez apporter les modifications suivantes à une réservation après achat :

-   Mettez à jour l’étendue de la réservation

-   Flexibilité de taille d’instance (le cas échéant)

-   Propriété

Vous pouvez également fractionner une réservation en blocs plus petits ou fusionner des réservations. Aucune des modifications n’entraîne de nouvelle transaction commerciale ou de changement de la date de fin de la réservation.

>[!NOTE]
>Une fois que vous avez acheté votre réservation, vous ne serez pas en mesure d’apporter directement les types de modifications suivants :
>
> - Région d’une réservation existante
> - SKU
> - Quantité
> - Duration
>
>Toutefois, vous pouvez  *échanger* une réservation si vous souhaitez y apporter des modifications.

## <a name="cancel-exchange-or-refund-reservations"></a>Annuler, échanger ou rembourser des réservations

Vous pouvez annuler, échanger ou rembourser des réservations avec certaines limitations. Pour plus d’informations, consultez  [Échanges et remboursements en libre-service pour les réservations Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).
