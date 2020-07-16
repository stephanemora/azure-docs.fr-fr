---
title: Effectuer une récupération d’urgence de machines virtuelles
description: Cet article explique comment effectuer une récupération d’urgence des machines virtuelles à l’aide d’AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5ccaa009c8e3e059597636a8bb78cc3bd255fe68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749952"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Effectuer une récupération d'urgence de machines virtuelles à l'aide de la solution Azure VMware

Cet article décrit le processus de récupération d'urgence de vos machines virtuelles avec la solution VMware HCX (Hybrid Cloud Extension) et en utilisant un cloud privé de la solution Azure VMware en tant que site de récupération ou cible.

La solution VMware HCX exécute diverses opérations qui permettent de contrôler avec souplesse et précision les stratégies de réplication. Les opérations disponibles sont les suivantes :

- Inverser : après un incident. Une inversion permet de faire d’un site B le site source, et d’un site A l’emplacement de résidence de la machine virtuelle protégée.

- Suspendre : suspend la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- Reprendre : suspend la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- Supprimer : supprime la stratégie de réplication actuelle associée à la machine virtuelle sélectionnée.

- Synchroniser maintenant : de la machine virtuelle source de synchronisation hors limites vers la machine virtuelle protégée.

Ce guide présente les scénarios de réplication suivants :

- Protéger une machine virtuelle ou un groupe de machines virtuelles.

- Effectuer un test de récupération d’une machine virtuelle ou d’un groupe de machines virtuelles.

- Récupérer une machine virtuelle ou un groupe de machines virtuelles.

- Inverser la protection d’une machine virtuelle ou d’un groupe de machines virtuelles.

## <a name="protect-virtual-machines"></a>Protéger des machines virtuelles

Connectez-vous au **Client vSphere** sur le site source, puis accédez au **plug-in HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Option HCX dans vSphere" border="true":::

Accédez à la zone **RÉCUPÉRATION D’URGENCE**, puis cliquez sur **PROTÉGER LES MACHINES VIRTUELLES**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Sélectionner Protéger les machines virtuelles" border="true":::

Dans la fenêtre qui s’ouvre, sélectionnez la Source et le Site distant. Dans ce cas, ce doit être le cloud privé AVS.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="Fenêtre Protéger les machines virtuelles" border="true":::

Si nécessaire, sélectionnez les options de réplication par défaut :

- **Activer la compression :** option recommandée pour les scénarios à faible débit.

- **Enable l’arrêt inactif :** suspend l’activité de la machine virtuelle pour s’assurer de la cohérence de la copie synchronisée avec le site distant.

- **Stockage de destination :** sélectionnez le magasin de données distant pour les machines virtuelles protégées. Dans un cloud privé AVS, cette sélection doit être le magasin de données vSAN.

- **Conteneur de calcul :** cluster vSphere ou pool de ressources distants.

- **Dossier de destination :** dossier de destination distant. Ce paramètre est facultatif et, à défaut de dossier sélectionné, les machines virtuelles sont synchronisées directement dans le cluster sélectionné.

- **RPO :** cette valeur correspond à l’intervalle de synchronisation entre la machine virtuelle source et la machine virtuelle protégée. Elle peut être comprise entre 5 minutes et 24 heures.

- **Intervalle des captures instantanées :** intervalle entre les captures instantanées.

- **Nombre de captures instantanées :** nombre total de captures instantanées au cours de l’intervalle configuré.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="Options de protection des machines virtuelles" border="true":::

Sélectionnez une ou plusieurs machines virtuelles dans la liste, puis configurez les options de réplication de machine virtuelle nécessaires.

Par défaut, les machines virtuelles héritent de la stratégie de paramètres globaux configurée dans les options de réplication par défaut. Pour chaque interface réseau de la machine virtuelle sélectionnée, configurez le **Groupe de ports réseau** distant, puis **Terminer** pour démarrer le processus de protection.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="Options d’interface réseau" border="true":::

Comme indiqué dans l’image suivante, vous pouvez surveiller le processus pour chacune des machines virtuelles sélectionnées dans la même zone de récupération d’urgence.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="Surveiller la progression de la protection" border="true":::

Une fois la machine virtuelle protégée, les différents instantanés sont visibles sous l’onglet **Instantanés**.

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="Liste d’instantanés" border="true":::

Le triangle jaune signifie que les instantanés et la machine virtuelle n’ont pas été testés dans le cadre d’une opération de test de récupération.

