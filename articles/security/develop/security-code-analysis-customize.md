---
title: Guide de personnalisation des tâches de Microsoft Azure Security Code Analysis
description: Cet article concerne la personnalisation des tâches dans l’extension Security Code Analysis
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718188"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Guide pratique : Configurer et personnaliser les tâches de build

Cette page décrit en détail les options de configuration disponibles dans chacune des tâches de build, en commençant par les tâches pour les outils d’analyse de la sécurité du code, suivies des tâches de publication post-traitement.

## <a name="anti-malware-scanner-task"></a>Tâche Anti-Malware Scanner (Analyseur anti-programme malveillant)

> [!NOTE]
> La tâche de build Anti-Malware Scanner nécessite un agent de build pour lequel Windows Defender est activé, ce qui est le cas sur les agents de build « VS2017 hébergé » et ultérieur. (Elle ne s’exécutera pas sur l’agent « hébergé » hérité/VS2015) Les signatures ne peuvent pas être mises à jour sur ces agents, mais la signature doit toujours être relativement actuelle, c’est-à-dire avoir moins de 3 heures.

Capture d’écran et détails sur la configuration ci-dessous.

![Personnalisation de la tâche de build Anti-Malware Scanner](./media/security-tools/5-add-anti-malware-task600.png) 

- Paramètres pour Type = **Basic** (De base)
- Avec Type = **Custom** (Personnalisé), vous pouvez spécifier des arguments de ligne de commande pour personnaliser l’analyse.

