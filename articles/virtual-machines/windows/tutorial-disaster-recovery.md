---
title: Tutoriel - Configurer la reprise d’activité pour les machines virtuelles Windows avec Azure Site Recovery
description: Découvrez comment activer la reprise d’activité après sinistre pour les machines virtuelles Windows dans une autre région Azure avec le service Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e76245c9ad08a9a826e1d0431c2dd01b61a6b860
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077569"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Tutoriel : Activer la reprise d’activité pour les machines virtuelles Windows

Ce tutoriel explique comment configurer la reprise d’activité après sinistre pour les machines virtuelles Azure qui exécutent Windows. Dans cet article, découvrez comment :

> [!div class="checklist"]
> * Activer la reprise d’activité après sinistre pour une machine virtuelle Windows
> * Procéder à une simulation de récupération d'urgence pour vérifier qu'elle fonctionne comme prévu
> * Arrêter la réplication de la machine virtuelle après le test

Quand vous activez la réplication pour une machine virtuelle, l’extension de service Mobilité Site Recovery est installée sur la machine virtuelle, puis elle est inscrite auprès d’[Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Pendant la réplication, les écritures réalisées sur les disques de machine virtuelle sont envoyées à un compte de stockage de cache de la région source. Les données sont envoyées de cet emplacement vers la région cible, et des points de récupération sont générés à partir des données.  Lorsque vous basculez une machine virtuelle pendant une récupération d'urgence, un point de récupération est utilisé pour créer une machine virtuelle dans la région cible.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible. Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de cet abonnement et vous disposez des autorisations nécessaires.
2. Si vous n’êtes pas l’administrateur, demandez à celui-ci de vous affecter :
    - Soit le rôle intégré Contributeur de machines virtuelles, soit des autorisations permettant d’effectuer les opérations suivantes :
        - Créer une machine virtuelle dans le réseau virtuel sélectionné
        - Écrire sur un compte de stockage Azure.
        - Écrire des données sur un disque managé Azure.
    - Le rôle intégré Contributeur Site Recovery pour gérer les opérations Site Recovery dans le coffre.
3. Nous vous recommandons d’utiliser une machine virtuelle Windows exécutant Windows Server 2012 ou une version ultérieure. Pour ce tutoriel, le disque de machine virtuelle ne doit pas être chiffré.
4. Si les connexions sortantes de la machine virtuelle utilisent un proxy basé sur une URL, vérifiez que celui-ci peut accéder à ces URL. L’utilisation d’un proxy authentifié n’est pas prise en charge.

    **Nom** | **Cloud public** | **Cloud du secteur public** | **Détails**
    --- | --- | --- | ---
    Stockage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Écrit des données entre la machine virtuelle et le compte de stockage de cache situé dans la région source.
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autorise et authentifie les URL du service Site Recovery.
    Réplication | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Communication entre la machine virtuelle et le service Site Recovery.
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | La machine virtuelle écrit des données de supervision et de diagnostic concernant Site Recovery.

4. Si vous utilisez des groupes de sécurité réseau pour limiter le trafic réseau des machines virtuelles, créez des règles de groupe de sécurité réseau qui autorisent la connectivité sortante (HTTPS 443) pour la machine virtuelle à l’aide de ces étiquettes de service (groupes d’adresses IP). Essayez d’abord ces règles sur un groupe de sécurité réseau de test.

    **Tag** | **Autoriser**
    --- | ---
    Étiquette du stockage | Permet d’écrire des données entre la machine virtuelle et le compte de stockage de cache.
    Étiquette Azure AD | Autorise l’accès à toutes les adresses IP qui correspondent à Azure AD.
    Étiquette EventsHub | Permet d’accéder à la supervision de Site Recovery.
    Étiquette AzureSiteRecovery | Permet d’accéder au service Site Recovery dans n’importe quelle région.
    GuestAndHybridManagement | Utilisez-la si vous souhaitez mettre automatiquement à niveau l’agent Mobilité Site Recovery qui est exécuté sur les machines virtuelles où est activée la réplication.
5.  Sur les machines virtuelles Windows, installez les dernières mises à jour Windows pour garantir que les machines virtuelles disposent des certificats racines les plus récents.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Créer une machine virtuelle et activer la récupération d'urgence

Lorsque vous créez une machine virtuelle, vous avez la possibilité d'activer la récupération d'urgence.

1. [Création d’une machine virtuelle](quick-create-portal.md) :
2. Sous l'onglet **Gestion**, sélectionnez **Activer la récupération d'urgence**.
3. Dans **Région secondaire**, sélectionnez la région cible vers laquelle vous souhaitez répliquer une machine virtuelle pour la récupération d'urgence.
4. Dans **Abonnement secondaire**, sélectionnez l'abonnement cible dans lequel la machine virtuelle cible sera créée. La machine virtuelle cible est créée lors du basculement de la machine virtuelle source de la région source vers la région cible.
5. Dans **Coffre Recovery Services**, sélectionnez le coffre que vous souhaitez utiliser pour la réplication. Si vous n’avez pas de coffre, cliquez sur **Créer**. Sélectionnez le groupe de ressources dans lequel vous souhaitez placer le coffre, ainsi qu'un nom de coffre.
6. Dans **Stratégie Site Recovery**, conservez la stratégie par défaut, ou sélectionnez **Créer** pour définir des valeurs personnalisées.

    - Les points de récupération sont créés à partir de captures instantanées des disques des machines virtuelles qui sont prises à un moment précis dans le temps. Lorsque vous basculez une machine virtuelle, vous utilisez un point de récupération pour la restaurer dans la région cible.
    - Un point de récupération de cohérence en cas d'incident est créé toutes les cinq minutes. Ce paramètre ne peut pas être modifié. Un instantané de cohérence en cas d'incident capture les données qui se trouvaient sur le disque lorsque l'instantané a été pris. Il n’ajoute aucune donnée en mémoire.
    - Par défaut, Site Recovery conserve les points de récupération de cohérence en cas d'incident pendant 24 heures. Vous pouvez définir une valeur personnalisée comprise entre 0 et 72 heures.
    - Un instantané de cohérence des applications est pris toutes les 4 heures. Un instantané de cohérence des applications
    - Par défaut, Site Recovery conserve les points de récupération pendant 24 heures.

7. Dans **Options de disponibilité**, spécifiez si la machine virtuelle est déployée de manière autonome, dans une zone de disponibilité ou dans un groupe à haute disponibilité.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="Activez la réplication sur la page des propriétés de gestion de la machine virtuelle."

8. Finalisez la création de la machine virtuelle.

>[!NOTE]
> Lorsque vous activez la réplication lors de la création d’une machine virtuelle Windows, seul le disque du système d’exploitation est répliqué. Vous devez initialiser les disques de données, après quoi Azure Site Recovery les réplique automatiquement.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Activer la récupération d'urgence sur une machine virtuelle Linux existante

Si vous souhaitez activer la récupération d'urgence sur une machine virtuelle existante plutôt que sur une nouvelle machine virtuelle, utilisez cette procédure.

1. Dans le portail Azure, ouvrez la page des propriétés de la machine virtuelle.
2. Dans **Opérations**, sélectionnez **Récupération d’urgence**.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Ouvrir les options de récupération d'urgence d'une machine virtuelle existante.":::

3. Dans **De base**, si la machine virtuelle est déployée dans une zone de disponibilité, vous pouvez sélectionner la récupération d'urgence entre les zones de disponibilité.
4. Dans **Région cible**, sélectionnez la région vers laquelle vous souhaitez répliquer la machine virtuelle. Les régions source et cible doivent se trouver dans le même locataire Azure Active Directory.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Définir les options de récupération d'urgence de base d'une machine virtuelle.":::

5. Sélectionnez **Suivant : Paramètres avancés**.
6. Dans **Paramètres avancés**, vous pouvez passer les paramètres en revue et modifier les valeurs des paramètres personnalisés. Par défaut, Site Recovery copie les paramètres source pour créer les ressources cibles.

    - **Abonnement cible**. Abonnement dans lequel la machine virtuelle cible est créée après le basculement.
    - **Groupe de ressources de la machine virtuelle cible**. Groupe de ressources dans lequel la machine virtuelle cible est créée après le basculement.
    - **Réseau virtuel cible**. Réseau virtuel Azure dans lequel se trouve la machine virtuelle cible lorsqu'elle est créée après le basculement.
    - **Disponibilité de la cible**. Lorsque la machine virtuelle cible est créée en tant qu'instance autonome, dans un groupe à haute disponibilité ou dans une zone de disponibilité.
    - **Placement de proximité**. Le cas échéant, sélectionnez le groupe de placement de proximité dans lequel se trouve la machine virtuelle cible après le basculement.
    - **Paramètres de stockage - Compte de stockage de cache**. La récupération utilise un compte de stockage situé dans la région source comme magasin de données temporaire. Les modifications des machines virtuelles sources sont mises en cache dans ce compte avant d’être répliquées vers l’emplacement cible.
        - Par défaut, un compte de stockage de cache est créé pour chaque coffre et réutilisé.
        - Vous pouvez sélectionner un autre compte de stockage si vous souhaitez personnaliser le compte de cache de la machine virtuelle.
    - **Paramètres de stockage - Disque de réplica managé**. Par défaut, Site Recovery crée des disques de réplica managés dans la région cible.
        -  Par défaut, le disque managé cible reflète les disques managés de la machine virtuelle source, en utilisant le même type de stockage (HDD/SSD standard ou SSD Premium).
        - Vous pouvez personnaliser le type de stockage conformément à vos besoins.
    - **Paramètres de réplication**. Affiche le coffre dans lequel se trouvent la machine virtuelle et la stratégie de réplication utilisée pour celle-ci. Par défaut, les points de récupération créés par Site Recovery pour la machine virtuelle sont conservés pendant 24 heures.
    - **Paramètres d’extension**. Indique que Site Recovery gère les mises à jour de l’extension de service Mobilité Site Recovery qui est installée sur les machines virtuelles que vous répliquez.
        - Le compte Azure Automation indiqué gère le processus de mise à jour.
        - Vous pouvez personnaliser le compte Automation.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Page montrant un résumé des paramètres de cible et de réplication.":::


6. Sélectionnez **Réviser + lancer la réplication**.

7. Sélectionnez **Démarrer la réplication**. Le déploiement démarre, et Site Recovery commence à créer des ressources cibles. Vous pouvez superviser la progression de la réplication dans les notifications.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Notification concernant la progression de la réplication.":::

## <a name="check-vm-status"></a>Vérifier l’état de la machine virtuelle

Une fois la réplication terminée, vous pouvez vérifier l’état de réplication de la machine virtuelle.

1. Ouvrez la page des propriétés de la machine virtuelle.
2. Dans **Opérations**, sélectionnez **Récupération d’urgence**.
3. Développez la section **Essentials** pour passer en revue les paramètres par défaut concernant le coffre, la stratégie de réplication et la cible.
4. Dans **Intégrité et état**, obtenez des informations sur l’état de réplication de la machine virtuelle, la version de l’agent, la préparation au basculement et les points de récupération les plus récents.

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Vue Essentials pour la reprise d’activité après sinistre de la machine virtuelle.":::

5. Dans **Affichage de l’infrastructure**, obtenez une vue d’ensemble des machines virtuelles sources et cibles, des disques managés et du compte de stockage de cache.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="Carte visuelle de l’infrastructure concernant la reprise d’activité après sinistre de la machine virtuelle.":::


## <a name="run-a-drill"></a>Effectuer un test

Procédez à un test pour vérifier que la reprise d’activité après sinistre fonctionne comme prévu. Lorsque vous effectuez un basculement de test, cela crée une copie de la machine virtuelle dans Azure sans impacter la réplication en cours, ou cela crée une copie dans votre environnement de production.

1. Dans la page de reprise d’activité après sinistre de la machine virtuelle, sélectionnez **Test de basculement**.
2. Dans **Test de basculement**, conservez le paramètre par défaut **Dernier point traité (objectif de délai de récupération faible)** pour le point de récupération.

   Cette option fournit l’objectif de point de récupération (RPO) le plus faible et constitue généralement l’option la plus rapide pour créer la machine virtuelle cible. Il traite tout d’abord toutes les données qui ont été envoyées au service Site Recovery, afin de créer un point de récupération pour chaque machine virtuelle avant de basculer vers celle-ci. Toutes les données sont répliquées sur ce point de récupération sur Site Recovery lorsque le basculement a été déclenché.

3. Sélectionnez le réseau virtuel dans lequel la machine virtuelle sera située après le basculement.

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Page dans laquelle définir les options de test de basculement":::

4. Le test de basculement commence. Vous pouvez superviser la progression dans les notifications.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Notifications concernant le test de basculement.":::

   Une fois le test de basculement terminé, la machine virtuelle indique l’état *Nettoyage du basculement de test en attente* dans la page **Essentials**.



## <a name="clean-up-resources"></a>Nettoyer les ressources

La machine virtuelle est automatiquement nettoyée par Site Recovery après le test.

1. Pour démarrer le nettoyage automatique, sélectionnez **Nettoyer le test de basculement**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Démarrage du nettoyage dans la page Essentials.":::

2. Dans **Nettoyer le test de basculement**, tapez les notes que vous souhaitez enregistrer pour le basculement, puis sélectionnez **Le test est terminé. Supprimez la ou les machines virtuelles du test du basculement**. Sélectionnez ensuite **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Page où enregistrer les notes et supprimer la machine virtuelle de test.":::

7. Le processus de suppression démarre. Vous pouvez superviser la progression dans les notifications.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Notifications pour superviser la suppression de la machine virtuelle de test.":::

### <a name="stop-replicating-the-vm"></a>Arrêter la réplication de la machine virtuelle

Une fois le test de la reprise d’activité après sinistre terminé, nous vous suggérons d’effectuer un basculement complet. Si vous ne souhaitez pas effectuer un basculement complet, vous pouvez désactiver la réplication. Cette opération effectue les actions suivantes :

- Elle supprime la machine virtuelle de la liste Site Recovery des machines répliquées.
- Elle arrête la facturation Site Recovery pour la machine virtuelle.
- Elle nettoie automatiquement les paramètres de réplication source.

Arrêtez la réplication comme suit :

1. Dans la page de reprise d’activité après sinistre de la machine virtuelle, sélectionnez **Désactiver la réplication**.
2. Dans **Désactiver la réplication**, sélectionnez les raisons pour lesquelles vous souhaitez désactiver la réplication. Sélectionnez ensuite **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Page où désactiver la réplication et indiquer la raison de la désactivation.":::


L’extension Site Recovery installée sur la machine virtuelle pendant la réplication n’est pas supprimée automatiquement. Si vous désactivez la réplication pour la machine virtuelle et ne souhaitez pas la réactiver, vous pouvez supprimer manuellement l’extension Site Recovery de la façon suivante :

1. Accédez à la machine virtuelle > **Paramètres** > **Extensions**.
2. Dans la page **Extensions**, sélectionnez chacune des entrées *Microsoft.Azure.RecoveryServices* pour Linux.
3. Dans la page des propriétés de l’extension, sélectionnez **Désinstaller**.

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Page où désinstaller l’extension de la machine virtuelle Site Recovery.":::



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré la reprise d’activité après sinistre pour une machine virtuelle Azure, puis vous avez effectué un test de reprise d’activité. À présent, vous pouvez effectuer le basculement complet de la machine virtuelle.

> [!div class="nextstepaction"]
> [Basculer une machine virtuelle vers une autre région](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
