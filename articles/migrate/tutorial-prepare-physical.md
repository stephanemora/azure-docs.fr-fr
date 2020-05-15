---
title: Préparer des serveurs physiques pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des serveurs physiques avec Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b7bde5df943a35bfcf08ace3b454a26dae8c1d89
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901425"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Préparer les serveurs physiques à une évaluation et à une migration vers Azure

Cet article explique comment préparer les serveurs physiques locaux à une évaluation et à une migration avec [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers. 

Ce tutoriel est le premier d’une série qui montre comment évaluer les serveurs physiques avec Azure Migrate. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure. Configurez des autorisations pour permettre à votre compte et vos ressources Azure de fonctionner avec Azure Migrate.
> * Préparez les serveurs physiques locaux à une évaluation.


> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les guides pratiques concernant l’évaluation des serveurs physiques.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure-for-server-assessment"></a>Préparer Azure pour l’évaluation des serveurs

Configurez Azure pour qu’il fonctionne avec Azure Migrate. 

**Tâche** | **Détails** 
--- | --- 
**Créer un projet Azure Migrate** | Votre compte Azure doit disposer d’autorisations Contributeur ou Propriétaire pour créer un projet. 
**Inscrire des fournisseurs de ressources (évaluation uniquement)** | Azure Migrate utilise une appliance Azure Migrate légère pour découvrir et évaluer les machines à l’aide d’Azure Migrate : Évaluation du serveur.<br/><br/> Lors de l’inscription d’appliances, les fournisseurs de ressources sont inscrits auprès de l’abonnement choisi dans l’appliance. [Plus d’informations](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Pour inscrire les fournisseurs de ressources, vous avez besoin d’un rôle Contributeur ou Propriétaire sur l’abonnement.
**Créer une application Azure AD (évaluation uniquement)** | Lors de l’inscription de l’appliance, Azure Migrate crée une application Azure AD (Azure Active Directory). Celle-ci est utilisée pour la communication entre les agents s’exécutant sur l’appliance et leurs services respectifs s’exécutant sur Azure. [Plus d’informations](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Vous devez disposer d’autorisations pour créer des applications Azure AD (disponibles dans le rôle Développeur d’applications).


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet 

Vérifiez que vous disposez des autorisations nécessaires pour créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance 

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
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-azure-for-physical-server-migration"></a>Préparer Azure pour la migration de serveurs physiques

Préparez Azure à migrer des serveurs physiques à l’aide de Migration de serveur.

**Tâche** | **Détails**
--- | ---
**Créer un projet Azure Migrate** | Votre compte Azure doit disposer d’autorisations Contributeur ou Propriétaire pour créer un projet.
**Vérifier les autorisations pour votre compte Azure** | Votre compte Azure a besoin d’autorisations pour créer une machine virtuelle et écrire sur un disque managé Azure.
**Créer un réseau Azure** | Configurez un réseau dans Azure.


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-azure-account-permissions"></a>Affecter des autorisations de compte Azure

Affectez le rôle Contributeur de machines virtuelles au compte Azure. Cela permet d’effectuer les opérations suivantes :
  - Créer une machine virtuelle dans le groupe de ressources sélectionné
  - Créer une machine virtuelle dans le réseau virtuel sélectionné
  - Écrire sur un disque managé Azure. 

### <a name="create-an-azure-network"></a>Créer un réseau Azure

[Configurez](../virtual-network/manage-virtual-network.md#create-a-virtual-network) un réseau virtuel Azure. Quand vous effectuez une réplication sur Azure, des machines virtuelles Azure sont créées et jointes au réseau virtuel Azure que vous avez spécifié lors de la configuration de la migration.


## <a name="prepare-for-physical-server-assessment"></a>Préparer les serveurs physiques à une évaluation

Pour préparer l’évaluation des serveurs physiques, vous devez vérifier les paramètres des serveurs physiques et les paramètres de déploiement des appliances :

### <a name="verify-physical-server-settings"></a>Vérifier les paramètres des serveurs physiques

1. Vérifiez la [configuration requise des serveurs physiques](migrate-support-matrix-physical.md#physical-server-requirements) pour l’évaluation.
2. Vérifiez que les [ports obligatoires](migrate-support-matrix-physical.md#port-access) sont ouverts sur les serveurs physiques.


### <a name="verify-appliance-settings"></a>Vérifier les paramètres de l’appliance

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-appliance.md#appliance---physical) la configuration requise de l’appliance pour les serveurs physiques.
2. Passez en revue les URL Azure auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
3. [Passez en revue](migrate-appliance.md#collected-data---vmware) les données que l’appliance recueillera pendant la détection et l’évaluation.
4. [Notez](migrate-support-matrix-physical.md#port-access) les conditions d’accès aux ports pour l’évaluation des serveurs physiques.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurer un compte pour la découverte des serveurs physiques

Azure Migrate a besoin d’autorisations pour découvrir les serveurs locaux.

- **Windows :** Configurez un compte d’utilisateur local sur tous les serveurs Windows que vous souhaitez inclure dans la découverte. Le compte d’utilisateur doit être ajouté aux groupes suivants : Utilisateurs de gestion à distance, Utilisateurs de l’Analyseur de performances et Utilisateurs du journal de performances
- **Linux :** Vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir.

## <a name="prepare-for-physical-server-migration"></a>Préparer les serveurs physiques à une migration

Passez en revue les conditions requises d’une migration des serveurs physiques.

> [!NOTE]
> Lors de la migration de machines physiques, Azure Migrate : Migration de serveur utilise la même architecture de réplication que la fonctionnalité de reprise d’activité après sinistre basée sur un agent du service Azure Site Recovery, et certains des composants utilisés partagent la même base de code. Certains contenus peuvent être liés à la documentation Site Recovery.

- [Passez en revue](migrate-support-matrix-physical-migration.md#physical-server-requirements) la configuration requise des serveurs physiques pour la migration.
- Azure Migrate : Migration de serveur utilise un serveur de réplication pour la migration des serveurs physiques :
    - [Passez en revue](migrate-replication-appliance.md#appliance-requirements) la configuration requise pour le déploiement de l’appliance de réplication et les [options](migrate-replication-appliance.md#mysql-installation) d’installation de MySQL sur l’appliance.
    - Passez en revue les [URL Azure](migrate-appliance.md#url-access) nécessaires à l’appliance de réplication pour accéder aux clouds publics et du secteur public.
    - Passez en revue les exigences d’accès au [port] (migrate-replication-appliance.md#port-access) pour l’appliance de réplication.




## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations de compte Azure.
> * Serveurs physiques préparés pour l’évaluation.

Passez au tutoriel suivant pour créer un projet Azure Migrate et évaluer les serveurs physiques qui doivent faire l’objet d’une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer des serveurs physiques](./tutorial-assess-physical.md)
