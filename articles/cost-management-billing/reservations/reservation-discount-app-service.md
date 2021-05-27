---
title: Remises sur les réservations pour Azure App Service
description: Découvrez comment les remises de réservation s’appliquent aux instances Azure App Service Premium v3 et Premium v2, ainsi qu’aux Timbres isolés.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: banders
ms.openlocfilehash: b6230f86fc33dca290f6d61f923024c9352d8600
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378505"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service"></a>Comment les remises de réservation s’appliquent à Azure App Service

Cet article vous permet de comprendre comment les remises s’appliquent aux instances Azure App Service Premium v3 et Premium v2, ainsi qu’aux Timbres isolés.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Comment les remises de réservation s’appliquent aux instances Premium v3

Quand vous achetez une instance réservée Azure App Service Premium v3, la remise de réservation est automatiquement appliquée aux instances App Service qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre le coût de vos instances Premium v3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Comment la remise s’applique à Azure App Service 

Une remise de réservation repose sur le principe de *capacité utilisée ou perdue*. Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.
Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n’est trouvée dans l’étendue spécifiée, les heures réservées sont perdues.

### <a name="reservation-discount-for-premium-v3-instances"></a>Remise de réservation pour les instances Premium v3

La remise de réservation Azure est appliquée aux instances Premium v3 en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les instances Premium v3 en cours d’exécution pour appliquer la remise de réservation. Pour les instances Premium v3 qui ne s’exécutent pas pendant une heure entière, la réservation est complétée avec d’autres instances qui n’utilisent pas de réservation, y compris celles qui s’exécutent simultanément. À la fin de l’heure, l’application de la réservation aux instances dans l’heure est verrouillée. Quand une instance ne s’exécute pas pendant une heure ou quand des instances s’exécutant simultanément durant l’heure ne complètent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une réservation à l’utilisation de machines virtuelles facturables. La représentation est basée sur l’achat d’une réservation et sur deux instances de machine virtuelle correspondantes.

