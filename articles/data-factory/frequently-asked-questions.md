---
title: 'Azure Data Factory : Forum Aux Questions (FAQ) | Microsoft Docs'
description: Réponses aux questions les plus fréquentes concernant Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345752"
---
# <a name="azure-data-factory-faq"></a>Forum Aux Questions Azure Data Factory
Cet article fournit des réponses aux questions fréquemment posées sur Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>qu'est-ce qu'Azure Data Factory ? 
Data Factory est un service cloud d’intégration de données entièrement géré qui automatise le déplacement et la transformation des données. À l’instar d’une usine qui utilise des machines visant à transformer des matières premières en produits manufacturés, Azure Data Factory orchestre des services existants qui collectent des données brutes et les transforment en informations prêtes à l’emploi. 

Grâce à Azure Data Factory, vous pouvez créer des flux de travail pilotés par les données pour déplacer des données entre des magasins locaux et cloud. Vous pouvez traiter et transformer des données à l’aide de services de calcul comme Azure HDInsight, Azure Data Lake Analytique et le runtime d’intégration de SQL Server Integration Services (SSIS). 

Avec Data Factory, vous pouvez exécuter votre traitement de données sur un service cloud Azure ou dans votre propre environnement de calcul auto-hébergé comme SSIS, SQL Server ou Oracle. Après avoir créé un pipeline qui exécute l’action que vous avez besoin, vous pouvez planifier son exécution périodique (horaire, quotidienne ou hebdomadaire, par exemple), fenêtre de temps de planification ou déclencher le pipeline à partir d’une occurrence d’événement. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Flux de contrôle et mise à l’échelle 
Pour prendre en charge les flux d’intégration de divers et les modèles dans l’entrepôt de données moderne, Data Factory permet de données flexible pipeline modélisation. Cela implique des flux de contrôle total paradigmes de programmation, notamment l’exécution conditionnelle, la création de branche dans les pipelines de données et la possibilité de transmettre explicitement des paramètres dans et entre ces flux. Flux de contrôle englobe également la transformation des données via la répartition des activités pour les moteurs d’exécution externe et les fonctionnalités du flux de données, y compris le déplacement des données à grande échelle, par le biais de l’activité de copie.

Data Factory vous donne la liberté de modéliser n’importe quel style de flux nécessaire pour l’intégration des données, qui peut être distribué à la demande ou de façon répétée selon une planification. Voici quelques flux courants permis par ce modèle :   

- Flux de contrôle :
    - Les activités peuvent être chaînées dans une séquence au sein d’un pipeline.
    - Les activités peuvent comprendre des branches dans un pipeline.
    - Paramètres :
        - Paramètres peuvent être définis au niveau du pipeline et les arguments peuvent être passés pendant que vous appelez le pipeline à la demande ou à partir d’un déclencheur.
        - Les activités peuvent consommer les arguments transmis au pipeline.
    - Transmission d’un état personnalisé :
        - Sorties de l’activité, y compris leur état, peuvent être consommées par une activité suivante dans le pipeline.
    - Bouclage des conteneurs :
        - L’activité foreach effectue une itération sur une collection spécifiée d’activités dans une boucle. 
- Flux basés sur un déclencheur :
    - Les pipelines peuvent être déclenchés à la demande ou selon un temps horloge.
- Flux delta :
    - Paramètres peuvent être utilisés pour définir votre borne haute pour la copie delta lors du déplacement des tables de dimension ou de référence à partir d’un magasin relationnel, localement ou dans le cloud, pour charger les données dans le lac. 

