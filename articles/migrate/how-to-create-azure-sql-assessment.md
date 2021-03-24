---
title: Créer une évaluation Azure SQL
description: Découvrez comment évaluer des instances SQL en vue de leur migration vers Azure SQL Managed Instance et Azure SQL Database.
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 21553adf81d5b34813785db7cd6bbe3caf71c210
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780483"
---
# <a name="create-an-azure-sql-assessment"></a>Créer une évaluation Azure SQL

Dans le cadre de votre migration vers Azure, vous devez évaluer vos charges de travail locales afin de déterminer la préparation nécessaire, d’identifier les risques et d’estimer les coûts ainsi que le niveau de complexité de la tâche.
Cet article explique comment évaluer les instances SQL découvertes en vue de leur migration vers Azure SQL à l’aide de l’outil Azure Migrate : Détection et évaluation.

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. 

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](./create-manage-projects.md) un projet Azure Migrate et que vous avez ajouté l’outil Azure Migrate : Détection et évaluation.
- Pour créer une évaluation, vous devez configurer une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md). L’appliance découvre les serveurs locaux et envoie les métadonnées et les données de performances à Azure Migrate. [En savoir plus](migrate-appliance.md)

## <a name="azure-sql-assessment-overview"></a>Vue d’ensemble des évaluations Azure SQL
Vous pouvez créer une évaluation Azure SQL avec un critère de dimensionnement **basé sur les performances**. 

**Critère de dimensionnement** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations basées sur les données de performances collectées | La **configuration Azure SQL** recommandée est basée sur les données de performances des instances et bases de données SQL, notamment l’utilisation de l’UC, le nombre de cœurs, les organisations et tailles des fichiers de base de données, les E/S de fichiers, les requêtes par lots par seconde et la taille et l’utilisation de la mémoire par chaque base de données.

[En savoir plus](concepts-azure-sql-assessment-calculation.md) sur les évaluations Azure SQL.

## <a name="run-an-assessment"></a>Exécuter une évaluation
Exécutez une évaluation comme suit :
1. Sur la page **Vue d’ensemble** > **Windows, Linux et SQL Server**, cliquez sur **Évaluer et migrer des serveurs**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Page Vue d’ensemble d’Azure Migrate":::
2. Sur **Azure Migrate : Détection et évaluation**, cliquez sur **Évaluer** et choisissez le type d’évaluation **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Liste déroulante permettant de choisir le type d’évaluation Azure SQL":::
3. Dans **Évaluer les serveurs**, vous pouvez voir que le type d’évaluation **Azure SQL** est présélectionné et que la source de détection est **Serveurs découverts par l’appliance Azure Migrate** par défaut.

4. Cliquez sur **Modifier** pour passer en revue les propriétés de l’évaluation.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Bouton Modifier à partir duquel les propriétés de l’évaluation peuvent être personnalisées":::
5. Dans Propriétés de l’évaluation > **Propriétés cibles** :
    - Dans **Emplacement cible**, sélectionnez la région Azure vers laquelle vous souhaitez effectuer la migration. 
        - Les recommandations en matière de configuration et de coût d’Azure SQL sont basées sur l’emplacement que vous spécifiez. 
    - Dans **Type de déploiement cible** :
        - Sélectionnez **Recommandé** si vous souhaitez qu’Azure Migrate évalue l’état de préparation de vos instances SQL pour la migration vers Azure SQL MI et Azure SQL DB et recommande l’option de déploiement cible, le niveau cible, la configuration d’Azure SQL et les estimations mensuelles les mieux adaptés. [En savoir plus](concepts-azure-sql-assessment-calculation.md)
        - Sélectionnez **Azure SQL DB** si vous souhaitez évaluer l’état de préparation de vos instances SQL pour la migration uniquement vers Azure SQL Database et examiner le niveau cible, la configuration d’Azure SQL et les estimations mensuelles.
        - Sélectionnez **Azure SQL MI** si vous souhaitez évaluer l’état de préparation de vos instances SQL pour la migration uniquement vers Azure SQL Managed Instance et examiner le niveau cible, la configuration d’Azure SQL et les estimations mensuelles.
    - Dans **Capacité de réserve**, spécifiez si vous souhaitez utiliser la capacité de réserve pour le serveur SQL après la migration.
        - Si vous sélectionnez une option capacité de réserve, vous ne pouvez pas spécifier de « Remise (%) ».

