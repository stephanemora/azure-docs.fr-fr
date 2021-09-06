---
title: Sauvegarder des machines virtuelles Azure Stack HCI avec MABS
description: Cet article contient les procédures afin de sauvegarder et de récupérer des machines virtuelles à l’aide du serveur de sauvegarde Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: e7a653f008541db548b4468a70c429fe72701797
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114722602"
---
# <a name="back-up-azure-stack-hci-virtual-machines-with-azure-backup-server"></a>Sauvegarder des machines virtuelles Azure Stack HCI avec le serveur de sauvegarde Azure

Cet article explique comment sauvegarder des machines virtuelles sur Azure Stack HCI à l’aide du serveur de sauvegarde Microsoft Azure (MABS).

## <a name="supported-scenarios"></a>Scénarios pris en charge

MABS peut sauvegarder les machines virtuelles Azure Stack HCI dans les scénarios suivants :

- **Hôte Azure Stack HCI** : sauvegardez et récupérez l’état du système ou la récupération complète de l’hôte Azure Stack HCI. L’agent de protection du serveur MABS doit être installé sur l’hôte.

- **Machines virtuelles dans un cluster avec stockage local ou direct** : sauvegardez des machines virtuelles invitées dans un cluster disposant d’un stockage local ou directement attaché. Par exemple : un disque dur, un appareil de réseau SAN (réseau de zone de stockage) ou un dispositif de stockage NAS (stockage attaché au réseau).

- **Machines virtuelles dans un cluster avec stockage CSV** : sauvegardez des machines virtuelles invitées hébergées sur un cluster Azure Stack HCI avec un stockage sur un volume partagé de cluster (CSV). L’agent de protection du serveur MABS est installé sur chaque nœud du cluster.

- **Déplacement d’une machine virtuelle dans un cluster** : lorsque les machines virtuelles sont déplacées au sein d’un cluster étendu/normal, MABS continue de protéger les ordinateurs virtuels tant que l’agent de protection MABS est installé sur l’hôte Azure Stack HCI. La façon dont MABS protège les machines virtuelles dépend du type de migration dynamique impliquée. À l’aide d’un déplacement de machine virtuelle au sein d’un cluster, MABS détecte la migration et sauvegarde la machine virtuelle du nouveau nœud de cluster sans nécessiter l’intervention de l’utilisateur. Étant donné que l’emplacement de stockage n’a pas changé, MABS poursuit les sauvegardes complètes rapides. 

- **Déplacement de machines virtuelles vers un autre cluster étendu/normal** : le déplacement de machines virtuelles vers un autre cluster étendu/normal n’est pas pris en charge.

## <a name="host-versus-guest-backup"></a>Sauvegarde des hôtes et des invités

MABS peut effectuer une sauvegarde de machines virtuelles au niveau de l’hôte ou de l’invité sur Azure Stack HCI. Au niveau de l’hôte, l’agent de protection du serveur MABS est installé sur le cluster ou le serveur hôte Azure Stack HCI. Il protège l’ensemble des machines virtuelles et les fichiers de données en cours d’exécution sur cet hôte.   Au niveau de l’invité, l’agent est installé sur chaque machine virtuelle. Il protège la charge de travail présente sur cette machine.

Ces deux méthodes présentent des avantages et des inconvénients :

- Les sauvegardes au niveau de l’hôte sont flexibles, car elles fonctionnent indépendamment du type de système d’exploitation exécuté sur les ordinateurs invités et ne nécessitent pas l’installation de l’agent de protection MABS sur chaque machine virtuelle. Si vous déployez une sauvegarde au niveau de l’hôte, vous récupérez un ordinateur virtuel dans son ensemble, ou encore des fichiers et dossiers (récupération au niveau de l’élément).

