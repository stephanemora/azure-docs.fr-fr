---
title: Réservation de capacité à la demande dans Azure (version préliminaire)
description: Découvrez comment réserver de la capacité de calcul dans une région ou une zone de disponibilité Azure avec la réservation de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: fe50e8db24f0f280365e435d8a205e9b45ac6ccb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774477"
---
# <a name="on-demand-capacity-reservation-preview"></a>Réservation de capacité à la demande (version préliminaire)

La réservation de capacité à la demande vous permet de réserver de la capacité de calcul dans une région ou une zone de disponibilité Azure pour n’importe quelle durée. Contrairement aux [Instances réservées](https://azure.microsoft.com/pricing/reserved-vm-instances/), vous n’êtes pas obligé de vous inscrire pour un engagement d’une durée de 1 an ou de 3 ans. Créez et supprimez des réservations à tout moment et ayez un contrôle total sur la façon dont vous souhaitez gérer vos réservations.  

Une fois la réservation de capacité créée, la capacité est immédiatement disponible et est réservée exclusivement à votre utilisation jusqu’à ce que la réservation soit supprimée.  


> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


La réservation de capacité comporte des propriétés de base qui sont toujours définies au moment de la création : 
- **Taille de la machine virtuelle**  : Chaque réservation correspond à une taille de machine virtuelle. Par exemple : `Standard_D2s_v3`. 
- **Emplacement**  : Chaque réservation est pour un emplacement (région). Si cet emplacement a des zones de disponibilité, la réservation peut également spécifier l’une des zones. 
- **Quantité**  : Chaque réservation a une quantité d’instances à réserver. 

Pour créer une réservation de capacité, ces paramètres sont passés à Azure en tant que demande de capacité. Si l’abonnement n’a pas le quota requis ou si Azure n’a pas de capacité disponible conforme à la spécification, le déploiement de la réservation échoue. Pour éviter un échec de déploiement, demandez plus de quota ou essayez une taille de machine virtuelle, un emplacement ou une combinaison de zones différents. 

Une fois qu’Azure a accepté une demande de réservation, il peut être consommé par des machines virtuelles de configurations correspondantes. Pour consommer la réservation de capacité, la machine virtuelle doit spécifier la réservation comme l’une de ses propriétés. Dans le cas contraire, la réservation de capacité reste inutilisée. L’un des avantages de cette conception est que vous pouvez cibler uniquement des charges de travail critiques vers des réservations et d’autres charges de travail non critiques peuvent s’exécuter sans capacité réservée.   

> [!NOTE]
> La réservation de capacité est également fournie avec le contrat SLA de disponibilité Azure pour une utilisation avec des machines virtuelles. Le contrat SLA ne sera pas appliqué pendant la préversion publique et sera défini lorsque la réservation de capacité sera mise à la disposition générale.


## <a name="register-for-capacity-reservation"></a>S’inscrire à la réservation de capacité 

Avant de pouvoir utiliser la fonctionnalité Réservation de capacité, vous devez inscrire votre abonnement pour la préversion. L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser Azure CLI ou PowerShell pour finaliser l’inscription de la fonctionnalité.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli1)

