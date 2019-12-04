---
title: Évaluer les machines virtuelles VMware pour la migration vers Azure avec Azure Migrate
description: Décrit comment évaluer les machines virtuelles VMware locales pour la migration vers Azure à l’aide d’Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 595b6705b4e876ce5b42a7de831136cb0b62b1f5
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196444"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Évaluer les machines virtuelles VMware avec Azure Migrate : Server Assessment

Cet article vous montre comment évaluer les machines virtuelles VMware locales à l’aide de l’outil Azure Migrate : Server Assessment.

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.



Ce tutoriel est le deuxième d’une série qui explique comment évaluer et migrer des machines virtuelles VMware vers Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configurer un projet Azure Migrate.
> * Configurer une appliance Azure Migrate qui s’exécute localement pour évaluer les machines virtuelles.
> * Démarrer la découverte continue des machines virtuelles locales. L’appliance envoie les données de configuration et de performances des machines virtuelles découvertes à Azure.
> * Regrouper les machines virtuelles découvertes, et évaluer le groupe de machines virtuelles.
> * Passer en revue l’évaluation.



> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné, pour vous permettre de configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les articles sur les procédures.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- [Effectuez](tutorial-prepare-vmware.md) le premier tutoriel de cette série. Si vous ne le faites pas, les instructions de ce tutoriel ne fonctionneront pas.
- Voici ce que vous avez dû faire dans le premier tutoriel :
    - [Configurer des autorisations Azure](tutorial-prepare-vmware.md#prepare-azure) pour Azure Migrate.
    - [Préparer VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) à l’évaluation. Vous devez vérifier les paramètres VMware et disposer des autorisations nécessaires pour créer une machine virtuelle VMware avec un modèle OVA. Vous devez également avoir un compte configuré pour la découverte de machines virtuelles. Les ports obligatoires doivent être disponibles, et vous devez connaître les URL nécessaires pour accéder à Azure.


## <a name="set-up-an-azure-migrate-project"></a>Configurer un projet Azure Migrate

Configurez un nouveau projet Azure Migrate comme suit.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-assess-vmware/assess-migrate.png)

4. Dans **Bien démarrer**, cliquez sur **Ajouter des outils**.
5. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.     
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. L’Asie, l’Europe, le Royaume-Uni et les États-Unis sont pris en charge.

    - La géographie du projet sert uniquement à stocker les métadonnées rassemblées à partir des machines virtuelles locales.
    - Vous pouvez sélectionner n’importe quelle région cible quand vous exécutez une migration.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Cliquez sur **Suivant**.
8. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Azure Migrate : Server Assessment** > **Suivant**.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. Dans **Sélectionner un outil de migration**, sélectionnez **Ignorer l’ajout d’un outil de migration pour l’instant** > **Suivant**.
10. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis cliquez sur **Ajouter des outils**.
11. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Vous êtes dirigé vers la page du projet. Si vous ne voyez pas le projet, vous pouvez y accéder à partir de **Serveurs** dans le tableau de bord Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Configurer la machine virtuelle de l’appliance

Azure Migrate : Server Assessment exécute une appliance de machine virtuelle VMware légère.

- Cette appliance effectue la découverte des machines virtuelles, puis envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate Server Assessment.
- Pour configurer l’appliance, vous devez :
    - Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server.
    - Créez l’appliance et vérifiez qu’elle peut se connecter à Azure Migrate Server Assessment.
    - Configurer l’appliance pour la première fois, puis l’inscrire auprès du projet Azure Migrate
- Vous pouvez configurer plusieurs appliances pour un seul projet Azure Migrate. Sur l’ensemble des appliances, la découverte de 35 000 machines virtuelles au maximum est prise en charge. 10 000 serveurs au maximum peuvent être découverts par appliance.

### <a name="download-the-ova-template"></a>Télécharger le modèle OVA

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Oui, avec l’hyperviseur vSphere VMware**.
3. Cliquez sur **Télécharger** pour télécharger le fichier de modèle .OVA.

    ![Télécharger le fichier .ova](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier .OVA est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pour la version 2.19.07.30, le code de hachage généré doit correspondre aux valeurs ci-après.

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>Créer la machine virtuelle de l’appliance

Importez le fichier téléchargé, puis créez une machine virtuelle.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).

    ![Déployer le modèle OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle. Sélectionnez l’objet d’inventaire dans lequel la machine virtuelle doit être hébergée.
5. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle.
6. Dans **Storage** (Stockage), spécifiez la destination du stockage de la machine virtuelle.
7. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
8. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle. Le réseau a besoin d’une connexion à Internet pour envoyer des métadonnées à Azure Migrate Server Assessment.
9. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).


### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux [URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance en suivant les étapes ci-après.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du Bureau de l’appliance en cliquant sur le raccourci de l’application.
4. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Licence** : Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
        - Cliquez sur **Paramètres du proxy** et spécifiez l’adresse du proxy et le port d’écoute, sous la forme http://ProxyIPAddress ou http://ProxyFQDN.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
    - **Synchronisation de l’heure** : l’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
    - **Installer les mises à jour** : L’appliance vérifie que les dernières mises à jour sont installées.
    - **Installer VDDK** : L’appliance vérifie que VDDK (VMWare vSphere Virtual Disk Development Kit) est installé.
        - Azure Migrate : Server Migration utilise VDDK pour répliquer les machines durant la migration vers Azure.
        - Téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé à l’emplacement spécifié sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Cliquez sur **Se connecter**. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sous le nouvel onglet, connectez-vous avec vos informations d’identification Azure.
    - Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
    - La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion réussie, revenez à l’application web.
2. Sélectionnez l’abonnement dans lequel le projet Azure Migrate a été créé, puis sélectionnez le projet.
3. Spécifiez un nom pour l’appliance. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
4. Cliquez sur **S'inscrire**.

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

L’appliance doit se connecter à vCenter Server pour découvrir les données de configuration et de performances des machines virtuelles.

