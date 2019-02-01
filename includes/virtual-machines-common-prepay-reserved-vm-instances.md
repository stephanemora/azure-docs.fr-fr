---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/30/2018
ms.openlocfilehash: 1da2278eee6fcea5c013e9c2f5f4ad3e3013b590
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906309"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Prépayer les machines virtuelles avec Azure Reserved VM Instances

Prépayez les machines virtuelles et réalisez des économies avec Azure Reserved Virtual Machine Instances. Pour plus d’informations, consultez [Offre Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter une instance de machine virtuelle réservée dans le [portail Azure](https://portal.azure.com). Pour acheter une instance :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
- Pour les abonnements Entreprise, les achats de réservation doivent être activés dans le [portal EA](https://ea.azure.com).
- Pour le programme CSP (Cloud Solution Provider), seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Déterminer la taille de machine virtuelle adaptée avant l’achat

Dans les données d’utilisation, les champs Sous-catégorie du compteur et Produit ne font pas la distinction entre la taille des machines virtuelles qui utilisent le stockage premium et les autres. Si vous utilisez ces champs pour déterminer la taille de machine virtuelle à utiliser pour la réservation, il se peut que vous achetiez une taille incorrecte et n’obteniez pas la remise attendue. Utilisez l’une des méthodes suivantes pour déterminer la taille de machine virtuelle appropriée lorsque vous achetez la réservation :

- Consultez le champ AdditionalInfo dans le fichier d’utilisation ou l’API d’utilisation pour déterminer la taille correcte de la machine virtuelle. N’utilisez pas les valeurs des champs Sous-catégorie du compteur ou Produit. Ces champs ne font pas la différence entre les versions S et les autres pour une machine virtuelle.
- Obtenez également des informations précises sur la taille d’une machine virtuelle à l’aide de PowerShell, d’Azure Resource Manager ou des détails de la machine virtuelle dans le Portail Azure.

Les instances de machines virtuelles réservées sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près :

- La remise de réservation ne s’applique pas aux machines virtuelles suivantes :
  - Machines virtuelles et services cloud classiques
  - Séries de machines virtuelles : Séries A, Av2 ou G
  - Machines virtuelles en préversion : Toutes séries de machines virtuelles ou tailles en préversion
- Clouds : Les réservations ne sont pas disponibles à l’achat dans les régions situées en Allemagne et en Chine.
- Quota insuffisant : Une réservation qui est limitée à un seul abonnement doit avoir un quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter de réservation pour 11 instances Standard_D1. La vérification du quota pour les réservations inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une réservation pour 10 instances Standard_D1 dans cet abonnement. 
- Restrictions de capacité : Dans de rares cas, Azure limite l’achat de nouvelles réservations pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région.

## <a name="buy-a-reserved-vm-instance"></a>Acheter une instance de machine virtuelle réservée

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation.
4. Renseignez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |NOM        |Nom de cette réservation.| 
    |Abonnement|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être un Accord Entreprise (numéro de l’offre : MS-AZR-0017P) ou Paiement à l’utilisation (numéro de l’offre : MS-AZR-0003P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|    
    |Étendue       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de réservation est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise de réservation est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Région    |Région Azure couverte par la réservation.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Optimiser pour     |La flexibilité de taille d’instance de machine virtuelle applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cela offre une assurance supplémentaire dans votre capacité à lancer les instances de machines virtuelles quand vous en avez besoin. La priorité de capacité est uniquement disponible si l’étendue de réservation comprend un seul abonnement. |
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région USA Est, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |
5. Vous pouvez afficher le coût de la réservation lorsque vous sélectionnez **Calculer le coût**.

    ![Capture d’écran avant de soumettre l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

    ![Capture d’écran après avoir soumis l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous devez annuler votre réservation, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous êtes amené à modifier votre réservation d’instances de machine virtuelle réservées pour une autre région, un autre groupe de taille de machine virtuelle ou un autre terme, vous pouvez l’échanger contre une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. Le terme de 1 ou 3 ans commence lorsque vous créez la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

## <a name="next-steps"></a>Étapes suivantes

La remise de réservation est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gérer les réservations dans Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de réservation est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