1. Utilisez [az feature register](/cli/azure/feature#az_feature_register) pour activer la préversion pour votre abonnement :

    ```azurecli-interactive
    az feature register --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Vérifiez l’état de l’inscription :

    ```azurecli-interactive
    az feature show --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul :

    ```azurecli-interactive
    az provider register --namespace Microsoft.Compute
    ``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Utilisez le[cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour activer la préversion pour votre abonnement :

    ```powershell-interactive
    Register-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Vérifiez l’état de l’inscription :

    ```powershell-interactive
    Get-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul :

    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ``` 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="benefits-of-capacity-reservation"></a>Avantages de la réservation de capacité 

- Une fois déployée, la capacité est réservée à votre utilisation et sera toujours disponible dans le cadre des contrats SLA applicables  
- Peut être déployée et supprimée à tout moment, sans engagement de terme 
- Peut être combinée automatiquement avec des instances réservées pour les remises sur les engagements de disponibilité  

## <a name="sla-for-capacity-reservation"></a>SLA pour la réservation de capacité 

Le contrat SLA pour la réservation de capacité sera défini ultérieurement lorsque la fonctionnalité sera généralement disponible.  


## <a name="limitations-and-restrictions"></a>Limitations et restrictions 

- La création de réservations de capacité requiert un quota de la même manière que la création de machines virtuelles. 
- Les machines virtuelles spot et les nœuds Azure Dedicated Host ne sont pas pris en charge avec la réservation de capacité. 
- Certaines contraintes de déploiement ne sont pas prises en charge : 
    - Groupe de placement de proximité 
    - Domaines de mise à jour 
    - Stockage UltraSSD  
- Seules les séries de machines virtuelles AV2, B, D, E & F sont prises en charge pendant la préversion publique. 
- Pour les séries de machines virtuelles prises en charge pendant la version préliminaire publique, jusqu’à 3 domaines d’erreur (FD) seront pris en charge. Un déploiement avec plus de 3 domaines d’erreur ne peut pas être déployé sur la réservation de capacité. 
- Les groupes à haute disponibilité ne sont pas pris en charge avec la réservation de capacité. 
- Dans cette version préliminaire, seul l’abonnement qui a créé la réservation peut l’utiliser. 
- Les réservations sont uniquement disponibles pour les clients Azure payants. Les comptes sponsorisés tels que la version d’évaluation gratuite et Azure for Students ne sont pas éligibles à l’utilisation de cette fonctionnalité. 


## <a name="pricing-and-billing"></a>Tarification et facturation 

Les réservations de capacité sont facturées au même tarif que la taille de machine virtuelle sous-jacente. Par exemple, si vous créez une réservation pour 10 quantités de machine virtuelle D2s_v3, dès que la réservation est créée, vous commencerez à être facturé pour 10 machines virtuelles D2s_v3, même si la réservation n’est pas utilisée.  

Si vous déployez ensuite une machine virtuelle D2s_v3 et spécifiez la réservation comme propriété, la réservation de capacité est utilisée. Une fois en cours d’utilisation, vous payez uniquement pour la machine virtuelle et rien de plus pour la réservation de capacité. Supposons que vous déployez 5 machines virtuelles D2s_v3 sur la réservation de capacité mentionnée précédemment. Vous verrez une facture pour 5 machines virtuelles D2s_v3 et 5 réservations de capacité inutilisées, toutes deux facturées au même tarif qu’une machine virtuelle D2s_v3.    

Les réservations de capacité utilisées et inutilisées sont éligibles pour les remises pour l’engagement de durée des instance de machine virtuelle réservée. Dans l’exemple ci-dessus, si vous avez des instances réservées pour 2 machines virtuelles D2s_v3 dans la même région Azure, la facturation pour 2 ressources (machine virtuelle ou réservation de capacité inutilisée) est mise à zéro et vous payez uniquement pour le reste des 8 ressources (c’est-à-dire 5 réservations de capacité inutilisées et 3 machines virtuelles D2s_v3). Dans ce cas, les remises d’engagement de durée peuvent être appliquées soit à la machine virtuelle, soit à la réservation de capacité inutilisée, qui sont toutes deux facturées au même taux de PAYG. 


## <a name="work-with-capacity-reservation"></a>Travailler avec la réservation de capacité 

La réservation de capacité peut être créée pour une taille de machine virtuelle spécifique dans une région ou une zone de disponibilité Azure. Toutes les réservations sont créées et gérées dans le cadre d’un groupe de réservations de capacité, ce qui permet la création d’un groupe pour gérer différentes tailles de machine virtuelle dans une seule application multiniveau. Chaque réservation est pour une taille de machine virtuelle et un groupe ne peut avoir qu’une seule réservation par taille de machine virtuelle.  

Pour consommer la réservation de capacité, spécifiez le groupe de réservations de capacité comme l’une des propriétés de machine virtuelle. Si le groupe n’a pas de réservation correspondante, Azure renvoie un message d’erreur. 

La quantité réservée pour la réservation peut être ajustée après le déploiement initial en modifiant la propriété capacité. Les autres modifications apportées à la réservation de capacité, telles que la taille ou l’emplacement de la machine virtuelle, ne sont pas autorisées. L’approche recommandée consiste à supprimer la réservation existante et à en créer une nouvelle avec les nouvelles spécifications. 

La réservation de capacité ne crée pas de limites sur le nombre de déploiements de machines virtuelles. Azure prend en charge l’allocation d’autant de machines virtuelles que vous le souhaitez à la réservation. Étant donné que la réservation elle-même requiert un quota, les contrôles de quota sont omis pour le déploiement de machines virtuelles jusqu’à la quantité réservée. L’allocation de machines virtuelles supplémentaires à la réservation est soumise à des contrôles de quota et à Azure qui remplit la capacité supplémentaire. Une fois déployés, ces instances de machine virtuelle supplémentaires peuvent entraîner la quantité de machines virtuelles allouées à la réservation pour dépasser la quantité réservée. Cet état est appelé la surallocation. Pour en savoir plus, consultez [Surallocation d’une réservation de capacité](capacity-reservation-overallocate.md). 


## <a name="capacity-reservation-lifecycle"></a>Cycle de vie d’une réservation de capacité

Quand une réservation est créée, Azure met de côté le nombre demandé d’instances de capacité à l’emplacement spécifié : 

![Image de réservation de capacité 1.](./media/capacity-reservation-overview/capacity-reservation-1.jpg) 

Effectuez le suivi de l’état de la réservation globale par le biais des propriétés suivantes :  
- `capacity` = Quantité totale d’instances réservées par le client 
- `virtualMachinesAllocated` = Liste des machines virtuelles allouées à la réservation de capacité et compte pour la consommation de la capacité. Ces machines virtuelles sont *En cours d’exécution* ou *Arrêtées* (*Allouées*), ou peuvent être dans un état de transition tel que *Démarrer* ou l' *Arrêt*. Cette liste ne comprend pas les machines virtuelles en état de désallocation, également appelées *Arrêtées* (*désallouées*). 
- `virtualMachinesAssociated` = Liste des machines virtuelles associées à la réservation de capacité. Cette liste répertorie toutes les machines virtuelles qui ont été configurées pour utiliser la réservation, y compris celles qui sont dans l’état désalloué.  

L’exemple ci-dessus commence par `capacity` égal à 2 et la longueur de `virutalMachinesAllocated` et `virtualMachinesAssociated` égal à 0.  

Lorsqu’une machine virtuelle est ensuite allouée à la réservation de capacité, elle utilise logiquement l’une des instances de capacité de réserve : 

![Image de réservation de capacité 2.](./media/capacity-reservation-overview/capacity-reservation-2.jpg) 

L’état de la réservation de capacité affiche désormais `capacity` égal à 2 et la longueur de `virutalMachinesAllocated` et de `virtualMachinesAssociated` égal à 1.  

Les allocations par rapport à la réservation de capacité réussissent si les machines virtuelles ont des propriétés correspondantes et qu’il existe au moins une instance de capacité vide.  

À l’aide de notre exemple, lorsqu’une troisième machine virtuelle est allouée à la réservation de capacité, la réservation passe à l’état [surutilisé](capacity-reservation-overallocate.md). Cette troisième machine virtuelle nécessitera un quota inutilisé et une augmentation de la capacité supplémentaire d’Azure. Une fois que la troisième machine virtuelle est allouée, la réservation de capacité se présente désormais comme suit : 

![Image de réservation de capacité 3.](./media/capacity-reservation-overview/capacity-reservation-3.jpg) 

Le `capacity` est égal à 2 et la longueur de `virutalMachinesAllocated` et `virtualMachinesAssociated` est égale à 3. 

Supposons à présent que l’application s’adapte à la valeur minimale de deux machines virtuelles. Étant donné que la machine virtuelle 0 a besoin d’une mise à jour, elle est choisie pour la désallocation. La réservation passe automatiquement à cet état : 

![Image de réservation de capacité 4.](./media/capacity-reservation-overview/capacity-reservation-4.jpg) 

Le `capacity` et la longueur de `virtualMachinesAllocated` sont égal à 2. Toutefois, la longueur de `virtualMachinesAssociated` est toujours 3, car la machine virtuelle 0, bien que désallouée, est toujours associée à la réservation de capacité.  

La réservation de capacité existera jusqu’à ce qu’elle soit supprimée explicitement. Pour supprimer une réservation de capacité, la première étape consiste à dissocier toutes les machines virtuelles dans la propriété `virtualMachinesAssociated`. Une fois la désassociation terminée, la réservation de capacité doit se présenter comme suit : 

![Image de réservation de capacité 5.](./media/capacity-reservation-overview/capacity-reservation-5.jpg) 

L’état de la réservation de capacité affiche désormais `capacity` égal à 2 et la longueur de `virtualMachinesAssociated` et de `virtualMachinesAllocated` égal à 0. À partir de cet état, la réservation de capacité peut être supprimée. Une fois supprimé, vous ne payez plus la réservation.  

![Image de réservation de capacité 6.](./media/capacity-reservation-overview/capacity-reservation-6.jpg)


## <a name="usage-and-billing"></a>Utilisation et facturation 

Quand une réservation de capacité est vide, l’utilisation de la machine virtuelle est signalée pour la taille de machine virtuelle correspondante et l’emplacement. Les [instances réservées de machine virtuelle](https://azure.microsoft.com/pricing/reserved-vm-instances/) peuvent couvrir une partie ou la totalité de l’utilisation de la réservation de capacité même lorsque les machines virtuelles ne sont pas déployées. 

### <a name="example"></a>Exemple

Par exemple, imaginons une réservation de capacité avec la quantité réservée 2 créée. L’abonnement a accès à une instance de machine virtuelle réservée correspondante de la même taille. Le résultat est deux flux d’utilisation pour la réservation de capacité, dont l’un est couvert par l’instance réservée : 

![Image de réservation de capacité 7.](./media/capacity-reservation-overview/capacity-reservation-7.jpg)

Dans l’image ci-dessus, une remise d’instance de machine virtuelle réservée est appliquée à l’une des instances inutilisées et le coût de cette instance est mis à zéro. Pour l’autre instance, le tarif PAYG sera facturé pour la taille de machine virtuelle réservée.  

Lorsqu’une machine virtuelle est allouée à la réservation de capacité, les autres composants de machine virtuelle tels que les disques, le réseau, les extensions et tout autre composant requis doivent également être alloués. Dans cet état, l’utilisation de la machine virtuelle reflète une machine virtuelle allouée et une instance de capacité inutilisée. L’instance de machine virtuelle réservée met à zéro le coût de la machine virtuelle ou de l’instance de la capacité inutilisée. Les autres frais liés aux disques, à la mise en réseau et aux autres composants associés à la machine virtuelle allouée s’affichent également sur la facture. 

![Image de réservation de capacité 8.](./media/capacity-reservation-overview/capacity-reservation-8.jpg)

Dans l’image ci-dessus, la remise d’instance réservée de machine virtuelle est appliquée à la machine virtuelle 0, qui est facturée uniquement pour les autres composants tels que le disque et la mise en réseau. L’autre instance inutilisée est facturée au tarif PAYG pour la taille de machine virtuelle réservée.


## <a name="frequently-asked-questions"></a>Forum aux questions 

- **Quel est le prix de la réservation de capacité à la demande ?**

    Le prix de votre réservation de capacité à la demande est identique au prix de la taille de machine virtuelle sous-jacente associée à la réservation. Lorsque vous utilisez la réservation de capacité, vous êtes facturé pour la taille de machine virtuelle que vous avez sélectionnée au tarif du paiement à l’utilisation, que la machine virtuelle ait été approvisionnée ou non.  Pour plus d’informations, consultez les pages de tarification des machines virtuelles [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) et [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

- **Suis-je facturé deux fois, pour le coût de la réservation de capacité à la demande et pour la machine virtuelle réelle quand je l’ai finalement configurée ?**

    Non, vous ne serez facturé qu’une seule fois pour la réservation de capacité à la demande.   

- **Puis-je appliquer l’instance de machine virtuelle réservée (RI) à la réservation de capacité à la demande pour réduire les coûts ?**

    Oui, vous pouvez appliquer les services RI existants ou futurs à des réservations de capacité à la demande et recevoir des remises RI. Les services RI disponibles sont appliqués automatiquement à la réservation de capacité de la même façon que pour les machines virtuelles.

- **Quelle est la différence entre l’instance de machine virtuelle réservée (RI) et la réservation de capacité à la demande ?**

    Les RI et les réservations de capacité à la demande s’appliquent aux machines virtuelles Azure. Toutefois, les services RI fournissent des taux de réservation réduits pour vos machines virtuelles par rapport au tarif du paiement à l’utilisation à la suite d’un engagement de durée, de 1 an ou de 3 ans. À l’inverse, les réservations de capacité à la demande ne nécessitent pas d’engagement. Vous pouvez créer ou supprimer une réservation de capacité à tout moment. Toutefois, aucune remise n’est appliquée et des frais sont facturés au tarif du paiement à l’utilisation une fois que la réservation de capacité a été correctement approvisionnée. Contrairement aux RI, qui hiérarchisent la capacité, mais ne le garantissent pas, lorsque vous achetez une réservation de capacité à la demande, Azure met de côté la capacité de calcul pour votre machine virtuelle et fournit une garantie SLA.  

- **Quels scénarios tireraient le meilleur parti des réservations de capacité à la demande ?**

    Les scénarios classiques incluent la continuité d’activité, la récupération d’urgence et le scale-out des applications stratégiques.  


## <a name="next-steps"></a>Étapes suivantes

Créez une réservation de capacité et commencez à réserver de la capacité de calcul dans une région ou une zone de disponibilité Azure. 

> [!div class="nextstepaction"]
> [Créer une réservation de capacité](capacity-reservation-create.md)
