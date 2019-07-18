---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/01/2019
ms.openlocfilehash: 9e0caa8b98133dad3af083e8910d0603bbd2563b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489907"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure

Prépayez les machines virtuelles et réalisez des économies avec Azure Reserved Virtual Machine Instances. Pour plus d’informations, consultez [Offre Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vous pouvez acheter une instance de machine virtuelle réservée dans le [portail Azure](https://portal.azure.com). Pour acheter une instance :

- Vous devez avoir le rôle Propriétaire pour au moins un abonnement entreprise ou un abonnement assorti d’un paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
- Pour le programme CSP (Cloud Solution Provider), seuls les agents administrateurs ou les agents commerciaux peuvent acheter les réservations.

La remise de réservation est appliquée automatiquement au nombre de machines virtuelles en cours d’exécution qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du [portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Déterminer la taille de machine virtuelle adaptée avant l’achat

Avant d’acheter une réservation, vous devez déterminer la taille de la machine virtuelle dont vous avez besoin. Les sections suivantes vous aideront à déterminer la taille de machine virtuelle appropriée.

### <a name="use-reservation-recommendations"></a>Utiliser les suggestions de réservation

Vous pouvez utiliser les suggestions de réservation pour déterminer les réservations à acheter.

- Les suggestions d’achat et une quantité recommandée s’affichent lorsque vous achetez une instance réservée de machine virtuelle sur le portail Azure.
- Azure Advisor fournit des suggestions d’achat pour les divers abonnements.  
- Vous pouvez utiliser les API pour obtenir des suggestions d’achat tant pour l’étendue partagée que pour l’étendue d’un abonnement unique. Pour plus d’informations, voir [API de suggestion d’achat d’instance réservée pour les entreprises](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pour les clients EA, des suggestions d’achat pour des étendues d’abonnement simples et partagées sont disponibles dans le [pack de contenu Power BI Azure consommation Insights](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Machines virtuelles et services cloud classiques

Les instances réservées de machines virtuelles s’appliquent aux machines virtuelles et services cloud classiques lorsque la flexibilité de taille d’instance est activée. Il n’existe pas de références (SKU) spéciales pour les machines virtuelles ou services cloud classiques. Les mêmes références (SKU) de machine virtuelle s’y appliquent.

Par exemple, vous pouvez convertir vos machines virtuelles ou services cloud classiques en machines virtuelles basées sur Azure Resource Manager. Dans cet exemple, la remise sur réservation s’applique automatiquement au machines virtuelles correspondantes. Il est inutile d’*échanger* une instance réservée existante ; elle s’applique automatiquement.

### <a name="analyze-your-usage-information"></a>Analyser vos informations d’utilisation
Pour faciliter la détermination des réservations à acheter, vous devez analyser vos informations d’utilisation.

Des données d’utilisation sont disponibles dans le fichier d’utilisation et les API. Combinez-les pour déterminer la réservation à acheter. Pour déterminer la quantité de réservations à acheter, vous devez identifier des instances de machine virtuelle très utilisées quotidiennement.

Évitez la sous-catégorie `Meter` et les champs `Product` dans les données d’utilisation. Ils ne font pas de distinction entre les tailles de machines virtuelles qui utilisent un stockage Premium. Si vous vous basez sur ces champs pour déterminer la taille de machine virtuelle en vue d’un achat de réservation, vous risquez d’opter pour une taille incorrecte. Ensuite, vous n’obtiendrez la remise sur réservation que vous attendez. Pour déterminer la taille correcte de machine virtuelle, consultez plutôt le champ `AdditionalInfo` dans le fichier ou l’API d’utilisation.

### <a name="purchase-restriction-considerations"></a>Considérations en matière de restriction d’achat

Des instances de machines virtuelles réservées sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près. Les remises sur réservation ne s’appliquent pas aux machines virtuelles suivantes :

- **Séries de machines virtuelles** : A, Av2 ou G.

- **Machines virtuelles en préversion** : toutes les séries ou tailles de machines virtuelles en préversion.

- **Clouds** : les réservations ne sont pas disponibles à l’achat dans les régions Allemagne et Chine.

- **Quota insuffisant** : une réservation dont l’étendue est limitée à un seul abonnement doit avoir un quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter de réservation pour 11 instances Standard_D1. La vérification du quota pour les réservations inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une réservation pour 10 instances Standard_D1 dans cet abonnement. Pour résoudre ce problème, vous pouvez [créer une demande d’augmentation de quota](../articles/azure-supportability/resource-manager-core-quotas-request.md).

- **Restrictions de capacité** : dans de rares cas, Azure limite l’achat de nouvelles réservations pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région donnée.

## <a name="buy-a-reserved-vm-instance"></a>Acheter une instance de machine virtuelle réservée

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Réservations**.
3. Sélectionnez **Ajouter** pour acheter une nouvelle réservation.
4. Complétez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |------------|--------------|
    |Nom        |Nom de cette réservation.|
    |Subscription|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement payable à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou facture défini sur l’abonnement.|    
    |Étendue       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de réservation est appliquée aux machines virtuelles incluses dans cet abonnement. </li><li>Partagé : la remise de réservation est appliquée aux machines virtuelles en cours d’exécution dans tous les abonnements au sein de votre contexte de facturation. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients titulaires d’abonnements payables à l’utilisation, l’étendue partagée est tous les abonnements payables à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Région    |Région Azure couverte par la réservation.|    
    |Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
    |Optimiser pour     |La flexibilité de taille d’instance de machine virtuelle applique la remise sur réservation aux autres machines virtuelles du même [groupe de tailles de machine virtuelle](https://aka.ms/RIVMGroups). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cela offre une assurance supplémentaire dans votre capacité à lancer les instances de machines virtuelles quand vous en avez besoin. La priorité de capacité est uniquement disponible si l’étendue de réservation comprend un seul abonnement. |
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région USA Est, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines en cours d’exécution. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Modifier une réservation après achat

Vous pouvez apporter les modifications suivantes à une réservation après achat :

- Mettre à jour l’étendue de la réservation
- Flexibilité de taille d’instance (le cas échéant)
- Propriété

Vous pouvez également fractionner une réservation en segments plus petits ou fusionner des réservations fractionnées. Aucune des modifications n’entraîne de nouvelle transaction commerciale ou de changement de la date de fin de la réservation.

Vous ne pouvez apporter directement les modifications suivantes après achat :

- Région d’une réservation existante
- SKU
- Quantité
- Durée

Toutefois, vous pouvez *échanger* une réservation si vous souhaitez y apporter des modifications.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous devez annuler votre réservation, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous êtes amené à modifier votre réservation d’instances de machine virtuelle réservées pour une autre région, un autre groupe de taille de machine virtuelle ou un autre terme, vous pouvez l’échanger contre une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. La période de 1 ou 3 ans commence lorsque vous créez la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

Pour plus d’informations sur l’échange ou le remboursement des réservations, voir [Échanges et remboursements de réservations](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Qu’est-ce qu’une réservation Azure ?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gérer les réservations dans Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Comprendre comment la remise de réservation est appliquée](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Comprendre l’utilisation d’une réservation pour un abonnement avec paiement à l’utilisation](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)
