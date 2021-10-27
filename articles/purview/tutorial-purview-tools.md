---
title: Découvrir les outils et utilitaires open source Azure Purview
description: Ce tutoriel liste les divers outils et utilitaires disponibles dans Azure Purview et décrit leur utilisation.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/10/2021
ms.openlocfilehash: 8c87adc48a9a717a6ca9796b6d3bb2ec780220f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007542"
---
# <a name="azure-purview-open-source-tools-and-utilities"></a>Outils et utilitaires open source Azure Purview

Cet article liste plusieurs outils et utilitaires open source (interfaces de ligne de commande, Python et PowerShell) qui vous aident à bien démarrer avec le service Azure Purview en quelques minutes. Ces outils ont été créés et développés grâce aux efforts collectifs du groupe de produits Azure Purview et de la communauté open source. L’objectif de ces outils est de faciliter et d’accélérer l’apprentissage, le démarrage, l’utilisation régulière et l’adoption à long terme de Purview.

### <a name="intended-audience"></a>Public concerné

- La communauté Azure Purview, qui comprend les clients, les développeurs, les éditeurs de logiciels indépendants, les partenaires, les promoteurs et les passionnés. 

- Le catalogue Azure Purview se base sur [Apache Atlas](https://atlas.apache.org/) et étend la prise en charge complète des API Apache Atlas. Nous invitons la communauté Apache Atlas, ses passionnés et ses développeurs à s’appuyer immodérément sur Azure Purview et à en faire l’apologie.

### <a name="purview-customer-journey-stages"></a>Phases du parcours client Purview

- *Débutants Purview* : Débutants qui découvrent le service Azure Purview et qui ont envie de comprendre et d’explorer le fonctionnement d’une solution de gouvernance des données unifiée multicloud. Une partie de ces débutants inclut des utilisateurs qui veulent comparer Purview à d’autres solutions concurrentes sur le marché de la gouvernance des données et l’essayer avant de l’adopter pour une utilisation à long terme.

- *Innovateurs Purview* : Innovateurs qui ont envie de comprendre les fonctionnalités existantes et récentes, de concevoir des idées et de conceptualiser les fonctionnalités à venir dans Purview. Ces derniers sont doués pour générer et développer des solutions pour les clients. Leurs idées futuristes font avancer la prochaine génération de produits de gouvernance des données à la pointe de la technologie.

- *Passionnés/Promoteurs Purview* : Passionnés qui comprennent à la fois des débutants et des innovateurs. Ces derniers ont développé une compréhension et des connaissances solides de Purview, ils sont donc enthousiastes quant à l’adoption de Purview. Ils peuvent promouvoir Purview en tant que service et former d’autres utilisateurs Purview ainsi que des clients potentiels dans le monde entier.

- *Adoptants Purview* : Adoptants qui se sont initiés à Purview, l’ont exploré et l’utilisent avec aisance depuis plusieurs mois.

- *Utilisateurs réguliers de Purview inscrits dans la durée* : Utilisateurs inscrits dans la durée qui utilisent Purview depuis plus d’un an et sont maintenant à l’aise dans les cas d’usage Purview les plus avancés sur le portail Azure et dans Purview Studio. Ils ont acquis des connaissances presque parfaites sur les API REST Purview et les cas d’usage supplémentaires pris en charge par le biais des API Purview.


## <a name="azure-purview-open-source-tools-and-utilities-list"></a>Liste des outils et utilitaires open source Azure Purview

1. [Purview-API-via-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/README.md) 

    - **Phases du parcours client recommandées** : *Débutants, Innovateurs, Passionnés, Adoptants, Utilisateurs réguliers inscrits dans la durée*
    - **Description** : Cet utilitaire couvre et s’appuie sur la totalité des [informations de référence sur l’API REST Azure Purview](/rest/api/purview/) de Microsoft docs. [Téléchargez-le et installez-le à partir de PowerShell Gallery](https://aka.ms/purview-api-ps). Il vous aide à exécuter toutes les API REST Purview documentées par le biais d’une interface PowerShell rapide et facile à utiliser. Utilisez et automatisez des API Purview à des fins d’utilisation régulière et à long terme par le biais de méthodes scriptées et de ligne de commande. Cet utilitaire est une alternative pour les clients qui cherchent à effectuer des tâches en bloc de manière automatisée, en mode batch ou des tâches cron planifiées, plutôt qu’en utilisant la méthode de l’interface graphique utilisateur qui consiste à utiliser le portail Azure et Purview Studio. Une documentation détaillée, un exemple de guide d’utilisation, une aide autonome et des exemples sont disponibles sur [GitHub : Azure-Purview-API-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell).

1. [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)

    - **Phases du parcours client recommandées** : *Débutants, Innovateurs, Passionnés*
    - **Description** : Script PowerShell permettant d’effectuer la configuration initiale du compte Purview. Utile à toute personne qui cherche à configurer plusieurs nouveaux comptes Purview en moins de 5 minutes.

1. [Purview Lab](https://aka.ms/purviewlab)

    - **Phases du parcours client recommandées** : *Débutants, Innovateurs, Passionnés*
    - **Description** : Atelier pratique présentant la myriade de fonctionnalités Purview qui vous aide à apprendre les concepts par une approche pratique qui vous permet d’exécuter chaque étape manuellement en toute autonomie pour développer la meilleure compréhension possible de Purview.

1. [Purview CLI](https://aka.ms/purviewcli)

    - **Phases du parcours client recommandées** : *Innovateurs, Passionnés, Adoptants, Utilisateurs réguliers inscrits dans la durée*
    - **Description** : Outil basé sur Python qui permet d’exécuter les API Purview similaires à [purview-API-via-PowerShell](https://aka.ms/purview-api-ps), mais avec des fonctionnalités limitées/moins nombreuses par rapport à celles du framework basé sur PowerShell.

1. [Purview Demo](https://aka.ms/pvdemo)

    - **Phases du parcours client recommandées** : *Débutants, Innovateurs, Passionnés*
    - **Description** : Outil basé sur un modèle Azure Resource Manager (ARM) qui permet de configurer et déployer automatiquement un nouveau compte Azure Purview de façon rapide et sécurisée en émettant une seule commande. Cet outil est similaire à [Purview-Starter-Kit](https://aka.ms/PurviewKickstart), avec une fonctionnalité supplémentaire qui permet de déployer d’autres sources de données préconfigurées : Azure SQL Database, compte Azure Data Lake Storage Gen2, Azure Data Factory, espace de travail Azure Synapse Analytics.

1. [PyApacheAtlas : Interface entre Azure Purview et Apache Atlas](https://github.com/wjohnson/pyapacheatlas) qui utilise des API Atlas

    - **Phases du parcours client recommandées** : *Innovateurs, Passionnés, Adoptants, Utilisateurs réguliers inscrits dans la durée*
    - **Description** : Package Python à utiliser avec Azure Purview et l’API Apache Atlas. Prend en charge le chargement en masse, la traçabilité personnalisée, etc. à partir d’un ensemble Python de classes et de modèles Excel. Le package prend en charge une interaction programmatique et un modèle Excel pour les chargements avec peu de code.

1. [Lecteur de notifications EventHub Purview](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/purview_atlas_eventhub_sample.py)

    - **Phases du parcours client recommandées** : *Innovateurs, Passionnés, Adoptants, Utilisateurs réguliers inscrits dans la durée*
    - **Description** : Cet outil montre comment lire l’EventHub de Purview et intercepter les notifications Kafka en temps réel provenant de l’EventHub au format Atlas Notifications (https://atlas.apache.org/2.0.0/Notifications.html). De plus, il génère un fichier CSV Excel des entités et ressources à la volée découvertes en direct pendant une analyse, ainsi que toutes les autres notifications intéressantes générées par Purview.


## <a name="feedback-and-disclaimer"></a>Commentaires et exclusion de responsabilité

Aucun de ces outils n’est fourni avec une garantie expresse de Microsoft visant la vérification de son efficacité ni avec des garanties de fonctionnement. Ils sont certifiés exempts de toute activité ou tout virus malveillants, et garantis sans collecte de données privées ou sensibles.

Pour faire part de commentaires ou poser des questions sur l’efficacité et le fonctionnement des outils pendant leur utilisation, contactez leurs propriétaires et auteurs respectifs à l’aide des coordonnées mentionnées dans le dépôt GitHub correspondant.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