Windows Defender utilise le client Windows Update pour télécharger et installer les signatures. Si la mise à jour de la signature échoue sur votre agent de build, le code d’erreur HRESULT provient probablement de Windows Update. Pour plus d’informations sur les erreurs de Windows Update et leur atténuation, visitez [cette page](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) et cette [page TechNet](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

## <a name="binskim-task"></a>Tâche BinSkim

> [!NOTE]
> Comme prérequis à l’exécution de la tâche BinSkim, votre build doit respecter une des conditions ci-dessous.
>    - Votre build produit des artefacts binaires à partir de code managé.
>    - Vous avez des artefacts binaires validés que vous voulez analyser avec BinSkim.

Capture d’écran et détails sur la configuration ci-dessous. 

![Installation de BinSkim](./media/security-tools/7-binskim-task-details.png)  
1. Définissez la configuration de build sur Debug pour produire des fichiers de débogage * **.pdb**. Ils sont utilisés par BinSkim pour mapper les problèmes trouvés dans le fichier binaire de sortie au code source. 
2. Choisissez Type = **Basic** (De base) et Fonction = **Analyze** (Analyser) pour éviter de rechercher et de créer votre propre ligne de commande. 
3. **Target** (Cible) : Un ou plusieurs spécificateurs vers un modèle de fichier, de répertoire ou de filtre qui se résout en un ou plusieurs fichiers binaires à analyser. 
    - Les cibles multiples doivent être séparées par un **point-virgule (;)** . 
    - Peut être un seul fichier ou contenir des caractères génériques.
    - Les répertoires doivent toujours se terminer par \*
    - Exemples :

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Si vous sélectionnez Type = **Command Line** (Ligne de commande), 
     - vérifiez que le premier argument de **BinSkim.exe** est le verbe **analyze** avec des chemins complets ou avec des chemins relatifs au répertoire source.
     - Pour l’entrée **Command Line** (Ligne de commande), les cibles multiples doivent être séparées par un espace.
     - Vous pouvez omettre le paramètre de fichier **/o** ou **/output** ; il sera ajouté pour vous ou remplacé. 
     - **Configuration de ligne de commande standard** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > Le caractère \* de fin est très important quand vous spécifiez un ou plusieurs répertoires pour la cible. 

Pour plus d’informations sur les arguments de ligne de commande, les règles par ID ou les codes de sortie pour BinSkim, consultez le [Guide de l’utilisateur BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Tâche Credential Scanner (Analyseur d’informations d’identification)
Capture d’écran et détails sur la configuration ci-dessous.
 
![Personnalisation de Credential Scanner](./media/security-tools/3-taskdetails.png)

Les options disponibles sont les suivantes : 
  - **Output Format** (Format de sortie) : TSV/ CSV/ SARIF/ PREfast
  - **Tool Version** (Version de l’outil) (recommandé : Latest (La plus récente))
  - **Scan Folder** (Analyser le dossier) : Le dossier à analyser dans votre dépôt
  - **Searchers File Type** (Type de fichier des chercheurs) : Options pour localiser le fichier des chercheurs utilisé pour l’analyse.
  - **Suppressions File** (Fichier des suppressions) : Un fichier [JSON](https://json.org/) peut être utilisé pour les problèmes de suppression dans le journal de sortie (plus de détails dans la section Ressources). 
  - **Verbose Output** (Sortie détaillée) : sa signification est explicite 
  - **Batch Size** (Taille de lot) : Nombre de threads simultanés utilisés pour exécuter des analyseurs d’informations d’identification en parallèle. La valeur par défaut est 20 (la valeur doit être comprise entre 1 et 2 147 483 647).
  - **Match Timeout** (Délai d’attente d’une correspondance) : Quantité de temps à passer à essayer une mise en correspondance d’un chercheur avant d’abandonner la vérification. 
  - **File Scan Read Buffer Size** (Taille de la mémoire tampon de lecture de l’analyse des fichiers) : Taille en octets de la mémoire tampon pour la lecture du contenu. (La valeur par défaut est 524 288) 
  - **Maximum File Scan Read Bytes** (Nombre maximal d’octets lus pour l’analyse des fichiers) : Nombre maximal d’octets à lire dans un fichier donné lors de l’analyse du contenu. (La valeur par défaut est 104 857 600) 
  - **Run this task** (Exécuter cette tâche) (sous **Control Options**) (Options de contrôle) : Spécifie le moment où la tâche doit être exécutée. Choisissez « Custom conditions » (Conditions personnalisées) pour spécifier des conditions plus complexes. 
  - **Version** : Version de la tâche de build dans Azure DevOps. Peu utilisé. 

## <a name="microsoft-security-risk-detection-task"></a>Tâche Microsoft Security Risk Detection
> [!NOTE]
> Vous devez créer et configurer un compte avec le service Risk Detection (Détection des risques) comme prérequis pour pouvoir utiliser cette tâche. Ce service nécessite un processus d’intégration distinct; il n’est pas « Plug-and-Play » comme la plupart des autres tâches de cette extension. Reportez-vous à [Microsoft Security Risk Detection](https://aka.ms/msrddocs) et à [Microsoft Security Risk Detection: How To](https://docs.microsoft.com/security-risk-detection/how-to/) pour des instructions.

Détails sur la configuration figurent ci-dessous.

Entrez les données nécessaires : vous pouvez pointez sur chaque option pour obtenir de l’aide.
   - **Azure DevOps Service Endpoint Name for MSRD** (Nom du point de terminaison du service Azure DevOps pour MRSD) : Si vous avez créé un type générique de point de terminaison de service Azure DevOps pour stocker l’URL de l’instance MSRD (que vous avez intégrée) et le jeton d’accès de l’API REST, vous pouvez choisir ce point de terminaison de service. Si ce n’est pas le cas, cliquez sur le lien Manage (Gérer) pour créer et configurer un nouveau point de terminaison de service pour cette tâche MSRD. 
   - **Account ID** (ID de compte) : Il s’agit d’un GUID qui peut être récupéré auprès de l’URL du compte MSRD.
   - **URLs to Binaries** (URL des fichiers binaires) : Liste délimitée par des points-virgules des URL disponibles publiquement (à utiliser par la machine de test à données aléatoires (fuzzing) pour télécharger les fichiers binaires).
   - **URLs of the Seed Files** (URL des fichiers de données initiales) : Liste délimitée par des points-virgules des URL disponibles publiquement (à utiliser par la machine de test à données aléatoires (fuzzing) pour télécharger les données initiales). Ce champ est facultatif si les fichiers de données initiales sont téléchargés avec les fichiers binaires.
   - **OS Platform Type** (Type de plateforme du système d’exploitation) : Type de plateforme du système d’exploitation (Windows ou Linux) des machines sur lesquelles exécuter le travail de test à données aléatoires (fuzzing).
   - **Windows Edition / Linux Edition** (Édition de Windows / Édition de Linux) : Édition du système d’exploitation des machines sur lesquelles exécuter le travail de test à données aléatoires (fuzzing). Vous pouvez remplacer la valeur par défaut si vos machines ont une autre édition du système d’exploitation.
   - **Package Installation Script** (Script d’installation du package) : Spécifiez votre script à exécuter sur une machine de test pour installer le programme cible des tests et ses dépendances avant de soumettre le travail de test à données aléatoires (fuzzing).
   - **Job Submission Parameters** (Paramètres de soumission du travail) :
       - **Seed Directory** (Répertoire des données initiales) : Chemin du répertoire sur la machine de test à données aléatoires (fuzzing) contenant les données initiales.
       - **Seed Extension** (Extension des données initiales) : Extension de fichier des données initiales
       - **Test Driver Executable** (Exécutable du pilote de test) : Chemin de l’exécutable cible sur la machine de test à données aléatoires (fuzzing).
       - **Test Driver Executable Architecture** (Architecture de l’exécutable du pilote de test) : L’architecture du fichier exécutable cible (x86 ou amd64).
       - **Test Driver Arguments** (Arguments du pilote de test) : Arguments de ligne de commande passés à l’exécutable cible des tests. Notez que le symbole "%testfile%", y compris les guillemets doubles, sera automatiquement remplacé par le chemin complet du fichier cible que le pilote de test va analyser et qu’il est obligatoire.
       - **Test Driver Process Exits Upon Test Completion** (Le processus du pilote de test s’arrête à la fin du test) : Cochez la case pour arrêter le pilote de test une fois l’opération terminée ; décochez la case si le pilote de test doit être fermé de façon explicite.
       - **Maximum Duration (in seconds)** (Durée des maximale en secondes) : Spécifiez une estimation de la durée d’attente raisonnable la plus longue nécessaire pour que le programme cible analyse un fichier d’entrée. Plus cette estimation est précise, plus l’exécution du test à données aléatoires (fuzzing) est efficace.
       - **Test Driver Can Be Run Repeatedly** (Le pilote de test peut être exécuté à plusieurs reprises) : Cochez la case si le pilote de test peut être exécuté de façon répétée sans dépendre d’un état global persistant/partagé.
       - **Test Driver Can Be Renamed** (Le pilote de test peut être renommé) : Cochez la case si l’exécutable du pilote de test peut être renommé et continuer à fonctionner correctement.
       - **The Fuzzing Application Runs as a Single OS Process** (L’application de test à données aléatoires (fuzzing) s’exécute sous la forme d’un seul processus du système d’exploitation) : Cochez la case si le pilote de test s’exécute sous la forme d’un seul processus du système d’exploitation ; décochez la case si le pilote de test génère des processus supplémentaires.


## <a name="roslyn-analyzers-task"></a>Tâche Analyseurs Roslyn
> [!NOTE]
> Comme prérequis à l’exécution de la tâche Analyseur Roslyn, votre build doit respecter les conditions suivantes.
>  - Votre définition de build comprend la tâche de build intégrée MSBuild ou VSBuild pour compiler du code C# (ou VB). Cette tâche s’appuie sur l’entrée et la sortie de cette tâche de build spécifique pour réexécuter la compilation MSBuild avec les analyseurs Roslyn activés.
>  - Visual Studio 2017 v15.5 ou ultérieur est installé sur l’agent de build qui exécute cette tâche de build (compilateur version 2.6.x).
>

Détails sur la configuration figurent ci-dessous.

Les options disponibles sont les suivantes : 
- **Ruleset** (Ensemble de règles) : SDL Required (SDL obligatoire), SDL Recommended (SDL recommandé), ou vous pouvez utiliser un ensemble de règles personnalisé.
- **Analyzers Version** (Version des analyseurs) (Recommandé : Latest (La plus récente))
- **Compiler Warnings Suppressions File** (Fichier des suppressions des avertissements du compilateur) : Fichier texte avec une liste d’ID d’avertissement qui doivent être supprimés. 
- **Run this task** (Exécuter cette tâche) (sous **Control Options**) (Options de contrôle) : Spécifie le moment où la tâche doit être exécutée. Choisissez **Custom conditions** (Conditions personnalisées) pour spécifier des conditions plus complexes. 

> [!NOTE]
> - Les analyseurs Roslyn sont intégrés au compilateur et peuvent être exécutés seulement dans le cadre de la compilation CSC.exe. Par conséquent, cette tâche nécessite la relecture/réexécution de la commande du compilateur exécutée précédemment dans la build. Pour cela, recherchez dans VSTS les journaux des tâches de build MSBuild (il n’existe aucun autre moyen pour la tâche d’obtenir de façon fiable la ligne de commande de compilation MSBuild à partir de la définition de build ; nous avons envisagé d’ajouter une zone de texte de forme libre pour permettre aux utilisateurs d’entrer leurs lignes de commande, mais il serait difficile de les maintenir à jour et de les synchroniser avec la build principale). Les builds personnalisées nécessitent la relecture de l’ensemble des commandes, et pas seulement celle des commandes du compilateur, et il n’est pas évident/fiable d’activer les analyseurs Roslyn dans ce cas. 
> - Les analyseurs Roslyn sont intégrés au compilateur et nécessitent la compilation pour être appelés. Cette tâche de build est implémentée par recompilation des projets C# qui ont déjà été générés avec seulement la tâche de build MSBuild/VSBuild, dans la même build / définition de build, mais dans ce cas avec les analyseurs activés. Si cette tâche de build s’exécute sur le même agent que la tâche de build d’origine, la sortie de la tâche de build MSBuild/VSBuild d’origine sera remplacée dans le dossier des sources « s » par la sortie de cette tâche de build. La sortie de la build sera la même, mais il est recommandé d’exécuter MSBuild, de copier la sortie dans le répertoire intermédiaire des artefacts, puis d’exécuter Roslyn.
>

Pour obtenir des ressources supplémentaires pour la tâche Analyseurs Roslyn, consultez [Analyseurs Roslyn sur Microsoft Docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

Le package des analyseurs installés et utilisés par cette tâche de build [se trouve ici](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 

## <a name="tslint-task"></a>Tâche TSLint

Pour plus d’informations sur TSLint, consultez [Dépôt GitHub TSLint](https://github.com/palantir/tslint)
>[!NOTE] 
>Comme vous le savez peut-être, TSLint sera déprécié au cours de l’année 2019 (Source : [Dépôt GitHub TSLint](https://github.com/palantir/tslint)) L’équipe examine actuellement [ESLint](https://github.com/eslint/eslint) comme alternative, et la création d’une nouvelle tâche pour ESLint est dans la feuille de route.

## <a name="publish-security-analysis-logs-task"></a>Tâche Publier les journaux d’analyse de la sécurité
Capture d’écran et détails sur la configuration ci-dessous.

![Personnalisation de Publier l’analyse de la sécurité](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artifact name** (Nom de l’artefact) : Peut être n’importe quel identificateur de chaîne
- **Artifact Type** (Type d’artefact) : Vous pouvez publier des journaux sur le serveur Azure-DevOps ou sur un partage de fichiers qui est accessible à l’agent de build. 
- **Tools** (Outils) : Vous pouvez choisir de conserver les journaux pour des outils individuels spécifiques ou sélectionner **All Tools** (Tous les outils) pour conserver tous les journaux. 

## <a name="security-report-task"></a>Tâche Rapport de sécurité
Capture d’écran et détails sur la configuration ci-dessous.  
![Configurer la post-analyse](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Reports** (Rapports) : Choisissez les fichiers de rapport à créer ; un rapport est créé dans chaque format **Console**, **TSV** et/ou **HTML**. 
- **Tools** (Outils) : Sélectionnez les outils de votre définition de build pour lesquels vous voulez obtenir un récapitulatif des problèmes détectés. Pour chaque outil sélectionné, il peut y avoir une option pour choisir si vous voulez voir dans le rapport seulement les erreurs, ou bien les erreurs et les avertissements. 
- **Advanced Options** (Options avancées) : Vous pouvez choisir de consigner un avertissement ou une erreur (et faire échouer la tâche) au cas où il n’existe pas de journal pour un des outils sélectionnés.
Vous pouvez personnaliser le dossier des journaux de base où les journaux doivent se trouver, mais ce n’est pas un scénario courant. 

## <a name="post-analysis-task"></a>Tâche Post-analyse
Capture d’écran et détails sur la configuration ci-dessous.

![Personnalisation de la post-analyse](./media/security-tools/a-post-analysis600.png) 
- **Tools** (Outils) : Sélectionnez les outils de votre définition de build pour lesquels vous voulez injecter un arrêt de la build en fonction de ses résultats. Pour chaque outil sélectionné, il peut y avoir une option pour choisir si vous voulez interrompre seulement pour les erreurs, ou bien pour les erreurs et les avertissements. 
- **Report** (Rapport) : Vous pouvez si vous le souhaitez écrire les résultats trouvés et qui provoquent l’arrêt de la build dans la fenêtre de la console Azure DevOps et dans le fichier journal. 
- **Advanced Options** (Options avancées) : Vous pouvez choisir de consigner un avertissement ou une erreur (et faire échouer la tâche) au cas où il n’existe pas de journal pour un des outils sélectionnés.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez d’autres questions sur l’extension et les outils proposés, consultez [notre page Forum aux questions](security-code-analysis-faq.md).


