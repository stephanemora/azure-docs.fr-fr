---
title: Concepts de réplication sans agent de machines virtuelles VMware
description: Décrit les concepts liés à la migration sans agent des machines virtuelles VMware dans Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 05/31/2021
ms.openlocfilehash: ed0560d85d267de90ff23d8aa66c1f628c90e3c6
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967439"
---
# <a name="azure-migrate-agentless-migration-of-vmware-virtual-machines"></a>Migration sans agent Azure Migrate de machines virtuelles VMware

Cet article décrit les concepts de réplication lors de la migration de machines virtuelles VMware à l’aide de la méthode de migration sans agent d’Azure Migrate : migration de serveur.

## <a name="replication-process"></a>Processus de réplication

L’option de réplication sans agent fonctionne en utilisant des instantanés VMware et la technologie Changed Block Tracking (CBT) de VMware pour répliquer les données des disques des machines virtuelles. Le diagramme de blocs suivant présente les différentes étapes de la migration de vos machines virtuelles à l’aide de l’outil Azure Migrate : migration de serveur.

 ![Étapes de migration.](./media/concepts-vmware-agentless-migration/migration-phases.png)

Lorsque la réplication est configurée pour une machine virtuelle, elle passe tout d’abord par une phase de réplication initiale. Pendant la réplication initiale, un instantané de machine virtuelle est pris et une copie complète des données des disques de l’instantané est répliquée sur les disques managés de votre abonnement cible.

Une fois la réplication initiale de la machine virtuelle terminée, le processus de réplication passe à une phase de réplication incrémentielle (réplication différentielle). Dans la phase de réplication incrémentielle, les modifications de données qui se sont produites depuis le début du dernier cycle de réplication terminé sont répliquées et écrites sur les disques managés du réplica, ce qui permet de synchroniser la réplication avec les modifications apportées à la machine virtuelle.

La technologie Changed Block Tracking (CBT) de VMware est utilisée pour effectuer le suivi des modifications entre les cycles de réplication. Au début du cycle de réplication, un instantané de machine virtuelle est pris et Changed Block Tracking est utilisée pour obtenir les modifications entre l’instantané actuel et le dernier instantané répliqué avec succès. Seules les données qui ont été modifiées depuis le cycle de réplication précédent sont répliquées pour maintenir la réplication de la machine virtuelle synchronisée. À la fin de chaque cycle de réplication, l’instantané est publié, et la consolidation de l’instantané est effectuée pour la machine virtuelle.

Lorsque vous effectuez l’opération de migration sur une machine virtuelle en réplication, il existe un cycle de réplication différentielle à la demande qui réplique les modifications restantes depuis le dernier cycle de réplication. Une fois le cycle à la demande terminé, les disques managés de réplica correspondant à la machine virtuelle sont utilisés pour créer la machine virtuelle dans Azure. Juste avant de déclencher la migration ou le basculement, vous devez arrêter la machine virtuelle locale. L’arrêt de la machine virtuelle garantit l’absence de perte de données pendant la migration.


Une fois la migration réussie et la machine virtuelle démarrée dans Azure, veillez à arrêter la réplication de la machine virtuelle. L’arrêt de la réplication entraîne la suppression des disques intermédiaires (disques d’amorçage) qui ont été créés lors de la réplication des données, et vous éviterez également d’encourir des frais supplémentaires associés aux transactions de stockage sur ces disques.

## <a name="replication-cycles"></a>Cycles de réplication

Les cycles de réplication font référence au processus périodique de transfert de données de l’environnement local vers des disques managés Azure. Un cycle de réplication complet comprend les étapes suivantes :

1. Créer un instantané VMware pour chaque disque associé à la machine virtuelle
2. Charger des données vers le compte de stockage des journaux dans Azure
3. Libérer l’instantané
4. Consolider les disques VMware

Un cycle est dit complet une fois les disques consolidés.

## <a name="components-involved-in-replication"></a>Composants impliqués dans la réplication

**Composants locaux :** L’appliance Azure Migrate possède les composants suivants qui sont responsables de la réplication.

- Agent DRA
- Agent de passerelle

**Composants Azure :** Le tableau suivant résume les différents artefacts Azure qui sont créés lors de l’utilisation de la méthode de migration sans agent des machines virtuelles VMware.