- La sauvegarde au niveau de l’invité se révèle utile pour protéger des charges de travail spécifiques en cours d’exécution sur une machine virtuelle. Au niveau de l’hôte, vous pouvez récupérer une machine virtuelle dans son ensemble ou des fichiers spécifiques. Toutefois, cette récupération n’est pas assurée dans le contexte d’une application spécifique. Par exemple, pour récupérer des éléments SharePoint spécifiques à partir d’une machine virtuelle sauvegardée, vous devez effectuer une sauvegarde de cette machine virtuelle au niveau de l’invité. Utilisez la sauvegarde au niveau de l’invité pour protéger les données stockées sur des disques directs. Le transfert direct permet à la machine virtuelle d’accéder directement au dispositif de stockage. Il ne stocke pas les données de volume virtuel dans un fichier de disque dur virtuel.

## <a name="backup-prerequisites"></a>Prérequis pour la sauvegarde

Voici les prérequis pour la sauvegarde des machines virtuelles avec MABS :

| Configuration requise | Détails |
| ------------ | ------- |
| Prérequis pour MABS | <ul> <li>Si vous souhaitez effectuer une récupération au niveau de l’élément pour les machines virtuelles (récupérer des fichiers, des dossiers, des volumes), vous devez installer le rôle Hyper-V sur le serveur MABS. Si vous souhaitez uniquement récupérer la machine virtuelle et non effectuer une récupération au niveau de l’élément, le rôle n’est pas obligatoire.</li> <li>Vous pouvez protéger jusqu’à 800 machines virtuelles de 100 Go chacune sur un serveur MABS et autoriser plusieurs serveurs MABS prenant en charge de plus grands clusters.</li> <li>MABS exclut le fichier d’échange des sauvegardes incrémentielles pour améliorer les performances de sauvegarde de la machine virtuelle.</li> <li>MABS peut sauvegarder un cluster ou un serveur dans le même domaine que le serveur MABS, ou dans un domaine enfant ou approuvé. Si vous souhaitez sauvegarder des machines virtuelles dans un groupe de travail ou un domaine non approuvé, vous devez configurer l’authentification. Pour un seul serveur, vous pouvez utiliser l’authentification NTLM ou par certificat. Pour un cluster, vous pouvez utiliser l’authentification par certificat uniquement.</li> <li>L’utilisation de la sauvegarde au niveau de l’hôte pour sauvegarder des données de machine virtuelle sur des disques directs n’est pas prise en charge. Dans ce scénario, nous vous recommandons d’utiliser une sauvegarde au niveau de l’hôte pour sauvegarder des fichiers VHD et une sauvegarde au niveau de l’invité pour sauvegarder les autres données qui ne sont pas visibles sur l’hôte.</li> <li>Vous pouvez sauvegarder des machines virtuelles stockées sur des volumes dédupliqués.</li> </ul> |
| Machine virtuelle | <ul> <li> La version des composants d’intégration qui s’exécute sur la machine virtuelle doit être la même que la version de l’hôte Azure Stack HCI. </li> <li> Pour chaque sauvegarde de machine virtuelle, vous aurez besoin d’espace libre sur le volume hébergeant les fichiers de disque dur virtuel afin d’offrir un espace suffisant pour les disques de différenciation (AVHD) lors de la sauvegarde. L’espace doit être au moins égal au temps de la fenêtre de calcul Taille initiale du disque *Taux de variation* Sauvegarde. Si vous exécutez plusieurs sauvegardes sur un cluster, vous aurez besoin d’une capacité de stockage suffisante pour accueillir les AVHD pour chacune des machines virtuelles à l’aide de ce calcul. </li> </ul> |
| Composants requis Linux | <ul><li> Vous pouvez sauvegarder des machines virtuelles Linux à l’aide de MABS. Seules les captures instantanées avec cohérence de fichiers sont prises en charge.</li></ul> |

## <a name="back-up-virtual-machines"></a>Sauvegarder des machines virtuelles

1. Configurez votre [serveur MABS](backup-azure-microsoft-azure-backup.md) et [votre stockage](backup-mabs-add-storage.md). Quand vous configurez votre stockage, utilisez ces instructions sur la capacité de stockage.

   - Taille moyenne des machines virtuelles : 100 Go
   - Nombre de machines virtuelles par serveur MABS : 800
   - Taille totale de 800 machines virtuelles : 80 To
   - Espace requis pour le stockage de sauvegarde : 80 To

