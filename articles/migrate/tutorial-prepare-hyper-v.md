---
title: Préparer des machines virtuelles Hyper-V pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles Hyper-V avec Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 78e8a42c4f1e101f8d083c8d58bb452aadfa3a87
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454565"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Préparer l’évaluation et la migration de machines virtuelles Hyper-V vers Azure

Cet article explique comment préparer l’évaluation et la migration de machines virtuelles Hyper-V locales vers Azure avec [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.

Ce tutoriel est le premier d’une série qui montre comment évaluer et migrer des machines virtuelles Hyper-V vers Azure. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure. Configurez des autorisations pour permettre à votre compte et vos ressources Azure de fonctionner avec Azure Migrate.
> * Préparer les machines virtuelles et les hôtes Hyper-V locaux pour l’évaluation des serveurs
> * Préparer les machines virtuelles et les hôtes Hyper-V locaux pour la migration des serveurs


> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration d’Hyper-V.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

### <a name="azure-permissions"></a>Autorisations Azure

Vous devez définir des autorisations pour le déploiement Azure Migrate.

- Autorisations pour permettre à votre compte Azure de créer un projet Azure Migrate.
- Autorisations pour permettre à votre compte d’inscrire l’appliance Azure Migrate. L’appliance est utilisée pour la détection et la migration Hyper-V. Lors de l’inscription de l’appliance, Azure Migrate crée deux applications Azure Active Directory (Azure AD) qui identifient de façon unique l’appliance :
    - La première application communique avec les points de terminaison de service Azure Migrate.
    - La seconde application accède à un coffre de clés Azure Key Vault créé pendant l’inscription pour stocker les informations Azure AD App et les paramètres de configuration de l’appliance.



### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

Vérifiez que vous disposez des autorisations nécessaires pour créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Vous pouvez affecter des autorisations pour permettre à Azure Migrate de créer les applications Azure AD créées pendant l’inscription d’appliance, à l’aide de l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général/locataire peut accorder des autorisations comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-for-hyper-v-assessment"></a>Préparer l’évaluation d’Hyper-V

Pour préparer l’évaluation d’Hyper-V, procédez comme suit :

1. Vérifiez les paramètres de l’hôte Hyper-V.
2. Configurez la communication à distance PowerShell sur chaque hôte, afin que l’appliance Azure Migrate puisse exécuter des commandes PowerShell sur l’hôte, via une connexion WinRM.
3. Si les disques de machine virtuelle se trouvent dans un stockage SMB distant, une délégation des informations d’identification est nécessaire.
    - Activez la délégation CredSSP afin que l’appliance Azure Migrate puisse agir en tant que client, en déléguant les informations d’identification à un hôte.
    - Vous permettez à chaque hôte d’agir en tant que délégué pour l’appliance, comme décrit ci-dessous.
    - Plus tard, quand vous configurerez l’appliance, vous activerez la délégation sur l’appliance.
4. Examinez la configuration requise de l’appliance, et l’accès à l’URL ou au port nécessaire pour celle-ci.
5. Créez un compte que l’appliance puisse utiliser pour découvrir les machines virtuelles.
6. Configurez les Services d’intégration Hyper-V sur chaque machine virtuelle que vous souhaitez découvrir et évaluer.


Vous pouvez configurer ces paramètres manuellement en suivant les procédures ci-dessous. Vous pouvez également exécuter le script de configuration des composants requis Hyper-V.

### <a name="hyper-v-prerequisites-configuration-script"></a>Script de configuration des composants requis Hyper-V

Le script valide les hôtes Hyper-V et configure les paramètres dont vous avez besoin pour découvrir et évaluer les machines virtuelles Hyper-V. Voici ce qu’elle fait :

- Vérifie que vous exécutez le script sur une version de PowerShell prise en charge.
- Vérifie que vous (l’utilisateur exécutant le script) disposez de privilèges Administrateur sur l’hôte Hyper-V.
- Vous permet de créer un compte d’utilisateur (non d’administrateur) local utilisé pour permettre au service Azure Migrate de communiquer avec l’hôte Hyper-V. Ce compte d’utilisateur est ajouté aux groupes suivants sur l’ordinateur hôte :
    - Utilisateurs de gestion à distance
    - Administrateurs Hyper-V
    - Utilisateurs de l’Analyseur de performances
- Vérifie que l’hôte exécute une version prise en charge d’Hyper-V et le rôle Hyper-V.
- Active le service WinRM et ouvre les ports 5985 (HTTP) et 5986 (HTTPs) sur l’ordinateur hôte (nécessaire pour la collecte de métadonnées).
- Active la communication à distance PowerShell sur l’hôte.
- Vérifie que le service d’intégration Hyper-V est activé sur toutes les machines virtuelles gérées par l’hôte.
- Active CredSSP sur l’hôte si nécessaire.

Exécutez le script comme suit :

1. Assurez-vous que PowerShell version 4.0 ou ultérieure est installé sur l’hôte Hyper-V.
2. Téléchargez le script à partir du [Centre de téléchargement Microsoft](https://aka.ms/migrate/script/hyperv). Le script est signé par chiffrement par Microsoft.
3. Validez l’intégrité du script à l’aide de fichiers de hachage MD5 ou SHA256. Les valeurs de code de hachage sont indiquées ci-dessous. Pour générer le hachage pour le script, exécutez la commande suivante :
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemple d’utilisation :
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Après avoir validé l’intégrité du script, exécutez-le sur chaque hôte Hyper-V à l’aide de la commande PowerShell suivante :
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valeurs de code de hachage

Les valeurs de hachage sont les suivantes :

| **Code de hachage** | **Valeur** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Vérifier les paramètres de l’hôte Hyper-V

1. Vérifiez la [configuration requise de l’hôte Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) pour l’évaluation du serveur.
2. Vérifiez que les [ports requis](migrate-support-matrix-hyper-v.md#assessment-port-requirements) sont ouverts sur les hôtes Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Activer la communication à distance PowerShell sur les hôtes

Configurez la communication à distance PowerShell sur chaque hôte, comme suit :

1. Sur chaque hôte, ouvrez une console PowerShell en tant qu’administrateur.
2. Exécutez cette commande :

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Activer CredSSP sur les hôtes

Si l’hôte comprend des machines virtuelles dotées de disques situés sur des partages SMB, effectuez cette étape sur l’hôte.

- Vous pouvez exécuter cette commande à distance sur tous les hôtes Hyper-V.
- Si vous ajoutez de nouveaux nœuds hôtes sur un cluster, ils sont automatiquement ajoutés pour la découverte, mais vous devez activer manuellement CredSSP sur les nouveaux nœuds si nécessaire.

Pour opérer l’activation, procédez comme suit :

1. Identifiez les hôtes Hyper-V exécutant des machines virtuelles Hyper-V avec des disques sur des partages SMB.
2. Exécutez la commande suivante sur chaque hôte Hyper-V identifié :

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Quand vous configurez l’appliance, vous terminez la configuration de CredSSP [en l'activant sur l’appliance](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Cela est décrit dans le didacticiel suivant de cette série.


### <a name="verify-appliance-settings"></a>Vérifier les paramètres de l’appliance

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) la configuration requise de l’appliance.
2. [Passez en revue](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) les URL Azure auxquelles l’appliance doit accéder.
3. Passez en revue les données que l’appliance va collecter pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-hyper-v.md#assessment-port-requirements) les conditions d’accès aux ports pour l’appliance.


### <a name="set-up-an-account-for-vm-discovery"></a>Configurer un compte pour la découverte de machine virtuelle

Azure Migrate a besoin d’autorisations pour découvrir les machines virtuelles locales.

- Configurez un compte d’utilisateur local ou de domaine avec des autorisations d’administrateur sur le cluster ou les hôtes Hyper-V.

    - Vous avez besoin d’un compte unique pour tous les hôtes et clusters que vous souhaitez inclure dans la découverte.
    - Il peut s’agir d’un compte local ou de domaine. Nous vous recommandons de faire en sorte qu’il dispose d’autorisations d’administrateur sur les hôtes Hyper-V ou les clusters.
    - Autrement, si vous ne souhaitez pas affecter d’autorisations d’administrateur, les autorisations suivantes sont nécessaires :
        - Utilisateurs de gestion à distance
        - Administrateurs Hyper-V
        - Utilisateurs de l’Analyseur de performances

### <a name="enable-integration-services-on-vms"></a>Activer les services d’intégration sur les machines virtuelles

Les services d’intégration doivent être activés sur chaque machine virtuelle afin qu’Azure Migrate puisse capturer les informations du système d’exploitation sur la machine virtuelle.

Activez les [services d’intégration Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) sur chaque machine virtuelle que vous souhaitez découvrir et évaluer.

## <a name="prepare-for-hyper-v-migration"></a>Préparer la migration Hyper-V

1. [Passez en revue](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) la configuration requise de l’hôte Hyper-V pour la migration.
2. [Passez en revue](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) la configuration requise des machines virtuelles Hyper-V que vous souhaitez migrer vers Azure.
3. [Notez](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) les URL Azure auxquelles les hôtes et les clusters Hyper-V ont besoin d’accéder pour la migration de machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations de compte Azure.
> * Préparé les machines virtuelles et hôtes Hyper-V à l’évaluation et à la migration.

Passez au tutoriel suivant pour créer un projet Azure Migrate et évaluer les machines virtuelles Hyper-V à migrer vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md)