6. Dans Propriétés de l’évaluation > **Critères d’évaluation** :
    - Par défaut, le critère Dimensionnement est **basé sur les performances**, ce qui signifie qu’Azure Migrate collecte les mesures de performances relatives aux instances SQL et aux bases de données qu’elles gèrent afin de recommander une configuration de taille optimale pour Azure SQL Database et/ou Azure SQL Managed Instance. Vous pouvez spécifier les valeurs suivantes :
        - **Historique des performances** pour indiquer la période de données sur laquelle vous souhaitez baser l’évaluation. (La valeur par défaut est une journée.)
        - **Utilisation en centile** pour indiquer la valeur de centile que vous souhaitez utiliser pour l’échantillon de performances. (La valeur par défaut est le 95e centile.)
    - Dans **Facteur de confort**, indiquez la mémoire tampon que vous souhaitez utiliser lors de l’évaluation. Celle-ci prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation. Par exemple, si vous utilisez un facteur de confort de 2 : 
        
        **Composant** | **Utilisation efficace** | **Ajouter un facteur de confort (2.0)**
        --- | --- | ---
        Cœurs | 2  | 4
        Mémoire | 8 Go | 16 Go
   
7. Dans **Tarifs** :
    - Dans **Offre/programme de licence**, spécifiez l’offre Azure à laquelle vous êtes inscrit, le cas échéant. Pour le moment, vous ne pouvez choisir qu’entre le paiement à l’utilisation et le Dev/Test de paiement à l’utilisation. 
        - Vous pouvez bénéficier d’une remise supplémentaire en appliquant une capacité de réserve et Azure Hybrid Benefit en plus de l’offre de paiement à l’utilisation. 
        - Vous pouvez appliquer Azure Hybrid Benefit en plus de Dev/Test en paiement à l’utilisation. Actuellement, l’évaluation ne prend pas en charge l’application de la capacité de réserve en plus de l’offre Dev/Test en paiement à l’utilisation.
    - Dans **Niveau de service**, choisissez l’option de niveau de service la plus appropriée en fonction des besoins de votre entreprise pour la migration vers Azure SQL Database et/ou Azure SQL Managed Instance : 
        - Sélectionnez **Recommandé** si vous souhaitez qu’Azure Migrate recommande le niveau de service le plus adapté à vos serveurs. Ce peut être Usage général ou Critique pour l’entreprise. En savoir plus
        - Sélectionnez **Usage général** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail axées sur le budget.
        - Sélectionnez **Critique pour l’entreprise** si vous souhaitez une configuration d’Azure SQL conçue pour des charges de travail à faible latence avec une haute résilience aux échecs et des basculements rapides.
    - Dans **Remise (%)** , ajoutez les remises applicables à votre abonnement que vous recevez en plus de l’offre Azure. Le paramètre par défaut est 0 %.
    - Dans **Devise**, sélectionnez la devise de facturation de votre compte.
    - Dans **Azure Hybrid Benefit**, indiquez si vous disposez déjà d’une licence SQL Server. Si c’est le cas et si vous êtes couvert par une Software Assurance active des abonnements SQL Server, vous pouvez demander Azure Hybrid Benefit au moment d’importer les licences dans Azure.
    - Si vous avez apporté des modifications, cliquez sur Enregistrer.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Bouton Enregistrer dans les propriétés de l’évaluation":::
8. Dans **Évaluer les serveurs**, cliquez sur Suivant.
9.  Dans **Sélectionner les serveurs à évaluer** > **Nom de l’évaluation**, spécifiez un nom pour l’évaluation.
10. Dans **Sélectionner ou créer un groupe**, sélectionnez **Créer**, puis spécifiez un nom de groupe.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Emplacement du bouton Nouveau groupe":::
11. Sélectionnez l’appliance, puis sélectionnez les serveurs que vous souhaitez ajouter au groupe. Puis cliquez sur Suivant.
12. Dans **Vérifier + créer l’évaluation**, passez en revue les détails de l’évaluation, puis cliquez sur Créer une évaluation pour créer le groupe et lancer l’évaluation.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Emplacement du bouton Vérifier et créer l’évaluation.":::
13. Une fois l’évaluation créée, accédez à la vignette **Windows, Linux et SQL Server** > **Azure Migrate : Détection et évaluation**, puis cliquez sur le numéro à côté de l’évaluation d’Azure SQL.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navigation jusqu’à l’évaluation créée":::
15. Cliquez sur le nom de l’évaluation que vous souhaitez afficher.