2. Configurez l’agent de protection MABS sur le serveur ou sur chaque nœud de cluster.

3. Dans la console Administrateur du serveur de sauvegarde Microsoft Azure, sélectionnez **Protection** > **Créer un groupe de protection** pour ouvrir l’Assistant **Création d’un nouveau groupe de protection**.

4. Dans la page **Sélectionner les membres du groupe**, sélectionnez les machines virtuelles que vous souhaitez protéger à partir des serveurs hôtes sur lesquels elles se trouvent. Nous vous recommandons de placer toutes les machines virtuelles avec la même stratégie de protection dans un groupe de protection. Pour utiliser efficacement l’espace, activez la colocation. La colocation vous permet de localiser des données à partir de différents groupes de protection sur le même stockage sur disque ou sur bande, afin que plusieurs sources de données aient un seul réplica et un seul volume des points de récupération.

5. Dans la page **Sélectionner la méthode de protection des données**, indiquez le nom d’un groupe de protection. Sélectionnez **Je souhaite une protection à court terme à l’aide de Disque** et **Je voudrais une protection en ligne** si vous souhaitez sauvegarder des données sur Azure à l’aide du service Sauvegarde Azure.

6. Dans **Spécifier les objectifs à court terme** > **Durée de conservation**, spécifiez la durée pendant laquelle vous souhaitez conserver les données de disque. Dans **Fréquence de synchronisation**, spécifiez la fréquence à laquelle les sauvegardes incrémentielles des données doivent s’exécuter. Au lieu de sélectionner un intervalle pour les sauvegardes incrémentielles, vous pouvez également activer **Juste avant un point de récupération**. Quand ce paramètre est activé, MABS exécute une sauvegarde complète rapide juste avant chaque point de récupération planifié.

    > [!NOTE]
    >Si vous protégez des charges de travail d’application, les points de récupération sont créés selon la fréquence de synchronisation, à condition que l’application prenne en charge les sauvegardes incrémentielles. Si ce n’est pas le cas, MABS exécute une sauvegarde complète rapide à la place d’une sauvegarde incrémentielle et crée des points de récupération conformément à la planification de la sauvegarde rapide.<br></br>Le processus de sauvegarde ne sauvegarde pas les points de contrôle associés aux machines virtuelles.

7. Dans la page **Vérifier l’allocation de disque**, vérifiez l’espace disque de pool de stockage alloué pour le groupe de protection.

   **Taille totale des données** correspond à la taille des données que vous souhaitez sauvegarder et **Disk space to be provisioned on MABS** (Espace disque à provisionner sur MABS) correspond à l’espace recommandé par MABS pour le groupe de protection. MABS choisit le volume de sauvegarde idéal en fonction des paramètres. Toutefois, vous pouvez modifier les choix de volume de sauvegarde dans **Détails de l’allocation de disque**. Pour les charges de travail, sélectionnez le stockage préféré dans le menu déroulant. Vos modifications changent les valeurs de **Stockage Total** et **Stockage libre** dans le volet **Stockage sur disque disponible**. L’espace sous-provisionné est la quantité de stockage que MABS vous suggère d’ajouter au volume pour continuer à procéder à des sauvegardes de façon fluide à l’avenir.

8. Dans la page **Choisir la méthode de création d’un réplica**, spécifiez comment la réplication initiale des données dans le groupe de protection sera effectuée. Si vous choisissez de **répliquer automatiquement sur le réseau**, nous vous recommandons de choisir une heure creuse. Pour de grandes quantités de données ou des conditions réseau peu optimales, envisagez de sélectionner **Manuellement**, ce qui implique de répliquer les données hors connexion à l’aide d’un support amovible.

9. Dans la page **Options de vérification de cohérence**, indiquez comment vous voulez automatiser les vérifications de cohérence. Vous pouvez activer une vérification pour qu’elle s’exécute uniquement lorsque les données de réplication deviennent incohérentes ou selon une planification. Si vous ne souhaitez pas configurer une vérification de cohérence automatique, vous pouvez exécuter une vérification manuelle à tout moment en cliquant avec le bouton droit sur le groupe de protection et en sélectionnant **Effectuer une vérification de cohérence**.

    Une fois le groupe de protection créé, la réplication initiale des données se produit selon la méthode que vous avez sélectionnée. Après la réplication initiale, chaque sauvegarde a lieu conformément aux paramètres du groupe de protection. Si vous avez besoin de récupérer des données sauvegardées, notez les points suivants :

