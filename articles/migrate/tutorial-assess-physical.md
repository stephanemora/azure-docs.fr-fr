---
title: Évaluer des serveurs physiques en vue d’une migration vers Azure avec Azure Migrate Server Assessment
description: Décrit comment évaluer des serveurs physiques locaux pour la migration vers Azure avec Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 25bd5241700d5950eb032a6c932470871e79945f
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514116"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Évaluer les serveurs physiques à l’aide d’Azure Migrate : Évaluation du serveur

Cet article vous montre comment évaluer des serveurs physiques locaux à l’aide de l’outil Azure Migrate : Évaluation du serveur.

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.

Ce didacticiel est le deuxième d’une série expliquant comment évaluer et migrer des serveurs virtuels physiques vers Azure. Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Configurer un projet Azure Migrate.
> * Configurez une appliance Azure Migrate qui s’exécute localement pour évaluer les serveurs physiques.
> * Démarrer la découverte continue des serveurs physiques locaux. L’appliance envoie à Azure les données de configuration et de performances des serveurs découverts.
> * Regroupez les serveurs découverts et évaluez le groupe de serveurs.
> * Passer en revue l’évaluation.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les articles sur les procédures.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

- [Effectuez](tutorial-prepare-physical.md) le premier tutoriel de cette série. Si vous ne le faites pas, les instructions de ce tutoriel ne fonctionneront pas.
- Voici ce que vous avez dû faire dans le premier tutoriel :
    - [Configurer des autorisations Azure](tutorial-prepare-physical.md) pour Azure Migrate.
    - [Préparer les serveurs physiques](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) pour l’évaluation. La configuration requise de l’appliance doit être vérifiée. Vous devez également avoir un compte configuré pour la découverte de serveurs physiques. Les ports obligatoires doivent être disponibles, et vous devez connaître les URL nécessaires pour accéder à Azure.




## <a name="set-up-an-azure-migrate-project"></a>Configurer un projet Azure Migrate

Configurez un nouveau projet Azure Migrate comme suit.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-assess-physical/assess-migrate.png)

4. Dans **Bien démarrer**, cliquez sur **Ajouter des outils**.
5. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.  
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

    - La géographie du projet sert uniquement à stocker les métadonnées collectées à partir des serveurs locaux.
    - Vous pouvez sélectionner n’importe quelle région cible quand vous exécutez une migration.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Cliquez sur **Suivant**.
8. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Azure Migrate : Server Assessment** > **Suivant**.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. Dans **Sélectionner un outil de migration**, sélectionnez **Ignorer l’ajout d’un outil de migration pour l’instant** > **Suivant**.
10. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis cliquez sur **Ajouter des outils**.
11. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Vous êtes dirigé vers la page du projet. Si vous ne voyez pas le projet, vous pouvez y accéder à partir de **Serveurs** dans le tableau de bord Azure Migrate.


## <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate

Azure Migrate : Server Assessment exécute une appliance légère.

- Cette appliance effectue la découverte du serveur physique, puis envoie les métadonnées et les données de performances du serveur à Azure Migrate Server Assessment.
- Pour configurer l’appliance, vous devez :
    - Télécharger un fichier compressé avec le script du programme d’installation Azure Migrate à partir du portail Azure.
    - Extraire le contenu du fichier compressé. Lancer la console PowerShell avec des privilèges administratifs.
    - Exécuter le script PowerShell pour lancer l’application web de l’appliance.
    - Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.
- Vous pouvez configurer plusieurs appliances pour un seul projet Azure Migrate. Parmi toutes les appliances, vous pouvez découvrir un nombre quelconque de serveurs physiques. Une appliance peut découvrir au maximum 1 000 serveurs.

### <a name="generate-the-azure-migrate-project-key"></a>Générer la clé de projet Azure Migrate

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, sélectionnez **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
3. Dans **1 : Générer une clé de projet Azure Migrate**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte de serveurs physiques ou virtuels. Le nom doit être alphanumérique et se composer au maximum de 14 caractères.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Détecter des machines pendant la création des ressources.
1. Une fois les ressources Azure créées, une **clé de projet Azure Migrate** est générée.
1. Copiez la clé, car vous en aurez besoin pour terminer l’inscription de l’appliance pendant sa configuration.

### <a name="download-the-installer-script"></a>Télécharger le script du programme d’installation