> [!NOTE]
> Comme les évaluations d’Azure SQL sont des évaluations basées sur les performances, nous vous recommandons d’attendre au moins un jour après le démarrage de la détection pour créer l’évaluation. Cela permet de collecter des données de performances avec un plus haut niveau de confiance. Si votre détection est toujours en cours, l’état de préparation de vos instances SQL sera marqué comme **Inconnu**. Dans l’idéal, une fois que vous avez démarré la détection, **attendez la période de performances que vous avez spécifiée (jour/semaine/mois)** avant de créer ou de recalculer l’évaluation afin d’obtenir un haut niveau de confiance. 

## <a name="review-an-assessment"></a>Réviser une évaluation

**Pour voir une évaluation** :

1. **Windows, Linux et SQL Server** > **Azure Migrate : Détection et évaluation**, puis cliquez sur le numéro à côté de l’évaluation d’Azure SQL.
2. Cliquez sur le nom de l’évaluation que vous souhaitez afficher. Voici un exemple (estimations et coûts, à titre d’exemple uniquement) : :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Vue d’ensemble de l’évaluation SQL":::
3. Passez en revue le récapitulatif de l’évaluation. Vous pouvez également modifier les propriétés de l’évaluation ou recalculer celle-ci.

#### <a name="discovered-items"></a>Éléments découverts

Cela indique le nombre de serveurs SQL, d’instances et de bases de données qui ont été évalués dans cette évaluation.
    
#### <a name="azure-readiness"></a>Préparé pour Azure

Cela indique la distribution des instances SQL évaluées : 
    
**Type de déploiement cible (dans les propriétés de l’évaluation)** | **Préparation**   
--- | --- |
**Recommandé** |  Prêt pour Azure SQL Database, Prêt pour Azure SQL Managed Instance, Potentiellement prêt pour la machine virtuelle Azure, Préparation inconnue (en cas de détection en cours ou de problèmes de détection à résoudre)
**Azure SQL DB** ou **Azure SQL MI** | Prêt pour Azure SQL Database ou Azure SQL Managed Instance, Non prêt pour Azure SQL Database ou Azure SQL Managed Instance, Préparation inconnue (en cas de détection en cours ou de problèmes de détection à résoudre)
     
Vous pouvez explorer au niveau du détail pour comprendre les détails concernant les avertissements/problèmes de migration que vous pouvez résoudre avant la migration vers Azure SQL. [En savoir plus](concepts-azure-sql-assessment-calculation.md). Vous pouvez également consulter les configurations recommandées d’Azure SQL pour la migration vers des bases de données Azure SQL et/ou des instances gérées.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Détails du coût d’Azure SQL Database et de Managed Instance