Pour plus d’informations, consultez [Tutoriel : Flux de contrôle](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Données transformées à l’échelle avec des pipelines sans code
La nouvelle expérience des outils basés sur un navigateur permet la création et le déploiement de pipelines sans code avec une expérience web interactive et moderne.

Pour les développeurs de données visuelles et les ingénieurs de données, l’interface utilisateur web de Data Factory est l’environnement de conception sans code que vous utiliserez pour créer des pipelines. Il est entièrement intégré à Visual Studio Git en ligne et fournit l’intégration continus et le développement itératif avec les options de débogage.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Kits de développement logiciel pour les utilisateurs expérimentés inter-plateformes riche
Data Factory V2 fournit un ensemble rich de kits de développement logiciel qui peut être utilisé pour créer, gérer et surveiller des pipelines à l’aide de votre IDE favori, y compris :
* Kit de développement logiciel (SDK) Python
* CLI de PowerShell
* Kit de développement logiciel (SDK) C#

Utilisateurs peuvent également utiliser la documentation de l’API REST pour interagir avec Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Développement itératif et débogage à l’aide des outils visual
Les outils visual Data Factory Azure activer le développement itératif et le débogage. Vous pouvez créer vos pipelines et des séries de tests à l’aide de la **déboguer** fonctionnalité dans le canevas du pipeline sans écrire une seule ligne de code. Vous pouvez afficher les résultats de vos séries de tests dans le **sortie** fenêtre du canevas du pipeline. Après la réussite de votre série de tests, vous pouvez ajouter des activités plus à votre pipeline et continuer le débogage de façon itérative. Vous pouvez également annuler vos séries de tests une fois qu’ils sont en cours d’exécution. 

Vous n’êtes pas obligé de publier vos modifications au service de fabrique de données avant de sélectionner **déboguer**. Cela est utile dans les scénarios où vous souhaitez vous assurer que les nouveaux ajouts ou modifications fonctionnent comme prévu avant de vous mettre à jour vos flux de travail de fabrique de données dans les environnements de développement, de test ou de production. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Possibilité de déployer des packages SSIS vers Azure 
Si vous voulez déplacer vos charges de travail SSIS, vous pouvez créer une fabrique de données et provisionner un runtime d’intégration Azure-SSIS. Un runtime d’intégration Azure-SSIS est un cluster entièrement géré de machines virtuelles Azure (nœuds) dédiées à l’exécution de vos packages SSIS dans le cloud. Pour obtenir des instructions pas à pas, consultez le didacticiel [Déployer des packages SSIS vers Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>Kits SDK
Si vous êtes un utilisateur expérimenté et recherchez une interface de programmation, Data Factory offre un ensemble complet de kits de développement logiciel que vous pouvez utiliser pour créer, gérer ou surveiller les pipelines à l’aide de votre IDE favori. La prise en charge des langages inclut .NET, PowerShell, Python et REST.

### <a name="monitoring"></a>Surveillance
Vous pouvez surveiller vos fabriques de données via PowerShell, un SDK ou les outils de surveillance visuels dans l’interface utilisateur du navigateur. Vous pouvez surveiller et gérer des flux personnalisés à la demande, en fonction de déclencheur et contrôlée par l’horloge de manière efficace. Annuler les tâches existantes, voir les échecs en un clin de œil, Descendre pour obtenir des messages d’erreur détaillés et déboguer les problèmes, à partir d’un volet unique sans changement de contexte ou naviguer dans les deux sens entre les écrans. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nouvelles fonctionnalités de SSIS dans Data Factory
Étant donné que la première version publique préliminaire en 2017, Data Factory a ajouté les fonctionnalités suivantes pour SSIS :

-   Prise en charge des trois configurations/variantes plus de base de données SQL Azure pour héberger la base de données SSIS (SSISDB) de projets/packages :
-   Base de données SQL avec des points de terminaison de service réseau virtuel
-   Instance gérée
-   Pool élastique
-   Prise en charge pour un réseau virtuel Azure Resource Manager sur un réseau virtuel classique à être déconseillé à l’avenir, ce qui vous permet d’injecter/joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel configuré pour la base de données SQL avec le service de réseau virtuel accès aux données des points de terminaison/MI/sur site. Pour plus d’informations, consultez également [joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).
-   Prise en charge pour l’authentification Azure Active Directory (Azure AD) et l’authentification SQL pour se connecter à la SSISDB, ce qui permet l’authentification Azure AD avec votre identité de la fabrique de données gérés pour les ressources Azure
-   Prise en charge pour mettre votre propre licence SQL Server pour bénéficier d’économies substantielles à partir de l’option Azure Hybrid Benefit sur site
-   Prise en charge d’Enterprise Edition du runtime d’intégration Azure-SSIS qui vous permet d’utiliser les fonctionnalités de premium/avancé, une interface de programme d’installation personnalisé pour installer des extensions/composants supplémentaires et un écosystème de partenaires. Pour plus d’informations, consultez également [Enterprise Edition, le programme d’installation personnalisée et 3e extensibilité tiers pour SSIS dans ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Intégration approfondie de SSIS dans Data Factory, qui vous permet d’appeler/déclencheur de première classe activités d’exécuter le Package SSIS dans des pipelines Data Factory et de les planifier via SSMS. Pour plus d’informations, consultez également [Modernize et étendre vos workflows ETL/ELT avec les activités SSIS dans ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Quel est le runtime d’intégration ?
Le runtime d’intégration est l’infrastructure de calcul Azure Data Factory utilise pour fournir les fonctionnalités d’intégration de données suivantes entre différents environnements réseau :

- **Déplacement des données** : Déplacement des données, le runtime d’intégration déplace les données entre les magasins de données source et de destination, tout en fournissant de prise en charge pour les connecteurs intégrés, conversion de format, le mappage de colonnes et performante et transfert de données évolutives.
- **Répartir les activités** : Pour la transformation, le runtime d’intégration fournit la possibilité d’exécuter en mode natif de packages SSIS.
- **Exécuter des packages SSIS** : Le runtime d’intégration exécute en mode natif les packages SSIS dans un environnement de calcul Azure géré. Le runtime d’intégration prend également en charge la distribution et la surveillance des activités de transformation exécutées sur un large éventail de services de calcul, comme Azure HDInsight, Azure Machine Learning, base de données SQL et SQL Server.

Vous pouvez déployer une ou plusieurs instances du runtime d’intégration selon les besoins pour déplacer et transformer des données. Le runtime d’intégration peut s’exécuter sur un réseau public Azure ou sur un réseau privé (en local, un réseau virtuel Azure ou un cloud privé virtuel Amazon Web Services [VPC]). 

Pour plus d’informations, consultez [Runtime d’intégration dans Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Quelle est la limite au nombre de runtimes d’intégration ?
Il n’existe aucune limite quant au nombre d’instances de runtime d’intégration autorisées dans une fabrique de données. Il existe toutefois une limite relative au nombre de cœurs de machines virtuelles que le runtime d’intégration peut utiliser par abonnement pour l’exécution de package SSIS. Pour plus d’informations, consultez la section [Limites de Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quels sont les concepts principaux d’Azure Data Factory ?
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ils fonctionnent ensemble en tant que plateforme sur laquelle composer des flux de travail pilotés par les données et constitués d’étapes de déplacement et de transformation de données.

### <a name="pipelines"></a>Pipelines
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline est un regroupement logique d’activités nécessaires pour effectuer une unité de travail. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingèrent des données à partir d’un objet blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données. L’avantage est que vous pouvez utiliser un pipeline pour gérer les activités en tant qu’ensemble au lieu de devoir gérer chaque activité individuellement. Vous pouvez chaîner les activités dans un pipeline pour qu’elles fonctionnent de manière séquentielle, ou vous pouvez les exécuter indépendamment, en parallèle.

### <a name="activities"></a>Activités
Les activités représentent une étape de traitement dans un pipeline. Par exemple, vous pouvez utiliser une activité de copie pour copier des données à partir d’une banque de données vers un autre magasin de données. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge trois types d’activités : les activités de déplacement des données, les activités de transformation des données et les activités de contrôle.

### <a name="datasets"></a>Groupes de données
Les jeux de données représentent les structures des données dans les magasins. Ils pointent vers ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de cette façon : Un service lié définit la connexion à la source de données, et un jeu de données représente la structure des données. Par exemple, un service lié Stockage Azure spécifie la chaîne de connexion pour se connecter au compte de stockage Azure. Et un jeu de données d’objets blob Azure Spécifie le conteneur d’objets blob et le dossier qui contient les données.

Les services liés ont deux objectifs dans Data Factory :

- Représenter un *magasin de données* qui inclut, mais sans s’y limiter, une base de données SQL Server locale, une instance de base de données Oracle, un partage de fichiers ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des magasins de données pris en charge, consultez [Activité de copie dans Azure Data Factory](copy-activity-overview.md).
- Pour représenter une *ressource de calcul* qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsight Hive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des activités de transformation et des environnements de calcul pris en charge, consultez [Transformer des données dans Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Déclencheurs
Les déclencheurs représentent des unités de traitement qui déterminent le moment où une exécution de pipeline est lancée. Il existe différents types de déclencheurs pour différents types d’événements. 

### <a name="pipeline-runs"></a>Exécutions de pipeline
Une exécution du pipeline est une instance d’exécution d’un pipeline. Généralement, vous instanciez une exécution de pipeline en transmettant des arguments aux paramètres définis dans le pipeline. Vous pouvez transmettre les arguments manuellement ou dans la définition du déclencheur.

### <a name="parameters"></a>parameters
Les paramètres sont des paires clé-valeur dans une configuration en lecture seule. Vous définissez des paramètres dans un pipeline, et vous transmettez les arguments pour les paramètres définis lors de l’exécution à partir d’un contexte d’exécution. Le contexte d’exécution est créé par un déclencheur ou à partir d’un pipeline que vous exécutez manuellement. Les activités contenues dans le pipeline utilisent les valeurs des paramètres.

Un jeu de données est un paramètre fortement typé et une entité que vous pouvez réutiliser ou référencer. Une activité peut référencer des jeux de données, et utiliser les propriétés qui sont définies dans la définition du jeu de données.

Un service lié est également un paramètre fortement typé qui contient des informations de connexion à un magasin de données ou à un environnement de calcul. Il est également une entité que vous pouvez réutiliser ou référencer.

### <a name="control-flows"></a>Flux de contrôle
Les flux de contrôle orchestrent des activités de pipeline telles que le chaînage des activités en une séquence, la création de branche, des paramètres que vous définissez au niveau du pipeline, et des arguments que vous transmettez lors de l’appel du pipeline à la demande ou à partir d’un déclencheur. Flux de contrôle incluent également un état personnalisé transmission et le bouclage des conteneurs (autrement dit, les itérateurs foreach).


Pour plus d’informations sur les concepts de Data Factory, consultez les articles suivants :

- [Jeux de données et services liés](concepts-datasets-linked-services.md)
- [Pipelines et activités](concepts-pipelines-activities.md)
- [Runtime d’intégration](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Quel est le modèle de tarification de Data Factory ?
Pour plus d’informations sur la tarification d’Azure Data Factory, consultez [Détails sur la tarification de Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Comment se tenir informé sur Data Factory ?
Pour obtenir les informations les plus récentes sur Azure Data Factory, consultez les sites suivants :

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Page d’accueil de la documentation](/azure/data-factory)
- [Page d’accueil du produit](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Présentation technique approfondie 

### <a name="how-can-i-schedule-a-pipeline"></a>Comment planifier un pipeline ? 
Vous pouvez utiliser le déclencheur Planificateur ou Fenêtre de temps pour planifier un pipeline. Le déclencheur utilise une horloge, permettant de planifier les pipelines périodiquement ou dans les modèles récurrents basés sur le calendrier, par exemple, sur (lundis à 18 h 00) et jeudis à 21 h 00. Pour plus d’informations, consultez l’article [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Puis-je passer des paramètres à une exécution de pipeline ?
Oui, les paramètres sont un concept de première classe, de niveau supérieur dans Data Factory. Vous pouvez les définir au niveau du pipeline et transmettre des arguments lors de l’exécution du pipeline à la demande ou à l’aide d’un déclencheur.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Puis-je définir des valeurs par défaut pour les paramètres des pipelines ? 
Oui. Vous pouvez définir des valeurs par défaut pour les paramètres des pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Une activité dans un pipeline peut-elle consommer des arguments transmis à une exécution de pipeline ? 
Oui. Chaque activité du pipeline peut consommer la valeur de paramètre transmise au pipeline et exécutée avec la construction `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Une propriété de sortie d’activité peut-elle être consommée dans une autre activité ? 
Oui. Une sortie d’activité peut être consommée dans une activité suivante avec la construction `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Comment gérer correctement les valeurs Null dans une sortie d’activité ? 
Vous pouvez utiliser la construction `@coalesce` dans les expressions pour gérer correctement les valeurs Null. 

## <a name="mapping-data-flows"></a>Flux de données de mappage

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Quelle version de la fabrique de données à utiliser pour créer des flux de données ?
Utilisez la version de Data Factory V2 pour créer des flux de données.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>J’étais un client de version préliminaire privée précédente qui a utilisé des flux de données, et j’ai utilisé la version d’évaluation de Data Factory V2 pour les flux de données.
Cette version est désormais obsolète. Utiliser Data Factory V2 pour les flux de données.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Les changements apportés à partir de la version préliminaire privée à la version préliminaire publique limitée en ce qui concerne les flux de données ?
Ne plus avoir à apporter vos propres clusters Azure Databricks. Data Factory gère la création du cluster et destructions. Jeux de données BLOB et des jeux de données Azure Data Lake Storage Gen2 est divisés en texte délimité et jeux de données Apache Parquet. Vous pouvez toujours utiliser Data Lake Storage Gen2 et stockage d’objets Blob pour stocker ces fichiers. Utiliser le service lié approprié pour les moteurs de stockage.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Puis-je migrer mon fabriques de version préliminaire privée de Data Factory V2 ?

Oui. [Suivez les instructions](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>J’ai besoin d’aide à dépanner ma logique de flux de données. Quelles informations dois-je fournir pour obtenir une aide ?

Lorsque Microsoft fournit l’aide ou la résolution des problèmes avec les flux de données, indiquez le plan du code DSL. Pour ce faire, procédez comme suit :

1. Dans le Concepteur de flux de données, sélectionnez **Code** dans l’angle supérieur droit. Ceci affichera le code JSON modifiable pour le flux de données.
2. Dans la vue code, sélectionnez **planifier** dans l’angle supérieur droit. Ce bouton bascule passe à partir de JSON pour le plan de script de le DSL de mise en forme en lecture seule.
3. Copiez et collez ce script ou l’enregistrer dans un fichier texte.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Comment accéder des données à l’aide de 80 autres types de jeu de données dans Data Factory ?

La fonctionnalité de mappage de flux de données autorise actuellement la que base de données SQL Azure, Azure SQL Data Warehouse, délimitée par des fichiers texte à partir de stockage Blob Azure ou Azure Data Lake Storage Gen2, Parquet fichiers et stockage d’objets Blob ou Data Lake Storage Gen2 en mode natif pour la source et récepteur. 

L’activité de copie pour organiser les données à partir de tous les autres connecteurs et puis exécuter une activité de flux de données pour transformer des données une fois qu’il est transféré. Par exemple, votre pipeline de copie tout d’abord dans le stockage Blob, puis une activité de flux de données utilisera un jeu de données dans la source à transformer les données.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des instructions pas à pas pour créer une fabrique de données, consultez les tutoriels suivants :

- [Démarrage rapide : Créer une fabrique de données](quickstart-create-data-factory-dot-net.md)
- [Tutoriel : Copier des données dans le cloud](tutorial-copy-data-dot-net.md)
