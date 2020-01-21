---
title: Évaluer des serveurs à l’aide de données serveur importées avec Azure Migrate Server Assessment
description: Décrit comment évaluer des serveurs locaux pour la migration vers Azure avec l’évaluation de serveur Azure Migrate et de données importées.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 060399952545c903fec8ecf08d99e438883c9fd1
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902537"
---
# <a name="assess-servers-by-using-imported-data"></a>Évaluer des serveurs à l’aide de données importées

Cet article explique comment évaluer des serveurs locaux avec l’outil [Azure Migrate : Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool), en important des métadonnées de serveur au format CSV (valeurs séparées par des virgules). Cette méthode d’évaluation ne vous oblige pas à configurer l’appliance Azure Migrate pour créer une évaluation. Elle est utile si :

- Vous souhaitez créer une évaluation initiale rapide avant de déployer l’appliance.
- Vous ne pouvez pas déployer l’appliance Azure Migrate dans votre organisation.
- Vous ne pouvez pas partager des informations d’identification qui autorisent l’accès aux serveurs locaux.
- Les contraintes de sécurité vous empêchent de collecter et d’envoyer les données collectées par l’appliance à Azure. Vous pouvez contrôler les données que vous partagez dans un fichier importé. En outre, la plupart des données (par exemple, la fourniture d’adresses IP) sont facultatives.

## <a name="before-you-start"></a>Avant de commencer

Tenez compte des points suivants :

- Vous pouvez ajouter au maximum 20 000 serveurs dans un même fichier CSV.
- Vous pouvez ajouter jusqu’à 20 000 serveurs dans un projet Azure Migrate au format CSV.
- Vous pouvez charger plusieurs fois les informations sur le serveur vers Server Assessment au format CSV.
- La collecte d’informations sur l’application est utile lors de l’évaluation de votre environnement local pour la migration. Toutefois, Server Assessment n’effectue pas actuellement d’évaluation au niveau de l’application, et ne prend pas en compte les applications lors de la création d’une évaluation.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Configurer un projet Azure Migrate.
> * Remplir un fichier CSV avec les informations sur le serveur.
> * Importer le fichier pour ajouter les informations sur le serveur dans Server Assessment.
> * Créer une évaluation et l’examiner.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné, afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les guides pratiques.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="set-azure-permissions-for-azure-migrate"></a>Configurer des autorisations Azure pour Azure Migrate

votre compte Azure doit être autorisé à créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis sélectionnez-le pour afficher les autorisations.
3. Vérifiez que vous disposez des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

## <a name="set-up-an-azure-migrate-project"></a>Configurer un projet Azure Migrate

Pour configurer un nouveau projet Azure Migrate

1. Dans le portail Azure, dans **Tous les services**, recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sous **Découvrir, évaluer et migrer des serveurs**, sélectionnez **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-assess-import/assess-migrate.png)

