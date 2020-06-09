---
title: Sauvegarder des machines virtuelles VMware avec le serveur de sauvegarde Azure
description: Dans cet article, découvrez comment utiliser le serveur de sauvegarde Azure pour sauvegarder des machines virtuelles VMware s’exécutant sur un serveur VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: c4bf61e2a02200b2e6af814ef4509081649e202d
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204716"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Sauvegarder des machines virtuelles VMware avec le serveur de sauvegarde Azure

Cet article explique comment sauvegarder des machines virtuelles VMware s’exécutant sur les hôtes VMware ESXi/vCenter Server vers Azure à l’aide du serveur de sauvegarde Azure.

Cet article explique comment :

- Configurer un canal sécurisé afin que le serveur de sauvegarde Azure puisse communiquer avec des serveurs VMware via HTTPS.
- Configurer un compte VMware que le serveur de sauvegarde Azure utilisera pour accéder au serveur VMware.
- Ajouter les informations d’identification du compte à la sauvegarde Azure.
- Ajouter le serveur vCenter ou ESXi au serveur de sauvegarde Azure.
- Configurer un groupe de protection qui contient les machines virtuelles VMware que vous souhaitez sauvegarder, spécifier les paramètres de sauvegarde et planifier la sauvegarde.

## <a name="before-you-start"></a>Avant de commencer

