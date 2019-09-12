---
title: Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure avec Azure Migrate | Microsoft Docs
description: Décrit comment préparer l’évaluation de machines virtuelles VMware locales et leur migration vers Azure à l’aide d’Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3b623675343d63385213091b66e220c5358c6437
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383322"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure

Cet article décrit comment préparer l’évaluation de machines virtuelles VMware locales et leur migration vers Azure à l’aide d’[Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers. 


Ce tutoriel est le premier d’une série qui explique comment évaluer et migrer des machines virtuelles VMware. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparez Azure. Configurez des autorisations pour permettre à votre compte et vos ressources Azure de fonctionner avec Azure Migrate.
> * Préparer des serveurs et machines virtuelles VMware locaux dans le cadre de l’évaluation de machines virtuelles.
> * Préparer des serveurs et machines virtuelles VMware locaux dans le cadre de la migration de machines virtuelles.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné, pour vous permettre de configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration relatives à VMware.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Vous devez disposer des autorisations Azure suivantes :

- Votre compte Azure doit être autorisé à créer un projet Azure Migrate à des fins d’évaluation et de migration. 
- Pour l’évaluation et la migration sans agent des machines virtuelles VMware, Azure Migrate exécute une appliance légère qui découvre les machines virtuelles, puis envoie leurs métadonnées et leurs données de performances à Azure Migrate. Dans Azure, vous devez disposer des autorisations nécessaires pour inscrire l’appliance Azure Migrate.
- Pour migrer des machines virtuelles VMware à l’aide d’Azure Migrate Server Migration, Azure Migrate crée un coffre de clés dans le groupe de ressources afin de gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Si vous déployez l’appliance Azure Migrate pour évaluer ou exécuter une migration sans agent de machines virtuelles, vous devez l’inscrire.

- Durant l’inscription de l’appliance, Azure Migrate crée deux applications Azure AD (Azure Active Directory) qui identifient cette appliance de façon unique
    - La première application communique avec les points de terminaison de service Azure Migrate.
    - La seconde application accède à un coffre de clés Azure Key Vault créé pendant l’inscription pour stocker les informations Azure AD App et les paramètres de configuration de l’appliance.
- Vous pouvez affecter des autorisations à Azure Migrate pour créer ces applications Azure AD à l’aide de l’une des méthodes suivantes :
    - Un administrateur général/locataire peut octroyer des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
    - L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée. 


#### <a name="grant-account-permissions"></a>Octroyer des autorisations au compte

L’administrateur général ou le locataire peuvent octroyer des autorisations de la façon suivante :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications 

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

## <a name="assign-role-assignment-permissions"></a>Affecter des autorisations d’attribution de rôle

Affectez des autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate, comme ceci :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.

    - Pour exécuter l’outil Server Assessment, les autorisations de **Contributeur** sont suffisantes.
    - Pour exécuter la migration de serveur sans agent, vous devez disposer des autorisations de **Propriétaire** (ou de **Contributeur** et d’**Administrateur de l’accès utilisateur**).

3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources. 



## <a name="prepare-for-vmware-vm-assessment"></a>Préparer l’évaluation des machines virtuelles VMware

Pour préparer l’évaluation des machines virtuelles VMware, vous devez vérifier les paramètres de machine virtuelle et d’hôte VMWare, ainsi que les paramètres de déploiement de l’appliance.

### <a name="verify-vmware-settings"></a>Vérifier les paramètres VMware

1. [Vérifiez](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) les exigences relatives au serveur VMware pour l’évaluation des machines virtuelles.
2. [Vérifiez](migrate-support-matrix-vmware.md#assessment-port-requirements) que les ports nécessaires sont ouverts sur les serveurs vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Configurer un compte pour l’évaluation

Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation et de migration sans agent. Dans le cadre de l’évaluation uniquement, vous avez besoin d’un compte en lecture seule pour le serveur vCenter Server.

Si vous utilisez un proxy de pare-feu basé sur des URL, autorisez l’accès aux [URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements) nécessaires.

Vérifiez que le proxy résout tous les enregistrements CNAME reçus durant la recherche des URL.


### <a name="verify-appliance-settings-for-assessment"></a>Vérifier les paramètres de l’appliance pour l’évaluation

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-support-matrix-vmware.md#assessment-appliance-requirements) les exigences relatives à la configuration de l’appliance Azure Migrate dans VMware.
2. [Passez en revue](migrate-support-matrix-vmware.md#assessment-url-access-requirements) les URL Azure auxquelles l’appliance doit accéder.
3. Passez en revue les données que l’appliance va collecter pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#assessment-port-requirements) les conditions d’accès aux ports pour l’appliance.
5. Vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA. Sur vCenter Server, vérifiez que votre compte dispose des autorisations nécessaires pour créer une machine virtuelle à l’aide d’un fichier OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Préparer une migration VMware sans agent

Passez en revue les exigences relatives à la migration sans agent des machines virtuelles VMware.

1. [Passez en revue](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) les exigences relatives au serveur VMware pour la migration sans agent.
2. Configurez un compte afin d’accéder au serveur vCenter Server avec les [autorisations nécessaires](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) pour la migration sans agent.
3. [Notez](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration sans agent.
4. [Passez en revue](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) les exigences relatives à l’appliance pour la migration sans agent.
5. Notez les conditions d’[accès aux URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) et d’[accès aux ports](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) des appliances pour la migration sans agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Préparer une migration VMware basée sur un agent

Passez en revue les exigences relatives à la migration basée sur un agent des machines virtuelles VMware.

1. [Passez en revue](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) les exigences relatives au serveur VMware pour la migration sans agent. 
2. Configurez un compte afin d’accéder au serveur vCenter Server avec les [autorisations nécessaires](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) pour la migration sans agent.
3. [Notez](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration basée sur un agent, notamment l’installation de Mobility Service sur chaque machine virtuelle à migrer.
4. Notez l’[accès aux URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Passez en revue les conditions d’[accès aux ports](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) pour le service Mobility Service s’exécutant sur chaque machine virtuelle ainsi que pour le serveur de configuration Azure Migrate.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :
 
> [!div class="checklist"] 
> * Configuré les autorisations Azure
> * Préparé VMware à l’évaluation et la migration


Passez au deuxième tutoriel afin de configurer un projet Azure Migrate et d’évaluer les machines virtuelles VMware pour une migration vers Azure.

> [!div class="nextstepaction"] 
> [Évaluer les machines virtuelles VMware](./tutorial-assess-vmware.md) 