| Composant | Région | Abonnement | Description |
| --- | --- | --- | --- |
| Coffre Recovery Services | Région du projet Azure Migrate | Abonnement du projet Azure Migrate | Utilisé pour orchestrer la réplication des données |
| Service Bus | Région cible | Abonnement du projet Azure Migrate | Utilisé pour la communication entre le service cloud et l’appliance Azure Migrate |
| Compte de stockage de journaux | Région cible | Abonnement du projet Azure Migrate | Utilisé pour stocker les données de réplication, qui sont lues par le service et appliquées sur le disque managé du client |
| Compte de stockage de la passerelle | Région cible | Abonnement du projet Azure Migrate | Utilisé pour stocker les états des machines pendant la réplication |
| Coffre de clés | Région cible | Abonnement du projet Azure Migrate | Gère les clés de compte de stockage de journaux, stocke les chaînes de connexion Service Bus |
| Machine virtuelle Azure | Région cible | Abonnement cible | Machine virtuelle créée dans Azure lors de la migration |
| Disques managés Azure | Région cible | Abonnement cible | Disques managés attachés aux machines virtuelles Azure |
| Cartes d’interface réseau | Région cible | Abonnement cible | Cartes réseau attachées aux machines virtuelles créées dans Azure |

## <a name="permissions-required"></a>Autorisations requises

Lorsque vous démarrez la réplication pour la première fois, l’utilisateur connecté doit se voir attribuer les rôles suivants :

- Propriétaire ou Contributeur et Administrateur de l’accès utilisateur sur le groupe de ressources du projet Azure Migrate et le groupe de ressources cible

Pour les réplications suivantes, l’utilisateur connecté doit se voir attribuer les rôles suivants :

- Propriétaire ou Contributeur sur le groupe de ressources du projet Azure Migrate et le groupe de ressources cible

Outre les rôles décrits ci-dessus, l’utilisateur connecté doit disposer de l’autorisation suivante au niveau de l’abonnement : Microsoft.Resources/subscriptions/resourceGroups/read.


## <a name="data-integrity"></a>Intégrité des données

Chaque cycle de réplication comporte deux étapes qui garantissent l’intégrité des données entre le disque local (disque source) et le disque répliqué dans Azure (disque cible).

1. Tout d’abord, nous vérifions si chaque secteur modifié dans le disque source est répliqué sur le disque cible. La validation est effectuée à l’aide d’images bitmap.
Le disque source est divisé en secteurs de 512 octets. Chaque secteur du disque source est mappé à un bit de l’image bitmap. Lorsque la réplication des données commence, l’image bitmap est créée pour tous les blocs modifiés (dans le cycle différentiel) du disque source qui doit être répliqué. De même, lorsque les données sont transférées vers le disque Azure cible, une image bitmap est créée. Une fois le transfert de données terminé, le service cloud compare les deux images bitmap pour garantir qu’aucun bloc modifié n’a été oublié. En cas de discordance entre les images bitmap, le cycle est considéré comme ayant échoué. Dans la mesure où chaque cycle est une resynchronisation, la discordance sera corrigée lors du cycle suivant.

1. Ensuite, nous nous assurons que les données transférées vers les disques Azure sont les mêmes que celles qui ont été répliquées à partir des disques sources. Chaque bloc modifié qui est chargé est compressé et chiffré avant d’être écrit sous forme de blob dans le compte de stockage de journaux. Nous calculons la somme de contrôle de ce bloc avant la compression. Cette somme de contrôle est stockée sous forme de métadonnées avec les données compressées. Lors de la décompression, la somme de contrôle des données est calculée et comparée à celle calculée dans l’environnement source. En cas de discordance, les données ne sont pas écrites sur les disques Azure et le cycle est considéré comme ayant échoué. Dans la mesure où chaque cycle est une resynchronisation, la discordance sera corrigée lors du cycle suivant.

## <a name="security"></a>Sécurité

L’appliance Azure Migrate compresse les données et les chiffre avant le chargement. Les données sont transmises via un canal de communication sécurisé sur HTTPS et utilisent TLS 1.2 ou une version ultérieure. De plus, le Stockage Azure chiffre automatiquement vos données quand elles sont conservées dans le cloud (chiffrement au repos).



## <a name="migration-phase"></a>Phase de migration

Lorsqu’une machine virtuelle subit une réplication, quelques états sont possibles :

