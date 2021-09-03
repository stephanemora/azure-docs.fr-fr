---
title: Estimer les coûts de diffusion d'applications par utilisateur pour Azure Virtual Desktop - Azure
description: Apprenez à estimer les coûts de facturation par utilisateur pour Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798786"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>Estimer les coûts de diffusion d'applications par utilisateur pour Azure Virtual Desktop

La tarification de l'accès par utilisateur d'Azure Virtual Desktop vous permet d'accorder l'accès aux applications et aux appareils de bureau hébergés avec Azure Virtual Desktop à des utilisateurs extérieurs à votre organisation. Pour en savoir plus sur la tarification de l'accès par utilisateur d'Azure Virtual Desktop, consultez [Tarification des licences et de l'accès par utilisateur](licensing.md). Cet article explique comment estimer les coûts d'accès utilisateur, à plein tarif, de votre déploiement.

>[!NOTE]
>Les prix indiqués dans cet article sont basés sur des abonnements par utilisateur à plein tarif, sans offres promotionnelles ni remises. De plus, gardez à l'esprit qu'un déploiement Azure Virtual Desktop entraîne des coûts supplémentaires, tels que les coûts d'utilisation de l'infrastructure. Pour en savoir plus sur ces autres coûts, consultez [Estimer le coût total d'un déploiement Azure Virtual Desktop](total-costs.md).

## <a name="requirements"></a>Configuration requise

Avant de pouvoir estimer les coûts d'accès par utilisateur d'un déploiement existant, vous devez disposer de ce qui suit :

- Un déploiement Azure Virtual Desktop qui comportait des utilisateurs actifs au cours du dernier mois
- [Azure Monitor pour votre déploiement Azure Virtual Desktop](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>Mesurer l'activité mensuelle des utilisateurs dans un pool d'hôtes

Afin d'estimer le coût total de fonctionnement d'un pool d'hôtes, vous devez d'abord connaître le nombre d'utilisateurs actifs au cours du dernier mois. Pour accéder à ce chiffre, vous pouvez utiliser Azure Monitor pour Azure Virtual Desktop.

Pour consulter le nombre d'utilisateurs actifs mensuels sur Azure Monitor :

1. Ouvrez le portail Azure, puis recherchez et sélectionnez **Azure Virtual Desktop**. Sélectionnez ensuite **Insights** afin d'ouvrir Azure Monitor pour Azure Virtual Desktop.

2. Sélectionnez le nom de l'abonnement ou du pool d'hôtes que vous souhaitez mesurer.

3. Dans l'onglet **Vue d'ensemble**, accédez au graphique **Utilisateurs mensuels (MAU)** de la section **Utilisation**.

4. Relevez la valeur Utilisateurs actifs mensuels (MAU) correspondant à la date la plus récente. La valeur MAU indique combien d'utilisateurs se sont connectés à ce pool d'hôtes au cours des 28 jours précédant cette date.

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>Estimer les coûts d'accès par utilisateur pour un pool d'hôtes Azure Virtual Desktop

Nous allons maintenant vérifier le montant facturé par période de facturation. Ce montant est déterminé par le nombre d'utilisateurs connectés à au moins un hôte de session dans votre abonnement inscrit.

En outre, deux niveaux tarifaires s'appliquent aux utilisateurs :

- Utilisateurs qui se connectent uniquement aux groupes d'applications RemoteApp
- Utilisateurs qui se connectent à au moins un groupe d'applications de bureau

Vous pouvez estimer le coût total en vérifiant combien d'utilisateurs de chaque niveau tarifaire se sont connectés aux hôtes de session dans votre abonnement.

Pour connaître le nombre d'utilisateurs de chaque niveau :

1. Accédez à la [page des tarifs d'Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) et recherchez les tarifs « Applications » et « Applications + Appareils de bureau » correspondant à votre région.
2. Utilisez le chiffre du volume de connexions que vous avez trouvé à l'étape 4 de la section [Mesurer l'activité mensuelle dans un pool d'hôtes](#measure-monthly-user-activity-in-a-host-pool) pour calculer le coût total des accès utilisateur.
   
   Si votre pool d'hôtes utilise un groupe d'applications RemoteApp, vous devrez multiplier le volume de connexions par la valeur tarifaire affichée dans « Applications ». Autrement dit, vous devrez utiliser l'équation suivante :

   Volume de connexions x tarif « Applications » par utilisateur = Coût total

   Si votre pool d'hôtes utilise un groupe d'applications Desktop, multipliez-le par le tarif « Applications + Appareils de bureau » par utilisateur, comme suit :

   Volume de connexions x tarif « Applications + Appareils de bureau » par utilisateur = Coût total

>[!IMPORTANT]
>En fonction de votre environnement, le prix réel peut être très différent de l'estimation que vous obtiendrez en suivant ces instructions. Par exemple, l'estimation peut être supérieure au coût réel parce que vos utilisateurs accèdent aux ressources de plusieurs pools d'hôtes, alors que vous n'êtes facturé qu'une seule fois par utilisateur pour chaque période de facturation. L'estimation peut également sous-estimer les coûts si l'activité des utilisateurs au cours de la période de 28 jours sur laquelle vous basez vos données ne correspond pas à votre activité utilisateur mensuelle standard. Par exemple, un mois avec une semaine de vacances ou une panne de service majeure se traduira par une activité utilisateur inférieure à la moyenne, ce qui ne vous fournira pas une estimation précise.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l'estimation du coût total d'un déploiement complet d'Azure Virtual Desktop, consultez [Estimer le coût total d'un déploiement Azure Virtual Desktop](total-costs.md). Pour en savoir plus sur les exigences et les coûts liés aux licences, consultez [Tarification des licences et de l'accès par utilisateur](licensing.md).