![Image montrant l’application d’une réservation à l’utilisation de machines virtuelles facturables](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Toute utilisation supérieure à la ligne de réservation est facturée au tarif standard de paiement à l’utilisation. Toute utilisation sous la ligne de réservation n’est pas facturée, car elle a déjà été payée dans le cadre de l’achat de la réservation.
2.  Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3.  Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par la réservation, et l’autre est facturée au tarif du paiement à l’utilisation.
4.  Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par la réservation, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports d’utilisation pour la facturation, voir [Comprendre l’utilisation des réservations](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-v2-instances"></a>Comment les remises de réservation s’appliquent aux instances Isolé v2

Quand vous achetez une instance réservée Azure App Service Isolé v2, la remise de réservation est automatiquement appliquée aux instances App Service qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre le coût de vos instances Isolé v2.

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Comment la remise s’applique à Azure App Service

Une remise de réservation repose sur le principe de _capacité utilisée ou perdue_. Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées. Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n’est trouvée dans l’étendue spécifiée, les heures réservées sont perdues.

### <a name="reservation-discount-for-isolated-v2-instances"></a>Remise de réservation pour les instances Isolé v2

La remise de réservation Azure est appliquée aux instances Isolé v2 en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les instances Isolé v2 en cours d’exécution pour appliquer la remise de réservation. Pour les instances Isolé v2 qui ne s’exécutent pas pendant une heure entière, la réservation est complétée avec d’autres instances qui n’utilisent pas de réservation, dont des instances s’exécutant simultanément. À la fin de l’heure, l’application de la réservation aux instances dans l’heure est verrouillée. Quand une instance ne s’exécute pas pendant une heure ou quand des instances s’exécutant simultanément durant l’heure ne complètent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports d’utilisation pour la facturation, voir [Comprendre l’utilisation des réservations](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Comment les remises de réservation s’appliquent aux timbres isolés

Après l’achat d’une capacité réservée de timbres isolés App Service, la remise de réservation est automatiquement appliquée aux frais de timbres dans une région. La remise de réservation s’applique à l’utilisation émise par le compteur de frais de timbre isolé. Les workers, les frontaux supplémentaires et toute autre ressource associée au timbre continuent d’être facturés au tarif régulier.

### <a name="reservation-discount-application"></a>Application de la remise de réservation

La remise sur les frais de timbres isolés de l’App Service s’applique aux timbres isolés en cours d’exécution sur une base horaire. Si vous n’avez pas de tampon déployé pendant une heure, la capacité réservée est gaspillée pour cette heure. Elle n’est pas reconduite.

Après l’achat, la réservation que vous achetez est jumelée à un timbre isolé courant dans une région spécifique. Si vous arrêtez ce tampon, les remises de réservation sont automatiquement appliquées à tous les autres tampons en cours d’exécution dans la région. Lorsqu’il n’existe aucun tampon, la réservation est appliquée au tampon suivant créé dans la région.

Lorsque les tampons ne s’exécutent pas pendant une heure complète, la réservation s’applique automatiquement à d’autres tampons correspondants dans la même région pendant la même heure.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Choisir un type de tampon - Windows ou Linux

Un tampon isolé vide émet le compteur de tampon Windows par défaut. Par exemple, quand aucun rôle de travail n’est déployé. Il continue à émettre le compteur lorsque les rôles de travail Windows sont déployés. Si vous déployez un rôle de travail Linux, le compteur prend la valeur du compteur de tampon Linux. Le tampon émet le compteur Windows lorsque les rôles de travail Linux et Windows sont déployés.

Par conséquent, le compteur de tampons peut changer entre Windows et Linux pendant toute la durée de vie du tampon. Pendant ce temps, les réservations sont spécifiques au système d’exploitation. Vous devez acheter une réservation qui prend en charge les rôles de travail que vous envisagez de déployer sur le tampon. Les tampons spécifiques à Windows et les tampons mixtes utilisent la réservation Windows. Les tampons avec des rôles de travail Linux utilisent la réservation Linux.

Le seul cas où vous devez acheter une réservation Linux est lorsque vous envisagez de _n’avoir que_ des rôles de travail Linux dans le tampon.

### <a name="discount-examples"></a>Exemples de remises

Les exemples suivants montrent comment la remise d’instance réservée de frais de timbre isolé s’applique en fonction des déploiements.

- **Exemple 1** : Vous achetez une instance de capacité de tampon réservé isolé dans une région sans tampon isolé App Service. Vous déployez un nouveau tampon dans la région et payez des tarifs réservés pour ce tampon.
- **Exemple 2 :** Vous achetez une instance de capacité de tampon réservé isolé dans une région qui a déjà déployé un tampon isolé App Service. Vous commencez à recevoir le tarif réservé pour le tampon déployé.
- **Exemple 3** : Vous achetez une instance de capacité de tampon réservé isolé dans une région qui a déjà déployé un tampon isolé App Service. Vous commencez à recevoir le tarif réservé pour le tampon déployé. Par la suite, vous supprimez le tampon et en déployez un nouveau. Vous recevez le tarif réservé pour le nouveau tampon. Les remises ne sont pas reportées pour les durées sans tampons déployés.
- **Exemple 4** : Vous achetez une instance de capacité de tampon réservé Linux isolé dans une région puis déployez un nouveau tampon dans la région. Lorsque le tampon est initialement déployé sans les rôles de travail, il émet le compteur de tampons Windows. Aucune remise n’est reçue. Lorsque le premier rôle de travail Linux déploie le tampon, il émet le compteur de tampon Linux et la remise de réservation s’applique. Si un rôle de travail Windows est déployé ultérieurement sur le tampon, le compteur de tampons revient à Windows. Vous ne recevez plus de remise pour la réservation de tampon réservé Linux isolée.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](manage-reserved-vm-instance.md).
- Si vous voulez en savoir plus sur l’achat anticipé d’une capacité réservée App Service Premium v3 et Timbre isolé pour faire des économies, consultez [Prépayer Azure App Service avec une capacité réservée](prepay-app-service.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
  - [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
  - [Gérer les réservations dans Azure](manage-reserved-vm-instance.md)
  - [Comprendre l’utilisation d’une réservation pour un abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
  - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
