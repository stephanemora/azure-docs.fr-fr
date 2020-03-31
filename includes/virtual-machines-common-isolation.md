---
title: Fichier include
description: Fichier include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123196"
---
Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client.  Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité.  Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

L’utilisation d’une taille isolée garantit que votre machine virtuelle sera la seule en cours d’exécution sur cette instance de serveur spécifique.  Les offres actuelles de machines virtuelles isolées sont les suivantes :
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Pour plus d’informations sur chaque taille isolée disponible, [cliquez ici](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Retrait de l’isolation D15_v2/DS15_v2 le 15 mai 2020
**Mise à jour du 10 février 2020 : La chronologie de retrait de l’« isolation » a été étendue au 15 mai 2020**

L’hôte dédié Azure est désormais GA, ce qui vous permet d’exécuter les machines virtuelles Linux et Windows de votre organisation sur des serveurs physiques à locataire unique. Nous prévoyons de remplacer entièrement les machines virtuelles Azure isolées par Azure Dedicated Host. Après le **15 mai 2020**, les machines virtuelles Azure D15_v2/DS15_v2 ne seront plus isolées du matériel.

## <a name="how-does-this-affect-me"></a>Dans quelle mesure cela m’affecte-t-il ?
Après le 15 mai 2020, nous ne fournirons plus de garantie d’isolation pour vos machines virtuelles Azure D15_v2/DS15_v2. 

## <a name="what-actions-should-i-take"></a>Que dois-je faire ?
Si vous n’avez pas besoin d’isolation matérielle, aucune action n’est nécessaire. 

Si vous avez besoin d’isolation, avant le 15 mai 2020, vous devez au choix :

•   [Migrer](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) votre charge de travail vers Azure Dedicated Host.

•   [Demander l’accès](https://aka.ms/D15iRequestAccess) à une machine virtuelle Azure D15i_v2 et DS15i_v2, pour obtenir les mêmes performances de prix. Cette option est disponible uniquement pour les scénarios de paiement à l’utilisation et d’instance réservée 1 an.    

•   [Migrer](https://azure.microsoft.com/blog/resize-virtual-machines/) votre charge de travail vers une autre machine virtuelle isolée Azure. 

Pour plus d’informations, voir ci-dessous :

## <a name="timeline"></a>Chronologie
| Date | Action | 
| --- | --- |
| 18 novembre 2019 | Disponibilité de D/DS15i_v2 (paiement à l’utilisation, instance réservée 1 an) |
| 14 mai 2020  | Dernier jour pour acheter l’instance réservée 1 an D/DS15i_v2 | 
| 15 mai 2020   | Garantie d’isolation D/DS15_v2 retirée | 
| 15 mai 2021  | Mise hors service de D/DS15i_v2 (tous les clients, sauf ceux qui ont acheté une instance réservée 3 ans de D/DS15_v2 avant le 18 novembre 2019)| 
| 17 novembre 2022  | Mise hors service de D/DS15i_v2 quand les instances réservées 3 ans arrivent à leur terme (pour les clients qui ont acheté une instance réservée 3 ans de D/DS15_v2 avant le 18 novembre 2019) | 

## <a name="faq"></a>Questions fréquentes (FAQ)
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>Q : La taille D/DS15_v2 va-t-elle être retirée ?
**R** : Non, seule la fonctionnalité d’« isolation » va être retirée. Si vous n’avez pas besoin d’isolation, vous n’avez rien à faire.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>Q : La taille D/DS15i_v2 va-t-elle être retirée ?
**R** : Oui, la taille ne sera disponible que jusqu’au 15 mai 2021. Les clients ayant acheté des instances réservées de 3 ans sur D/DS15_v2 avant le 18 novembre 2019 auront accès à D/DS15i_v2 jusqu’au 17 novembre 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Q : Pourquoi je ne vois pas les nouvelles tailles D/DS15i_v2 dans le portail ?
**R** : Si vous êtes un client D/DS15_v2 actif et souhaitez utiliser les nouvelles tailles de D/DS15i_v2, remplissez ce [formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Q : Pourquoi je ne vois aucun quota pour les nouvelles tailles D/DS15i_v2 ?
**R** : Si vous êtes un client D/DS15_v2 actif et souhaitez utiliser les nouvelles tailles de D/DS15i_v2, remplissez ce [formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q : Quand les autres tailles isolées vont-elles être mises hors service ?
**R** : Nous fournirons des rappels 12 mois à l’avance de la désaffectation officielle des tailles.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q : Y a-t-il un temps d’arrêt lorsque ma machine virtuelle arrive sur un matériel non isolé ?
**R** : Si vous n’avez pas besoin d’isolation, vous n’avez rien à faire et vous ne subirez aucun temps d’arrêt.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Q : Les prix changent-ils pour passer à une machine virtuelle non isolée ?
**R** : Non 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Q : J’ai déjà acheté une instance réservée 1 an ou 3 ans pour D15_v2 ou Ds15_v2. Comment la remise sera-t-elle appliquée à mon utilisation de la machine virtuelle ?
**R** : Les instances réservées achetées avant le 18 novembre 2019 étendent automatiquement la couverture à la nouvelle série de machines virtuelles isolées. 

| Instance réservée |  Flexibilité de la taille d’instance | Droit aux avantages |   
| --- | --- | --- |
|   D15_v2  |   Off     |   D15_v2 et D15i_v2 |    
|   D15_v2  |   Il en va  |   La série D15_v2 et D15i_v2 tireront toutes parti de l’instance réservée. |    
|   D14_v2  |   Il en va  |   La série D15_v2 et D15i_v2 tireront toutes parti de l’instance réservée. |    
 
Même chose pour la série Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Q : Je souhaite acheter des instances réservées supplémentaires pour Dv2. Laquelle dois-je choisir ?
**R** : Toutes les instances réservées achetées après le 18 novembre 2019 ont le comportement suivant. 

| Instance réservée |  Flexibilité de la taille d’instance | Droit aux avantages |   
| --- | --- | --- |
| D15_v2 |  Off |   D15_v2 uniquement  
| D15_v2 |  Il en va |    La série D15_v2 tirera parti de l’instance réservée. La nouvelle D15i_v2 ne pourra pas bénéficier de l’instance réservée de ce type. | 
| D15i_v2 |     Off | D15i_v2 uniquement |  
| D15i_v2 |     Il en va  | D15i_v2 uniquement | 
 
La flexibilité de la taille d’instance ne peut pas être appliquée à d’autres tailles, telles que D2_v2, D4_v2 ou D15_v2. Même chose pour la série Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Q : Puis-je acheter une nouvelle instance réservée 3 ans pour D15i_v2 et DS15i_v2 ?
**R** : Malheureusement non, seule une instance réservée 1 an est disponible pour un nouvel achat.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Q : Puis-je transférer mon instance réservée D15_v2/DS15_v2 existante vers une instance réservée de taille isolée ?
**R** : Cette action n’est pas nécessaire, car l’avantage s’applique aux tailles tant isolées que non isolées. Toutefois, Azure prendra en charge le remplacement des instances réservées D15_v2/DS15_v2 existantes par D15i_v2/DS15i_v2. Pour toutes les autres instances réservées Dv2/Dsv2, utilisez l’instance réservée existante ou achetez de nouvelles instances réservées pour les tailles isolées.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q : Je suis un client Azure Service Fabric qui s’appuie sur les niveaux de durabilité Argent ou Or. Ce changement a-t-il un impact sur moi ?
**R** : Non. Les garanties fournies par les [niveaux de durabilité](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) de Service Fabric continuent de fonctionner même après ce changement. Si vous avez besoin d’une isolation matérielle physique pour d’autres raisons, vous devrez peut-être effectuer l’une des actions décrites ci-dessus. 