- Veillez à exécuter une version de vCenter/ESXi prise en charge pour la sauvegarde. Reportez-vous au tableau de prise en charge [ici](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Assurez-vous que vous avez configuré le serveur de sauvegarde Azure. Dans le cas contraire, [effectuez cette opération](backup-azure-microsoft-azure-backup.md) avant de commencer. Vous devez exécuter le serveur de sauvegarde Azure avec les dernières mises à jour.
- Vérifiez que les ports réseau suivants sont ouverts :
    - TCP 443 entre MABS et vCenter
    - TCP 443 et TCP 902 entre MABS et l'hôte ESXi


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Créer une connexion sécurisée au serveur vCenter

Par défaut, le serveur de sauvegarde Azure communique avec les serveurs VMware via HTTPS. Pour configurer la connexion HTTPS, téléchargez le certificat de l’autorité de certification (AC) VMware et importez-le sur le serveur de sauvegarde Azure.

### <a name="before-you-begin"></a>Avant de commencer

- Si vous ne souhaitez pas utiliser le protocole HTTPS, vous pouvez [désactiver la validation de certificat HTTPS pour tous les serveurs VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- En général, vous vous connectez au serveur vCenter/ESXi depuis un navigateur sur le serveur de sauvegarde Azure via le client web vSphere. La première fois, la connexion n’est pas sécurisée et le message suivant s’affiche.
- Il est important de comprendre comment le serveur de sauvegarde Azure gère les sauvegardes.
  - Dans un premier temps, le serveur de sauvegarde Azure sauvegarde les données dans le stockage sur disque local. Le serveur de sauvegarde Azure utilise un pool de stockage, un ensemble de disques et de volumes sur lequel il stocke les points de récupération de disque pour ses données protégées. Il peut s’agir d’un stockage en attachement direct (DAS), un SAN Fibre Channel, ou un appareil de stockage ou SAN iSCSI. Il est important de vérifier que vous disposez de suffisamment de stockage pour la sauvegarde locale de vos données de machines virtuelles VMware.
  - Le serveur de sauvegarde Azure effectue ensuite la sauvegarde à partir du stockage de disque local vers Azure.
  - [Obtenez de l’aide](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) pour déterminer la quantité d’espace de stockage dont vous avez besoin. Ces informations concernent DPM, mais elles peuvent être utilisées pour le serveur de sauvegarde Azure.

### <a name="set-up-the-certificate"></a>Configurer le certificat

Pour configurer un canal sécurisé, procédez comme suit :

1. Dans le navigateur sur le serveur de sauvegarde Azure, entrez l’URL du client web vSphere. Si la page de connexion n’apparaît pas, vérifiez les paramètres de connexion et de proxy du navigateur.

    ![Client web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Dans la page de connexion du client web vSphere, cliquez sur **Download trusted root CA certificates** (Télécharger les certificats d’autorité de certification racine de confiance).

    ![Télécharger un certificat d’autorité de certification racine de confiance](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Un fichier nommé **download** est téléchargé. En fonction de votre navigateur, vous recevez un message vous demandant d’ouvrir ou d’enregistrer le fichier.

    ![Télécharger le certificat d’autorité de certification](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Enregistrez le fichier sur l’ordinateur du serveur de sauvegarde Azure avec une extension .zip.

5. Cliquez avec le bouton droit de la souris sur **download.zip** > **Extraire tout**. Le contenu du fichier .zip est extrait dans un dossier nommé **certs**, qui contient les éléments suivants :
   - Le fichier de certificat racine est doté d’une extension qui commence par une séquence numérotée comme .0 et .1.
   - Le fichier CRL est doté d’une extension qui commence par une séquence comme .r0 ou .r1. Le fichier CRL est associé à un certificat.

    ![Certificats téléchargés](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Dans le dossier **certs**, cliquez avec le bouton droit sur le fichier de certificat racine et sélectionnez > **Renommer**.

    ![Renommer un certificat racine](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Remplacez l’extension du certificat racine par .crt et confirmez. L’icône du fichier est remplacée par une icône représentant un certificat racine.

8. Cliquez sur le certificat racine et dans le menu contextuel, sélectionnez **Installer le certificat**.

9. Dans l’**Assistant Importation de certificat**, sélectionnez **Ordinateur Local** comme destination du certificat, cliquez sur **Suivant** pour continuer. Si un message vous demande si vous voulez autoriser les modifications sur cet ordinateur, confirmez.

    ![Accueil de l’Assistant](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Dans la page **Magasin de certificats**, sélectionnez **Placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir** pour choisir un magasin.

    ![Stockage des certificats](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Dans **Sélectionner un magasin de certificats**, sélectionnez **Autorités de certification racines de confiance** comme dossier de destination des certificats, puis cliquez sur **OK**.

    ![Dossier de destination des certificats](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. À la **fin de l’Assistant Importation de certificat**, vérifiez le dossier, puis cliquez sur **Terminer**.

    ![Vérifier que le certificat se trouve dans le dossier approprié](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Après avoir confirmé l’importation de certificat, connectez-vous au serveur vCenter pour vérifier que votre connexion est sécurisée.

### <a name="disable-https-certificate-validation"></a>Désactiver la validation de certificat HTTPS

Si vous avez des limites sécurisées au sein de votre organisation et que vous ne souhaitez pas utiliser le protocole HTTPS entre les serveurs VMware et l’ordinateur du serveur de sauvegarde Azure, désactivez HTTPS comme suit :

1. Copiez et collez le texte suivant dans un fichier .txt.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Enregistrez le fichier sous le nom **DisableSecureAuthentication.reg** sur votre serveur de sauvegarde Azure.

3. Double-cliquez sur le fichier pour activer l’entrée de Registre.

## <a name="create-a-vmware-role"></a>Créer un rôle VMware

Le serveur de sauvegarde Azure a besoin d’un compte d’utilisateur avec des autorisations d’accès à vCenter Server/l’hôte ESXi. Créez un rôle VMware avec des privilèges spécifiques, puis associez-y un compte utilisateur.

1. Connectez-vous à vCenter Server (ou à l’hôte ESXi si vous n’utilisez pas vCenter Server).
2. Dans le panneau **Navigateur**, cliquez sur **Administration**.

    ![Administration](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Dans **Administration** > **Rôles**, cliquez sur l’icône d’ajout de rôle (le symbole +).

    ![Ajouter un rôle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Dans **Créer un rôle** > **Nom du rôle**, entrez *BackupAdminRole*. Vous pouvez choisir n’importe quel nom de rôle, à condition qu’il permettre de reconnaître l’objectif du rôle.

5. Sélectionnez les privilèges, comme décrit dans le tableau ci-dessous, puis cliquez sur **OK**.  Le nouveau rôle s’affiche dans la liste du panneau **Rôles**.
   - Cliquez sur l’icône située à côté de l’étiquette du parent pour développer le parent et afficher les privilèges enfant.
   - Pour sélectionner les privilèges VirtualMachine, vous devez atteindre plusieurs niveaux dans la hiérarchie parent-enfant.
   - Vous n’avez pas besoin de sélectionner tous les privilèges enfant au sein d’un privilège parent.

    ![Hiérarchie des privilèges parent-enfant](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Autorisations des rôles

| Privilèges du compte d’utilisateur vCenter 6.7                     | Privilèges du compte d’utilisateur vCenter 6.5                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Datastore cluster.Configure a datatstore cluster            | Datastore cluster.Configure a datatstore cluster            |
| Datastore.AllocateSpace                                      | Datastore.AllocateSpace                                      |
| Datastore.Browse datastore                                   | Datastore.Browse datastore                                   |
| Opérations sur les fichiers Datastore.Low-level                          | Opérations sur les fichiers Datastore.Low-level                          |
| Global.Disable methods                                       | Global.Disable methods                                       |
| Global.Enable methods                                        | Global.Enable methods                                        |
| Global.Licenses                                              | Global.Licenses                                              |
| Global.Log event                                             | Global.Log event                                             |
| Attributs personnalisés Global.Manage                              | Attributs personnalisés Global.Manage                              |
| Global.Set custom attribute                                  | Global.Set custom attribute                                  |
| Host.Local operations.Create virtual machine                | Host.Local operations.Create virtual machine                |
| Network.Assign network                                       | Network.Assign network                                       |
| Resource. Assign virtual machine to resource pool           | Resource. Assign virtual machine to resource pool           |
| vApp.Add virtual machine                                     | vApp.Add virtual machine                                     |
| vApp.Assign resource pool                                    | vApp.Assign resource pool                                    |
| vApp.Unregister                                              | vApp.Unregister                                              |
| VirtualMachine.Configuration. Add Or Remove Device          | VirtualMachine.Configuration. Add Or Remove Device          |
| Virtual machine.Configuration.Acquire disk lease            | Virtual machine.Configuration.Disk lease                     |
| Virtual machine.Configuration.Add new disk                   | Virtual machine.Configuration.Add new disk                   |
| Virtual machine.Configuration.Advanced configuration        | Virtual machine.Configuration.Advanced                       |
| Virtual machine.Configuration.Toggle disk change tracking   | Virtual machine.Configuration.Disk change tracking          |
| Virtual machine.Configuration.Configure Host USB device     | Virtual machine.Configuration.Host USB device               |
| Virtual machine.Configuration.Extend virtual disk           | Virtual machine.Configuration.Extend virtual disk           |
| Virtual machine.Configuration.Query unowned files           | Virtual machine.Configuration.Query unowned files           |
| Virtual machine.Configuration.Change Swapfile placement     | Virtual machine.Configuration.Swapfile placement            |
| Virtual machine.Guest Operations.Guest Operation Program Execution | Virtual machine.Guest Operations.Guest Operation Program Execution |
| Virtual machine.Guest Operations.Guest Operation Modifications | Virtual machine.Guest Operations.Guest Operation Modifications |
| Virtual machine.Guest Operations.Guest Operation Queries    | Virtual machine.Guest Operations.Guest Operation Queries    |
| Virtual machine.Interaction.Device connection             | Virtual machine.Interaction.Device connection             |
| Virtual machine.Interaction.Guest operating system management by VIX API | Virtual machine.Interaction.Guest operating system management by VIX API |
| Virtual machine.Interaction.Power Off                      | Virtual machine.Interaction.Power Off                      |
| Virtual machine.Inventory.Create new                        | Virtual machine.Inventory.Create new                        |
| Virtual machine .Inventory.Remove                            | Virtual machine .Inventory.Remove                            |
| Virtual machine .Inventory.Register                          | Virtual machine .Inventory.Register                          |
| Virtual machine.Provisioning.Allow disk access             | Virtual machine.Provisioning.Allow disk access             |
| Virtual machine.Provisioning.Allow file access             | Virtual machine.Provisioning.Allow file access             |
| Virtual machine.Provisioning.Allow read-only disk access   | Virtual machine.Provisioning.Allow read-only disk access   |
| Virtual machine.Provisioning.Allow virtual machine download | Virtual machine.Provisioning.Allow virtual machine download |
| Virtual machine .Snapshot management.  Créer l’instantané       | Virtual machine .Snapshot management.  Créer l’instantané       |
| Virtual machine.Snapshot management.Remove Snapshot        | Virtual machine.Snapshot management.Remove Snapshot        |
| Virtual machine.Snapshot management.Revert to snapshot     | Virtual machine.Snapshot management.Revert to snapshot     |

<br>

| **Privilèges du compte d’utilisateur vCenter 6.0**                | **Privilèges du compte d’utilisateur vCenter 5.5** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                    | Network.Assign                              |
| Global.Manage  custom attributes                           | Datastore.AllocateSpace                     |
| Global.Set  custom attribute                               | VirtualMachine.Config.ChangeTracking        |
| Host.Local  operations.Create virtual machine              | VirtualMachine.State.RemoveSnapshot         |
| Réseau.  Attribuer un réseau                                   | VirtualMachine.State.CreateSnapshot         |
| Resource.  Attribuer une machine virtuelle à une liste de ressources partagées         | VirtualMachine.Provisioning.DiskRandomRead  |
| Virtual  machine.Configuration.Add new disk                | VirtualMachine.Interact.PowerOff            |
| Virtual  machine.Configuration.Advanced                    | VirtualMachine.Inventory.Create             |
| Virtual  machine.Configuration.Disk change tracking        | VirtualMachine.Config.AddNewDisk            |
| Virtual  machine.Configuration.Host USB device             | VirtualMachine.Config.HostUSBDevice         |
| Virtual  machine.Configuration.Query unowned files         | VirtualMachine.Config.AdvancedConfig        |
| Virtual  machine.Configuration.Swapfile placement          | VirtualMachine.Config.SwapPlacement         |
| Virtual  machine.Interaction.Power Off                     | Global.ManageCustomFields                   |
| Virtual  machine.Inventory. Création                     |                                             |
| Virtual  machine.Provisioning.Allow disk access            |                                             |
| Virtual  machine.Provisioning. Autoriser l’accès au disque en lecture seule |                                             |
| Virtual  machine.Snapshot management.Create snapshot       |                                             |
| Virtual  machine.Snapshot management.Remove Snapshot       |                                             |

## <a name="create-a-vmware-account"></a>Créer un compte VMware

1. Pour créer un compte d’utilisateur, dans le panneau **Navigateur** de vCenter Server, cliquez sur **Utilisateurs et groupes**. Si vous n’utilisez pas vCenter Server, créez le compte sur l’hôte ESXi approprié.

    ![Option Utilisateurs et groupes](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Le panneau **Utilisateurs et groupes vCenter** s’affiche.

2. Dans le panneau **Utilisateurs et groupes vCenter**, dans l’onglet **Utilisateurs**, cliquez sur l’icône d’ajout d’utilisateurs (symbole +).

    ![Panneau Utilisateurs et groupes vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Dans la boîte de dialogue **Nouvel utilisateur**, ajoutez les informations utilisateur > **OK**. Dans cette procédure, le nom d’utilisateur est BackupAdmin.

    ![Boîte de dialogue Nouvel utilisateur](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Pour associer le compte d’utilisateur au rôle, dans le panneau **Navigateur**, cliquez sur **Autorisations globales**. Dans le panneau **Autorisations globales**, dans l’onglet **Gérer**, cliquez sur l’icône d’ajout (symbole +).

    ![Panneau Autorisations globales](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Dans la boîte de dialogue **Global Permissions Root - Add Permission** (Racine des autorisations globales - Ajouter une autorisation), cliquez sur **Ajouter** pour choisir l’utilisateur ou le groupe.

    ![Choisir un utilisateur ou un groupe](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Dans **Sélectionner des utilisateurs/groupes**, choisissez **BackupAdmin** > **Ajouter**. Dans la zone **Utilisateurs**, le format *domain\username* est utilisé pour le compte d’utilisateur. Si vous souhaitez utiliser un autre domaine, sélectionnez-le dans la liste **Domaine**. Cliquez sur **OK** pour ajouter les utilisateurs sélectionnés à la boîte de dialogue **Ajouter une autorisation**.

    ![Ajouter un utilisateur BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Dans **Rôle attribué**, dans la liste déroulante, sélectionnez **BackupAdminRole** > **OK**.

    ![Affecter un utilisateur à un rôle](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Dans l’onglet **Gérer** du panneau **Autorisations globales**, le nouveau compte d’utilisateur et le rôle associé apparaissent dans la liste.

## <a name="add-the-account-on-azure-backup-server"></a>Ajouter le compte sur le serveur de sauvegarde Azure

1. Ouvrez le serveur de sauvegarde Azure. Si vous ne trouvez pas l’icône sur le Bureau, ouvrez la Sauvegarde Microsoft Azure dans la liste des applications.

    ![Icône du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** >  **Serveurs de production** > **Gérer VMware**.

    ![Console du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Dans la boîte de dialogue **Gérer les informations d’identification**, cliquez sur **Ajouter**.

    ![Boîte de dialogue Gérer les informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Dans **Ajouter des informations d’identification**, entrez le nom et la description des nouvelles informations d’identification, puis spécifiez le nom d’utilisateur et le mot de passe définis sur le serveur VMware. Le nom *Contoso Vcenter credential* est utilisé pour identifier les informations d’identification dans cette procédure. Si le serveur VMware et le serveur de sauvegarde Azure ne se trouvent pas sur le même domaine, spécifiez le domaine dans le nom d’utilisateur.

    ![Boîte de dialogue Ajouter des informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Cliquez sur **Ajouter** pour ajouter les nouvelles informations d’identification.

    ![Boîte de dialogue Gérer les informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Ajouter le serveur vCenter Server

Ajoutez le serveur vCenter Server au serveur de sauvegarde Azure.

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** > **Serveurs de production** > **Ajouter**.

    ![Ouvrir l’Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Dans la page **Assistant Ajout d’un serveur de production** > **Sélectionner un type de serveur de production**, sélectionnez **Serveurs VMware**, puis cliquez sur **Suivant**.

    ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Dans **Sélectionner les ordinateurs**  **Nom/adresse IP du serveur**, spécifiez le nom de domaine complet (FQDN) ou l’adresse P du serveur VMware. Spécifiez le nom vCenter si tous les serveurs ESXi sont gérés par le même serveur vCenter. Sinon, ajoutez l’hôte ESXi.

    ![Spécifier le serveur VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Dans **Port SSL**, entrez le port utilisé pour communiquer avec le serveur VMware. 443 est le port par défaut, mais vous pouvez le modifier si votre serveur VMware effectue l’écoute sur un port différent.

5. Dans **Indiquer les informations d’identification**, sélectionnez les informations d’identification que vous avez créées précédemment.

    ![Indiquer les informations d’identification](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Cliquez sur **Ajouter** pour ajouter le serveur VMware à la liste des serveurs. Cliquez ensuite sur **Suivant**.

    ![Ajouter un serveur VMware et des informations d’identification](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Dans la page **Résumé**, cliquez sur **Ajouter** pour ajouter le serveur VMware au serveur de sauvegarde Azure. Le nouveau serveur est ajouté immédiatement. Aucun agent n’est nécessaire sur le serveur VMware.

    ![Ajouter le serveur VMware au serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Vérifiez les paramètres dans la page **Terminer**.

   ![Page Terminer](./media/backup-azure-backup-server-vmware/summary-screen.png)

Si plusieurs hôtes ESXi ne sont pas gérés par le serveur vCenter, ou si vous avez plusieurs instances de vCenter Server, vous devez réexécuter l’Assistant pour ajouter les serveurs.

## <a name="configure-a-protection-group"></a>Configurer un groupe de protection

Ajoutez des machines virtuelles VMware pour la sauvegarde. Les groupes de protection rassemblent plusieurs machines virtuelles et appliquent les mêmes paramètres de sauvegarde et de conservation des données à toutes les machines virtuelles du groupe.

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Protection**, > **Nouveau**.

    ![Ouvrir l’Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Dans la page d’accueil de l’Assistant **Création d’un nouveau groupe de protection**, cliquez sur **Suivant**.

    ![Boîte de dialogue Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Dans la page **Sélectionner le type de groupe de protection**, sélectionnez **Serveurs**, puis cliquez sur **Suivant**. La page **Sélectionner les membres du groupe** s’affiche.

1. Dans **Sélectionner les membres du groupe**, sélectionnez les machines virtuelles (ou dossiers de machines virtuelles) que vous souhaitez sauvegarder. Cliquez ensuite sur **Suivant**.

    - Lorsque vous sélectionnez un dossier, les machines virtuelles ou dossiers à l’intérieur de ce dossier sont également sélectionnés pour la sauvegarde. Vous pouvez désactiver les dossiers ou machines virtuelles que vous ne souhaitez pas sauvegarder.
1. Si une machine virtuelle ou un dossier fait déjà l’objet d’une sauvegarde, vous ne pouvez pas le sélectionner. Cela garantit que des points de récupération ne sont pas créés en double pour la même machine virtuelle.

    ![Sélectionner les membres du groupe](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Dans la page **Sélectionner la méthode de protection des données**, entrez le nom du groupe de protection, et définissez les paramètres de protection. Pour sauvegarder sur Azure, définissez la protection à court terme sur **Disque** et activez la protection en ligne. Cliquez ensuite sur **Suivant**.

    ![Sélectionner la méthode de protection des données](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Dans **Spécifier les objectifs à court terme**, spécifiez la durée pendant laquelle vous souhaitez conserver les données sauvegardées sur disque.
   - Dans **Durée de rétention**, spécifiez le nombre de jours les points de récupération de disque doivent être conservés.
   - Dans **Fréquence de synchronisation**, spécifiez la fréquence d’enregistrement des points de récupération de disque.
       - Si vous ne souhaitez pas définir d’intervalle de sauvegarde, vous pouvez activer l’option **Juste avant un point de récupération** afin qu’une sauvegarde s’exécute juste avant chaque point de récupération planifié.
       - Les sauvegardes à court terme sont des sauvegardes complètes et non incrémentielles.
       - Cliquez sur **Modifier** pour modifier les heures/dates des sauvegardes à court terme.

         ![Spécifier les objectifs à court terme](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Dans **Vérifier l’allocation de disque**, vérifiez l’espace disque fourni pour les sauvegardes. de machines virtuelles.

   - Les allocations de disques recommandées dépendent de la durée de rétention spécifiée, du type de charge de travail et de la taille des données protégées. Apportez les modifications nécessaires, puis cliquez sur **Suivant**.
   - **Taille des données :** taille des données dans le groupe de protection.
   - **Espace disque :** quantité d’espace disque recommandée pour le groupe de protection. Pour modifier ce paramètre, vous devez allouer un espace total légèrement supérieur au volume de croissance estimé pour chaque source de données.
   - **Colocaliser des données :** si vous activez la colocalisation, plusieurs sources de données dans la protection peuvent être mappées à un seul réplica et à un seul volume de points de récupération. La colocalisation n’est pas prise en charge pour toutes les charges de travail.
   - **Augmenter automatiquement :** si vous activez ce paramètre et si les données contenues dans le groupe protégé dépassent l’allocation initiale, le serveur de sauvegarde Azure tentera d’augmenter la taille du disque de 25 %.
   - **Détails de pool de stockage :** affiche l’état du pool de stockage, notamment la taille totale et la taille de disque restante.

    ![Vérifier l’allocation de disque](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Dans **Choisir la méthode de création d’un réplica**, spécifiez la façon dont vous souhaitez effectuer la sauvegarde initiale, puis cliquez sur **Suivant**.
   - Les valeurs **Automatiquement sur le réseau** et **Maintenant** sont définies par défaut.
   - Si vous utilisez la valeur par défaut, nous vous recommandons de spécifier une heure creuse. Choisissez **Ultérieurement** et spécifiez le jour et l’heure.
   - Pour de grandes quantités de données ou des conditions réseau peu optimales, pensez à répliquer les données hors connexion à l’aide d’un média amovible.

    ![Choisir la méthode de création d’un réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Dans **Options de vérification de cohérence**, indiquez comment et quand automatiser les vérifications de cohérence. Cliquez ensuite sur **Suivant**.
      - Vous pouvez exécuter les vérifications de cohérence lorsque les données du réplica deviennent incohérentes ou en fonction d’une planification définie.
      - Si vous ne voulez pas configurer de vérifications de cohérence automatiques, vous pouvez exécuter une vérification manuelle. Pour ce faire, cliquez avec le bouton droit sur le groupe de protection > **Effectuer une vérification de cohérence**.

1. Dans **Spécifier les données de protection en ligne**, sélectionnez les machines virtuelles ou les dossiers de machines virtuelles à sauvegarder. Vous pouvez sélectionner les membres un à un ou cliquer sur **Sélectionner tout** pour choisir tous les membres. Cliquez ensuite sur **Suivant**.

    ![Spécifier les données de protection en ligne](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Dans la page **Spécifier la planification de sauvegarde en ligne**, spécifiez la fréquence à laquelle vous souhaitez sauvegarder les données du stockage local vers Azure.

    - Des points de récupération cloud des données seront générés en fonction du calendrier. Cliquez ensuite sur **Suivant**.
    - Le point de récupération généré est transféré vers le coffre Recovery Services dans Azure.

    ![Spécifier la planification de sauvegarde en ligne](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Dans la page **Spécifier la stratégie de rétention en ligne**, sélectionnez la durée pendant laquelle vous souhaitez conserver les points de récupération créés à partir des sauvegardes quotidiennes, hebdomadaires, mensuelles et annuelles dans Azure. Cliquez ensuite sur **Suivant**.

    - La durée de conservation des données dans Azure n’est soumise à aucune restriction.
    - La seule limite que vous devez respecter est que vous ne pouvez pas avoir plus de 9999 points de récupération par instance protégée. Dans cet exemple, l’instance protégée est le serveur VMware.

    ![Spécifier la stratégie de rétention en ligne](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Vérifiez les paramètres dans la page **Résumé**, puis cliquez sur **Créer un groupe**.

    ![Résumé des paramètres et des membres du groupe de protection](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Pour sauvegarder vSphere 6.7, effectuez les opérations suivantes :

- Activer TLS 1.2 sur le serveur DPM

>[!NOTE]
>Sur VMWare 6.7 et ultérieur, TLS est le protocole de communication activé.

- Définissez les clés de Registre comme suit :

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes lors de la configuration des sauvegardes, passez en revue le [guide de dépannage du serveur de sauvegarde Azure](./backup-azure-mabs-troubleshoot.md).
