---
title: Préparer des machines virtuelles VMware pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles VMware avec Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: f00d5ba4841427098b0ab79ad1930e357008b6e0
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030793"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure

Cet article vous aide à préparer l’évaluation de machines virtuelles VMware locales et/ou leur migration vers Azure à l’aide d’[Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.


Ce tutoriel est le premier d’une série qui explique comment évaluer et migrer des machines virtuelles VMware. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure pour qu’il fonctionne avec Azure Migrate.
> * Préparez VMware pour l’évaluation des machines virtuelles.
> * Préparez VMware pour la migration des machines virtuelles.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils sont utiles lorsque vous apprenez à configurer un déploiement et comme preuve de concept rapide. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration relatives à VMware.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Vous devez disposer des autorisations suivantes.

**Tâche** | **autorisations**
--- | ---
**Créer un projet Azure Migrate** | Votre compte Azure doit être autorisé à créer un projet.
**Inscrire l’appliance Azure Migrate** | Azure Migrate utilise une appliance Azure Migrate légère pour évaluer les machines virtuelles VMware avec Azure Migrate Server Assessment et pour exécuter la [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware avec Azure Migrate Server Migration. Cette appliance effectue la découverte des machines virtuelles et envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate.<br/><br/>Lors de l’inscription de l’appliance, les fournisseurs de ressources suivants sont inscrits dans l’abonnement choisi dans l’appliance : Microsoft.OffAzure, Microsoft.Migrate et Microsoft.KeyVault. L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. Pour inscrire les fournisseurs de ressources, vous avez besoin d’un rôle Contributeur ou Propriétaire sur l’abonnement.<br/><br/> Dans le cadre de l’intégration, Azure Migrate crée deux applications Azure Active Directory (Azure AD) :<br/> - La première application est utilisée pour la communication (authentification et autorisation) entre les agents exécutés sur l’appliance et leurs services respectifs exécutés sur Azure. Cette application n’a pas les privilèges requis pour effectuer des appels ARM ou des accès RBAC sur une ressource.<br/> - La deuxième application est exclusivement utilisée pour accéder au coffre Key Vault créé dans l’abonnement de l’utilisateur pour la migration sans agent. Elle est fournie avec un accès RBAC sur le coffre Azure Key Vault (créé dans le locataire du client) lorsque la découverte est lancée à partir de l’appliance.
**Créer un coffre de clés** | Pour migrer des machines virtuelles VMware avec Azure Migrate Server Migration, Azure Migrate crée un coffre de clés afin de gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate.






### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Pour inscrire l’appliance, vous affectez des autorisations pour permettre à Azure Migrate de créer les applications Azure AD durant l’inscription d’appliance. Les autorisations peuvent être affectées à l’aide de l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

> [!NOTE]
> - Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
> - Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général ou le locataire peuvent octroyer des autorisations de la façon suivante :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**. Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

### <a name="assign-permissions-to-create-a-key-vault"></a>Attribuer des autorisations pour créer un coffre Key Vault

Pour permettre à Azure Migrate de créer un coffre Key Vault, attribuez les autorisations suivantes :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.

    - Pour exécuter l’outil Server Assessment, les autorisations de **Contributeur** sont suffisantes.
    - Pour exécuter la migration de serveur sans agent, vous devez disposer des autorisations de **Propriétaire** (ou de **Contributeur** et d’**Administrateur de l’accès utilisateur**).

3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources.



## <a name="prepare-for-vmware-vm-assessment"></a>Préparer l’évaluation des machines virtuelles VMware

Pour préparer l’évaluation des machines virtuelles VMware, vous devez :

- **Vérifier les paramètres VMware**. Vérifiez que vCenter Server et les machines virtuelles que vous voulez migrer répondent aux exigences.
- **Configurer un compte d’évaluation**. Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation.
- **Vérifiez la configuration requise de l’appliance**. Vérifiez les exigences relatives au déploiement pour l’appliance Azure Migrate utilisée dans le cadre de l’évaluation.

### <a name="verify-vmware-settings"></a>Vérifier les paramètres VMware

1. [Vérifiez](migrate-support-matrix-vmware.md#vmware-requirements) les exigences relatives au serveur VMware pour l’évaluation.
2. [Vérifiez](migrate-support-matrix-vmware.md#port-access) que les ports nécessaires sont ouverts sur vCenter Server.
3. Sur vCenter Server, vérifiez que votre compte dispose des autorisations nécessaires pour créer une machine virtuelle à l’aide d’un fichier OVA. Vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurer un compte pour l’évaluation

Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation et de migration sans agent.

- Si vous envisagez de découvrir des applications ou de visualiser des dépendances sans utiliser d’agent, créez un compte vCenter Server avec un accès en lecture seule, ainsi que des privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.

  ![Privilèges du compte vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Si vous n’envisagez pas de découvrir des applications ni de visualiser des dépendances sans utiliser d’agent, configurez un compte en lecture seule pour vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Vérifier les paramètres de l’appliance pour l’évaluation

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-appliance.md#appliance---vmware) la configuration requise de l’appliance pour les machines virtuelles VMware.
2. [Passez en revue](migrate-appliance.md#url-access) les URL Azure auxquelles l’appliance doit accéder. Si vous utilisez un pare-feu ou un proxy basé sur des URL, vérifiez qu’il autorise l’accès aux URL nécessaires.
3. [Passez en revue](migrate-appliance.md#collected-data---vmware) les données que l’appliance recueillera pendant la détection et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#port-access) les conditions d’accès aux ports pour l’appliance.




## <a name="prepare-for-agentless-vmware-migration"></a>Préparer une migration VMware sans agent

Passez en revue les exigences relatives à la migration sans agent des machines virtuelles VMware.

1. [Passez en revue](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) la configuration requise pour les serveurs VMware et les [autorisations](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) dont Azure Migrate a besoin pour accéder au vCenter Server pour la migration sans agent à l’aide d’Azure Migrate Server Migration.
2. [Passez en revue](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration sans agent.
4. [Passez en revue](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) les exigences relatives à l’utilisation de l’appliance Azure Migrate pour la migration sans agent.
5. Notez les conditions d’[accès aux URL](migrate-appliance.md#url-access) et d’[accès aux ports](migrate-support-matrix-vmware-migration.md#agentless-ports) pour la migration sans agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Préparer une migration VMware basée sur un agent

Passez en revue les exigences relatives à la [migration basée sur un agent](server-migrate-overview.md) des machines virtuelles VMware.

1. [Passez en revue](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) la configuration requise pour les serveurs VMware et les autorisations dont Azure Migrate a besoin pour accéder au vCenter Server pour la migration basée sur un agent à l’aide d’Azure Migrate Server Migration.
2. [Passez en revue](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration basée sur un agent, notamment l’installation de Mobility Service sur chaque machine virtuelle à migrer.
3. Les migrations basées sur un agent utilisent une appliance de réplication :
    - [Passez en revue](migrate-replication-appliance.md#appliance-requirements) la configuration requise pour le déploiement de l’appliance de réplication et les [options](migrate-replication-appliance.md#mysql-installation) d’installation de MySQL sur l’appliance.
    - Passez en revue les conditions d’accès aux [URL](migrate-replication-appliance.md#url-access) et aux [ports](migrate-replication-appliance.md#port-access) pour l’appliance de réplication.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations Azure
> * Préparé VMware à l’évaluation et la migration


Passez au deuxième tutoriel afin de configurer un projet Azure Migrate et d’évaluer les machines virtuelles VMware pour une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles VMware](./tutorial-assess-vmware.md)