- **Réplication initiale (mise en file d’attente) :** La machine virtuelle est mise en file d’attente à des fins de réplication (ou de migration) lorsque d’autres machines virtuelles consomment les ressources locales pendant la réplication (ou la migration). Une fois les ressources libérées, cette machine virtuelle sera traitée.
- **Réplication initiale :** La machine virtuelle est en cours de réplication initiale. Lorsque la machine virtuelle subit une réplication initiale, vous ne pouvez pas procéder au test de migration ni à la migration. Vous pouvez uniquement arrêter la réplication à ce stade.
- **Test de migration en attente :** La machine virtuelle est en phase de réplication différentielle, et vous pouvez maintenant effectuer un test de migration (ou une migration).
- **Migration en cours (mise en file d’attente) :** La machine virtuelle est mise en file d’attente à des fins de migration lorsque d’autres machines virtuelles consomment les ressources locales pendant la réplication (ou la migration). Une fois les ressources libérées, la machine virtuelle sera traitée pour la migration.
- **Migration en cours :** La machine virtuelle est en train d’être migrée. Vous pouvez sélectionner le lien pour vérifier le travail de migration en cours. Ce travail se compose de cinq étapes : vérification de la configuration requise pour la migration, arrêt de la machine virtuelle (étape facultative), préparation à la migration, création d’une machine virtuelle Azure, démarrage de la machine virtuelle Azure.
- **Non applicable :** Lorsque la machine virtuelle a bien été migrée ou que vous avez arrêté la réplication, l’état devient « Non applicable ». Lorsque vous arrêtez la réplication et que l’opération se termine correctement, la machine virtuelle est supprimée de la liste des machines répliquées. La machine virtuelle se trouve dans l’onglet Machines virtuelles de l’Assistant de réplication.

> [!Note]
> Certaines machines virtuelles sont placées à l’état Mise en file d’attente pour garantir un impact minimal sur l’environnement source en raison de la consommation d’IOPS de stockage. Ces machines virtuelles sont traitées selon la logique de planification, comme décrit dans la section suivante.

## <a name="scheduling-logic"></a>Logique de planification

La réplication initiale est planifiée lorsque la réplication est configurée pour une machine virtuelle. Elle est suivie de réplications incrémentielles (réplications différentielles).

Les cycles de réplication différentielle sont planifiés comme suit :

- Le premier cycle de réplication différentielle est planifié immédiatement après la fin du cycle de réplication initiale.
- Les cycles de réplication différentielle suivants sont planifiés selon la logique suivante : max [(durée du cycle de réplication différentielle précédent/2), 1 heure].

Autrement dit, la réplication différentielle suivante ne sera pas planifiée avant une heure. Par exemple, si une machine virtuelle prend quatre heures pour un cycle de réplication différentielle, le prochain cycle de réplication différentielle est planifié deux heures après, et non dans l’heure qui suit.

> [!Note]
> La logique de planification est différente une fois la réplication initiale terminée. Le premier cycle différentiel est planifié immédiatement après la fin de la réplication initiale et les cycles suivants suivent la logique de planification décrite ci-dessus.


- Lorsque vous déclenchez une migration, un cycle de réplication différentielle à la demande (cycle de réplication différentielle avant basculement) est effectué pour la machine virtuelle avant la migration.

**Définition de la priorité des machines virtuelles pour les différentes étapes de réplication**

- Les réplications de machine virtuelle en cours sont prioritaires par rapport aux réplications planifiées (nouvelles réplications).
- Le cycle de prémigration (réplication différentielle à la demande) a la priorité la plus élevée, suivi du cycle de réplication initiale. Le cycle de réplication différentielle a la priorité la plus faible.

En d’autres termes, chaque fois qu’une opération de migration est déclenchée, le cycle de réplication à la demande de la machine virtuelle est planifié et les autres réplications en cours sont reléguées au second plan si elles sont en concurrence pour les ressources.

**Contraintes :**

Nous utilisons les contraintes suivantes pour nous assurer de ne pas dépasser les limites d’IOPS sur les réseaux SAN.

- Chaque appliance Azure Migrate prend en charge la réplication de 52 disques en parallèle.
- Chaque hôte ESXi prend en charge huit disques. Chaque hôte ESXi dispose d’une mémoire tampon NFC de 32 Mo. Nous pouvons donc planifier 8 disques sur l’hôte (chaque disque occupe 4 Mo de mémoire tampon pour la réplication initiale ou différentielle).
- Chaque magasin de données peut avoir un maximum de 15 instantanés de disque. La seule exception est lorsque plus de 15 disques sont attachés à une machine virtuelle.

## <a name="scale-out-replication"></a>Réplication avec Scale-out

Azure Migrate prend en charge la réplication simultanée de 500 machines virtuelles. Lorsque vous envisagez de répliquer plus de 300 machines virtuelles, vous devez déployer une appliance de Scale-out. L’appliance de Scale-out est similaire à une appliance primaire d’Azure Migrate, mais se compose uniquement d’un agent de passerelle pour faciliter le transfert de données vers Azure. Le diagramme suivant illustre la méthode recommandée pour utiliser l’appliance de Scale-out.

![Configuration avec Scale-out](./media/concepts-vmware-agentless-migration/scale-out-configuration.png)


Vous pouvez déployer l’appliance de Scale-out à tout moment après avoir configuré l’appliance primaire et que 300 machines virtuelles aient été répliquées simultanément. Lorsque 300 machines virtuelles sont répliquées simultanément, vous devez déployer l’appliance de Scale-out pour continuer.

## <a name="stop-replication"></a>Arrêter la réplication

