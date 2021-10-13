---
title: Résoudre les problèmes d’utilisation de réservations Azure
description: Cet article vous aide à comprendre et à résoudre les problèmes de réservations Azure qui n’indiquent aucune utilisation ou une utilisation nulle (0) dans le portail Azure. L’utilisation qui ne correspond pas est également expliquée.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 4c758dfdc40e23197128bc08cccc5748f08f7ed4
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534891"
---
# <a name="troubleshoot-reservation-utilization"></a>Résoudre les problèmes d’utilisation de réservations

Cet article vous aide à comprendre et à résoudre les problèmes de réservations Azure qui n’indiquent aucune utilisation ou une utilisation nulle (0) dans le portail Azure. L’utilisation qui ne correspond pas est également expliquée.

## <a name="symptoms"></a>Symptômes

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Réservations**.
1. Dans la liste des réservations, recherchez la quantité d’utilisation d’une réservation dans la colonne **Utilisation (%)** . Elle peut être de 0 %.
1. Sélectionnez la réservation.
1. Dans la page de vue d’ensemble de la réservation, le pourcentage utilisé dans le graphique peut ne pas correspondre à la valeur indiquée dans la liste des réservations.

## <a name="cause"></a>Cause

La colonne **Utilisation (%)** dans le portail Azure indique la valeur du jour. Cette valeur est calculée lorsque les données d’utilisation arrivent à partir de là où les ressources s’exécutent. Azure se sert de l’utilisation pour calculer le pourcentage d’utilisation.

Certaines ressources signalent l’utilisation plus lentement que d’autres. De plus, certains types de produits comme les bases de données SQL sont lents à signaler leurs données d’utilisation.

En raison de la latence, le calcul de l’utilisation peut indiquer des valeurs inférieures à l’utilisation réelle. La différence est perceptible à la limite de la journée. Dans ce cas, si Azure n’obtient pas les données d’utilisation de quatre à huit heures, il calcule une valeur de 0 %. La valeur 0 % est indiquée parce que les données d’utilisation ne sont pas parvenues, et il semble que la réservation n’applique aucun avantage à aucune ressource.

Lorsque les données d’utilisation arrivent, la valeur change jusqu’à atteindre le pourcentage correct. Lorsque toutes les données d’utilisation sont collectées, la valeur correcte est déterminée et affichée avec précision dans le graphique.

## <a name="solution"></a>Solution

Si vous constatez que vos valeurs d’utilisation ne correspondent pas à vos attentes, passez en revue le graphique pour obtenir une vue d’ensemble de l’utilisation réelle. Toute valeur de point datant de plus de deux jours doit être exacte. Les moyennes à plus long terme, de sept à 30 jours, doivent être exactes.

## <a name="other-common-scenarios"></a>Autres scénarios courants
- Vous avez peut-être arrêté l’exécution de la ressource A et démarré la ressource B qui ne s’applique pas à la réservation que vous avez achetée. Pour résoudre ce risque, vous devrez peut-être échanger la réservation pour la faire correspondre à la ressource appropriée. 
- Vous avez peut-être déplacé une ressource d’un abonnement à une autre ou d’un groupe de ressources à un autre, alors que l’étendue de la réservation est différente de celle de la destination vers laquelle la ressource est déplacée. Pour résoudre ce cas, vous devrez peut-être modifier l’étendue de la réservation.
- Vous avez peut-être acheté une autre réservation qui a également appliqué un avantage à la même étendue et, par conséquent, moins d’une instance réservée existante a appliqué un avantage. Pour résoudre ce problème, vous devrez peut-être échanger/rembourser une des réservations.
- Vous avez peut-être cessé d’exécuter une ressource particulière et, par conséquent, cette dernière a cessé d’émettre une utilisation et l’application de l’avantage a cessé. Pour résoudre ce risque, vous devrez peut-être échanger la réservation pour la faire correspondre à la ressource appropriée. 
- Vous avez peut-être modifié l’étendue de la réservation et cela a entraîné l’arrêt de l’application d’un avantage aux ressources. Pour y remédier, vous devrez peut-être modifier à nouveau l’étendue de la réservation pour vous assurer que les ressources sont déployées dans la même étendue.
- L’abonnement sur lequel la réservation a été étendue a été supprimé ou déplacé, de sorte que l’avantage n’est pas appliqué aux ressources. Pour rectifier cela, vous devrez peut-être modifier l’étendue de la réservation.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion des réservations, consultez [Gérer les réservations pour les ressources Azure](manage-reserved-vm-instance.md).
