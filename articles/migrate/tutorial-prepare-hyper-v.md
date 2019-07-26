---
title: Préparer des machines virtuelles Hyper-V pour l’évaluation et la migration vers Azure avec Azure Migrate | Microsoft Docs
description: Décrit comment préparer l’évaluation et la migration des machines virtuelles Hyper-V vers Azure à l’aide d’Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840368"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Préparer l’évaluation et la migration de machines virtuelles Hyper-V vers Azure

Cet article explique comment préparer l’évaluation et la migration de machines virtuelles Hyper-V locales vers Azure avec [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres de fournisseurs de logiciels indépendants tiers. 

Ce tutoriel est le premier d’une série qui montre comment évaluer et migrer des machines virtuelles Hyper-V vers Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer Azure Configurer les autorisations afin que votre compte Azure et les ressources fonctionnent avec Azure Migrate
> * Préparer les machines virtuelles et les hôtes Hyper-V locaux pour l’évaluation des serveurs
> * Préparer les machines virtuelles et les hôtes Hyper-V locaux pour la migration des serveurs


> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné, afin de vous permettre de configurer rapidement une preuve de concept. Ils utilisent des options par défaut dans la mesure du possible, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration d’Hyper-V.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

### <a name="azure-permissions"></a>Autorisations Azure

Vous devez disposer de deux autorisations pour déployer Azure Migrate :

- Votre compte Azure doit être autorisé à créer un projet Azure Migrate à des fins d’évaluation et de migration. 
- Votre compte Azure doit être autorisé à inscrire l’appliance Azure Migrate.
    - Pour l’évaluation, Azure Migrate exécute une appliance légère qui découvre les machines virtuelles Hyper-V et envoie les métadonnées et les données de performances de la machine virtuelle à Azure Migrate.
    - Lors de l’inscription de l’appliance, Azure Migrate crée deux applications Azure Active Directory (Azure AD) qui identifient de façon unique l’appliance :
        - La première application communique avec les points de terminaison de service Azure Migrate.
        - La deuxième application accède à un coffre de clés Azure créé pendant l’inscription pour stocker les informations sur l’application Azure AD et les paramètres de configuration d’appliance.
    - Vous pouvez affecter des autorisations à Azure Migrate pour créer ces applications Azure AD en appliquant l’une des méthodes suivantes:
        - Un administrateur général/locataire peut accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
        - Un administrateur général/locataire peut affecter au compte le rôle Développeur d’applications (qui dispose des autorisations).
    - Il est intéressant de noter que :
        - Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
        - Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations une fois l’appliance configurée. 


### <a name="assign-permissions-to-create-project"></a>Affecter des autorisations pour créer un projet

Vérifiez que vous disposez des autorisations nécessaires pour créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour afficher les autorisations.
3. Vous devez disposer d’autorisations **Contributeur** ou **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Si vous déployez l’appliance Azure Migrate pour évaluer des machines virtuelles, vous devez l’inscrire.

- Lors de l’inscription de l’appliance, Azure Migrate crée deux applications Azure Active Directory (Azure AD) qui identifient de façon unique l’appliance :
    - La première application communique avec les points de terminaison de service Azure Migrate.
    - La deuxième application accède à un coffre de clés Azure créé pendant l’inscription pour stocker les informations sur l’application Azure AD et les paramètres de configuration d’appliance.
- Vous pouvez affecter des autorisations à Azure Migrate pour créer ces applications Azure AD en appliquant l’une des méthodes suivantes:
    - Un administrateur général/locataire peut accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
    - Un administrateur général/locataire peut affecter au compte le rôle Développeur d’applications (qui dispose des autorisations).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations une fois l’appliance configurée. 


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général/locataire peut accorder des autorisations comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications 

Un administrateur général/locataire peut attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-for-hyper-v-assessment"></a>Préparer l’évaluation d’Hyper-V

Pour préparer l’évaluation d’Hyper-V, vérifiez les paramètres de machine virtuelle et d’hôte Hyper-V, puis vérifiez les paramètres de déploiement de l’appliance.

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

Si des disques de machine virtuelle se trouvent sur des partages SMB, effectuez cette étape sur chaque hôte Hyper-V concerné. Cette étape sert à découvrir les informations de configuration des machines virtuelles Hyper-V ayant des disques sur des partages SMB. Si vous n’avez pas de disques de machine virtuelle sur des partages SMB, vous pouvez ignorer cette étape.

1. Identifiez les hôtes Hyper-V exécutant des machines virtuelles Hyper-V avec des disques sur des partages SMB.
2. Exécutez la commande suivante sur chaque hôte Hyper-V identifié :

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- L’authentification CredSSP permet à l’hôte Hyper-V de déléguer des informations d’identification pour le compte du client Azure Migrate.
- Vous pouvez exécuter cette commande à distance sur tous les hôtes Hyper-V.
- Si vous ajoutez de nouveaux nœuds hôtes sur un cluster, ils sont automatiquement ajoutés pour la découverte, mais vous devez activer manuellement CredSSP sur les nouveaux nœuds si nécessaire.

### <a name="verify-appliance-settings"></a>Vérifier les paramètres de l’appliance

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le tutoriel suivant, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) la configuration requise de l’appliance.
2. [Passez en revue](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) les URL Azure auxquelles l’appliance devra accéder.
3. Passez en revue les données que l’appliance recueillera pendant la découverte et l’évaluation.
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

- Activez les [services d’intégration Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) sur chaque machine virtuelle que vous souhaitez découvrir et évaluer. 

## <a name="prepare-for-hyper-v-migration"></a>Préparer la migration Hyper-V

1. [Passez en revue](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) la configuration requise de l’hôte Hyper-V pour la migration.
2. [Passez en revue](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) la configuration requise des machines virtuelles Hyper-V que vous souhaitez migrer vers Azure.
3. [Notez](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) les URL Azure auxquelles les hôtes et les clusters Hyper-V ont besoin d’accéder pour la migration de machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :
 
> [!div class="checklist"] 
> * Configuré les autorisations de compte Azure.
> * Préparé les machines virtuelles et hôtes Hyper-V à l’évaluation et à la migration.

Passez au tutoriel suivant pour créer un projet Azure Migrate et évaluer les machines virtuelles Hyper-V à migrer vers Azure.

> [!div class="nextstepaction"] 
> [Évaluer les machines virtuelles Hyper-V](./tutorial-assess-hyper-v.md) 
