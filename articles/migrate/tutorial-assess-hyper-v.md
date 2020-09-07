---
title: Évaluer des machines virtuelles Hyper-V pour la migration vers Azure avec Azure Migrate | Microsoft Docs
description: Décrit comment évaluer des machines virtuelles Hyper-V locales pour la migration vers Azure avec Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: eae7e2d371ed8156debe9ae24cf0744bd6273943
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950270"
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
    - [Préparer l’évaluation](tutorial-prepare-hyper-v.md#prepare-for-assessment) des machines virtuelles et des hôtes Hyper-V.
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

### <a name="generate-the-azure-migrate-project-key"></a>Générer la clé de projet Azure Migrate

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, sélectionnez **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**.
3. Dans **1 : Générer une clé de projet Azure Migrate**, attribuez un nom à l'appliance Azure Migrate que vous allez configurer pour la détection de machines virtuelles Hyper-V. Il doit s'agir d'un nom alphanumérique de 14 caractères maximum.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Détecter des machines pendant la création des ressources.
1. Une fois les ressources Azure créées, une **clé de projet Azure Migrate** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

### <a name="download-the-vhd"></a>Télécharger le disque dur virtuel

Dans **2 : Télécharger l'appliance Azure Migrate**, sélectionnez le fichier .VHD et cliquez sur **Télécharger**. 

   ![Sélections relatives à Détecter des machines](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Sélections pour la génération de la clé](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.

2. Exécutez la commande PowerShell suivante pour générer le code de hachage du fichier ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :

    - Pour le cloud public Azure :

        **Scénario** | **Télécharger** | **SHA256**
        --- | --- | ---
        Hyper-V (10,4 Go) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Pour Azure Government :

        **Scénario*** | **Télécharger** | **SHA256**
        --- | --- | ---
        Hyper-V (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140424) |  8025f315e41c01ebdb4ffb1de87982ae6cc4ea7c4cce612612c7e90a44e79b44


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
5. Dans **Choisir un réseau**, spécifiez le commutateur virtuel qui sera utilisé par la machine virtuelle. Le commutateur nécessite une connexion à Internet pour envoyer des données à Azure. [En savoir plus](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) sur la création d’un commutateur virtuel.
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
1. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
      - Cliquez sur **Configurer le proxy**, puis spécifiez l'adresse du proxy (sous la forme http://ProxyIPAddress ou http://ProxyFQDN) ) et le port d'écoute.
      - Spécifiez les informations d’identification si le proxy nécessite une authentification.
      - Seuls les proxys HTTP sont pris en charge.
      - Si vous avez ajouté des détails de proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte des machines virtuelles fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate Server Assessment vérifie que les dernières mises à jour sont installées sur l'appliance. Au terme de la vérification, vous pouvez cliquer sur **Afficher les services de l'appliance** pour voir l'état et les versions des composants exécutés sur l'appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet Azure Migrate** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Évaluation de serveur > Découvrir > Gérer les appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Cliquez sur **Connexion**. Une invite de connexion Azure s’ouvre dans un nouvel onglet du navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion établie, revenez à l’application web. 
4. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des [autorisations](tutorial-prepare-hyper-v.md#prepare-azure) adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.



### <a name="delegate-credentials-for-smb-vhds"></a>Déléguer des informations d’identification pour les disques durs virtuels sur SMB

Si vous utilisez des disques durs virtuels sur des SMB, vous devez activer la délégation des informations d’identification de l’appliance aux hôtes Hyper-V. Pour cela, vous autorisez chaque hôte à agir en tant que délégué de l’appliance. Si vous avez effectué les tutoriels dans l’ordre, vous l’avez fait dans le tutoriel précédent, quand vous avez préparé Hyper-V pour l’évaluation et la migration. Vous devez avoir configuré CredSSP pour les hôtes [manuellement](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials) ou en [exécutant un script](tutorial-prepare-hyper-v.md#run-the-script) qui le fait pour vous.

Activez sur l’appliance comme suit :

#### <a name="option-1"></a>Option 1 :

Sur la machine virtuelle de l’appliance, exécutez cette commande. HyperVHost1/HyperVHost2 sont des exemples de noms d’hôte.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
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

1. À l’**Étape 1 : Fournir les informations d'identification de l'hôte Hyper-V**, cliquez sur **Ajouter les informations d'identification** afin de spécifier un nom convivial pour les informations d'identification, puis ajoutez un **Nom d'utilisateur** et un **Mot de passe** pour l'hôte/le cluster Hyper-V que l'appliance utilisera afin de détecter les machines virtuelles. Cliquez sur **Save**(Enregistrer).
1. Si vous souhaitez ajouter plusieurs informations d'identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d'autres informations d'identification. Plusieurs informations d'identification peuvent être prises en charge pour la détection de machines virtuelles Hyper-V.
1. À l’**Étape 2 : Fournir les détails de l'hôte/du cluster Hyper-V**, cliquez sur **Ajouter une source de détection** afin de spécifier l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V ainsi que le nom convivial des informations d'identification à utiliser pour la connexion à l'hôte/au cluster.
1. Vous pouvez soit **Ajouter un seul élément**  à la fois, soit **Ajouter plusieurs éléments**  en une seule fois. Une option permet également de connaître les détails de l'hôte/du cluster Hyper-V via **Importer CSV**.

    ![Sélections relatives à l'ajout d'une source de détection](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Si vous choisissez **Ajouter un seul élément**, vous devez spécifier un nom convivial pour les informations d'identification ainsi que l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V, puis cliquer sur **Enregistrer**.
    - Si vous choisissez **Ajouter plusieurs éléments** _(sélectionné par défaut)_ , vous pouvez ajouter plusieurs enregistrements à la fois en spécifiant l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V avec le nom convivial des informations d'identification dans la zone de texte. **Vérifiez** les enregistrements ajoutés et cliquez sur **Enregistrer**.
    - Si vous choisissez **Importer CSV**, vous pouvez télécharger un fichier de modèle CSV, puis renseigner le fichier avec l'**adresse IP/nom de domaine complet** de l'hôte/du cluster Hyper-V ainsi qu'un nom convivial pour les informations d'identification. Importez ensuite le fichier dans l'appliance, **vérifiez** les enregistrements dans le fichier, puis cliquez sur **Enregistrer**.

1. Dès que vous cliquerez sur Enregistrer, l'appliance essaiera de valider la connexion aux hôtes/clusters Hyper-V ajoutés et affichera l'**État de validation** dans le tableau pour chaque hôte/cluster.
    - Pour les hôtes/clusters validés avec succès, vous pouvez afficher plus de détails en cliquant sur leur adresse IP/nom de domaine complet.
    - Si la validation d'un hôte échoue, examinez l'erreur en cliquant sur **Échec de validation** dans la colonne État du tableau. Réglez le problème et recommencez la validation.
    - Pour supprimer des hôtes ou des clusters, cliquez sur **Supprimer**.
    - Vous ne pouvez pas supprimer un hôte spécifique d’un cluster. Vous pouvez supprimer seulement la totalité du cluster.
    - Vous pouvez ajouter un cluster, même s’il existe des problèmes avec des hôtes spécifiques dans le cluster.
1. Vous pouvez **revalider** la connectivité aux hôtes/clusters à tout moment avant de lancer la détection.
1. Cliquez sur **Démarrer la détection** pour lancer la détection des machines virtuelles à partir des hôtes/clusters validés. Une fois la détection lancée, vous pouvez vérifier son état pour chaque hôte/cluster du tableau.

Ceci démarre la découverte. Il faut environ 2 minutes par hôte pour que les métadonnées des serveurs détectés apparaissent sur le portail Azure.

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
