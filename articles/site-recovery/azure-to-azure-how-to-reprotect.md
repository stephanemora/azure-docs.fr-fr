---
title: Reprotéger les machines virtuelles Azure basculées vers la région Azure principale avec Azure Site Recovery | Microsoft Docs
description: Décrit comment reprotéger les machines virtuelles Azure dans une région secondaire, après basculement à partir d’une région principale, à l’aide d’Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: eabb7d194a3ef65282befab1ae59e85ba56f2f5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472161"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Reprotéger les machines virtuelles Azure basculées vers la région principale


Lorsque vous [basculez](site-recovery-failover.md) des machines virtuelles Azure d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md), le démarrage des machines virtuelles dans la région secondaire se fait dans un état non protégé. Si les machines virtuelles rebasculent vers la région principale, vous devez effectuer les opérations suivantes :

- Reprotégez les machines virtuelles dans la région secondaire, afin qu’elles commencent à se répliquer dans la région principale.
- Après l’exécution de la reprotection et de la réplication des machines virtuelles, vous pouvez les basculer de la région secondaire vers la région principale.

## <a name="prerequisites"></a>Prérequis
1. Le basculement de machine virtuelle de la région principale vers la région secondaire doit être validé.
2. Le site cible principal doit être disponible et vous devez être en mesure d’accéder aux ressources ou de créer des ressources dans cette région.

## <a name="reprotect-a-vm"></a>Reprotéger une machine virtuelle

1. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant échoué, puis sélectionnez **Reprotéger**. La direction de reprotection doit afficher secondaire à principal.

   ![Reprotéger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Vérifiez les informations Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité. Cliquez ensuite sur **OK**. Si des ressources sont marquées comme nouvelles, elles seront créées dans le cadre du processus de reprotection.
3. Le travail de reprotection amorce le site cible avec les données les plus récentes. Une fois que cela se termine, la réplication différentielle a lieu. Ensuite, vous pouvez rebasculer vers le site principal. Vous pouvez sélectionner le compte de stockage ou le réseau que vous voulez utiliser pendant la reprotection à l’aide de l’option Personnaliser.

   ![Option Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personnaliser les paramètres de reprotection

Vous pouvez personnaliser les propriétés suivantes de la machine virtuelle cible durant une reprotection.

![Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriété |Notes  |
|---------|---------|
|Groupe de ressources cible     | Modifiez le groupe de ressources cible dans lequel la machine virtuelle est créée. Dans le cadre de la reprotection, la machine virtuelle cible est supprimée. Vous pouvez choisir un nouveau groupe de ressources sous lequel créer la machine virtuelle après le basculement.        |
|Réseau virtuel cible     | Le réseau cible ne peut pas être modifié lors de la tâche de reprotection. Pour modifier le réseau, refaites le mappage réseau.         |
|Stockage cible (la machine virtuelle secondaire n’utilise pas de disques managés)     | Vous pouvez modifier le compte de stockage que la machine virtuelle utilise après le basculement.         |
|Disques managés de réplica (la machine virtuelle secondaire utilise des disques managés)    | Site Recovery crée des disques managés de réplica dans la région primaire pour mettre en miroir les disques managés de la machine virtuelle secondaire.         |
|Stockage du cache     | Vous pouvez spécifier un compte de stockage de cache à utiliser lors de la réplication. Par défaut, un nouveau compte de stockage de cache est créé, s’il n’existe pas.         |
|Groupe à haute disponibilité     |Si la machine virtuelle dans la région secondaire fait partie d’un groupe à haute disponibilité, vous pouvez choisir un groupe à haute disponibilité pour la machine virtuelle cible dans la région principale. Par défaut, Site Recovery tente de trouver le groupe à haute disponibilité dans la région principale et de l’utiliser. Au cours de la personnalisation, vous pouvez spécifier un groupe à haute disponibilité.         |


### <a name="what-happens-during-reprotection"></a>Que se passe-t-il durant la reprotection ?

Par défaut les événements suivants se produisent :

1. Un compte de stockage de cache est créé dans la région où la machine virtuelle ayant basculé est en cours d’exécution.
2. Si le compte de stockage cible (le compte de stockage d’origine dans la région principale) n’existe pas, un nouveau est créé. Le nom de compte de stockage affecté est le nom du compte de stockage utilisé par la machine virtuelle secondaire, suivi du suffixe « asr ».
3. Si votre machine virtuelle utilise des disques managés, les disques managés de réplica sont créés dans la région primaire pour stocker les données répliquées à partir des disques de la machine virtuelle secondaire.
4. Si le groupe à haute disponibilité cible n’existe pas, un nouveau est créé dans le cadre du travail de reprotection si nécessaire. Si vous avez personnalisé les paramètres de la reprotection, le groupe sélectionné est utilisé.

Lorsque vous déclenchez un travail de reprotection et que la cible de que machine virtuelle existe, les événements suivants se produisent :

1. La machine virtuelle côté cible est désactivée si elle est en cours d’exécution.
2. Si la machine virtuelle utilise des disques managés, une copie des disques d’origine est créée avec le suffixe '-ASRReplica'. Les disques d’origine sont supprimés. Les copies '-ASRReplica' sont utilisées pour la réplication.
3. Si la machine virtuelle utilise des disques non managés, les disques de données de la machine virtuelle cible sont détachés et utilisés pour la réplication. Une copie du disque du système d’exploitation est créée et attachée à la machine virtuelle. Le disque d’origine du système d’exploitation est détaché et utilisé pour la réplication.
4. Seules les modifications entre le disque source et le disque cible sont synchronisées. Les sauvegardes différentielles sont calculées en comparant deux disques, puis transférées. Ci-dessous, vous trouverez la durée estimée.
5. Une fois la synchronisation terminée, la réplication delta commence à créer un point de récupération conformément à la stratégie de réplication.

Lorsque vous déclenchez un travail de reprotection et que la machine virtuelle cible et les disques n’existent pas, les événements suivants se produisent :
1. Si la machine virtuelle utilise des disques managés, des disques de réplica sont créés avec le suffixe '-ASRReplica'. Les copies '-ASRReplica' sont utilisées pour la réplication.
2. Si la machine virtuelle à l’aide de disques non gérés, des disques de réplica sont créés dans le compte de stockage cible.
3. Les disques entiers sont copiés à partir de la région ayant échoué vers la nouvelle région cible.
4. Une fois la synchronisation terminée, la réplication delta commence à créer un point de récupération conformément à la stratégie de réplication.

#### <a name="estimated-time--to-do-the-reprotection"></a>Durée estimée pour effectuer la reprotection 

En général, Azure Site Recovery ne réplique pas la totalité des données vers la région source. Voici les conditions qui déterminent la quantité de données répliquées :

1.  Si la source de données de la machine virtuelle est supprimée, endommagée ou inaccessible pour une raison quelconque (modification/suppression du groupe de ressources par exemple), alors, pendant la reprotection, la fin du runtime d’intégration aura lieu puisqu’il n’existera aucune donnée disponible sur la région source à utiliser.
2.  Si les données de la machine virtuelle source sont disponibles, alors seules les sauvegardes différentielles sont calculées en comparant deux disques, puis transférées. Consultez le tableau ci-dessous pour obtenir la durée estimée 

|**Situation de l’exemple ** | **Durée de la reprotection  ** |
|--- | --- |
|La région source a une machine virtuelle avec un disque standard d’1 To<br/>- Seulement 127 Go de données sont utilisés ; le reste du disque est vide<br/>- Le disque est de type standard, avec un débit de 60 Mio/S<br/>- Aucune modification des données n’est effectuée après le basculement| Durée approximative de 45 minutes à 1,5 heure<br/> - Lors de la reprotection, Site Recovery remplira la somme de contrôle de données entières, ce qui utilisera 127 Go/45 Mo et prendra environ 45 minutes<br/>- Une surcharge de temps de 20 à 30 minutes est nécessaire pour que Site Recovery procède à la mise à l’échelle automatique<br/>- Aucun frais de sortie |
|La région source a une machine virtuelle avec un disque standard d’1 To<br/>- Seulement 127 Go de données sont utilisés ; le reste du disque est vide<br/>- Le disque est de type standard, avec un débit de 60 Mio/S<br/>- Modification de 45 Go de données après le basculement| Durée approximative 1 à 2 heures<br/>- Lors de la reprotection, Site Recovery remplira la somme de contrôle de données entières, ce qui utilisera 127 Go/45 Mo et prendra environ 45 minutes<br/>- Temps de transfert pour appliquer les modifications aux 45 Go de données : 45 Go/45 Mbit/s et environ 17 minutes<br/>- Les frais de sortie sont appliqués uniquement aux 45 Go de données, et non pas à la somme de contrôle|
 



## <a name="next-steps"></a>Étapes suivantes

Une fois la machine virtuelle protégée, vous pouvez commencer un basculement. Le basculement arrête la machine virtuelle dans la région secondaire et crée et démarre la machine virtuelle dans la région principale, avec un léger temps d’indisponibilité. Nous vous recommandons de choisir une période en conséquence et d’effectuer un test de basculement avant d’initialiser un basculement complet vers le site principal. [En savoir plus](site-recovery-failover.md) sur le basculement.