L’estimation du coût mensuel comprend les coûts de calcul et de stockage pour les configurations d’Azure SQL correspondant au type de déploiement recommandé d’Azure SQL Database et/ou Azure SQL Managed Instance. [En savoir plus](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Vérifier la préparation nécessaire

1. Cliquez sur **État de préparation d’Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Détails de l’état de préparation d’Azure SQL":::
1. Dans l’état de préparation d’Azure SQL, passez en revue l’**état de préparation d’Azure SQL DB** et l’**état de préparation d’Azure SQL MI** pour les instances SQL évaluées :
    - **Prêt** : L’instance est prête à être migrée vers Azure SQL DB/MI sans problème de migration ni avertissement. 
        - Prêt (icône d’informations bleue avec lien hypertexte) : L’instance est prête à être migrée vers Azure SQL DB/MI sans problème de migration, mais présente quelques avertissements de migration que vous devez examiner. Vous pouvez cliquer sur le lien hypertexte pour passer en revue les avertissements de migration et l’aide relative à la correction recommandée : :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="Évaluation avec état Prêt":::       
    - **Non prêt** : L’instance présente un ou plusieurs problèmes relatifs à la migration vers Azure SQL DB/MI. Vous pouvez cliquer sur le lien hypertexte et passer en revue les problèmes de migration et l’aide relative à la correction recommandée.
    - **Inconnu** : Azure Migrate ne peut pas évaluer l’état de préparation, car la détection est en cours ou des problèmes rencontrés pendant la détection doivent être résolus à partir du panneau des notifications. Si le problème persiste, contactez le support technique Microsoft. 
1. Examinez le type de déploiement recommandé pour l’instance SQL qui est déterminé par la matrice ci-dessous :

    - **Type de déploiement cible** (tel que sélectionné dans les propriétés de l’évaluation) : **Recommandé**

        **Préparation à Azure SQL DB** | **Préparation à Azure SQL MI** | **Type de déploiement recommandé** | **Configuration d’Azure SQL et estimations de coût calculées ?**
         --- | --- | --- | --- |
        Ready | Ready | Azure SQL DB ou Azure SQL MI.[En savoir plus](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Oui
        Ready | Non prêt ou Inconnu | Azure SQL DB | Oui
        Non prêt ou Inconnu | Ready | Azure SQL Database Managed Instance  | Oui
        Pas prêt | Pas prêt | Potentiellement prêt pour une machine virtuelle Azure. [En savoir plus](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | Non
        Non prêt ou Inconnu | Non prêt ou Inconnu | Unknown | Non
    
    - **Type de déploiement cible** (tel que sélectionné dans les propriétés de l’évaluation) : **Azure SQL DB**
    
        **Préparation à Azure SQL DB** | **Configuration d’Azure SQL et estimations de coût calculées ?**
        --- | --- |
        Ready | Oui
        Pas prêt | Non
        Unknown | Non
    
    - **Type de déploiement cible** (tel que sélectionné dans les propriétés de l’évaluation) : **Azure SQL MI**
    
        **Préparation à Azure SQL MI** | **Configuration d’Azure SQL et estimations de coût calculées ?**
         --- | --- |
        Ready | Oui
        Pas prêt | Non
        Unknown | Non

4. Cliquez sur le zoom avant du nom de l’instance pour afficher le nombre de bases de données utilisateur, les détails de l’instance, notamment les propriétés de l’instance, le calcul (étendue à l’instance) et les détails du stockage de la base de données source.
5. Cliquez sur le nombre de bases de données utilisateur pour consulter la liste des bases de données et leurs détails. Voici un exemple (estimations et coûts, à titre d’exemple uniquement) : :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="Détails de l’instance SQL":::
5. Cliquez sur Afficher les détails dans la colonne Problèmes de migration pour passer en revue les problèmes de migration et les avertissements relatifs à un type de déploiement cible particulier. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problèmes et avertissements de migration de base de données":::

### <a name="review-cost-estimates"></a>Réviser les estimations de coûts
Le résumé de l’évaluation affiche une estimation des coûts mensuels de calcul et de stockage pour les configurations d’Azure SQL correspondant au type de déploiement recommandé pour des bases de données Azure SQL et/ou des instances gérées.

1. Vérifiez les coûts totaux mensuels. Les coûts sont agrégés pour toutes les instances SQL du groupe évalué.
    - Les estimations de coût sont basées sur la configuration recommandée d’Azure SQL pour une instance. 
    - Les coûts mensuels estimés pour le calcul et le stockage sont affichés. Voici un exemple (estimations et coûts, à titre d’exemple uniquement) :
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Détails des coûts":::

1. Vous pouvez descendre dans la hiérarchie au niveau d’une instance pour voir la configuration d’Azure SQL et les estimations de coûts au niveau d’une instance.  
1. Vous pouvez également descendre dans la hiérarchie jusqu’à la liste des bases de données pour examiner la configuration d’Azure SQL et les estimations de coûts par base de données lorsqu’une configuration d’Azure SQL Database est recommandée.

### <a name="review-confidence-rating"></a>Examiner le niveau de confiance
Azure Migrate attribue un niveau de confiance à toutes les évaluations d’Azure SQL en fonction de la disponibilité des points de données de performance/d’utilisation nécessaires pour calculer l’évaluation de toutes les bases de données et instances SQL évaluées. Ce niveau est exprimé en étoiles, 1 étoile étant le plus bas niveau et 5 étoiles le plus haut.
 
Le niveau de confiance vous permet d’estimer la fiabilité des suggestions qui sont fournies par l’évaluation concernant la taille.  Les niveaux de confiance sont les suivants.

**Disponibilité des points de données** | **Niveau de confiance**
--- | ---
0 %-20 % | 1 étoile
21 %-40 % | 2 étoiles
41 %-60 % | 3 étoiles
61 %-80 % | 4 étoiles
81 %-100 % | 5 étoiles

[En savoir plus](concepts-azure-sql-assessment-calculation.md#confidence-ratings) sur les niveaux de confiance.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](concepts-azure-sql-assessment-calculation.md) sur le mode de calcul des évaluations d’Azure SQL.
- Commencez à migrer des instances et des bases de données SQL à l’aide d’[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
