---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/11/2019
ms.openlocfilehash: 766856438b22661b961bfbadc0b63376031622f6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850826"
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

### <a name="services-that-get-vm-reservation-discounts"></a>Services qui obtiennent des remises de réservation de machines virtuelles

Vos réservations de machine virtuelle peuvent s’appliquer à l’utilisation de machines virtuelles émise à partir de plusieurs services, pas seulement pour vos déploiements de machine virtuelle. Les ressources qui obtiennent des remises de réservation varient en fonction du paramètre de flexibilité de la taille d’instance.

#### <a name="instance-size-flexibility-setting"></a>Paramètre de flexibilité de la taille d’instance

Le paramètre de flexibilité de la taille de l’instance détermine les services qui obtiennent les remises d’instance réservée.

Que le paramètre soit activé ou désactivé, les remises de réservation s’appliquent automatiquement à toute utilisation de machine virtuelle correspondante lorsque la valeur *ConsumedService* est `Microsoft.Compute`. Par conséquent, vérifiez vos données d’utilisation pour la valeur *ConsumedService*. Voici quelques exemples :

- Machines virtuelles
- Groupes identiques de machines virtuelles
- Service de conteneur
- Déploiements de Azure Batch (en mode abonnement utilisateur)
- Azure Kubernetes Service (AKS)
- Service Fabric

Lorsque le paramètre est activé, les remises de réservation s’appliquent automatiquement à l’utilisation de la machine virtuelle correspondante lorsque la valeur *ConsumedService* est l’un des éléments suivants :

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Vérifiez la valeur *ConsumedService* dans vos données d’utilisation pour déterminer si l’utilisation est éligible pour les remises de réservation.

Pour plus d’informations à propos de la flexibilité de la taille d’instance, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

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

- **Restrictions de capacité** : dans de rares cas, Azure limite l’achat de nouvelles réservations pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région donnée.

## <a name="buy-a-reserved-vm-instance"></a>Acheter une instance de machine virtuelle réservée

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez **Tous les services** > **Réservations**.
1. Sélectionnez **Ajouter** pour acheter une nouvelle réservation, puis cliquez sur **Machine virtuelle**.
1. Complétez les champs obligatoires. Les instances de machines virtuelles en cours d’exécution qui correspondent aux attributs que vous sélectionnez se qualifient pour bénéficier de la remise de réservation. Le nombre réel de vos instances de machines virtuelles qui obtiennent la remise dépend de l’étendue et de la quantité sélectionnées.

| Champ      | Description|
|------------|--------------|
|Subscription|Abonnement utilisé pour payer la réservation. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement payable à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou facture défini sur l’abonnement.|    
|Étendue       |L’étendue de la réservation peut couvrir un seul abonnement ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>**Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.</li><li>**Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.</li><li>**Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.</li></ul>|
|Région    |Région Azure couverte par la réservation.|    
|Taille de la machine virtuelle     |Taille des instances de machines virtuelles.|
|Optimiser pour     |La flexibilité de la taille d’instance de machine virtuelle est sélectionnée par défaut. Cliquez sur **Paramètres avancés** pour modifier la valeur de la flexibilité de taille d’instance afin d’appliquer la remise de réservation à d’autres machines virtuelles dans le même [Groupe de taille de machine virtuelle](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). La priorité de capacité priorise la capacité de centre de données pour vos déploiements. Cela offre une assurance supplémentaire dans votre capacité à lancer les instances de machines virtuelles quand vous en avez besoin. La priorité de capacité est uniquement disponible si l’étendue de réservation comprend un seul abonnement. |
|Terme        |Une année ou trois ans.|
|Quantité    |Nombre d’instances achetées au sein de la réservation. La quantité correspond au nombre d’instances de machines virtuelles en cours d’exécution pouvant bénéficier de la remise de facturation. Par exemple, si vous exécutez 10 machines virtuelles Standard_D2 dans la région USA Est, vous devez spécifier 10 comme quantité pour optimiser l’avantage pour toutes les machines virtuelles en cours d’exécution. |

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
- Duration

Toutefois, vous pouvez *échanger* une réservation si vous souhaitez y apporter des modifications.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous devez annuler votre réservation, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur le tarif le plus bas de votre prix d’achat ou le prix actuel de la réservation. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous devez changer votre réservation d’instances de machine virtuelle réservées pour une autre région, un autre groupe de taille de machine virtuelle ou un autre terme, vous pouvez l’échanger. L’échange doit être destiné à une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. Le terme d’un an ou de trois ans commence à courir dès la création de la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

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
