---
title: Évaluer des machines virtuelles Hyper-V pour la migration vers Azure avec Azure Migrate | Microsoft Docs
description: Décrit comment évaluer des machines virtuelles Hyper-V locales pour la migration vers Azure avec Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: c627902268af3a91e172223c1741dd24ea21fa92
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535449"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Évaluer des machines virtuelles Hyper-V Azure Migrate Server Assessment

Cet article vous montre comment évaluer des machines virtuelles Hyper-V locales à l’aide de l’outil [Azure Migrate Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Ce tutoriel est le deuxième d’une série qui montre comment évaluer et migrer des machines virtuelles Hyper-V vers Azure. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un projet Azure Migrate.
> * Configurer et inscrire une appliance Azure Migrate.
> * Démarrer la découverte continue des machines virtuelles locales.
> * Regrouper les machines virtuelles découvertes et évaluer le groupe.
> * Passer en revue l’évaluation.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les articles sur les procédures.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- [Effectuez](tutorial-prepare-hyper-v.md) le premier tutoriel de cette série. Si vous ne le faites pas, les instructions de ce tutoriel ne fonctionneront pas.
- Voici ce que vous avez dû faire dans le premier tutoriel :
    - [Préparer Azure](tutorial-prepare-hyper-v.md#prepare-azure) pour qu’il fonctionne avec Azure Migrate.
    - [Préparer l’évaluation](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) des machines virtuelles et des hôtes Hyper-V.
    - [Vérifier](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) ce dont vous avez besoin pour déployer l’appliance Azure Migrate dans le cadre de l’évaluation d’Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Configurer un projet Azure Migrate

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Dans les résultats de la recherche, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Dans **Bien démarrer**, cliquez sur **Ajouter des outils**.
5. Sous l’onglet **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet et la région où vous voulez créer le projet. Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

    - La région du projet est utilisée seulement pour stocker les métadonnées collectées à partir des machines virtuelles locales.
    - Vous pouvez sélectionner une autre région cible Azure quand vous migrez les machines virtuelles. Toutes les régions Azure sont prises en charge pour la cible de migration.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Cliquez sur **Suivant**.
8. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Azure Migrate : Server Assessment** > **Suivant**.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Dans **Sélectionner un outil de migration**, sélectionnez **Ignorer l’ajout d’un outil de migration pour l’instant** > **Suivant**.
10. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis cliquez sur **Ajouter des outils**.
11. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Vous êtes dirigé vers la page du projet. Si vous ne voyez pas le projet, vous pouvez y accéder à partir de **Serveurs** dans le tableau de bord Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate


Azure Migrate Server Assessment utilise une appliance Azure Migrate légère. L’appliance effectue la découverte des machines virtuelles, puis envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate. L’appliance peut être configurée de plusieurs façons.

- Configurez-la sur une machine virtuelle Hyper-V au moyen d’un disque dur virtuel Hyper-V téléchargé. Il s’agit de la méthode utilisée dans ce tutoriel.
- Configurez-la sur une machine virtuelle Hyper-V ou machine physique avec un script d’installation PowerShell. [Cette méthode](deploy-appliance-script.md) doit être utilisée si vous ne pouvez pas configurer une machine virtuelle à l’aide du disque dur virtuel, ou si vous êtes dans Azure Government.

Après avoir créé l’appliance, vérifiez qu’elle peut se connecter à Azure Migrate Server Assessment, configurez-la pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

### <a name="download-the-vhd"></a>Télécharger le disque dur virtuel

Téléchargez le modèle de disque dur virtuel compressé pour l’appliance.

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Oui, avec Hyper-V**.
3. Cliquez sur **Télécharger** pour télécharger le fichier de disque dur virtuel.

    ![Télécharger la machine virtuelle](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.

2. Exécutez la commande PowerShell suivante pour générer le code de hachage du fichier ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Pour l’appliance version 2.19.07.30, le hachage généré doit correspondre à ces valeurs.

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Créer la machine virtuelle de l’appliance

Importez le fichier téléchargé, puis créez la machine virtuelle.

1. Après avoir téléchargé le fichier VHD compressé sur l’hôte Hyper-V sur lequel sera placée la machine virtuelle de l’appliance, extrayez le fichier compressé.
    - À l’emplacement d’extraction, le fichier est décompressé dans le dossier **AzureMigrateAppliance_VersionNumber**.
    - Ce dossier contient un sous-dossier également appelé **AzureMigrateAppliance_VersionNumber**.
    - Ce sous-dossier contient trois autres sous-dossiers : **Snapshots**, **Virtual Hard Disks** et **Virtual Machines**.

2. Ouvrez le Gestionnaire Hyper-V. Dans **Actions**, cliquez sur **Importer la machine virtuelle**.

    ![Déployer le disque dur virtuel](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Dans l’Assistant Importation de machine virtuelle > **Avant de commencer**, cliquez sur **Suivant**.
3. Sous **Localiser le dossier**, sélectionnez le dossier **Virtual Machines**. Cliquez ensuite sur **Suivant**.
1. Dans **Sélectionner une machine virtuelle**, cliquez sur **Suivant**.
2. Dans **Choisir le type d’importation**, cliquez sur **Copier la machine virtuelle (créer un identifiant unique)** . Cliquez ensuite sur **Suivant**.
3. Dans **Choisir la destination**, laissez la valeur par défaut. Cliquez sur **Suivant**.
4. Dans **Dossiers de stockage**, laissez la valeur par défaut. Cliquez sur **Suivant**.
5. Dans **Choisir un réseau**, spécifiez le commutateur virtuel qui sera utilisé par la machine virtuelle. Le commutateur nécessite une connexion à Internet pour envoyer des données à Azure. [En savoir plus](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) sur la création d’un commutateur virtuel.
6. Dans **Récapitulatif**, passez en revue les paramètres. Puis, cliquez sur **Terminer**.
7. Dans Gestionnaire Hyper-V > **Machines virtuelles**, démarrez la machine virtuelle.


## <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

> [!NOTE]
> Si vous configurez l’appliance à l’aide d’un [script PowerShell](deploy-appliance-script.md) au lieu du disque dur virtuel téléchargé, ne tenez pas compte des deux premières étapes de cette procédure.

1. Dans Gestionnaire Hyper-V> **Machines virtuelles**, cliquez avec le bouton droit sur la machine virtuelle > **Se connecter**.
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du Bureau de l’appliance en cliquant sur le raccourci de l’application.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Licence** : Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
      - Cliquez sur **Paramètres du proxy** et spécifiez l’adresse du proxy et le port d’écoute, sous la forme http://ProxyIPAddress ou http://ProxyFQDN.
      - Spécifiez les informations d’identification si le proxy nécessite une authentification.
      - Seuls les proxys HTTP sont pris en charge.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte des machines virtuelles fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate Server Assessment vérifie que les dernières mises à jour sont installées sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Cliquez sur **Se connecter**. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sous le nouvel onglet, connectez-vous avec vos informations d’identification Azure.
    - Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
    - La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion réussie, revenez à l’application web.
4. Sélectionnez l’abonnement où le projet Azure Migrate a été créé. Sélectionnez ensuite le projet.
5. Spécifiez un nom pour l’appliance. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
6. Cliquez sur **S'inscrire**.


### <a name="delegate-credentials-for-smb-vhds"></a>Déléguer des informations d’identification pour les disques durs virtuels sur SMB

Si vous utilisez des disques durs virtuels sur des SMB, vous devez activer la délégation des informations d’identification de l’appliance aux hôtes Hyper-V. Ce tutoriel requiert les éléments suivants :

- Vous permettez à chaque hôte d’agir en tant que délégué pour l’appliance. Si vous avez effectué les tutoriels dans l’ordre, vous l’avez fait dans le tutoriel précédent, quand vous avez préparé Hyper-V pour l’évaluation et la migration. Vous devez avoir configuré CredSSP pour les hôtes [manuellement](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts) ou en [exécutant un script](tutorial-prepare-hyper-v.md#prepare-with-a-script) qui le fait pour vous.
- Activez la délégation CredSSP afin que l’appliance Azure Migrate puisse agir en tant que client, en déléguant les informations d’identification à un hôte.

Activez sur l’appliance comme suit :

#### <a name="option-1"></a>Option 1 :

Sur la machine virtuelle de l’appliance, exécutez cette commande. HyperVHost1/HyperVHost2 sont des exemples de noms d’hôte.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Exemple : ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Option 2 :

Vous pouvez également effectuer cette opération dans l’éditeur d’objets de stratégie de groupe sur l’appliance :

1. Dans **Stratégie de l’ordinateur local** > **Configuration de l’ordinateur**, cliquez sur **Modèles d’administration** > **Système** > **Délégation d’informations d’identification**.
2. Double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification**, puis sélectionnez **Activé**.
3. Dans **Options**, cliquez sur **Afficher**, puis ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.
4. Ensuite, dans **Délégation d’informations d’identification**, double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification avec l’authentification de serveur NTLM uniquement**. À nouveau, ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Connectez-vous de l’appliance à des hôtes ou des clusters Hyper-V, et démarrez la découverte des machines virtuelles.

1. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte que l’appliance doit utiliser pour découvrir les machines virtuelles. Spécifiez un nom convivial pour les informations d’identification, puis cliquez sur **Enregistrer les détails**.
2. Cliquez sur **Ajouter un hôte**, puis spécifiez les détails de l’hôte/du cluster Hyper-V.
3. Cliquez sur **Valider**. Après la validation, le nombre de machines virtuelles qui peuvent être découvertes sur chaque hôte/cluster s’affiche.
    - Si la validation échoue pour un hôte, examinez l’erreur en pointant sur l’icône dans la colonne **État**. Corrigez les problèmes et recommencez la validation.
    - Pour supprimer des hôtes ou des clusters, sélectionnez > **Supprimer**.
    - Vous ne pouvez pas supprimer un hôte spécifique d’un cluster. Vous pouvez supprimer seulement la totalité du cluster.
    - Vous pouvez ajouter un cluster, même s’il existe des problèmes avec des hôtes spécifiques dans le cluster.
4. Après la validation, cliquez sur **Enregistrer et démarrer la découverte** pour démarrer le processus de découverte.

Ceci démarre la découverte. Il faut environ 1,5 minutes par hôte pour que les métadonnées des serveurs détectés apparaissent dans le portail Azure.

### <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les machines virtuelles apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.

## <a name="set-up-an-assessment"></a>Configurer une évaluation

Il existe deux types d’évaluations que vous pouvez exécuter avec Azure Migrate Server Assessment.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille de machine virtuelle locale<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.



### <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Passez en revue les [meilleures pratiques](best-practices-assessment.md) liées à la création d’évaluations.
2. Dans **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Évaluer**.

    ![Évaluer](./media/tutorial-assess-hyper-v/assess.png)

3. Dans **Évaluer les serveurs**, spécifiez un nom pour l’évaluation.
4. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.

    ![Propriétés de l’évaluation](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. Un groupe rassemble une ou plusieurs machines virtuelles à évaluer.
4. Dans **Ajouter des machines au groupe**, sélectionnez les machines virtuelles à ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe, puis exécutez l’évaluation.

    ![Créer une évaluation](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.


## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : Indique si les machines virtuelles peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des machines virtuelles dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.


### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** >  **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Évaluations**.
2. Dans **Évaluations**, cliquez sur une évaluation pour l’ouvrir.

    ![Récapitulatif de l’évaluation](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Niveau de confiance](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Passez au troisième tutoriel de la série pour découvrir comment migrer des machines virtuelles Hyper-V vers Azure avec Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Migrer des machines virtuelles Hyper-V](./tutorial-migrate-hyper-v.md)
