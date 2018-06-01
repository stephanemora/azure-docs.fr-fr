---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307564"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Prépayer les machines virtuelles avec Azure Reserved VM Instances

Prépayez les machines virtuelles et réalisez des économies avec Azure Reserved Virtual Machine Instances. Pour plus d’informations, consultez [Offre des instances de machines virtuelles réservées Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter une instance réservée Azure sur le [portail Azure](https://portal.azure.com). Pour acheter une instance réservée :
-   Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
-   Pour les abonnements Entreprise, les achats d’instance réservée doivent être activés dans le [portal EA](https://ea.azure.com).
-   Pour le programme CSP, seuls les agents administrateurs ou les agents commerciaux peuvent acheter les instances réservées.

## <a name="buy-a-reserved-instance"></a>Acheter une instance réservée
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle instance réservée.
4. Renseignez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise sur les instances réservées. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |NOM        |Le nom de cette instance réservée.| 
    |Abonnement|L’abonnement utilisé pour payer l’instance réservée. Les coûts initiaux de l’instance réservée sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être un contrat Entreprise (numéro de l’offre : MS-AZR-0017P) ou Paiement à l’utilisation (numéro de l’offre : MS-AZR-0003P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|    
    |Étendue       |L’étendue de l’instance réservée peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise sur l’instance réservée est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise sur l’instance réservée est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Lieu    |Région Azure couverte par l’instance réservée.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de l’instance réservée. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région Est des États-Unis, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |
5. Vous pouvez afficher le coût de l’instance réservée lorsque vous sélectionnez **Calculer le coût**.

    ![Capture d’écran avant de soumettre l’achat d’une instance réservée](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

    ![Capture d’écran après la soumission de l’achat d’une instance réservée](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Étapes suivantes 
La remise sur l’instance réservée est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de l’instance réservée. Vous pouvez mettre à jour l’étendue de l’instance réservée par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API. 

Pour savoir comment gérer une instance réservée, consultez [Gérer des instances réservées Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Pour plus d’informations sur les instances réservées Azure, consultez les articles suivants :

- [Réaliser des économies sur les machines virtuelles avec les instances réservées](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gérer des instances réservées Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Instances réservées dans le programme CSP de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.