4. Dans **Mise en route**, sélectionnez **Ajouter des outils**.
5. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. Pour plus d'informations :

    - Passez en revue les [zones géographiques prises en charge](migrate-support-matrix.md#supported-geographies). La géographie du projet sert uniquement à stocker les métadonnées rassemblées à partir des machines virtuelles locales.
    - Vous pouvez sélectionner n’importe quelle région cible quand vous exécutez une migration.

    ![Créer un projet Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Sélectionnez **Suivant**.
8. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Azure Migrate : Server Assessment** > **Suivant**.

    ![Créer une évaluation Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. Dans **Sélectionner un outil de migration**, sélectionnez **Ignorer l’ajout d’un outil de migration pour l’instant** > **Suivant**.
10. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis sélectionnez **Ajouter des outils**.
11. Attendez quelques minutes, le temps nécessaire au déploiement du projet Azure Migrate. Vous serez alors dirigé vers la page du projet. Si vous ne voyez pas le projet, vous pouvez y accéder à partir de **Serveurs** dans le tableau de bord Azure Migrate.

## <a name="prepare-the-csv"></a>Préparer le fichier CSV

Téléchargez le modèle CSV et ajoutez-y les informations sur le serveur.

### <a name="download-the-template"></a>Téléchargez le modèle

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, sélectionnez **Découvrir**.
2. Dans **Détecter des machines**, sélectionnez **Importer à l’aide de CSV**.
3. Sélectionnez **Télécharger** pour télécharger le modèle CSV. Vous pouvez aussi [le télécharger directement](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Télécharger le modèle CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Ajouter les informations sur le serveur

Collectez les données du serveur et ajoutez-les au fichier CSV.

- Pour collecter les données, vous pouvez les exporter à partir des outils que vous utilisez pour la gestion des serveurs locaux, par exemple VMware vSphere ou votre base de données de gestion des configurations (CMDB).
- Pour consulter les exemples de données, téléchargez notre [exemple de fichier](https://go.microsoft.com/fwlink/?linkid=2108405).

Le tableau suivant récapitule les champs du fichier à remplir :

**Nom du champ** | **Obligatoire** | **Détails**
--- | --- | ---
**Nom du serveur** | Oui | Nous vous recommandons de spécifier le nom de domaine complet (FQDN).
**Adresse IP** | Non | Adresse du serveur.
**Cœurs** | Oui | Nombre de cœurs de processeur alloués au serveur.
**Mémoire** | Oui | RAM totale, en Mo, allouée au serveur.
**Nom du système d’exploitation** | Oui | Système d’exploitation du serveur.
**Version du SE** | Non | Version du système d’exploitation du serveur.
**Nombre de disques** | Non | Inutile si les détails sur le disque individuel sont fournis.
**Taille du disque 1**  | Non | Taille maximale du disque, en Go<br/>Vous pouvez ajouter les détails d’autres disques en [ajoutant des colonnes](#add-multiple-disks) dans le modèle. Vous pouvez ajouter jusqu’à huit disques.
**Opérations de lecture sur le disque 1** | Non | Opérations de lecture sur le disque par seconde.
**Opérations d’écriture sur le disque 1** | Non | Opérations d’écriture sur le disque par seconde.
**Débit de lecture sur le disque 1** | Non | Données lues à partir du disque par seconde, en Mo par seconde.
**Débit d’écriture sur le disque 1** | Non | Données écrites sur le disque par seconde, en Mo par seconde.
**Pourcentage d’utilisation du processeur** | Non | Pourcentage d’utilisation du processeur.
**Pourcentage d’utilisation de la mémoire** | Non | Pourcentage de RAM utilisé.
**Total des opérations de lecture sur les disques** | Non | Opérations de lecture sur le disque par seconde.
**Total des opérations d’écriture sur les disques** | Non | Opérations d’écriture sur le disque par seconde.
**Total du débit de lecture sur les disques** | Non | Données lues à partir du disque, en Mo par seconde.
**Total du débit d’écriture sur les disques** | Non | Données écrites sur le disque, en Mo par seconde.
**Débit d’entrée réseau** | Non | Données reçues par le serveur, en Mo par seconde.
**Débit de sortie réseau** | Non | Données transmises par le serveur, en Mo par seconde.
**Type de microprogramme** | Non | Microprogramme du serveur. Les valeurs peuvent être « BIOS » ou « UEFI ».
**Type de serveur** | Non | Les valeurs peuvent être « Physique » ou « Virtuel ».
**Hyperviseur** | Non | Hyperviseur sur lequel un ordinateur est en cours d’exécution. <br/> Les valeurs peuvent être « VMware », « Hyper-V », « Xen », « AWS », « GCP » ou « Autre ».
**Numéro de version de l’hyperviseur** | Non | Version de l’hyperviseur.
**ID de machine virtuelle** | Non | Identificateur de la machine virtuelle. Il s’agit de la valeur **InstanceUUid** pour la machine virtuelle VMware vCenter, ou de l’**ID de machine virtuelle Hyper-V** pour Hyper-V.
**ID Virtual Machine Manager** | Non | Il s’agit de la valeur **InstanceUUid** pour VMWare vCenter. Elle n’est pas nécessaire pour Hyper-V.
**Adresse MAC**| Non | Adresse MAC du serveur.
**ID BIOS** | Non | ID BIOS du serveur.
**ID de serveur personnalisé** | Non | ID de serveur uniques locaux sur site. <br/> Utiles pour le suivi du serveur importé par ID local.
**Nom de l’application 1** | Non | Nom de la charge de travail exécutée sur le serveur.<br/>Vous pouvez ajouter les détails d’autres applications en [ajoutant des colonnes](#add-multiple-applications) dans le modèle. Vous pouvez ajouter jusqu’à cinq applications.
**Type d’application 1** | Non | Type de charge de travail en cours d’exécution sur le serveur
**Version d’application 1** | Non | Version de la charge de travail en cours d’exécution sur le serveur.
**Expiration de la licence de l’application 1** | Non | Expiration de la licence de la charge de travail (le cas échéant).
**Unité commerciale** | Non | Unité commerciale à laquelle appartient le serveur.
**Propriétaire commercial** | Non | Propriétaire de l’unité commerciale.
**Nom de l’application métier** | Non | Nom de l’application à laquelle l’application appartient.
**Lieu** | Non | Centre de données dans lequel se trouve le serveur.
**Date de retrait du serveur** | Non | Date de retrait du serveur physique ou du serveur physique sous-jacent du serveur virtuel.

### <a name="add-operating-systems"></a>Ajouter des systèmes d’exploitation

L’évaluation reconnaît des noms de système d’exploitation spécifiques. Tout nom que vous spécifiez doit correspondre exactement à l’une des chaînes de la [liste des noms pris en charge](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Ajouter plusieurs disques

Le modèle fournit des champs par défaut pour le premier disque. Vous pouvez ajouter des colonnes similaires pour un maximum de huit disques.

Par exemple, pour spécifier tous les champs pour un deuxième disque, ajoutez ces colonnes :

- Taille du disque 2
- Opérations de lecture sur le disque 2
- Opérations d’écriture sur le disque 2
- Débit de lecture sur le disque 2
- Débit d’écriture sur le disque 2

### <a name="add-multiple-applications"></a>Ajouter plusieurs applications

Le modèle fournit des champs pour une seule application. Vous pouvez ajouter des colonnes similaires pour un maximum de 5 applications.  

Par exemple, pour spécifier tous les champs pour une deuxième application, ajoutez ces colonnes :

- Nom de l’application 2
- Type de l’application 2
- Version de l’application 2
- Expiration de la licence de l’application 2

> [!NOTE]
> Les informations sur l’application sont utiles lors de l’évaluation de votre environnement local pour la migration. Toutefois, Azure Migrate Server Assessment n’effectue pas actuellement d’évaluation au niveau de l’application, et ne prend pas en compte les applications lors de la création d’une évaluation.

## <a name="import-the-server-information"></a>Importer les informations du serveur

Après avoir ajouté des informations au modèle CSV, importez les serveurs dans Server Assessment.

1. Dans Azure Migrate, dans **Détecter des machines**, accédez au modèle terminé.
2. Sélectionnez **Importer**.
3. L’état de l’importation est affiché.
    - Si des avertissements s’affichent dans l’état, vous pouvez soit les corriger, soit poursuivre sans les résoudre.
    - Pour améliorer la précision de l’évaluation, améliorez l’exactitude des informations sur le serveur, comme indiqué dans les avertissements.
    - Pour afficher et corriger les avertissements, sélectionnez **Télécharger le fichier .CSV des détails de l’avertissement**. Cette opération télécharge le fichier CSV avec des avertissements inclus. Passez en revue les avertissements et résolvez les problèmes si nécessaire.
    - Si des erreurs s’affichent dans l’état et que l’état de l’importation est **Échec**, vous devez corriger ces erreurs avant de pouvoir continuer l’importation :
        1. Téléchargez le fichier CSV, qui contient désormais les détails sur l’erreur.
        1. Examinez et corrigez les erreurs en fonction des besoins. 
        1. Rechargez le fichier modifié.
4. Quand l’état de l’importation est **Terminé**, cela signifie que les informations sur le serveur ont été importées.

## <a name="update-server-information"></a>Mettre à jour les informations sur le serveur

Vous pouvez mettre à jour les informations sur un serveur en réimportant les données du serveur avec le même **nom de serveur**. Vous ne pouvez pas modifier le champ **Nom de serveur**. La suppression de serveurs n’est pas prise en charge actuellement.

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Pour vérifier que les serveurs apparaissent dans le portail Azure une fois la découverte effectuée

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, sélectionnez l’icône qui affiche le nombre de **Serveurs découverts**.
3. Sélectionnez l’onglet **Basé sur l’importation**.

## <a name="set-up-and-run-an-assessment"></a>Configurer et exécuter une évaluation

Vous pouvez créer deux types d’évaluations avec Server Assessment.

**Type d’évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les valeurs des données de performances spécifiées. | **Taille de machine virtuelle recommandée** : dépend des données d’utilisation du processeur et de la mémoire.<br/><br/> **Type de disque recommandé (disque managé Standard ou Premium)**  : dépend des IOPS et du débit des disques locaux.
**Localement** | Évaluations basées sur le dimensionnement local. | **Taille de machine virtuelle recommandée** : dépend de la taille de serveur spécifiée.<br/><br> **Type de disque recommandé** : dépend du paramètre de type de stockage que vous sélectionnez pour l’évaluation.

Pour exécuter une évaluation

1. Passez en revue les [meilleures pratiques](best-practices-assessment.md) liées à la création d’évaluations.
2. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : Server Assessment**, sélectionnez **Évaluer**.

    ![Évaluer](./media/tutorial-assess-physical/assess.png)

3. Dans **Évaluer les serveurs**, spécifiez un nom pour l’évaluation.
4. Dans **Source de découverte**, sélectionnez **Machines ajoutées via l’importation dans Azure Migrate**.
5. Sélectionnez **Tout afficher** pour passer en revue les propriétés de l’évaluation.

    ![Propriétés de l’évaluation](./media/tutorial-assess-physical/view-all.png)

6. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe. Un groupe rassemble une ou plusieurs machines virtuelles à évaluer.
7. Dans **Ajouter des machines au groupe**, sélectionnez les serveurs à ajouter au groupe.
8. Sélectionnez **Créer une évaluation** pour créer le groupe, puis exécutez l’évaluation.

    ![Créer une évaluation](./media/tutorial-assess-physical/assessment-create.png)

9. Une fois l’évaluation créée, vous pouvez la voir dans **Serveurs** > **Azure Migrate : Server Assessment** > **Évaluations**.
10. Sélectionnez **Exporter l’évaluation** pour la télécharger sous la forme d’un fichier Microsoft Excel.

## <a name="review-an-assessment"></a>Réviser une évaluation

Une évaluation décrit les éléments suivants :

- **Préparé pour Azure** : indique si les serveurs peuvent faire l’objet d’une migration vers Azure.
- **Estimation des coûts mensuels** : coûts mensuels de calcul et de stockage estimés pour l’exécution des serveurs dans Azure.
- **Estimation des coûts de stockage mensuels** : coûts estimés pour le stockage sur disque après la migration.

### <a name="view-an-assessment"></a>Voir une évaluation

1. Dans **Objectifs de migration** > **Serveurs**, sélectionnez **Évaluations** dans **Azure Migrate : Évaluation de serveur**.
2. Dans **Évaluations**, sélectionnez une évaluation pour l’ouvrir.

    ![Récapitulatif de l’évaluation](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examiner la préparation pour Azure

1. Dans **Préparé pour Azure**, déterminez si les serveurs sont prêts pour la migration vers Azure.
2. Passez en revue l’état :
    - **Disponible pour Azure** : Azure Migrate recommande une taille de machine virtuelle et donne des estimations de coût pour les machines virtuelles de l’évaluation.
    - **Disponible sous conditions** : affiche les problèmes et les corrections suggérées.
    - **Non disponible pour Azure** : affiche les problèmes et les corrections suggérées.
    - **État de la préparation inconnu** : Azure Migrate ne peut pas évaluer la préparation, en raison de problèmes de disponibilité des données.

3. Sélectionnez un état **Préparé pour Azure**. Vous pouvez voir les informations relatives à l’état de préparation des serveurs et explorer ces derniers en détail, notamment en ce qui concerne les valeurs pour le calcul, le stockage et le réseau.

### <a name="review-cost-details"></a>Passer en revue les détails des coûts

Cette vue montre une estimation des coûts de calcul et de stockage liés à l’exécution des machines virtuelles dans Azure. Vous pouvez :

- Passez en revue les coûts mensuels de calcul et de stockage. Les coûts sont agrégés pour tous les serveurs du groupe évalué.

    - Les estimations des coûts sont basées sur les suggestions de taille d’une machine ainsi que sur ses disques et ses propriétés.
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés.
    - L’estimation du coût s’applique à l’exécution des serveurs locaux en tant que machines virtuelles IaaS (Infrastructure-as-a-Service). Server Assessment ne tient pas compte des coûts PaaS (Platform-as-a-Service) ou SaaS (Software-as-a-Service).

- Passez en revue les estimations des coûts de stockage mensuels. Cette vue montre les coûts de stockage agrégés pour le groupe évalué, répartis selon différents types de disques de stockage.
- Explorez les détails des machines virtuelles spécifiques.

> [!NOTE]
> Les niveaux de confiance ne sont pas attribuées aux évaluations des serveurs importés dans Server Assessment à l’aide d’un fichier CSV.

## <a name="supported-operating-system-names"></a>Noms des systèmes d’exploitation pris en charge

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professionnel
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * importés des serveurs dans Azure Migrate : Server Assessment au format CSV.
> * créé et passé en revue une évaluation.

À présent, [déployez une appliance](./migrate-appliance.md) pour des évaluations plus précises, et regroupez les serveurs afin d’effectuer une évaluation plus poussée à l’aide d’une [analyse des dépendances](./concepts-dependency-visualization.md).