## <a name="back-up-replica-virtual-machines"></a>Sauvegarder des machines virtuelles de réplication

Si MABS s’exécute sur Windows Server 2012 R2 ou version ultérieure, vous pouvez sauvegarder des machines virtuelles de réplication. Cela est utile pour plusieurs raisons :

**Réduit l’impact des sauvegardes sur la charge de travail en cours d’exécution** : la sauvegarde d’une machine virtuelle entraîne une certaine surcharge au fur et à mesure de la création d’un instantané. En déchargeant le processus de sauvegarde vers un site distant secondaire, la charge de travail en cours d’exécution n’est plus affectée par l’opération de sauvegarde. Cela s’applique uniquement aux déploiements où la copie de sauvegarde est stockée sur un site distant. Par exemple, vous pouvez effectuer des sauvegardes quotidiennes et stocker des données localement pour garantir des temps de restauration rapides, mais effectuer des sauvegardes mensuelles ou trimestrielles à partir de machines virtuelles de réplication stockées à distance pour une conservation à long terme.

**Économise la bande passante** : dans un déploiement classique de succursale distante/siège, vous avez besoin d’une quantité appropriée de bande passante provisionnée pour transférer les données de sauvegarde entre les sites. Si vous créez une stratégie de réplication et basculement, en plus de votre stratégie de sauvegarde de données, vous pouvez réduire la quantité de données redondantes envoyées sur le réseau. En sauvegardant les données de la machine virtuelle de réplication plutôt que celles du serveur principal, vous économisez la surcharge liée à l’envoi des données sauvegardées sur le réseau.

**Active la sauvegarde de l’hébergeur** : vous pouvez utiliser un centre de données hébergé en tant que site de réplication, sans avoir besoin d’un centre de données secondaire. Dans ce cas, le contrat SLA de l’hébergeur exige une sauvegarde cohérente des machines virtuelles de réplication.

Une machine virtuelle de réplication est désactivée jusqu’à ce qu’un basculement soit lancé, et VSS ne peut pas garantir une sauvegarde cohérente au niveau des applications pour une machine virtuelle de réplication. La sauvegarde d’une machine virtuelle de réplication ne sera donc cohérente qu’en cas d’incident. Si la cohérence en cas d’incident ne peut pas être garantie, la sauvegarde échoue et cela peut se produire dans un certain nombre de conditions :

- La machine virtuelle de réplication n’est pas saine et se trouve dans un état critique.

- La machine virtuelle de réplication est en cours de resynchronisation (dans l’état Resynchronisation en cours ou Resynchronisation nécessaire).

- La réplication initiale entre le site principal et le site secondaire est en cours ou en attente pour la machine virtuelle.

- Les journaux .hrl sont appliqués à la machine virtuelle de réplication, ou une action précédente pour appliquer les journaux .hrl sur le disque virtuel a échoué, a été annulée ou a été interrompue.

- La migration ou le basculement de l'ordinateur virtuel de réplication est en cours.

## <a name="recover-backed-up-virtual-machines"></a>Récupérer des machines virtuelles sauvegardées

Lorsque vous pouvez récupérer une machine virtuelle sauvegardée, vous utilisez l’Assistant Récupération pour sélectionner la machine virtuelle et le point de récupération spécifique. Pour ouvrir l’Assistant Récupération et récupérer une machine virtuelle :

1. Dans la console Administrateur MABS, saisissez le nom de la machine virtuelle ou développez la liste des éléments protégés, accédez à **Toutes les données HyperV protégées** et sélectionnez la machine virtuelle que vous souhaitez récupérer.

2. Dans le volet **Points de récupération pour**, sélectionnez dans le calendrier n’importe quelle date pour afficher les points de récupération disponibles. Ensuite, dans le volet **Chemin**, sélectionnez le point de récupération que vous souhaitez utiliser dans l’Assistant Récupération.