Il existe des différences clés entre une machine virtuelle hors tension et une machine virtuelle sous tension.
La capture d’écran ci-dessus montre le processus de synchronisation d’une machine virtuelle sous tension. Celui-ci entame la synchronisation jusqu’à la prise de la première capture instantanée, qui est une copie complète de la machine virtuelle, puis prend les captures suivantes dans l’intervalle configuré.

Pour une machine virtuelle hors tension, le processus synchronise une copie, puis la machine virtuelle apparaît inactive et l’opération de protection s’affiche comme terminée.

Lorsque la machine virtuelle est sous tension, le processus de synchronisation démarre sur le site distant.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Effectuer un test de récupération de machines virtuelles

Connectez-vous au **Client vSphere** sur le site distant qui est le cloud privé AVS. Dans le **plug-in HCX**, dans la zone Récupération d’urgence, sélectionnez l’ellipse verticale sur toute machine virtuelle pour afficher le menu Opérations. Sélectionnez **Tester la récupération de la machine virtuelle**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Sélectionner Tester la récupération de la machine virtuelle" border="true":::

Dans la nouvelle fenêtre, sélectionnez les options pour le test. Sélectionnez l’instantané à utiliser pour tester les différents états de la machine virtuelle.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Choisir un instantané, puis cliquer sur Tester" border="true":::

Après que vous avez cliqué sur **Tester**, l’opération de récupération commence.

Une fois l’opération de test de récupération terminée, vous pouvez vérifier la nouvelle machine virtuelle dans le vCenter du cloud privé AVS.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="Vérifier l’opération de récupération" border="true":::

Enfin, une fois les tests effectués sur la machine virtuelle ou toute application s’exécutant sur celle-ci, effectuez un nettoyage pour supprimer l’instance de test.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="Nettoyer l’instance de test" border="true":::

## <a name="recover-virtual-machines"></a>Récupérer les machines virtuelles

Connectez-vous au **Client vSphere** sur le site distant qui est le cloud privé AVS, puis accédez au **plug-in HCX**.

Pour le scénario de récupération, un groupe de machines virtuelles est utilisé pour cet exemple.

Sélectionnez la machine virtuelle à récupérer dans la liste, ouvrez le menu **ACTIONS**, puis sélectionnez **Récupérer des machines virtuelles**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="Récupérer les machines virtuelles" border="true":::

Configurez les options de récupération pour chaque instance, puis cliquez sur **Récupérer** pour démarrer l’opération de récupération.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="Confirmation de la récupération des machines virtuelles" border="true":::

Une fois l’opération de récupération terminée, les nouvelles machines virtuelles apparaissent dans l’inventaire du vCenter Server distant.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Effectuer une réplication inverse sur des machines virtuelles

Connectez-vous au **Client vSphere** sur votre cloud privé AVS, puis accédez au **plug-in HCX**.
Avant le démarrage de la réplication inverse, les machines virtuelles d’origine sur le site source doivent impérativement être hors tension. L’opération échoue si les machines virtuelles ne sont pas hors tension.

Sélectionnez dans la liste les machines virtuelles à restaurer par réplication vers le site source, ouvrez le menu **ACTIONS**, puis sélectionnez **Inverser**. Dans la fenêtre contextuelle, cliquez sur **Inverser** pour démarrer la réplication.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Sélectionner l’action Inverser dans les opérations de protection" border="true":::

Vous pouvez surveiller la réplication dans la section Détails de chaque machine virtuelle.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="Examiner les résultats de l’action Inverser" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatisation de plan de récupération d’urgence

Actuellement, la solution VMware HCX ne dispose d'aucun mécanisme intégré pour créer et automatiser un plan de récupération d'urgence. Cette fonctionnalité n’existe pas dans HCX. Toutefois, HCX fournit un ensemble d’API REST, dont certaines pour exécuter l’opération Récupération d’urgence.

La spécification de l’API est accessible dans HCX Manager via l’URL.

Ces API couvrent les opérations de Récupération d’urgence suivantes.

- Protéger

- Recover

- Tester la récupération

- Récupération planifiée

- Inverser

- Requête

- Tester le nettoyage

- Suspendre

- Reprendre

- Supprimer la protection

- Reconfigurer

Vous trouverez ci-dessous un exemple de charge utile d’opération de récupération dans un fichier JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Ces API permettent à un client de créer un mécanisme personnalisé pour automatiser la création et l’exécution d’un plan de récupération d’urgence.