Lorsque vous arrêtez la réplication, les disques managés intermédiaires (disques d’amorçage) créés lors de la réplication sont supprimés. La machine virtuelle pour laquelle la réplication est arrêtée peut être répliquée et migrée à nouveau en suivant la procédure habituelle.

Vous pouvez arrêter la réplication à deux moments :

-  Lorsque la réplication est en cours
-  Lorsque la migration d’une machine virtuelle est terminée

En guise de meilleure pratique, vous devriez toujours arrêter la réplication une fois que la machine virtuelle a été migrée vers Azure afin de vous assurer de ne pas encourir de frais supplémentaires pour les transactions de stockage sur les disques managés intermédiaires (disques d’amorçage).

Dans certains cas, vous remarquerez que l’arrêt de la réplication prend un certain temps. Cela est dû au fait que chaque fois que vous arrêtez la réplication, le cycle de réplication en cours est terminé (uniquement lorsque la machine virtuelle est en synchronisation différentielle) avant la suppression des artefacts.

## <a name="impact-of-churn"></a>Impact de l’attrition

Avant de planifier le cycle suivant, nous essayons de réduire au minimum la quantité de transfert de données dans chaque cycle de réplication en permettant aux données d’être pliées autant que possible. La réplication sans agent opérant un repli des données, le _modèle de variation_ est plus important que le _taux de variation_. Quand un fichier est écrit à plusieurs reprises, le taux n’a pas un impact important. Toutefois, un modèle dans lequel un secteur sur deux est écrit entraîne une variation élevée lors du cycle suivant.

## <a name="management-of-replication"></a>Gestion de la réplication

### <a name="throttling"></a>Limitation

Vous pouvez augmenter ou diminuer la bande passante de réplication à l’aide de _NetQosPolicy_. Le préfixe _AppNamePrefix_ à utiliser dans _NetQosPolicy_ est « GatewayWindowsService.exe ». 

Vous pouvez créer une stratégie sur l’appliance Azure Migrate pour limiter le trafic de réplication de l’appliance en créant une stratégie telle que celle-ci :

```New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB```

> [!NOTE]
> Cela s’applique à toutes les machines virtuelles répliquées simultanément à partir de l’appliance Azure Migrate.

Vous pouvez également augmenter et diminuer la bande passante de réplication selon une planification à l’aide de l’[exemple de script](./common-questions-server-migration.md).

### <a name="blackout-window"></a>Fenêtre d’indisponibilité

Azure Migrate fournit un mécanisme basé sur la configuration par le biais duquel les clients peuvent spécifier l’intervalle de temps pendant lequel ils ne souhaitent pas que les réplications se poursuivent. Cet intervalle de temps s’appelle la fenêtre d’indisponibilité. La nécessité d’une fenêtre d’indisponibilité peut survenir dans plusieurs scénarios, par exemple lorsque l’environnement source est soumis à des contraintes de ressources, lorsque les clients veulent que la réplication se déroule uniquement en dehors des heures de bureau, etc.

> [!NOTE]
> Les cycles de réplication existants au début de la fenêtre d’indisponibilité se terminent avant que la réplication soit interrompue.

Une fenêtre d’indisponibilité peut être spécifiée pour l’appliance en créant ou en mettant à jour le fichier GatewayDataWorker.json dans C:\ProgramData\Microsoft Azure\Config. Un fichier standard se présente sous la forme suivante :

```
{
    
    "BlackoutWindows": "List of blackout windows"
    
}
```

    
La liste des fenêtres d’indisponibilité est une chaîne délimitée par le caractère « | » au format « DayOfWeek;StartTime;Duration ». La durée peut être spécifiée en jours, heures et minutes. Par exemple, les fenêtres d’indisponibilité peuvent être spécifiées comme suit :

```
{
    
    "BlackoutWindows": "Monday;7:00;7h | Tuesday;8:00;1d7h | Wednesday;16:00;1d | Thursday;18:00;5h | Friday;13:00;8m"
    
}
```
    
La première valeur de l’exemple ci-dessus indique une fenêtre d’indisponibilité commençant tous les lundis à 7 h, heure locale (heure sur l’appliance) et durant 7 heures.

Une fois que le fichier GatewayDataWorker.json est créé ou mis à jour avec ces contenus, le service de passerelle sur l’appliance doit être redémarré pour que ces modifications prennent effet.

Dans le scénario de Scale-out, l’appliance primaire et l’appliance de Scale-out respectent les fenêtres d’indisponibilité de manière indépendante. À titre de meilleure pratique, nous vous recommandons de garder les fenêtres cohérentes entre les différents appareils.

## <a name="next-steps"></a>Étapes suivantes
[Migrez des machines virtuelles VMware](tutorial-migrate-vmware.md) avec une migration sans agent.