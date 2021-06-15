---
title: Configurer SnapCenter pour Oracle sur BareMetal Infrastructure
description: Découvrez comment configurer SnapCenter pour Oracle sur BareMetal Infrastructure.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: cd3163b90d65f3e33fa56a190ed854069afd6703
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579205"
---
# <a name="configure-snapcenter-for-oracle-on-baremetal-infrastructure"></a>Configurer SnapCenter pour Oracle sur BareMetal Infrastructure

Dans cet article, nous allons suivre les étapes de la configuration de NetApp SnapCenter pour exécuter Oracle sur BareMetal Infrastructure.

## <a name="add-storage-hosts-to-snapcenter"></a>Ajouter des hôtes de stockage à SnapCenter

Tout d’abord, ajoutons des hôtes de stockage à SnapCenter. 

Lorsque vous démarrez la session SnapCenter et enregistrez l’exemption de sécurité, l’application démarre. Connectez-vous à SnapCenter sur votre machine virtuelle à l’aide des informations d’identification Active Directory.

https://\<hostname\>:8146/

Vous êtes maintenant prêt à ajouter un emplacement de stockage de production et un emplacement de stockage secondaire.

### <a name="add-the-production-storage-location"></a>Ajouter l’emplacement de stockage de production

Pour ajouter votre machine virtuelle de stockage de production :

1. Sélectionnez **Systèmes de stockage** dans le menu de gauche, puis sélectionnez **+ Nouveau**.