Dans **2 : Télécharger l’appliance Azure Migrate**, cliquez sur **Télécharger**.

   ![Sélections pour la découverte de machines](./media/tutorial-assess-physical/servers-discover.png)


   ![Sélections pour la génération de la clé](./media/tutorial-assess-physical/generate-key-physical.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation du cloud public : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exemple d’utilisation du cloud du secteur public : ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :
    - Pour le cloud public :

        **Scénario** | **Télécharger*** | **Valeur de hachage**
        --- | --- | ---
        Physique (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Pour Azure Government :

        **Scénario** | **Télécharger*** | **Valeur de hachage**
        --- | --- | ---
        Physique (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce

### <a name="run-the-azure-migrate-installer-script"></a>Exécuter le script du programme d’installation Azure Migrate

Le script du programme d’installation effectue les opérations suivantes :

- Installe des agents et une application web pour la découverte et l’évaluation des serveurs physiques.
- Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
- Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
    - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Exécutez le script comme suit :

1. Extrayez le fichier .zip dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un ordinateur d’une appliance Azure Migrate existante.
2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

    - Pour le cloud public : 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Pour Azure Government : 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Une fois son exécution terminé, le script lance l’application web de l’appliance.

Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Ouvrez un navigateur sur une machine qui peut se connecter à l’appliance, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en cliquant sur le raccourci de l’application.
2. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Connectivité** : L’application vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
        - Cliquez sur **Configurer le proxy** et spécifiez l’adresse du proxy (sous la forme http://ProxyIPAddress ou http://ProxyFQDN) ) et le port d’écoute.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
        - Si vous avez ajouté des détails de proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte de serveur fonctionne correctement.
    - **Installer les mises à jour** : L’évaluation de serveur Azure Migrate vérifie que les dernières mises à jour sont installées sur l’appliance. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des composants s’exécutant sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet Azure Migrate** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Évaluation de serveur > Découvrir > Gérer les appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Cliquez sur **Connexion**. Une invite de connexion Azure s’ouvre dans un nouvel onglet du navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion établie, revenez à l’application web. 
4. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des [autorisations](tutorial-prepare-physical.md) adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l’inscription de l’appliance terminée, vous pouvez consulter les détails de l’inscription en cliquant sur **Afficher les détails**.


## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

À présent, connectez-vous à partir de l’appliance aux serveurs physiques à découvrir, puis démarrez la découverte.

1. À l’**Étape 1 : Fournir les informations d’identification pour la découverte des serveurs physiques ou virtuels Windows et Linux**, cliquez sur **Ajouter les informations d’identification** pour attribuer un nom convivial aux informations d’identification, ajoutez un **nom d’utilisateur** et un **mot de passe** pour un serveur Windows ou Linux. Cliquez sur **Enregistrer**.
1. Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d’identification. Plusieurs informations d’identification sont prises en charge pour la découverte de serveurs physiques.
1. Dans **Étape 2 : Fournir les détails du serveur physique ou virtuel**, cliquez sur **Ajouter une source de découverte** pour spécifier l’**adresse IP/nom de domaine complet** du serveur ainsi que le nom convivial des informations d’identification pour se connecter au serveur.
1. Vous pouvez soit **ajouter un seul élément** à la fois, soit **ajouter plusieurs éléments** simultanément. Il existe également une option permettant de fournir les détails du serveur via **Importer au format CSV**.

    ![Sélections pour l’ajout de la source de découverte](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Si vous choisissez **Ajouter un seul élément**, vous pouvez choisir le type de système d’exploitation, attribuer un nom convivial aux informations d’identification, ajouter l’**adresse IP/nom de domaine complet** du serveur, puis cliquer sur **Enregistrer**.
    - Si vous choisissez **Ajouter plusieurs éléments**, vous pouvez ajouter plusieurs enregistrements à la fois en spécifiant l’**adresse IP/nom de domaine complet** du serveur avec le nom convivial des informations d’identification dans la zone de texte. **Vérifiez** les enregistrements ajoutés, puis cliquez sur **Enregistrer**.
    - Si vous choisissez **Importer au format CSV** _(sélectionné par défaut)_ , vous pouvez télécharger un fichier de modèle CSV, compléter le fichier avec l’**adresse IP/nom de domaine complet** du serveur et le nom convivial des informations d’identification. Importez ensuite le fichier dans l’appliance, **vérifiez** les enregistrements dans le fichier, puis cliquez sur **Enregistrer**.

1. Dès que vous cliquez sur Enregistrer, l’appliance tente de valider la connexion aux serveurs ajoutés et affiche l’**état de validation** de chaque serveur dans le tableau.
    - Si la validation échoue pour un serveur, examinez l’erreur en cliquant sur **Échec de la validation** dans la colonne État du tableau. Corrigez le problème et recommencez la validation.
    - Pour supprimer un serveur, cliquez sur **Supprimer**.
1. Vous pouvez **revalider** la connectivité aux serveurs à tout moment avant de lancer la découverte.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte des serveurs validés. Une fois la découverte lancée, vous pouvez vérifier l’état de la découverte de chaque serveur dans le tableau.


Ceci démarre la découverte. Il faut environ deux minutes par serveur pour que les métadonnées du serveur détecté s’affichent sur le portail Azure.

### <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte effectuée, vous pouvez vérifier que les serveurs apparaissent dans le portail Azure.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.

## <a name="set-up-an-assessment"></a>Configurer une évaluation

Vous pouvez créer deux types d’évaluation à l’aide d’Azure Migrate : Server Assessment.

**Évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | **Taille de machine virtuelle recommandée** : En fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : En fonction des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : En fonction de la taille du serveur local<br/><br> **Type de disque recommandé** : En fonction du paramètre de type de stockage que vous sélectionnez pour l’évaluation.


### <a name="run-an-assessment"></a>Exécuter une évaluation

Exécutez une évaluation comme suit :

1. Passez en revue les [meilleures pratiques](best-practices-assessment.md) liées à la création d’évaluations.
2. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : Mosaïque**  d’évaluation de serveur, cliquez sur **Évaluer**.

    ![Évaluer](./media/tutorial-assess-physical/assess.png)

2. Dans **Évaluer les serveurs**, spécifiez un nom pour l’évaluation.
3. Cliquez sur **Tout afficher** pour passer en revue les propriétés de l’évaluation.

    ![Propriétés de l’évaluation](./media/tutorial-assess-physical/view-all.png)

3. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. Un groupe rassemble un ou plusieurs serveurs à évaluer.
4. Dans **Ajouter des machines au groupe**, sélectionnez les serveurs à ajouter au groupe.
5. Cliquez sur **Créer une évaluation** pour créer le groupe, puis exécutez l’évaluation.

    ![Créer une évaluation](./media/tutorial-assess-physical/assessment-create.png)

6. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment** > **Évaluations**.
7. Cliquez sur **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Excel.



## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : Indique si les serveurs peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : Coûts mensuels de calcul et de stockage estimés pour l’exécution des serveurs dans Azure.
- **Estimation des coûts de stockage mensuels** : Coûts estimés pour le stockage sur disque après la migration.

### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** >  **Serveurs**, cliquez sur **Évaluations** dans **Azure Migrate : Évaluation de serveur**.
2. Dans **Évaluations**, cliquez sur une évaluation pour l’ouvrir.

    ![Récapitulatif de l’évaluation](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examiner la préparation pour Azure

1. Dans **Préparé pour Azure**, vérifiez si les serveurs sont prêts pour la migration vers Azure.
2. Passez en revue l’état :
    - **Disponible pour Azure** : Azure Migrate recommande une taille de machine virtuelle et donne des estimations de coût pour les machines virtuelles de l’évaluation.
    - **Disponible sous conditions** : Montre des problèmes et leur correction suggérée.
    - **Non disponible pour Azure** : Montre des problèmes et leur correction suggérée.
    - **État de la préparation inconnu** : Utilisé quand Azure Migrate ne peut pas évaluer la préparation, en raison de problèmes de disponibilité des données.

2. Cliquez sur un état **Préparé pour Azure**. Vous pouvez voir les informations relatives à l’état de préparation des serveurs et explorer ces derniers en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.



### <a name="review-cost-details"></a>Passer en revue les détails des coûts

Cette vue montre une estimation des coûts de calcul et de stockage liés à l’exécution des machines virtuelles dans Azure.

1. Passez en revue les coûts mensuels de calcul et de stockage. Les coûts sont agrégés pour tous les serveurs du groupe évalué.

    - Les estimations des coûts sont basées sur les suggestions de taille d’une machine ainsi que sur ses disques et ses propriétés.
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés.
    - L’estimation des coûts porte sur l’exécution des serveurs locaux en tant que machines virtuelles IaaS. Azure Migrate Server Assessment ne prend pas en compte les coûts PaaS ou SaaS.

2. Vous pouvez passer en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon différents types de disque de stockage.
3. Vous pouvez explorer les détails de serveurs spécifiques.


### <a name="review-confidence-rating"></a>Examiner le niveau de confiance

Quand vous effectuez des évaluations basées sur les performances, un niveau de confiance est affecté à l’évaluation.

![Niveau de confiance](./media/tutorial-assess-physical/confidence-rating.png)

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

Passez au troisième didacticiel de la série pour découvrir comment migrer des serveurs physiques vers Azure avec Azure Migrate : Server Migration.

> [!div class="nextstepaction"]
> [Migrer des serveurs physiques](./tutorial-migrate-physical-virtual-machines.md)