### <a name="specify-vcenter-server-details"></a>Spécifier les détails vCenter Server
1. Dans **Spécifier les détails vCenter Server**, spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter Server. Vous pouvez laisser le port par défaut, ou spécifier un port personnalisé sur lequel votre serveur vCenter Server est à l’écoute.
2. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte vCenter Server que l’appliance doit utiliser pour découvrir les machines virtuelles sur le serveur vCenter. Vérifiez que le compte dispose des [autorisations nécessaires pour la découverte](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Vous pouvez définir l’étendue de la découverte en limitant l’accès au compte vCenter. Pour en savoir plus sur la découverte délimitée, cliquez [ici](tutorial-assess-vmware.md#scoping-discovery).
3. Cliquez sur **Valider la connexion** pour vérifier que l’appliance peut se connecter à vCenter Server.

### <a name="specify-vm-credentials"></a>Spécifier des informations d’identification de machine virtuelle
Pour la découverte des applications, des rôles et des fonctionnalités, et pour la visualisation des dépendances des machines virtuelles, vous pouvez fournir des informations d’identification de machine virtuelle qui permettent d’accéder aux machines virtuelles VMware. Vous pouvez ajouter des informations d’identification pour les machines virtuelles Windows et d’autres pour les machines virtuelles Linux. [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sur les privilèges d’accès nécessaires.

> [!NOTE]
> Cette entrée est facultative. Elle est nécessaire pour activer la découverte des applications et pour visualiser les dépendances sans agent.

1. Dans **Découvrir les applications et les dépendances sur les machines virtuelles**, cliquez sur **Ajouter les informations d’identification**.
2. Sélectionnez le **Système d’exploitation**.
3. Fournissez un nom convivial pour les informations d’identification.
4. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez un compte disposant au moins d’un accès invité sur les machines virtuelles.
5. Cliquez sur **Add**.

Une fois que vous avez spécifié les informations d’identification de vCenter Server et de la machine virtuelle (facultatif), cliquez sur **Enregistrer et démarrer la découverte** pour démarrer la découverte de l’environnement local.

Environ 15 minutes sont nécessaires pour que les métadonnées des machines virtuelles découvertes apparaissent dans le portail. La découverte des applications, des rôles et des fonctionnalités installés prend un certain temps et la durée dépend du nombre de machines virtuelles découvertes. Pour 500 machines virtuelles, il faut environ 1 heure pour que l’inventaire des applications s’affiche dans le portail Azure Migrate.

### <a name="scoping-discovery"></a>Définition de l’étendue de la découverte

Vous pouvez définir l’étendue de la découverte en limitant l’accès du compte vCenter utilisé à cet effet. Vous pouvez définir une étendue et l’appliquer à des centres de données, des clusters, un dossier de clusters, des hôtes, un dossier d’hôtes ou des machines virtuelles individuelles vCenter Server.

Pour définir l’étendue, vous devez exécuter la procédure suivante :
1.  Créez un compte d’utilisateur vCenter.
2.  Définissez un nouveau rôle avec les privilèges requis. (<em>requis pour la migration de serveur sans agent</em>)
3.  Affectez des autorisations au compte d’utilisateur sur les objets vCenter.

**Créer un compte d’utilisateur vCenter**
1.  Connectez-vous au client web vSphere en tant qu’administrateur de serveur vCenter.
2.  Cliquez sur **Administration** > **SSO users and Groups (Groupes et utilisateurs SSO)**  >  onglet **Utilisateurs**.
3.  Cliquez sur l’icône **Nouvel utilisateur**.
4.  Renseignez les informations requises pour créer un utilisateur, puis cliquez sur **OK**.

**Définir un nouveau rôle avec les privilèges requis** (<em>requis pour la migration de serveur sans agent</em>)
1.  Connectez-vous au client web vSphere en tant qu’administrateur de serveur vCenter.
2.  Accédez à **Administration** > **Gestionnaire de rôles**.
3.  Sélectionnez votre serveur vCenter dans le menu déroulant.
4.  Cliquez sur l’action **Créer un rôle**.
5.  Tapez un nom pour le nouveau rôle (par exemple, <em>Azure_Migrate</em>).
6.  Attribuez ces [autorisations](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) au rôle qui vient d’être défini.
7.  Cliquez sur **OK**.

**Affecter des autorisations sur les objets vCenter**

Il existe deux approches pour affecter des autorisations sur des objets d’inventaire dans vCenter au compte d’utilisateur vCenter avec un rôle qui lui est affecté.
- Pour l’évaluation de serveur, le rôle en **lecture seule** doit être appliqué au compte d’utilisateur vCenter pour tous les objets parents sur lesquels les machines virtuelles à découvrir sont hébergées. Tous les objets parents (hôte, dossier des hôtes, cluster, dossier des clusters) de la hiérarchie jusqu’au centre de données doivent être inclus. Ces autorisations doivent être propagées aux objets enfants dans la hiérarchie.

    De même pour la migration de serveur, un rôle défini par l’utilisateur (peut être nommé  <em>Azure _Migrate</em>) avec ces [privilèges](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) attribués doit être appliqué au compte d’utilisateur vCenter pour tous les objets parents sur lesquels les machines virtuelles à migrer sont hébergées.

  ![Assigner des autorisations](./media/tutorial-assess-vmware/assign-perms.png)

- L’approche alternative consiste à affecter le compte d’utilisateur et le rôle au niveau du centre de données et à les propager aux objets enfants. Attribuez ensuite au compte un rôle **Aucun accès** pour chaque objet (comme les machines virtuelles) que vous ne souhaitez pas découvrir/migrer. Cette configuration est fastidieuse. Elle entraîne des contrôles d’accès accidentels, car chaque nouvel objet enfant créé hérite automatiquement de l’accès de son parent. Nous vous recommandons donc d’utiliser la première approche.

> [!NOTE]
> Pour le moment, Server Assessment ne peut pas découvrir les machines virtuelles si le compte vCenter s’est vu octroyer un accès au niveau du dossier de machine virtuelle vCenter. Si vous souhaitez définir l’étendue de la découverte en fonction de dossiers de machines virtuelles, vous pouvez le faire en vérifiant que le compte vCenter dispose d’un accès en lecture seule affecté au niveau de la machine virtuelle.  Voici des instructions sur la façon de procéder :
>
> 1. Attribuez des autorisations en lecture seule à toutes les machines virtuelles des dossiers de machines virtuelles représentant l’étendue de la découverte.
> 2. Octroyez un accès en lecture seule à tous les objets parents où les machines virtuelles sont hébergées. Tous les objets parents (hôte, dossier des hôtes, cluster, dossier des clusters) de la hiérarchie jusqu’au centre de données doivent être inclus. Vous n’avez pas besoin de propager les autorisations à tous les objets enfants.
> 3. Utilisez les informations d’identification pour la détection en sélectionnant le centre de données en tant qu’*étendue de collection*. La configuration RBAC garantit que l’utilisateur vCenter correspondant aura accès uniquement aux machines virtuelles spécifiques d’un locataire.
>
> Notez que les dossiers d’hôtes et de clusters sont pris en charge.

### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

Une fois la découverte effectuée, vous pouvez vérifier que les machines virtuelles apparaissent dans le portail Azure.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.

## <a name="set-up-an-assessment"></a>Configurer une évaluation

Vous pouvez créer deux types d’évaluation à l’aide d’Azure Migrate : Évaluation de serveur.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**En fonction des performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille de machine virtuelle locale<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.


### <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Passez en revue les [meilleures pratiques](best-practices-assessment.md) liées à la création d’évaluations.
2. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : Mosaïque**  d’évaluation de serveur, cliquez sur **Évaluer**.

    ![Évaluation](./media/tutorial-assess-vmware/assess.png)

2. Dans **Évaluer les serveurs**, spécifiez un nom pour l’évaluation.
3. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.

    ![Propriétés de l’évaluation](./media/tutorial-assess-vmware/view-all.png)

3. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. Un groupe rassemble une ou plusieurs machines virtuelles à évaluer.
4. Dans **Ajouter des machines au groupe**, sélectionnez les machines virtuelles à ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe, puis exécutez l’évaluation.

    ![Créer une évaluation](./media/tutorial-assess-vmware/assessment-create.png)

6. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment** > **Évaluations**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.



## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : Indique si les machines virtuelles peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des machines virtuelles dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.

### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** >  **Serveurs**, cliquez sur **Évaluations** dans **Azure Migrate : Évaluation de serveur**.
2. Dans **Évaluations**, cliquez sur une évaluation pour l’ouvrir.

    ![Récapitulatif de l’évaluation](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examiner la préparation pour Azure

1. Dans **Préparé pour Azure**, vérifiez si les machines virtuelles sont prêtes pour la migration vers Azure.
2. Passez en revue l’état des machines virtuelles :
    - **Disponible pour Azure** : Azure Migrate recommande une taille de machine virtuelle et donne des estimations de coût pour les machines virtuelles de l’évaluation.
    - **Disponible sous conditions** : Montre des problèmes et leur correction suggérée.
    - **Non disponible pour Azure** : Montre des problèmes et leur correction suggérée.
    - **État de la préparation inconnu** : Utilisé quand Azure Migrate ne peut pas évaluer la préparation, en raison de problèmes de disponibilité des données.

2. Cliquez sur un état **Préparé pour Azure**. Vous pouvez voir les informations relatives à l’état de préparation des machines virtuelles et explorer ces dernières en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.



### <a name="review-cost-details"></a>Passer en revue les détails des coûts

Cette vue montre une estimation des coûts de calcul et de stockage liés à l’exécution des machines virtuelles dans Azure.

1. Passez en revue les coûts mensuels de calcul et de stockage. Les coûts sont agrégés pour toutes les machines virtuelles du groupe évalué.

    - Les estimations des coûts sont basées sur les suggestions de taille d’une machine ainsi que sur ses disques et ses propriétés.
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés.
    - L’estimation des coûts porte sur l’exécution des machines virtuelles locales en tant que machines virtuelles IaaS. Azure Migrate Server Assessment ne prend pas en compte les coûts PaaS ou SaaS.

2. Vous pouvez passer en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon différents types de disque de stockage.
3. Vous pouvez explorer les détails de machines virtuelles spécifiques.


### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Quand vous effectuez des évaluations basées sur les performances, un niveau de confiance est affecté à l’évaluation.

![Niveau de confiance](./media/tutorial-assess-vmware/confidence-rating.png)

- Un niveau allant de 1 étoile (le plus faible) à 5 étoiles (le plus élevé) est attribué.
- Le niveau de confiance vous aide à estimer la fiabilité des suggestions de taille fournies par l’évaluation.
- Le niveau de confiance est basé sur la disponibilité des points de données nécessaires pour calculer l’évaluation.

Les niveaux de confiance d’une évaluation sont les suivants.

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles

[Découvrez plus en détail](best-practices-assessment.md#best-practices-for-confidence-ratings) les bonnes pratiques relatives aux niveaux de confiance.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configurer une appliance Azure Migrate
> * Créer et passer en revue une évaluation

Passez au troisième tutoriel de la série pour découvrir comment migrer des machines virtuelles VMware vers Azure avec Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Migrer des machines virtuelles VMware](./tutorial-migrate-vmware.md)