2. Entrez les informations requises pour **Ajouter un système de stockage** :

      - Système de stockage : Entrez l’adresse IP de la machine virtuelle de stockage fournie par Microsoft Operations.
      - Entrez le nom d’utilisateur créé ; la valeur par défaut est **snap center**.
      - Entrez le mot de passe créé lorsque Microsoft Operations a [modifié le mot de passe du client en utilisant REST](set-up-snapcenter-to-route-traffic.md#steps-to-integrate-snapcenter).
      - Laissez l’option **Envoyer une notification de support automatique au système de stockage pour les opérations ayant échoué** décochée.
      - Sélectionnez **Consigner les événements SnapCenter dans syslog**.

3. Sélectionnez **Envoyer**.

    Une fois le stockage vérifié, l’adresse IP du système de stockage ajouté est affichée avec le nom d’utilisateur entré.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/oracle-baremetal-snapcenter-add-production-storage-complete.png" alt-text="Capture d’écran montrant l’adresse IP du système de stockage ajouté.":::

    La valeur par défaut est une machine virtuelle de stockage par locataire. Si un client a plusieurs locataires, il est recommandé de configurer toutes les machines virtuelles de stockage dans SnapCenter.

### <a name="add-secondary-storage-location"></a>Ajouter un emplacement de stockage secondaire

Pour ajouter l’emplacement de stockage de récupération d’urgence (DR), les abonnements du client dans les emplacements principal et DR doivent être appariés. Contactez Microsoft Operations pour obtenir de l’aide.

L’ajout d’un emplacement de stockage secondaire est similaire à l’ajout de l’emplacement de stockage principal. Toutefois, une fois que les emplacements principal et DR sont appariés, l’accès à l’emplacement de stockage secondaire doit être vérifié en effectuant un test ping sur le stockage dans l’emplacement secondaire. 

>[!NOTE]
>Si le test ping échoue, c’est généralement parce qu’il n’existe pas d’itinéraire par défaut sur l’hôte pour le réseau local virtuel (VLAN) du client.

Une fois le test ping vérifié, répétez les étapes que vous avez utilisées pour ajouter le stockage principal, mais faites-le maintenant pour l’emplacement de la récupération d’urgence sur un hôte de récupération d’urgence. Nous vous recommandons d’utiliser le même certificat dans les deux emplacements, mais ce n’est pas obligatoire.

## <a name="set-up-oracle-hosts"></a>Configurer les hôtes Oracle

>[!NOTE]
>Ce processus concerne tous les hôtes Oracle, quel que soit leur emplacement : production ou récupération d’urgence.

1. Avant d’ajouter les hôtes à SnapCenter et d’installer les plug-ins SnapCenter, vous devez installer Java 1.8. Vérifiez que Java 1.8 est installé sur chaque hôte avant de poursuivre.

2. Créez le même utilisateur non racine sur chacun des hôtes Oracle Real Application Clusters (RAC) et ajoutez-le à /etc/sudoers. Vous devrez fournir un nouveau mot de passe.

3. Une fois que l’utilisateur a été créé, il doit être ajouté à /etc/sudoers avec un ensemble explicite d’autorisations. Ces autorisations sont disponibles en accédant à l’emplacement suivant sur la machine virtuelle SnapCenter : C:\ProgramData\NetApp\SnapCenter\Package Repository et en ouvrant le fichier oracle\_checksum.

4. Copiez l’ensemble de commandes approprié, en fonction du package sudo, où « LINUXUSER » est remplacé par le nom d’utilisateur nouvellement créé. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/netapp-snapcenter-oracle-checksum-details.png" alt-text="Capture d’écran des détails du fichier oracle_checksum.":::

    L’ensemble de commandes pour le package sudo 1.8.7 ou une version ultérieure est copié dans /etc/sudoers.

5. Une fois que l’utilisateur a été ajouté à sudoers, dans SnapCenter, sélectionnez **Settings** (Paramètres) > **Credential** (Informations d’identification) > **New** (Nouveau).

6. Remplissez la zone Credential (Informations d’identification) comme suit :

    - **Nom des informations d’identification** : Fournissez un nom identifiant le nom d’utilisateur et le fichier sudoers.
    - **Authentification** : Linux.
    - **Nom d’utilisateur** : Fournissez le nom d’utilisateur nouvellement créé.
    - **Mot de passe** : <Enter Password>
    - Cochez la case **Use sudo privileges** (Utiliser les privilèges sudo).
    
7. Sélectionnez **OK**.

8. Sélectionnez **Hosts** (Hôtes) dans la navigation de gauche, puis sélectionnez **+ Add** (+ Ajouter).

9. Entrez les valeurs suivantes pour **Add Host** (Ajouter un hôte) :

    - **Type d’hôte** : Linux.
    - **Nom d’hôte** : Entrez le nom de domaine complet ou l’adresse IP de l’hôte RAC principal.
    - **Informations d’identification** : Sélectionnez la liste déroulante, puis les nouvelles informations d’identification créées pour le fichier sudoers.
    - Cochez la case **Oracle Database**.

    >[!NOTE]
    >Vous devez entrer l’une des adresses IP réelles de l’hôte Oracle. L’entrée d’une adresse IP virtuelle de nœud ou d’une adresse IP d’analyse n’est pas prise en charge.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-details.png" alt-text="Capture d’écran montrant les détails du nouvel hôte.":::
    
10. Sélectionnez **More Options** (Plus d’options). Assurez-vous que l’option **Add all hosts in the Oracle RAC** (Ajouter tous les hôtes dans Oracle RAC) est sélectionnée. Sélectionnez **Save** (Enregistrer), puis **Submit** (Envoyer).

11. Le programme d’installation du plug-in tente à présent de communiquer avec l’adresse IP fournie. Si la communication est réussie, l’identité de l’hôte Oracle RAC fourni est confirmée lorsque vous sélectionnez le bouton **Confirm and Submit** (Confirmer et envoyer).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-confirm-fingerprint.png" alt-text="Capture d’écran montrant la nouvelle empreinte digitale de l’hôte.":::

12. Après la confirmation de l’hôte Oracle RAC initial, SnapCenter essaie de communiquer avec tous les autres serveurs Oracle RAC et de les confirmer dans le cadre du cluster. Sélectionnez **Confirm Others and Submit** (Confirmer les autres et envoyer).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-fingerprint-authentication.png" alt-text="Capture d’écran montrant l’authentification de l’empreinte digitale.":::

    Un écran d’état s’affiche pour les ordinateurs hôtes gérés afin de vous informer que SnapCenter est en train d’installer le plug-in Oracle sélectionné. Vous pouvez vérifier la progression de l’installation en consultant les journaux qui se trouvent dans /opt/NetApp/snapcenter/scc/logs/DISCOVER-PLUGINS\_0.log sur l’un des hôtes Oracle RAC. 

    Une fois les plug-ins installés, l’écran suivant s’affiche.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-installed-plugins.png" alt-text="Capture d’écran montrant tous les plug-ins SnapCenter installés.":::

## <a name="add-credentials-for-oracle-recovery-manager"></a>Ajouter des informations d’identification pour Oracle Recovery Manager

La méthode d’authentification du catalogue Oracle Recovery Manager (RMAN) permet de s’authentifier auprès de la base de données du catalogue RMAN. Si vous avez configuré un mécanisme de catalogue externe et inscrit votre base de données pour son catalogage, vous devez ajouter l’authentification du catalogue RMAN. Les informations d’authentification RMAN peuvent être ajoutées à tout moment.

1. Dans SnapCenter, sélectionnez **Settings** (Paramètres) dans le menu de gauche, puis sélectionnez **Credential** (Informations d’identification). Sélectionnez **New** (Nouveau) dans le coin supérieur droit.

2. Entrez le **nom des informations d’identification** pour appeler les informations d’identification RMAN stockées dans SnapCenter. Dans la liste déroulante **Authentication** (Authentification), sélectionnez **Oracle RMAN Catalog for Authentication** (Catalogue Oracle RMAN pour l’authentification). Entrez votre nom d'utilisateur et votre mot de passe. Sélectionnez **OK**.

3. Une fois les informations d’identification ajoutées, les paramètres de la base de données doivent être modifiés dans SnapCenter. Sélectionnez la base de données dans les ressources. Sélectionnez **Database Settings** (Paramètres de la base de données) dans le coin supérieur droit de la fenêtre principale.

4. Sur l’écran des paramètres de la base de données, sélectionnez **Configure Database** (Configurer la base de données).

5. Développez **Configure RMAN Catalog Settings** (Configurer les paramètres du catalogue RMAN). Sélectionnez la liste déroulante pour **Use Existing Credential previously set for this database** (Utiliser les informations d’identification existantes précédemment définies pour cette base de données) et sélectionnez l’option appropriée. Ajoutez le nom TNS pour cette base de données individuelle. Sélectionnez **OK**.

    >[!NOTE]
    >Des informations d’identification différentes doivent être créées à l’étape précédente pour chaque combinaison unique de nom d’utilisateur et mot de passe. Si vous préférez, SnapCenter accepte un seul ensemble d’informations d’identification pour toutes les bases de données.
    >
    >Même si les informations d’identification RMAN ont été ajoutées à la base de données, RMAN ne sera pas catalogué, sauf si l’option Catalog Backup with Oracle Recovery Manager (RMAN) [Sauvegarde du catalogue avec Oracle Recovery Manager (RMAN)] est également cochée pour chaque stratégie de protection, comme indiqué dans la section suivante relative à la création de stratégies de protection (selon vos stratégies de sauvegarde).

## <a name="create-protection-policies"></a>Créer des stratégies de protection

Une fois que vos hôtes ont été correctement ajoutés à SnapCenter, vous êtes prêt à créer vos stratégies de protection. 

Sélectionnez **Ressources** dans le menu de gauche. SnapCenter présente toutes les bases de données identifiées qui existaient sur les hôtes RAC. Les types d’instances uniques pour bn6p1X et grid sont ignorés dans le cadre du schéma de protection. À ce stade, l’état de toutes les bases de données doit être « Non protégée ».

Comme indiqué dans la [présentation](netapp-snapcenter-integration-oracle-baremetal.md#oracle-disaster-recovery-architecture), les différents types de fichiers ont des fréquences d’instantanés différentes qui permettent un objectif de point de récupération local d’environ 15 minutes en effectuant des sauvegardes des fichiers journaux d’archive toutes les 15 minutes. Les fichiers de données sont ensuite sauvegardés à des intervalles plus longs, par exemple toutes les heures. Par conséquent, deux stratégies différentes sont créées.

Une fois les bases de données RAC identifiées, plusieurs stratégies de protection sont créées, y compris une stratégie pour les fichiers de données et les fichiers de contrôle, et une autre pour les journaux d’archive.

### <a name="datafiles-protection-policy"></a>Stratégie de protection des fichiers de données

Procédez comme suit pour créer une stratégie de protection des fichiers de données.

1. Dans SnapCenter, sélectionnez **Settings** (Paramètres) dans le menu de gauche, puis sélectionnez **Policies** (Stratégies) dans le menu supérieur. Sélectionnez **Nouveau**.

2. Sélectionnez la case d’option pour **Datafiles and control files** (Fichiers de données et de contrôle) et faites défiler la page vers le bas.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy.png" alt-text="Capture d’écran montrant les détails de la nouvelle stratégie de sauvegarde de base de données.":::

3. Sélectionnez la case d’option **Hourly** (Toutes les heures). Si l’intégration à RMAN est souhaitée pour le catalogue et que les informations d’identification RMAN ont déjà été ajoutées, cochez la case **Catalog backup with Oracle Recovery Manager (RMAN)** [Sauvegarde du catalogue avec Oracle Recovery Manager (RMAN)]. Sélectionnez **Suivant**. Si la sauvegarde du catalogue est activée, les [informations d’identification RMAN doivent être ajoutées](#add-credentials-for-oracle-recovery-manager) pour que le catalogage se produise.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-backup-policy-frequency.png" alt-text="Capture d’écran montrant les options permettant de choisir la fréquence de planification dans votre stratégie de sauvegarde.":::

    >[!IMPORTANT]
    >Une sélection doit être effectuée pour la stratégie de planification même si une fréquence de sauvegarde autre que toutes les heures ou tous les jours, etc., est souhaitée. La fréquence de sauvegarde réelle est définie plus tard dans le processus. Ne sélectionnez pas « None » (Jamais), sauf si toutes les sauvegardes sous cette stratégie sont uniquement à la demande.

4. Dans le menu de gauche, sélectionnez **Retention** (Rétention). Il existe deux types de paramètres de rétention qui sont définis pour une stratégie de sauvegarde. Le premier paramètre de rétention est le nombre maximal d’instantanés à la demande à conserver. En fonction de vos stratégies de sauvegarde, un nombre défini d’instantanés sont conservés, comme dans l’exemple suivant de 48 instantanés à la demande conservés pendant 14 jours. Définissez la stratégie appropriée de rétention à la demande en fonction de vos stratégies de sauvegarde.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy-retention.png" alt-text="Capture d’écran montrant le paramètre de rétention de la stratégie de sauvegarde de base de données.":::

5. Le paramètre de rétention suivant est le nombre planifié d’instantanés à conserver en fonction de l’entrée précédente (toutes les heures, tous les jours, toutes les semaines, etc.). Sélectionnez **Next** (Suivant).

6. Dans l’écran **Replication** (Réplication), cochez la case **Update SnapMirror after creating a local snapshot copy** (Mettre à jour SnapMirror après avoir créé une copie locale de l’instantané) et sélectionnez **Next** (Suivant). Les autres entrées sont laissées sur les valeurs par défaut.

    >[!NOTE]
    >SnapVault n’est actuellement pas pris en charge dans l’environnement Oracle BareMetal Infrastructure.

    La réplication peut être ajoutée ultérieurement en revenant à l’écran de stratégie de protection et en sélectionnant **Modify protection policy** (Modifier la stratégie de protection) et **Replication** (Réplication) dans le menu de gauche.

7. La page **Script** est l’endroit où vous entrez tous les scripts nécessaires à l’exécution avant ou après la sauvegarde Oracle. Actuellement, les scripts faisant partie du processus SnapCenter ne sont pas pris en charge. Sélectionnez **Suivant**.

8. Sélectionnez **Verification** (Vérification) dans le menu de gauche. Si vous voulez avoir la possibilité de vérifier l’intégrité des instantanés pour la récupération, cochez la case à côté de « Hourly » (Toutes les heures). Aucune commande de script de vérification n’est actuellement prise en charge. Sélectionnez **Suivant**.

    >[!NOTE]
    >L’emplacement et la planification réels du processus de vérification sont ajoutés dans une section ultérieure, Attribuer des stratégies de protection aux ressources.

9. Sur la page **Summary** (Résumé), vérifiez que tous les paramètres sont entrés comme prévu, puis sélectionnez **Finish** (Terminer).

### <a name="archive-logs-protection-policy"></a>Stratégie de protection des journaux d’archive

Suivez à nouveau les étapes précédentes pour créer votre stratégie de protection des journaux d’archive. À l’étape 2, sélectionnez la case d’option pour **Archive logs** (Journaux d’archive) au lieu de « Datafiles and control files » (Fichiers de données et de contrôle).

## <a name="assign-protection-policies-to-resources"></a>Attribuer des stratégies de protection aux ressources

Une fois qu’une stratégie a été créée, elle doit être associée à une ressource pour que SnapCenter commence à s’exécuter dans le cadre de cette stratégie.

### <a name="datafiles-resource-group"></a>Groupe de ressources de fichiers de données

1. Sélectionnez **Resources** (Ressources) dans le menu de gauche, puis sélectionnez **New Resource Group** (Nouveau groupe de ressources) dans l’angle supérieur droit de la fenêtre principale.

2. Ajoutez le **nom** de ce groupe de ressources et des balises pour faciliter la recherche.

    >[!NOTE]
    >Nous vous recommandons de cocher la case de l’option **Use custom name format for Snapshot copy** (Utiliser le format de nom personnalisé pour la copie d’instantané) et d’ajouter les entrées suivantes : **$ResourceGroup**, **$Policy** et **$ScheduleType**. Cela garantit que le préfixe de l’instantané est conforme à la norme de SnapCenter et que le nom de l’instantané fournit les détails nécessaires en un coup d’œil. Si vous laissez l’option **Use custom name format for Snapshot copy** (Utiliser le format de nom personnalisé pour la copie d’instantané) désactivée, toute entrée placée dans **Name** (Nom) devient le préfixe des instantanés créés. Assurez-vous que le nom entré identifie la base de données et ce qui est protégé, par exemple, les fichiers de données et les fichiers de contrôle. 

3. Sous « Backup settings » (Paramètres de sauvegarde), ajoutez **/u95** pour exclure les journaux d’archive.

4. Dans la page des ressources, déplacez toutes les bases de données protégées par la même stratégie de protection des sauvegardes de « Available Resources » (Ressources disponibles) vers « Selected Resources » (Ressources sélectionnées). N’ajoutez pas les instances de base de données Oracle pour chaque hôte, seulement les bases de données. Sélectionnez **Suivant**.

5. Sélectionnez la stratégie de protection des fichiers de données et des fichiers de contrôle que vous avez créée précédemment. Après avoir sélectionné la stratégie, sélectionnez le crayon sous « Configure schedules » (Configurer les planifications).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-datafiles-resource-group-available-policies.png" alt-text="Capture d’écran montrant la sélection de la stratégie de protection afin de configurer les planifications.":::

6. Ajoutez la planification de la stratégie sélectionnée à exécuter. Vérifiez que la date de début est postérieure à la date et à l’heure actuelles.

    >[!NOTE]
    >La fréquence planifiée ne doit pas nécessairement correspondre à la fréquence sélectionnée lors de la création de la stratégie. Pour une exécution horaire, nous vous recommandons de laisser « Repeat every 1 hours » (Répéter toutes les heures). L’heure de début servira d’heure de début chaque heure pour que la sauvegarde se produise. Veillez à ce que la planification de la protection des fichiers de données ne chevauche pas celle de la protection des journaux d’archive, car une seule sauvegarde peut être active à un moment donné.

7. La vérification permet de s’assurer que l’instantané créé est utilisable. Le processus de vérification est complet et comprend notamment la création de clones de tous les volumes de base de données Oracle, le montage de la base de données sur l’hôte principal et la vérification de sa capacité de récupération. Ce processus prend du temps et consomme beaucoup de ressources. Si vous souhaitez configurer la planification de la vérification, sélectionnez le signe **+** .

    >[!NOTE]
    >Le processus de vérification occupe des ressources sur l’hôte pendant une durée importante. Si vous ajoutez une vérification, nous vous recommandons de le faire sur un hôte de l’emplacement secondaire, le cas échéant.
    
    La capture d’écran suivante montre la vérification d’un nouveau groupe de ressources pour lequel la réplication n’a pas été activée et les instantanés n’ont pas été répliqués au moment de sa création. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-verification-schedules.png" alt-text="Capture d’écran montrant comment configurer la planification de la vérification pour le groupe de ressources.":::

    Si la réplication est activée et qu’un hôte de l’emplacement de récupération d’urgence sera utilisé pour la vérification, passez à l’étape 2 dans la sous-section suivante. Cette étape vous permet de charger des localisateurs secondaires pour vérifier les sauvegardes sur l’emplacement secondaire.

### <a name="add-verification-schedule-for-new-resource-group"></a>Ajouter une planification de vérification pour le nouveau groupe de ressources

1. Sélectionnez **Run verification after backup** (Exécuter la vérification après la sauvegarde) ou **Run scheduled verification** (Exécuter une vérification planifiée), puis sélectionnez une fréquence préprogrammée dans la liste déroulante. Si la récupération d’urgence est activée, vous pouvez sélectionner **Verify on secondary storage location** (Vérifier sur l’emplacement de stockage secondaire) pour exécuter la fréquence dans l’emplacement de récupération d’urgence, ce qui réduit la sollicitation des ressources en production. Sélectionnez **OK**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-add-verification-schedules.png" alt-text="Capture d’écran montrant comment ajouter une planification de vérification.":::

    Si la vérification n’est pas nécessaire ou si elle est déjà configurée localement, passez à la configuration de SMTP (étape 5) ci-dessous.

2. Si la mise à jour de SnapMirror a été activée lors de la création de la stratégie de protection des fichiers de données et qu’un emplacement de stockage secondaire a été ajouté, SnapCenter détecte la réplication entre les deux emplacements. Dans ce cas, un écran s’affiche pour vous permettre de charger des localisateurs secondaires afin de vérifier les sauvegardes sur l’emplacement secondaire. Sélectionnez **Load locators** (Charger des localisateurs).

3. Après avoir sélectionné « Load locators », SnapCenter présente les relations SnapMirror qu’il a trouvées et qui contiennent des fichiers de données et des fichiers de contrôle. Celles-ci doivent correspondre à l’infrastructure de récupération d’urgence fournie par Microsoft Operations. Sélectionnez le crayon sous « Configure Schedules » (Configurer les planifications).

4. Cochez la case de l’option **Verify on secondary storage location** (Vérifier sur l’emplacement de stockage secondaire). 

5. Si un serveur SMTP est disponible, SnapCenter peut envoyer un e-mail aux administrateurs de SnapCenter. Entrez les informations suivantes si vous souhaitez qu’un e-mail soit envoyé.

    - **Préférence d’e-mail** : Entrez la fréquence de votre choix pour la réception des e-mails.
    - **De** : Entrez l’adresse e-mail que SnapCenter utilisera pour envoyer des e-mails.
    - **À** : Entrez l’adresse e-mail à laquelle SnapCenter enverra les e-mails.
    - **Objet** : Entrez l’objet que SnapCenter utilisera pour envoyer l’e-mail.
    - Cochez la case **Attach job report** (Joindre le rapport de travail).
    - Sélectionnez **Suivant**.

6. Vérifiez les paramètres sur la page de résumé et sélectionnez **Finish** (Terminer).

Une fois qu’un groupe de ressources a été créé, pour l’identifier :

1. Sélectionnez **Ressources** dans le menu de gauche.
2. Sélectionnez le menu déroulant dans la fenêtre principale, à côté de « View » (Affichage) et sélectionnez **Resource Group** (Groupe de ressources).
 
    S’il s’agit de votre premier groupe de ressources, seul le nouveau groupe de ressources créé pour les fichiers de données et les fichiers de contrôle sera présent. Si vous avez également créé un groupe de ressources pour les journaux d’archive, vous le verrez également.

### <a name="archive-log-resource-group"></a>Groupe de ressources de journaux d’archive

Pour attribuer des stratégies de protection à votre groupe de ressources de journaux d’archive, suivez les mêmes étapes que celles que vous avez suivies pour attribuer des stratégies de protection à votre groupe de ressources de fichiers de données. Les seuls différences sont les suivantes :

- À l’étape 3, n’ajoutez pas **/u95** pour exclure les journaux d’archive ; laissez ce champ vide.
- À l’étape 6, nous vous recommandons de sauvegarder les journaux d’archive toutes les 15 minutes.
- L’onglet de vérification est vide pour les journaux d’archive. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une sauvegarde à la demande de votre base de données Oracle dans SnapCenter :

> [!div class="nextstepaction"]
> [Créer une sauvegarde à la demande dans SnapCenter](create-on-demand-backup-oracle-baremetal.md)
