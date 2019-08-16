---
title: Vue d’ensemble de la documentation de Microsoft Azure Security Code Analysis
description: Cet article est une vue d’ensemble de l’extension Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718168"
---
# <a name="about-microsoft-security-code-analysis"></a>À propos de Microsoft Security Code Analysis

L’**extension Microsoft Security Code Analysis** permet aux équipes d’intégrer de façon homogène l’analyse de la sécurité du code dans leurs pipelines Azure DevOps CI/CD, conformément aux recommandations des experts Microsoft du [cycle de vie de développement sécurisé](https://www.microsoft.com/securityengineering/sdl/practices) (SDL, Secure Development Lifecycle). La sécurité est simplifiée grâce à une expérience utilisateur cohérente qui rend transparente la complexité liée à l’exécution de différents outils. Avec une mise à disposition des outils basée sur NuGet, les équipes n’ont plus besoin de gérer leur installation ou leur mise à jour. Avec les interfaces des tâches de build Ligne de commande et De base, tous les utilisateurs, des experts chevronnés de ces outils aux développeurs moins spécialisés, peuvent avoir le contrôle qu’ils souhaitent sur les outils. Les équipes peuvent également tirer parti des puissantes fonctionnalités de post-traitement, comme la publication de journaux pour la conservation, la génération de rapports pratiques axés sur les développeurs, et la configuration d’interruptions des builds en cas de régression.

## <a name="why-microsoft-security-code-analysis"></a>Pourquoi Microsoft Security Code Analysis

### <a name="security-simplified"></a>Sécurité simplifiée

L’ajout d’outils d’analyse de la sécurité du code à votre pipeline Azure DevOps est aussi simple qu’ajouter de nouvelles tâches. Personnalisez-les ou utilisez les valeurs par défaut. Les tâches s’exécutent dans le cadre de votre pipeline DevOps et produisent des journaux détaillant toutes sortes de découvertes.

### <a name="clean-builds"></a>Des builds propres

Après avoir résolu les problèmes initiaux signalés par les outils, vous pouvez configurer l’extension pour interrompre les builds en cas de nouveaux problèmes. La configuration de la build d’intégration continue à chaque demande de tirage n’a jamais été aussi simple !

### <a name="set-it-and-forget-it"></a>Définissez-la, puis oubliez-la

Les tâches et les outils de build peuvent être définis pour rester à jour (et ils le sont par défaut). S’il existe une version mise à jour de l’outil, il n’est pas nécessaire de la télécharger et de l’installer : cette extension s’en occupe pour vous. 

>>>
### <a name="under-the-hood"></a>En coulisses

Les tâches de build de l’extension Microsoft Security Code Analysis rendent transparente la complexité de opérations suivantes :
  - Exécution d’outils d’analyse statique de la sécurité
  - Traitement des résultats des fichiers journaux pour créer un rapport récapitulatif ou interrompre la build.
>>>

## <a name="security-code-analysis-toolset"></a>Ensemble d’outils d’analyse de la sécurité du code

L’extension Microsoft Security Code Analysis met à votre disposition les dernières versions des outils d’analyse importants. L’extension inclut à la fois des outils internes à Microsoft et des outils open source. Les outils sont automatiquement téléchargés sur l’agent hébergé dans le cloud une fois que vous configurez et que vous exécutez le pipeline en utilisant la tâche de build correspondante. Vous trouverez ci-dessous l’ensemble des outils actuellement disponibles dans l’extension. Restez informé et envoyez-nous vos suggestions d’outils susceptibles d’être ajoutés.

### <a name="anti-malware-scanner"></a>Analyseur anti-programme malveillant

La tâche de build Analyseur anti-programme malveillant est désormais incluse dans l’extension Microsoft Security Code Analysis. Elle doit être exécutée sur un agent de build sur lequel Windows Defender est déjà installé. Pour plus d’informations, consultez le [site web de Defender](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim est un analyseur léger exécutable portable qui vérifie les paramètres du compilateur et de l’éditeur de liens ainsi que d’autres caractéristiques des fichiers binaires relatives à la sécurité. La tâche de build fournit un wrapper de ligne de commande autour de l’application BinSkim.exe. BinSkim est un outil open source : pour plus d’informations, visitez [BinSkim sur GitHub](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Credential Scanner

Les mots de passe et les autres secrets stockés dans le code source sont actuellement un problème important. Credential Scanner est un outil d’analyse statique propriétaire qui détecte les informations d’identification, les secrets, les certificats et tout autre contenu sensible dans votre code source et la sortie de votre build.

### <a name="microsoft-security-risk-detection"></a>Microsoft Security Risk Detection

Security Risk Detection est le service cloud de test à données aléatoires (fuzzing) de Microsoft qui vous aide à identifier les bogues de sécurité exploitables dans les logiciels. Ce service nécessite un processus d’intégration distinct. Pour plus d’informations, visitez [MSRD sur docs.microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Analyseurs Roslyn

Outil d’analyse statique intégré au compilateur de Microsoft pour l’analyse du code managé (C# et VB). Pour plus d’informations, visitez [Analyseurs Roslyn sur docs.microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint est un outil d’analyse statique extensible qui recherche les erreurs de lisibilité, de maintenabilité et de fonctionnalités dans le code TypeScript. Il est pris en charge dans beaucoup d’éditeurs modernes et de systèmes de build, et il peut être personnalisé avec vos propres règles, configurations et formateurs lint. TSLint est un outil open source : pour plus d’informations, visitez [TSLint sur GitHub](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Analyse et post-traitement des résultats

L’extension Microsoft Security Code Analysis a également trois tâches post-traitement utiles pour vous aider à traiter et à analyser les résultats trouvés par les tâches des outils de sécurité. Elles sont généralement ajoutées dans le pipeline après toutes les autres tâches liées aux outils.

### <a name="publish-security-analysis-logs"></a>Publier les journaux d’analyse de la sécurité
La tâche de build Publier les journaux d’analyse de la sécurité conserve les fichiers journaux des outils de sécurité exécutés pendant la build, pour en permettre l’examen et le suivi.

Ils peuvent être publiés sur les artefacts de serveur Azure (sous forme de fichier zip) ou de copies sur un partage de fichiers accessible à partir de votre agent de build privé.

### <a name="security-report"></a>Rapport de sécurité
La tâche de build Rapport de sécurité analyse les fichiers journaux créés par les outils de sécurité exécutés pendant la build et crée un fichier de rapport récapitulatif unique avec tous les problèmes trouvés par les outils d’analyse.

La tâche peut être configurée pour indiquer les découvertes pour des outils spécifiques ou pour tous les outils, et vous pouvez aussi choisir le niveau de problèmes (erreurs, ou erreurs et avertissements) à indiquer.

### <a name="post-analysis-build-break"></a>Post-analyse (interruption de la build)
La tâche de build Post-analyse permet au client d’injecter un arrêt de la build et de faire échouer la build au cas où un ou plusieurs outils d’analyse indiquent des découvertes ou des problèmes dans le code.

La tâche peut être configurée pour interrompre la build pour des problèmes détectés par des outils spécifiques ou pour tous les outils, et également en fonction de la gravité des problèmes trouvés (erreurs ou avertissements).

>[!NOTE]
>Les tâches de build individuelles vont réussir, par conception, dès lors que l’outil se termine correctement, qu’il y ait ou non des problèmes détectés, pour que la build puisse s’exécuter jusqu’à la fin, ce qui permet l’exécution de tous les outils.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions sur l’intégration et l’installation de Security Code Analysis, consultez notre [Guide d’installation et d’intégration](security-code-analysis-onboard.md)

Pour plus d’informations sur la configuration des tâches de build, consultez notre [Guide de configuration](security-code-analysis-customize.md).

Si vous avez d’autres questions sur l’extension et les outils proposés, consultez [notre page Forum aux questions](security-code-analysis-faq.md).