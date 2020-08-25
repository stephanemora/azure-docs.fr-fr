---
title: Préparer des machines virtuelles Hyper-V pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles Hyper-V avec Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109618"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Préparer l’évaluation et la migration de machines virtuelles Hyper-V vers Azure

Cet article vous aide à préparer l’évaluation et la migration de machines virtuelles Hyper-V locales vers Azure en utilisant [Azure Migrate : Évaluation du serveur](migrate-services-overview.md#azure-migrate-server-assessment-tool) et [Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool).


Ce tutoriel est le premier d’une série qui montre comment évaluer et migrer des machines virtuelles Hyper-V vers Azure. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure pour qu’il fonctionne avec Azure Migrate.
> * Préparer l’évaluation de machines virtuelles Hyper-V.
> * Préparer la migration de machines virtuelles Hyper-V. 

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Le tableau récapitule les tâches que vous devez effectuer dans Azure. Des instructions sont fournies sous le tableau.

**Tâche** | **Détails** | **autorisations**
--- | --- | ---
**Créer un projet Azure Migrate** | Un projet Azure Migrate fournit un emplacement central pratique pour orchestrer et gérer les évaluations et les migrations à l’aide des outils Azure Migrate, des outils Microsoft et des outils tiers. | Votre compte Azure doit avoir des autorisations de Contributeur ou de Propriétaire dans le groupe de ressources où le projet réside.
**Inscrire l’appliance** | Azure Migrate utilise une appliance Azure Migrate légère pour détecter et évaluer les machines virtuelles Hyper-V. [Plus d’informations](migrate-appliance-architecture.md#appliance-registration) | Pour inscrire l’appliance, votre compte Azure doit avoir des autorisations de Contributeur ou de Propriétaire sur l’abonnement Azure.
**Créer une application Azure AD** | Lors de l’inscription de l’appliance, Azure Migrate crée une application Azure AD (Azure Active Directory). Celle-ci permet la communication entre les agents s’exécutant sur l’appliance et Azure Migrate. | Votre compte Azure doit être autorisé à créer des applications Azure AD.
**Créer une machine virtuelle** | Vous devez avoir les autorisations nécessaires pour créer une machine virtuelle dans le groupe de ressources et le réseau virtuel, et pour écrire sur un disque managé Azure. | Votre compte Azure doit avoir le rôle Contributeur de machines virtuelles.


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

Vérifiez que vous disposez des autorisations nécessaires pour créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Attribuer des autorisations pour créer des applications Azure AD

Vous pouvez attribuer des autorisations pour permettre à Azure Migrate de créer l’application Azure AD pendant l’inscription de l’appliance, à l’aide de l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

> [!NOTE]
> - L’application n’a aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
> - Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général/locataire peut accorder des autorisations comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

### <a name="assign-azure-account-permissions"></a>Affecter des autorisations de compte Azure

Affectez le rôle Contributeur de machines virtuelles au compte, ce qui vous permettra d’effectuer les tâches suivantes :

- Créer une machine virtuelle dans le groupe de ressources sélectionné
- Créer une machine virtuelle dans le réseau virtuel sélectionné
- Écrire sur un disque managé Azure. 


### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

[Configurez un réseau Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Les machines locales sont répliquées sur des disques managés Azure. Quand vous basculez vers Azure pour la migration, les machines virtuelles Azure sont créées à partir de ces disques managés, et elles sont jointes au réseau Azure que vous avez configuré.


## <a name="prepare-for-assessment"></a>Préparer pour l’évaluation

Vous pouvez préparer Hyper-V pour l’évaluation de machines virtuelles manuellement ou à l’aide d’un script de configuration. Voici les étapes de préparation. Si vous vous servez d’un script, ces étapes sont configurées automatiquement.

**Étape** | **Script** | **Manuel**
--- | --- | ---
**Vérifier la configuration requise pour l’hôte Hyper-V** | Le script vérifie que l’hôte exécute une version prise en charge d’Hyper-V et le rôle Hyper-V.<br/><br/> Active le service WinRM et ouvre les ports 5985 (HTTP) et 5986 (HTTPs) sur l’ordinateur hôte (nécessaire pour la collecte de métadonnées). | Vérifiez la [configuration requise de l’hôte Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) pour l’évaluation du serveur.<br/><br/> Vérifiez que les [ports requis](migrate-support-matrix-hyper-v.md#port-access) sont ouverts sur les hôtes Hyper-V.
**Vérifier la version de PowerShell** | Vérifie que vous exécutez le script sur une version de PowerShell prise en charge. | Vérifiez que vous exécutez PowerShell version 4.0 ou ultérieure sur l’hôte Hyper-V.
**Créer un compte** | Vérifie que vous (l’utilisateur exécutant le script) disposez de privilèges Administrateur sur l’hôte Hyper-V.<br/><br/>  Vous permet de créer un compte d’utilisateur (non d’administrateur) local utilisé par le service Azure Migrate pour communiquer avec l’hôte Hyper-V. Ce compte d’utilisateur est ajouté aux groupes suivants sur l’ordinateur hôte :<br/><br/> - Utilisateurs de gestion à distance<br/><br/> - Administrateurs Hyper-V<br/><br/>- Utilisateurs de l’Analyseur de performances | Configurez un compte d’utilisateur local ou de domaine avec des autorisations d’administrateur sur le cluster ou les hôtes Hyper-V.<br/><br/> - Vous avez besoin d’un compte unique pour tous les hôtes et clusters que vous souhaitez inclure dans la détection.<br/><br/> - Il peut s’agir d’un compte local ou de domaine. Nous vous recommandons de faire en sorte qu’il dispose d’autorisations d’administrateur sur les hôtes Hyper-V ou les clusters.<br/><br/> Autrement, si vous ne souhaitez pas affecter d’autorisations d’administrateur, les autorisations suivantes sont nécessaires : Utilisateurs de gestion à distance ; Administrateurs Hyper-V ; Utilisateurs de l’Analyseur de performances.
**Activer la communication à distance de PowerShell** | Active la communication à distance de PowerShell sur l’hôte, ce qui permet à l’appliance Azure Migrate d’exécuter des commandes PowerShell sur l’hôte, par le biais d’une connexion WinRM.| Pour la configurer, sur chaque hôte, ouvrez une console PowerShell en tant qu’administrateur, puis exécutez cette commande :<br/><br/>``` Enable-PSRemoting -force ```
**Configurer Hyper-V Integration Services** | Vérifie que les services d’intégration Hyper-V sont activés sur toutes les machines virtuelles gérées par l’hôte. |  [Activez Hyper-V Integration Services](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) sur chaque machine virtuelle.<br/><br/> Si vous utilisez Windows Server 2003, [suivez ces instructions](prepare-windows-server-2003-migration.md).
**Déléguer les informations d’identification si les disques de machine virtuelle se trouvent sur des partages SMB distants** | Le script délègue les informations d’identification. | [Activez CredSSP](#enable-credssp-to-delegate-credentials) pour déléguer les informations d’identification.

### <a name="run-the-script"></a>Exécuter le script

Exécutez le script comme suit :

1. Assurez-vous que PowerShell version 4.0 ou ultérieure est installé sur l’hôte Hyper-V.
2. Téléchargez le script à partir du [Centre de téléchargement Microsoft](https://aka.ms/migrate/script/hyperv). Le script est signé par chiffrement par Microsoft.
3. Validez l’intégrité du script à l’aide de fichiers de hachage MD5 ou SHA256. Les valeurs de code de hachage sont indiquées ci-dessous. Pour générer le hachage pour le script, exécutez la commande suivante :
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemple d’utilisation :
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Après avoir validé l’intégrité du script, exécutez-le sur chaque hôte Hyper-V à l’aide de la commande PowerShell suivante :
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valeurs de code de hachage

Les valeurs de hachage sont les suivantes :

| **Code de hachage** | **Valeur** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Activer CredSSP pour déléguer les informations d’identification

Si l’hôte comprend des machines virtuelles dotées de disques situés sur des partages SMB, effectuez cette étape sur l’hôte.

- Vous pouvez exécuter cette commande à distance sur tous les hôtes Hyper-V.
- Si vous ajoutez de nouveaux nœuds hôtes sur un cluster, ils sont automatiquement ajoutés pour la découverte, mais vous devez activer manuellement CredSSP sur les nouveaux nœuds si nécessaire.

Pour opérer l’activation, procédez comme suit :

1. Identifiez les hôtes Hyper-V exécutant des machines virtuelles Hyper-V avec des disques sur des partages SMB.
2. Exécutez la commande suivante sur chaque hôte Hyper-V identifié :

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Quand vous configurez l’appliance, vous terminez la configuration de CredSSP en [l’activant sur l’appliance](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Cela est décrit dans le didacticiel suivant de cette série.


## <a name="prepare-for-appliance-deployment"></a>Préparer le déploiement de l’appliance

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-appliance.md#appliance---hyper-v) la configuration requise de l’appliance.
2. Passez en revue les URL Azure auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls). Si vous utilisez un pare-feu ou un proxy basé sur des URL, vérifiez qu’il autorise l’accès aux URL nécessaires.
3. Passez en revue les données que l’appliance va collecter pendant la découverte et l’évaluation.
4. [Passez en revue](migrate-appliance.md#collected-data---hyper-v) les conditions d’accès aux ports pour l’appliance.


## <a name="prepare-for-hyper-v-migration"></a>Préparer la migration Hyper-V

1. [Passez en revue](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) la configuration requise de l’hôte Hyper-V pour la migration et les URL Azure auxquelles les hôtes et les clusters Hyper-V ont besoin d’accéder pour la migration de machine virtuelle.
2. [Passez en revue](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) la configuration requise des machines virtuelles Hyper-V que vous souhaitez migrer vers Azure.
3. Certaines modifications doivent être apportées aux machines virtuelles afin qu’elles puissent être migrées vers Azure.
    - Il est important d’effectuer ces modifications avant de commencer la migration. Si vous migrez la machine virtuelle avant d’effectuer la modification, elle risque de ne pas démarrer dans Azure.
    - Examinez les modifications à apporter aux machines [Windows](prepare-for-migration.md#windows-machines) et [Linux](prepare-for-migration.md#linux-machines).



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations de compte Azure.
> * Préparé les machines virtuelles et hôtes Hyper-V à l’évaluation et à la migration.
> * Préparé le déploiement de l’appliance Azure Migrate.

Passez au tutoriel suivant pour créer un projet Azure Migrate, déployer l’appliance, et détecter et évaluer les machines virtuelles Hyper-V à migrer vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md)
