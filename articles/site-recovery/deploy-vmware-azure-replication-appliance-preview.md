---
title: Déployer une appliance de réplication Azure Site Recovery - Préversion
description: Cet article décrit le support et la configuration requise lors du déploiement de l’appliance de réplication pour la récupération d’urgence de VMware sur Azure avec Azure Site Recovery - Préversion
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: e4021aa0f5572a51ca4d3ddda37f64f4da46a3b4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535350"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>Déployer une appliance de réplication Azure Site Recovery - Préversion

>[!NOTE]
> Les informations contenues dans cet article s’appliquent à Azure Site Recovery - Préversion. Pour plus d’informations sur la configuration requise pour le serveur de configuration dans les versions Classic, [consultez cet article](vmware-azure-configuration-server-requirements.md).

>[!NOTE]
> Assurez-vous de créer un coffre Recovery Services pour configurer l’appliance de préversion. N’utilisez pas un coffre existant.

Vous devez déployer une appliance de réplication locale quand vous utilisez [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure.

- L’appliance de réplication coordonne les communications entre les machines locales VMware et Azure. Il gère également la réplication des données.
- [En savoir plus](vmware-azure-architecture-preview.md) sur les composants et processus de l’appliance de réplication Azure Site Recovery.

## <a name="hardware-requirements"></a>Configuration matérielle requise

**Composant** | **Prérequis**
--- | ---
Cœurs d’unité centrale | 8
RAM | 32 Go
Nombre de disques | 3, y compris le disque du système d’exploitation - 80 Go, disque de données 1 - 620 Go, disque de données 2 - 620 Go

## <a name="software-requirements"></a>Configuration logicielle requise

**Composant** | **Prérequis**
--- | ---
Système d’exploitation | Windows Server 2016
Paramètres régionaux du système d’exploitation | Anglais (en-*)
Rôles Windows Server | N’activez pas ces rôles : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Stratégies de groupe | N’activez pas ces stratégies de groupe : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Aucun site web par défaut préexistant <br> - Aucune application/aucun site web préexistants ne doivent écouter le port 443 <br>- Activer [l’authentification anonyme](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Activer le paramètre [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
FIPS (Federal Information Processing Standard) | Ne pas activer le mode FIPS|

## <a name="network-requirements"></a>Configuration requise pour le réseau

|**Composant** | **Prérequis**|
|--- | ---|
|Type d’adresse IP | statique|
|Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données)|
|Type de carte réseau | VMXNET3 (si l’appliance est une machine virtuelle VMware)|


### <a name="allow-urls"></a>Autoriser les URL

Assurez-vous que les URL suivantes sont autorisées et accessibles à partir de l’appliance de réplication Azure Site Recovery pour la connectivité continue :

  | **URL**                  | **Détails**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Accédez au portail Azure.              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Pour vous connecter à votre abonnement Azure.  |
  |`*.microsoftonline.com `|Créez des applications Azure Active Directory (AD) pour que l’appliance communique avec Azure Site Recovery. |
  |management.azure.com |Créez des applications Azure AD pour que l’appliance communique avec le service Azure Azure Site Recovery. |
  |`*.services.visualstudio.com `|Chargez les journaux d’applications utilisés pour la supervision interne. |
  |`*.vault.azure.net `|Gérez les secrets dans Azure Key Vault. Remarque : Vérifiez que les machines à répliquer y ont accès. |
  |aka.ms |Autorisez l’accès aux liens. Utilisé pour les mises à jour de l’appliance Azure Site Recovery. |
  |download.microsoft.com/download |Autoriser les téléchargements à partir du téléchargement Microsoft. |
  |`*.servicebus.windows.net `|Communication entre l’appliance et le service Azure Site Recovery. |
  |`*.discoverysrv.windowsazure.com `|Se connecter à l’URL du service de découverte Azure Site Recovery. |
  |`*.hypervrecoverymanager.windowsazure.com `|Se connecter aux URL du micro-service Azure Site Recovery.  |
  |`*.blob.core.windows.net `|Télécharger des données vers le stockage Azure qui est utilisé pour créer des disques cibles |
  |`*.backup.windowsazure.com `|URL du service de protection : microservice utilisé par Azure Site Recovery pour le traitement et la création de disques répliqués dans Azure |

> [!NOTE]
> Les liens privés ne sont pas pris en charge avec la version préliminaire.

## <a name="prepare-azure-account"></a>Préparer le compte Azure

Pour créer et inscrire l’appliance de réplication Azure Site Recovery, vous avez besoin d’un compte Azure avec :

- Des autorisations de niveau Contributeur ou Propriétaire sur l’abonnement Azure.
- Des autorisations permettant d’inscrire des applications Azure Active Directory (AAD).
- Des autorisations de niveau Contributeur ou Propriétaire, ainsi que des autorisations d’administrateur d’accès utilisateur sur l’abonnement Azure pour créer un coffre de clés, utilisé lors de l’inscription de l’appliance de réplication Azure Site Recovery avec Azure.

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous octroyer les autorisations nécessaires.

## <a name="prerequisites"></a>Configuration requise

**Les autorisations requises pour le coffre de clés sont les suivantes** :

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**Procédez comme suit pour attribuer les autorisations requises** :

1. Dans le portail Microsoft Azure, recherchez **Abonnements**, puis sous **Services**, sélectionnez le champ de recherche **Abonnements** pour rechercher l’abonnement Azure.

2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer le coffre Recovery Services.

3. Dans l’abonnement, sélectionnez **Contrôle d’accès** (IAM) > **Vérifier l’accès**. Dans **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.

4. Dans **Ajouter une attribution de rôle**, sélectionnez **Ajouter**, le rôle Contributeur ou Propriétaire, puis le compte. Sélectionnez ensuite **Enregistrer**.

  Pour inscrire l’appliance, votre compte Azure doit disposer d’autorisations pour inscrire des applications AAD.

  **Procédez comme suit pour attribuer les autorisations requises** :

  - Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur *Oui* par défaut).

  - Si les paramètres **Inscriptions d’applications** ont la valeur *Non*, demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général/le locataire peuvent également attribuer le rôle Développeur d’applications à un compte pour permettre l’inscription d’une application AAD.


## <a name="prepare-infrastructure"></a>Préparer l’infrastructure

Vous devez configurer une appliance de réplication Azure Site Recovery dans l’environnement local pour permettre la récupération sur votre ordinateur local. Pour en savoir plus sur les opérations effectuées par l’appliance, [consultez cette section](vmware-azure-architecture-preview.md)

Accédez à **Coffres Recovery Services** > **Démarrage**. Dans les machines VMware sur Azure, sélectionnez **Préparer l’infrastructure** et poursuivez avec les sections détaillées ci-dessous :

![Préversion du coffre Recovery Services](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

Pour configurer un nouvel appareil, vous pouvez utiliser un modèle OVF (recommandé) ou PowerShell. Assurez-vous que vous disposez des configurations requises pour le [matériel](#hardware-requirements) et les [logiciels](#software-requirements), ainsi que pour tous les autres prérequis.

## <a name="create-azure-site-recovery-replication-appliance"></a>Créer une appliance de réplication Azure Site Recovery

Vous pouvez créer l’appliance de réplication Site Recovery à l’aide du modèle OVF ou via PowerShell.

>[!NOTE]
> La configuration de l’appliance doit être effectuée de façon séquentielle. L’inscription parallèle de plusieurs appliances ne peut pas être effectuée.


### <a name="create-replication-appliance-through-ovf-template"></a>Créer une appliance de réplication via le modèle OVF

Nous vous recommandons d’utiliser cette approche car Azure Site Recovery s’assure que toutes les configurations requises sont gérées par le modèle.
Le modèle OVF tourne sur une machine avec les spécifications requises.

![Préparer l’infrastructure pour la création d’appliances](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**Suivez ces étapes :**

1. Téléchargez le modèle OVF pour configurer une appliance sur votre environnement local.
2. Une fois le déploiement terminé, mettez sous tension la machine virtuelle de l’appliance pour accepter la licence d’évaluation Microsoft.
3. Dans l’écran suivant, donnez un mot de passe à l’utilisateur administrateur.
4. Sélectionnez **Finaliser**, le système redémarre et vous pouvez vous connecter avec le compte d'utilisateur d’administrateur.

### <a name="set-up-the-appliance-through-powershell"></a>Configurer l’appliance via PowerShell

En cas de restrictions organisationnelles, vous pouvez configurer manuellement l’appliance de réplication Site Recovery par le biais de PowerShell. Procédez comme suit :

1. Téléchargez les programmes d'installation à partir d’[ici](https://aka.ms/V2ARcmApplianceCreationPowershellZip) et placez ce dossier sur l’appliance de réplication Azure Site Recovery.
2. Après avoir correctement copié le dossier zip, décompressez et extrayez les composants du dossier.
3. Allez au chemin d’accès dans lequel le dossier est extrait et exécutez le script PowerShell suivant en tant qu’administrateur :

    **DRInstaller.ps1**  

## <a name="register-appliance"></a>Inscrire l’appliance
  Une fois l’appliance créée, le gestionnaire de configuration de l’appliance Microsoft Azure est lancé automatiquement. Les conditions préalables telles que la connectivité Internet, la synchronisation de l’heure, les configurations système et les stratégies de groupe (répertoriées ci-dessous) sont validées.

  - CheckRegistryAccessPolicy : empêche l’accès aux outils de modification du Registre.
      - Clé : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - La valeur de DisableRegistryTools ne doit pas être égale à 0.

  - CheckCommandPromptPolicy : empêche l’accès à l’invite de commande.

      - Clé : HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - La valeur de DisableCMD ne doit pas être égale à 0.

  - CheckTrustLogicAttachmentsPolicy : logique de confiance pour les fichiers joints.

      - Clé : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - La valeur de UseTrustedHandlers ne doit pas être égale à 3.

  - CheckPowershellExecutionPolicy : active l’exécution des scripts.

      - La stratégie d’exécution de PowerShell ne doit pas être AllSigned ou Restricted
      - Vérifiez que la stratégie de groupe « Activer le gestionnaire des pièces jointes d’exécution de script » n’est pas définie sur Désactivé ou sur « Autoriser uniquement les scripts signés »


  **Utilisez les étapes suivantes pour inscrire l’appliance** :

1. Configurez les paramètres proxy en basculant sur l’option **Utiliser le proxy pour se connecter à Internet**.

    Tous les services Azure Site Recovery utilisent ces paramètres pour se connecter à Internet. Seuls les proxys HTTP sont pris en charge.

2. Assurez-vous que les [URL requises](#allow-urls) sont autorisées et accessibles à partir de l’appliance de réplication Azure Site Recovery pour la connectivité continue.

3. Une fois les conditions préalables vérifiées, dans l’étape suivante, les informations sur tous les composants de l’appliance sont extraites. Passez en revue l’état de tous les composants, puis cliquez sur **Continuer**. Après avoir enregistré les informations, choisissez la connectivité de l’appliance.

4. Après avoir enregistré les informations de connectivité, sélectionnez **Continuer** pour effectuer l’inscription avec Microsoft Azure.

5. Assurez-vous que les [conditions préalables](#prerequisites) sont remplies, puis procédez à l'inscription.

    ![Inscrire l’appliance](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **Nom convivial de l’appliance** : indiquez un nom convivial avec lequel vous souhaitez suivre cette appliance dans le portail Azure sous infrastructure du coffre Recovery Services.

  - **Clé de l’appliance de réplication Azure Site Recovery** : copiez la clé du portail en accédant à **Coffre Recovery Services** > **Démarrage** > **Préparer l’infrastructure VMware vers Azure**.

  - Après avoir collé la clé, sélectionnez **Se connecter.**
    Vous serez redirigé vers un nouvel onglet d’authentification.

      Par défaut, un code d’authentification est généré comme indiqué ci-dessous, dans la page Gestionnaire d’authentification. Utilisez le code sous l’onglet d’authentification.

  - Entrez vos informations d’identification de Microsoft Azure pour terminer l’inscription.

      Une fois l’inscription terminée, vous pouvez fermer l’onglet et passer au Gestionnaire de configuration pour poursuivre la configuration.

      ![Code d’authentification](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > Un code d’authentification expire dans les 5 minutes après sa création. En cas d’inactivité pendant une période supérieure à celle-ci, vous êtes invité à vous reconnecter à Azure.


6. Sélectionnez **Se connecter** pour vous reconnecter à la session. Pour le code d’authentification, consultez la section *Résumé* ou *Enregistrer un coffre Azure Recovery Services* dans le Gestionnaire de configuration.

7. Une fois la connexion établie, l’abonnement, le groupe de ressources et les détails du coffre Recovery Services s’affichent. Vous pouvez vous déconnecter au cas où vous souhaiteriez modifier le coffre. Sinon, sélectionnez **Continuer** pour poursuivre.

    ![Appliance inscrite](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    Une fois l’inscription réussie, passez à la configuration des détails de vCenter.

    ![Configuration de vCenter](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. Sélectionnez **Ajouter un serveur vCenter** pour ajouter des informations sur vCenter. Entrez le nom du serveur ou l’adresse IP des informations sur vCenter et sur le port. Après cela, indiquez le nom d’utilisateur, le mot de passe et le nom convivial utilisés pour extraire les détails de la [machine virtuelle gérée par le biais du vCenter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery). Les détails du compte d’utilisateur seront chiffrés et stockés localement sur l’ordinateur.

>[!NOTE]
> Si vous essayez d’ajouter le même vCenter Server à plusieurs appliances, assurez-vous que le même nom convivial est utilisé dans les deux appliances.

9. Après avoir enregistré les informations sur vCenter, sélectionnez **Ajouter les informations d’identification de la machine virtuelle** pour fournir les détails de l’utilisateur des machines virtuelles découvertes via le vCenter.

   >[!NOTE]
   > - Pour le système d’exploitation Linux, assurez-vous de fournir des informations d’identification racine et, pour le système d’exploitation Windows, un compte d’utilisateur avec des privilèges d’administrateur doit être ajouté, ces informations d’identification seront utilisées pour pousser l’agent de mobilité sur la machine virtuelle source pendant l’opération d’activation de la réplication. Les informations d’identification peuvent être choisies par machine virtuelle dans le portail Azure lors de l’activation du flux de travail de réplication.
   > - Accédez à l’outil de configuration de l’appliance pour modifier ou ajouter des informations d’identification afin d’accéder à vos ordinateurs.

10. Après avoir ajouté les détails, sélectionnez **Continuer** pour installer tous les composants de l’appliance de réplication Azure Site Recovery et inscrivez-vous auprès des services Azure. Cette activité peut prendre jusqu’à 30 minutes.

    Veillez à ne pas fermer le navigateur pendant que la configuration est en cours.


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>Afficher l’appliance de réplication Azure Site Recovery dans le portail Azure

Une fois la configuration de l’appliance de réplication Azure Site Recovery terminée, accédez au portail Azure **Coffre Recovery Services**.

Sélectionnez **Préparer l’infrastructure (Préversion)** sous **Démarrage**, vous pouvez voir que l’appliance de réplication Azure Site Recovery est déjà inscrite avec ce coffre. Vous avez terminé ! Commencez à protéger vos machines sources par le biais de cette appliance de réplication.

Lorsque vous cliquez sur *Sélectionner 1 appliance*, vous êtes redirigé vers la vue Appliance de réplication Azure Site Recovery où s’affiche la liste des appliances inscrites dans ce coffre.

Vous pouvez également voir un onglet pour les **éléments découverts** qui répertorie tous les serveurs vCenter/hôtes vSphere détectés.

![Préversion de l’appliance de réplication](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>Taille et capacité
Une appliance qui utilise un serveur de processus intégré pour protéger la charge de travail peut gérer jusqu’à 200 machines virtuelles en fonction des configurations suivantes :

  |UC |    Mémoire |    Taille du disque cache |    Taux de modification des données |    Ordinateurs protégés |
  |---|-------|--------|------|-------|
  |16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz)    | 32 Go |    1 To |    >1 To à 2 To    | Permet de répliquer de 151 à 200 machines.|

- Vous pouvez procéder à la découverte de tous les ordinateurs d’un serveur vCenter, à l’aide de l’une des appliances de réplication du coffre.

- Vous pouvez [basculer un ordinateur protégé](switch-replication-appliance-preview.md) entre différentes appliances dans le même coffre, étant donné que l’appliance sélectionnée est saine.

Pour plus d’informations sur l’utilisation de plusieurs appliances et le basculement d’une appliance de réplication, consultez cet [article](switch-replication-appliance-preview.md)

## <a name="next-steps"></a>Étapes suivantes
Configurez la récupération d’urgence des [machines virtuelles VMware](vmware-azure-tutorial.md) vers Azure.
