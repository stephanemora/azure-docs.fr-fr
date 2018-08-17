---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631161"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Prépayer les machines virtuelles avec Azure Reserved VM Instances

Prépayez les machines virtuelles et réalisez des économies avec Azure Reserved Virtual Machine Instances. Pour plus d’informations, consultez [Offre Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter une instance de machine virtuelle réservée dans le [portail Azure](https://portal.azure.com). Pour acheter une instance :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
- Pour les abonnements Entreprise, les achats de réservation doivent être activés dans le [portal EA](https://ea.azure.com).
- Pour le programme CSP, seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

## <a name="determine-the-right-vm-size-before-purchase"></a>Déterminer la bonne taille de machine virtuelle avant l’achat

Les champs Sous-catégorie du compteur et Produit des données d’utilisation ne distinguent pas les tailles de machine virtuelle qui utilisent le stockage Premium de celles qui ne l’utilisent pas. L’utilisation de ces champs pour déterminer la taille de machine virtuelle pour l’achat de réservation peut entraîner l’achat d’une réservation incorrecte et ne pas vous garantir de remises sur réservation. Utilisez l’une des méthodes ci-dessous pour déterminer la taille de machine virtuelle appropriée pour l’achat de réservation.

- Consultez le champ AdditionalInfo dans votre fichier d’utilisation ou l’API d’utilisation pour déterminer la taille correcte de la machine virtuelle pour un achat de réservation. N’utilisez pas les valeurs des champs de sous-catégorie du compteur ou de produit, car ces champs ne distinguent pas les versions S et non S d’une machine virtuelle.
- Vous pouvez également obtenir des informations précises sur la taille d’une machine virtuelle à l’aide de PowerShell, d’Azure Resource Manager ou des détails de la machine virtuelle dans le portail Azure.

Les instances de machines virtuelles réservées sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près :

- Machines virtuelles en préversion : aucune série ni taille de machine virtuelle en préversion ne peut faire l’objet d’un achat de réservation.
- Clouds : les réservations ne sont pas disponibles à l’achat dans les régions Azure Gouvernement des États-Unis, Allemagne et Chine.
- Quota insuffisant : une réservation qui est limitée à un seul abonnement doit avoir un quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter de réservation pour 11 instances Standard_D1. La vérification du quota pour les réservations inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une réservation pour 10 instances Standard_D1 dans cet abonnement. 
- Restrictions de capacité : dans de rares cas, Azure limite l’achat de nouvelles réservations pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région.

## <a name="buy-a-reserved-vm-instance"></a>Acheter une instance de machine virtuelle réservée

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation.
4. Renseignez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |NOM        |Nom de cette réservation.| 
    |Abonnement|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être un contrat Entreprise (numéro de l’offre : MS-AZR-0017P) ou Paiement à l’utilisation (numéro de l’offre : MS-AZR-0003P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|    
    |Étendue       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de réservation est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise de réservation est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Lieu    |Région Azure couverte par la réservation.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Optimiser pour     |La flexibilité de taille d’instance de machine virtuelle applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité réserve une capacité de centre de données pour vos déploiements. Cela offre une assurance supplémentaire dans votre capacité à lancer les instances de machines virtuelles quand vous en avez besoin. La priorité de capacité est uniquement disponible si l’étendue de réservation comprend un seul abonnement. |
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région USA Est, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |
5. Vous pouvez afficher le coût de la réservation lorsque vous sélectionnez **Calculer le coût**.

    ![Capture d’écran avant de soumettre l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Sélectionnez **Achat**.
7. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

    ![Capture d’écran après avoir soumis l’achat de la réservation](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Étapes suivantes

La remise de réservation est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Pour plus d’informations sur les réservations Azure, voir les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Manage reservations in Azure](../articles/billing/billing-manage-reserved-vm-instance.md) (Gérer les réservations dans Azure)
- [Comprendre comment la remise sur réservation est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Comprendre l’usage d’une réservation pour votre abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’usage d’une réservation pour votre inscription d’entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Microsoft Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
