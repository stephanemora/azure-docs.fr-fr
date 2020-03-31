---
title: Déplacer des ressources Azure vers une autre région
description: Fournit une vue d’ensemble du déplacement des ressources Azure entre les régions Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75476591"
---
# <a name="moving-azure-resources-across-regions"></a>Déplacement des ressources Azure entre les régions

Cet article fournit des informations sur le déplacement des ressources Azure entre les régions Azure.

Les zones géographiques, les régions et les Zones de disponibilité Azure constituent la base de l’infrastructure mondiale d’Azure. Les [zones géographiques](https://azure.microsoft.com/global-infrastructure/geographies/) d’Azure contiennent généralement au moins deux [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/). Une région est une zone géographique contenant des zones de disponibilité et plusieurs centres de données. 

Une fois les ressources déployées dans une région Azure spécifique, il existe plusieurs raisons pour lesquelles vous pouvez souhaiter déplacer des ressources vers une autre région.

- **Aligner sur un lancement d’une région** : Déplacer vos ressources vers une région Azure récemment introduite qui n’était pas disponible auparavant.
- **Aligner en raison des services/fonctionnalités** : Déplacer des ressources pour profiter des services ou fonctionnalités qui sont disponibles dans une région spécifique.
- **Répondre au développement de l’activité** : Déplacer des ressources vers une région en réponse à des changements de l’activité, comme des fusions ou des acquisitions.
- **Aligner pour cause de proximité** : Déplacer des ressources vers une région proche de votre entreprise.
- **Respecter les exigences relatives aux données** : Déplacer des ressources afin de respecter les exigences de résidence des données ou les besoins en matière de classification des données. [Plus d’informations](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)
- **Respecter les exigences de déploiement** : Déplacer des ressources qui ont été déployées par erreur ou déplacez-les en réponse aux besoins de capacité. 
- **Respecter la désaffectation** : Déplacer des ressources en raison de la désaffectation des régions.

## <a name="move-process"></a>Processus de déplacement

Le processus de déplacement réel dépend des ressources que vous déplacez. Toutefois, il existe quelques étapes clés communes :

- **Vérifier les prérequis** : Les prérequis incluent de s’assurer que les ressources dont vous avez besoin dont disponibles dans la région source, vérifier que votre quota est suffisant et vérifier que votre abonnement peut accéder à la région cible.
- **Analyser les dépendances** : Vos ressources peuvent avoir des dépendances avec d’autres ressources. Avant le déplacement, déterminez les dépendances afin que les ressources déplacées continuent à fonctionner comme prévu après le déplacement.
- **Préparer le déplacement** : Les étapes à suivre dans votre région principale avant le déplacement. Par exemple, vous devrez peut-être exporter un modèle Azure Resource Manager ou démarrer la réplication des ressources de la source vers la cible.
- **Déplacer les ressources** : Le mode de déplacement des ressources dépend de ce qu’elles sont. Vous devrez peut-être déployer un modèle dans la région cible ou basculer les ressources sur la cible.
- **Abandonner les ressources cibles** : Après avoir déplacé des ressources, vous souhaiterez peut-être examiner les ressources qui sont maintenant dans la région cible et décider si vous en avez besoin ou pas.
- **Valider le déplacement** : Après avoir vérifié les ressources dans la région cible, certaines ressources peuvent nécessiter une action de validation finale. Par exemple, dans une région cible qui est désormais la région principale, vous devrez peut-être configurer la récupération d’urgence vers une nouvelle région secondaire. 
- **Nettoyer la source** : Enfin, une fois que tout est en cours d’exécution dans la nouvelle région, vous pouvez nettoyer et désactiver les ressources que vous avez créées pour le déplacement et les ressources dans votre région principale.



## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des ressources prenant en charge l’opération de déplacement entre les régions, consultez [Prise en charge de l’opération de déplacement pour les ressources](region-move-support.md).
