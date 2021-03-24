---
title: Comment résoudre les défaillances du runtime U-SQL Azure Data Lake Analytics
description: Apprenez à résoudre les défaillances du runtime U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030611"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Apprenez à résoudre les défaillances du runtime U-SQL en raison des modifications du runtime

Le runtime U-SQL Azure Data Lake, y compris le compilateur, l’optimiseur et le gestionnaire de travaux, est ce qui traite votre code U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Choix de votre version du runtime U-SQL

Lorsque vous envoyez des travaux U-SQL depuis Visual Studio, le Kit de développement logiciel (SDK) ADL ou le Portail Azure Data Lake Analytics, votre travail utilise le runtime par défaut actuellement disponible. Les nouvelles versions du runtime U-SQL sont publiées régulièrement et incluent à la fois les mises à jour mineures et les correctifs de sécurité.

Vous pouvez également choisir une version du runtime personnalisée ; soit parce que vous souhaitez essayer une nouvelle mise à jour, que vous devez rester sur une version antérieure d’un runtime, ou que l’on vous a fourni un correctif logiciel pour signaler un problème pour lequel vous ne pouvez pas attendre la nouvelle mise à jour normale.

> [!CAUTION]
> Le choix d’un runtime différent de la valeur par défaut est susceptible d’interrompre vos travaux U-SQL. Utilisez ces autres versions à des fins de test uniquement.

Dans de rares cas, le Support Microsoft peut épingler une version différente d’un runtime comme valeur par défaut pour votre compte. Veillez à annuler cet épinglage dès que possible. Si vous restez épinglé à cette version, elle expirera à une date ultérieure.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Surveillance de la version du runtime U-SQL de vos travaux

Vous pouvez voir l’historique de la version du runtime utilisée par vos anciens travaux dans l’historique des travaux de votre compte par le biais de l’explorateur de travaux de Visual Studio ou de l’historique des travaux du Portail Azure.

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Sélectionnez **Afficher tous les travaux**. Une liste de tous les travaux actifs et récemment terminés dans le compte apparaît.
3. Le cas échéant, cliquez sur **Filtrer** pour rechercher les travaux par **Intervalle de temps**, **Nom du travail** et **Auteur**.
4. Vous pouvez voir le runtime utilisé dans les travaux terminés.

![Affichage de la version du runtime d’un travail passé](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Les versions de runtime disponibles changent au fil du temps. Le runtime par défaut est toujours appelé « default » et nous conservons au moins le runtime précédent, afin qu’il reste disponible durant un certain temps. Nous rendons également disponibles des runtimes spéciaux pour diverses raisons. Les runtimes nommés explicitement suivent généralement le format suivant (les italiques sont utilisées pour les parties variables et [] indique des parties facultatives) :

release_YYYYMMDD_adl_buildno[_modifier]

Par exemple, release_20190318_adl_3394512_2 correspond à la deuxième version de la build 3394512 de la publication du runtime du 18 mars 2019 et release_20190318_adl_3394512_private correspond à une build privée de la même version. Remarque : La date est liée au moment où le dernier archivage a été effectué pour cette version et pas nécessairement à la date de publication officielle.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Résoudre les problèmes de version du runtime U-SQL

Vous pouvez rencontrer deux problèmes de version du runtime :

1. Un script ou un code utilisateur change le comportement d’une version à l’autre. Ces changements cassants sont normalement communiqués à l’avance avec la publication des notes de publication. Si vous rencontrez un tel changement cassant, contactez le Support Microsoft pour signaler ce comportement cassant (au cas où il n’a pas encore été documenté) et envoyez vos travaux sur l’ancienne version du runtime.

2. Vous avez utilisé un runtime autre que celui par défaut, explicitement ou implicitement lorsqu’il a été épinglé à votre compte, et ce runtime a été supprimé après un certain temps. Si vous expérimentez des problèmes de runtimes manquants, mettez à niveau vos scripts pour qu’ils s’exécutent avec le runtime par défaut actuel. Si vous avez besoin de plus de temps, contactez le Support Microsoft

## <a name="known-issues"></a>Problèmes connus

1. La référence au fichier Newtonsoft.Json version 12.0.3 ou ultérieure dans un script USQL entraîne l’échec de compilation suivant :

    *« Nous sommes désolés, les travaux exécutés dans votre compte Data Lake Analytics vont probablement s’exécuter plus lentement ou ne pas se terminer. Un problème inattendu nous empêche de restaurer automatiquement cette fonctionnalité sur votre compte Azure Data Lake Analytics. Les ingénieurs Azure Data Lake ont été contactés pour enquêter. »*  

    Où la pile des appels contiendra :  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Solution**: Utilisez le fichier Newtonsoft.json v12.0.2 ou une version antérieure.
2. Les clients peuvent voir des fichiers et dossiers temporaires dans leur magasin. Ceux-ci sont produits dans le cadre de l’exécution normale du travail, mais généralement supprimés avant que les clients les voient. Dans certaines circonstances, rares et aléatoires, ils peuvent rester visibles pendant un certain temps. Ils sont finalement supprimés et ne sont jamais comptabilisés dans le stockage utilisateur, ou génèrent des frais. En fonction de la logique du travail des clients, ils peuvent occasionner des problèmes. Par exemple, si le travail énumère tous les fichiers du dossier, puis compare des listes de fichiers, il peut échouer en raison de la présence de fichiers temporaires inattendus. De même, si un travail en aval énumère tous les fichiers d’un dossier donné pour un traitement ultérieur, il peut également énumérer les fichiers temporaires.  

    **Solution**: un correctif est identifié dans le runtime où les fichiers temporaires seront stockés dans un dossier temporaire au niveau du compte autre que le dossier de sortie actuel. Les fichiers temporaires seront écrits dans ce nouveau dossier temporaire, et supprimés à la fin de l’exécution du travail.  
    Étant donné que ce correctif traite les données client, il est extrêmement important qu’il soit correctement validé dans MSFT avant sa publication. Ce correctif est censé être disponible en tant que runtime bêta au milieu de l’année 2021 et en tant que runtime par défaut au cours du deuxième semestre de l’année 2021. 


## <a name="see-also"></a>Voir aussi

- [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md)
- [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Surveiller des travaux dans Azure Data Lake Analytics avec le Portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
