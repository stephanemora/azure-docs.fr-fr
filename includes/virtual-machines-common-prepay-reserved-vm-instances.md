---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/14/2019
ms.openlocfilehash: c7ff7ab0800449c2a3aa9d58bd036981caccaa1c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156258"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Prépayer les Machines virtuelles avec des Instances de machine virtuelle réservée Azure (RI)

Prépayez les machines virtuelles et réalisez des économies avec Azure Reserved Virtual Machine Instances. Pour plus d’informations, consultez [Offre Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter une instance de machine virtuelle réservée dans le [portail Azure](https://portal.azure.com). Pour acheter une instance :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
- Pour le programme CSP (Cloud Solution Provider), seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

La remise de réservation est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Déterminer la taille de machine virtuelle adaptée avant l’achat

Avant d’acheter une réservation, vous devez déterminer la taille de la machine virtuelle dont vous avez besoin. Les sections suivantes vous aideront à déterminer la bonne taille de machine virtuelle.

### <a name="use-reservation-recommendations"></a>Utiliser les recommandations de réservation

Vous pouvez utiliser les recommandations de réservation pour vous aider à déterminer les réservations que vous devez acheter.

- Recommandations d’achat et la quantité recommandée sont à afficher lorsque vous achetez une instance de machine virtuelle réservée dans le portail Azure.
- Azure Advisor fournit des recommandations d’achat pour les abonnements individuels.  
- Vous pouvez utiliser les API pour obtenir des recommandations d’achat pour la portée partagée et étendue de l’abonnement unique. Pour plus d’informations, consultez [réservé recommandation d’achat instance API pour les clients enterprise](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pour les clients EA, acheter des recommandations pour partagés et étendues de l’abonnement unique sont disponibles avec la [pack de contenu Azure consommation Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Machines virtuelles classiques et les services cloud

Instances réservées de machine virtuelle s’appliquent aux deux machines virtuelles classiques et services de cloud lors de la flexibilité de taille d’instance est activée automatiquement. Il ne sont pas les références (SKU) spécial pour les machines virtuelles classiques ou des services cloud. Les mêmes références (SKU) de machine virtuelle s’y appliquent.

Par exemple, vous pourrez convertir vos machines virtuelles classiques ou des services cloud pour les machines virtuelles basées sur Azure Resource Manager. Dans cet exemple, la remise de réservation s’applique automatiquement à la mise en correspondance les machines virtuelles. Il est inutile de *exchange* une instance réservée existante - il applique automatiquement.

### <a name="analyze-your-usage-information"></a>Analyser vos informations d’utilisation
Vous devez analyser vos informations d’utilisation pour aider à déterminer les réservations que vous devez acheter.

Données d’utilisation sont disponibles dans le fichier d’utilisation et les API. Les utiliser ensemble pour déterminer quels réservation à acheter. Vous devez rechercher les instances de machine virtuelle qui ont une utilisation élevée quotidiennement pour déterminer la quantité de réservation à acheter.

Éviter le `Meter` sous-catégorie et `Product` champs dans les données d’utilisation. Ils ne pas faire la distinction entre les tailles de machines virtuelles qui utilisent le stockage premium. Si vous utilisez ces champs pour déterminer la taille de machine virtuelle à l’achat de réservation, vous pourrez acheter une taille incorrecte. Ensuite, vous n’obtiendrez la remise de réservation que vous attendez. Au lieu de cela, reportez-vous à la `AdditionalInfo` champ dans votre fichier d’utilisation ou les API d’utilisation pour déterminer la taille correcte de la machine virtuelle.

### <a name="purchase-restriction-considerations"></a>Considérations relatives à la restriction d’achat

Les Instances de machine virtuelle réservées sont disponibles pour la plupart des tailles de machine virtuelle à quelques exceptions près. Les remises de réservation ne s’appliquent pour les machines virtuelles suivantes :

- **Séries de machines virtuelles** -séries A, série Av2 ou la série G.

- **Machines virtuelles dans la version préliminaire** -n’importe quel séries de machines virtuelles ou de la taille est en version préliminaire.

- **Clouds** -réservations ne sont pas disponibles à l’achat dans les régions Allemagne ou Chine.

- **Quota insuffisant** -une réservation est limitée à un seul abonnement doit avoir le quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter de réservation pour 11 instances Standard_D1. La vérification du quota pour les réservations inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une réservation pour 10 instances Standard_D1 dans cet abonnement. Vous pouvez [créer un devis demande d’augmentation](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour résoudre ce problème.

- **Restrictions de capacité** - dans de rares circonstances, les limites de tailles de l’achat de nouvelles réservations pour le sous-ensemble de machine virtuelle, en raison d’une faible capacité dans une région Azure.

## <a name="buy-a-reserved-vm-instance"></a>Acheter une instance de machine virtuelle réservée

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation.
4. Renseignez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |------------|--------------|
    |Nom        |Nom de cette réservation.|
    |Abonnement|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|    
    |`Scope`       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de réservation est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise de réservation est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Région    |Région Azure couverte par la réservation.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Optimiser pour     |La flexibilité de taille d’instance de machine virtuelle applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cela offre une assurance supplémentaire dans votre capacité à lancer les instances de machines virtuelles quand vous en avez besoin. La priorité de capacité est uniquement disponible si l’étendue de réservation comprend un seul abonnement. |
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région USA Est, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Modifier une réservation après l’achat

Vous pouvez apporter les modifications suivantes à une réservation après achat :

- Étendue de réservation de mise à jour
- Flexibilité de taille d’instance (le cas échéant)
- Propriété

Vous pouvez également fractionner une réservation en plus petits blocs et fusion déjà fractionné réservations. Aucune des modifications entraînent une nouvelle transaction commerciale ou modifier la date de fin de la réservation.

Vous ne pouvez apporter les types de modifications suivants après l’achat, directement :

- Région d’une réservation existante
- SKU
- Quantité
- Duration

Toutefois, vous pouvez *exchange* une réservation si vous souhaitez apporter des modifications.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous devez annuler votre réservation, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous êtes amené à modifier votre réservation d’instances de machine virtuelle réservées pour une autre région, un autre groupe de taille de machine virtuelle ou un autre terme, vous pouvez l’échanger contre une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. La période de 1 ou 3 ans commence lorsque vous créez la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

Pour plus d’informations sur comment exchange ou un remboursement des réservations, consultez [échanges de réservation et les remboursements](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Qu’est-ce qu’une réservation Azure ?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gérer les réservations dans Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Comprendre comment la remise de réservation est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)