3. Dans le menu **Actions**, sélectionnez **Récupérer** pour ouvrir l’Assistant Récupération.

    La machine virtuelle et le point de récupération que vous avez sélectionnés s’affichent dans l’écran **Vérifier la récupération sélectionnée**. Sélectionnez **Suivant**.

4. Dans l’écran **Sélectionner le type de récupération**, sélectionnez l’emplacement où vous souhaitez restaurer les données, puis sélectionnez **Suivant**.

    - **Récupérer sur l'instance d'origine** : lors d'une récupération sur l'instance d'origine, le disque dur virtuel d'origine et tous les points de contrôle associés sont supprimés. MABS récupère le disque dur virtuel et d’autres fichiers de configuration à l’emplacement d’origine à l’aide de l’enregistreur VSS Hyper-V. À la fin du processus de récupération, les machines virtuelles sont toujours hautement disponibles.
        Le groupe de ressources doit être présent pour la récupération. S’il n’est pas disponible, effectuez la récupération à un autre emplacement, puis rendez la machine virtuelle hautement disponible.

    - **Récupération en tant que machine virtuelle sur n’importe quel hôte** : MABS prend en charge la fonction de récupération à un autre emplacement (ALR), qui permet de récupérer de façon transparente une machine virtuelle Azure Stack HCI protégée sur un hôte différent au sein du même cluster, indépendamment de l’architecture du processeur. Les machines virtuelles Azure Stack HCI récupérées sur un nœud de cluster ne sont pas hautement disponibles. Si vous choisissez cette option, l’Assistant Récupération vous présente un écran supplémentaire pour identifier la destination et le chemin de destination.
    
        >[!NOTE]
        >Si vous sélectionnez l'hôte d'origine, le comportement est le même que celui de l'option **Récupérer sur l'instance d'origine**. Le disque dur virtuel d'origine et tous les points de contrôle associés sont supprimés.

    - **Copier dans un dossier réseau** : MABS prend en charge la récupération au niveau de l’élément, qui vous permet d’effectuer une récupération au niveau de l’élément de fichiers, dossiers, volumes et disques durs virtuels (VHD) à partir d’une sauvegarde au niveau de l’hôte de machines virtuelles Azure Stack HCI vers un partage réseau ou un volume sur un serveur protégé par MABS. Il n’est pas nécessaire d’installer l’agent de protection MABS à l’intérieur de l’invité pour effectuer une récupération au niveau de l’élément. Si vous choisissez cette option, l’Assistant Récupération vous présente un écran supplémentaire pour identifier la destination et le chemin de destination.

5. Dans **Spécifier les options de récupération**, configurez les options de récupération, puis sélectionnez **Suivant** :

    - Si vous récupérez une machine virtuelle avec une bande passante faible, sélectionnez **Modifier** pour activer **Limitation de l’utilisation de la bande passante réseau**. Après avoir activé l’option de limitation, vous pouvez spécifier la quantité de bande passante que vous souhaitez rendre disponible et l’heure à laquelle cette bande passante est disponible.
    - Sélectionnez **Activer la récupération SAN au moyen d’instantanés matériels** si vous avez configuré votre réseau.
    - Sélectionnez **Send an e-mail when the recovery completes** (Envoyer un e-mail quand la récupération est terminée), puis indiquez les adresses e-mail si vous souhaitez que des notifications par e-mail soient envoyées une fois le processus de récupération terminé.

6. Dans l’écran de résumé, vérifiez que tous les détails sont corrects. Si les détails ne sont pas corrects ou si vous souhaitez apporter une modification, sélectionnez **Précédent**. Si vous êtes satisfait des paramètres, sélectionnez **Récupérer** pour démarrer le processus de récupération.

7. L’écran **État de la récupération** fournit des informations sur le travail de récupération.

## <a name="next-steps"></a>Étapes suivantes

[Récupérer des données à partir d’un serveur de sauvegarde Azure](./backup-azure-alternate-dpm-server.md)
