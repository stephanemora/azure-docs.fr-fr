---
title: Forum aux questions de la documentation de Microsoft Azure Security Code Analysis
description: Cet article contient les questions les plus fréquentes sur l’extension Security Code Analysis
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
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718176"
---
# <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
Vous avez des questions ? Pour plus d’informations, consultez les questions les plus fréquentes ci-dessous.

## <a name="general-faqs"></a>Questions fréquentes générales

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Puis-je installer l’extension sur mon serveur TFS (et non pas Azure DevOps) ? 

Non, l’extension n’est pas disponible pour le téléchargement et l’installation sur TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Dois-je exécuter Microsoft Security Code Analysis avec ma build ? 

Oui et non. Selon le type d’outil d’analyse, le code source lui-même peut être le seul élément nécessaire, ou bien la sortie de la génération peut être nécessaire. Par exemple, étant donné que Credential Scanner analyse les fichiers dans la structure de dossiers du dépôt de code, vous pouvez exécuter les tâches de génération Credential Scanner et de Publier les journaux d’analyse de la sécurité dans une build autonome pour récupérer les résultats.
Pour les autres outils qui analysent les artefacts après la build, comme BinSkim, la build sera d’abord nécessaire.

### <a name="can-i-break-my-build-when-results-are-found"></a>Puis-je interrompre ma build quand des résultats sont trouvés ? 
Oui, vous pouvez introduire un arrêt de la build quand un outil signale un problème ou trouve un certain élément dans son fichier journal. Ajoutez simplement la tâche de build Post-Analyse et cochez la case correspondant aux outils pour lesquels vous voulez interrompre la build. Vous pouvez choisir d’interrompre la build quand un outil signale des erreurs, ou des avertissements et des erreurs, dans l’interface utilisateur de la tâche Post-Analysis.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>En quoi les arguments de ligne de commande sont-ils différents dans Azure DevOps et dans les outils de poste de travail autonomes ? 

Pour l’essentiel, les tâches de build Azure DevOps sont des wrappers directs autour des arguments de ligne de commande des outils de sécurité. Tout ce que vous passeriez normalement à l’outil sur la ligne de commande à partir de votre poste de travail, vous pouvez le passer à l’entrée Arguments de la tâche de build.
Voici une liste des différences notables :
 - L’outil s’exécutera à partir du dossier source de l’agent $(Build.SourcesDirectory) ou %BUILD_SOURCESDIRECTORY. Exemple : C:\agent\_work\1\s 
 - Les chemins dans les arguments peuvent être relatifs à la racine du répertoire source indiqué ci-dessus, ou absolu, en exécutant un agent local avec des emplacements de déploiement connus de ressources locales ou avec de variables de build Azure DevOps
 - Les outils fourniront automatiquement un chemin de fichier ou un dossier de sortie ; si un chemin de sortie est fourni, il sera supprimé et remplacé par un chemin vers notre emplacement connu des journaux sur l’agent de build
 - Certains paramètres de ligne de commande supplémentaires sont nettoyés et supprimés sur certains outils, comme l’ajout ou la suppression d’options pour garantir qu’aucune interface utilisateur n’est lancée.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Puis-je exécuter une tâche de build (par exemple Credential Scanner) sur plusieurs dépôts dans une build Azure DevOps ? 

Non, l’exécution des outils de développement sécurisé sur plusieurs dépôts dans un même pipeline n’est pas prise en charge actuellement.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>Le fichier de sortie que j’ai spécifié n’est pas créé ou le fichier de sortie que j’ai spécifié est introuvable 

Les tâches de build nettoient actuellement l’entrée utilisateur et mettent à jour l’emplacement du fichier de sortie généré à un emplacement commun sur l’agent de build. Pour plus d’informations sur cet emplacement, consultez les questions suivantes.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Où les fichiers de sortie générés par les outils sont-ils enregistrés ? 

Les tâches de build ajoutent automatiquement des chemins de sortie à l’emplacement connu suivant sur l’agent de build $(Agent.BuildDirectory)\_sdt\log. En faisant de cet emplacement le standard, nous pouvons garantir que d’autres équipes produisant des journaux d’analyse du code ou les consommant y ont accès.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Puis-je mettre en file d’attente une build pour exécuter ces tâches sur un agent de build hébergé ? 

Oui, toutes les tâches et tous les outils de l’extension peuvent être exécutés sur un agent de build hébergé.

>[!NOTE]
> La tâche de build Anti-Malware nécessite un agent de build pour lequel Windows Defender est activé, ce qui est le cas sur les agents de build « VS2017 hébergé » et ultérieur. (Elle ne s’exécute pas sur l’agent « Hébergé » hérité/VS2015.) Les signatures ne peuvent pas être mises à jour sur ces agents, mais la signature doit toujours être relativement actuelle, c’est-à-dire avoir moins de 3 heures.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Puis-je exécuter ces tâches de build dans le cadre d’un pipeline de mise en production (par opposition à un pipeline de build) ? 
Dans la plupart des cas, oui. Cependant, les tâches qui publient des artefacts ne sont pas prises en charge par Azure DevOps pour être exécutées à partir de pipelines de mise en production : « La seule catégorie de tâches qui ne fonctionnent pas avec Release (Mise en production) est celle des tâches qui publient des artefacts. La raison en est que pour l’instant, nous ne prenons pas en charge la publication d’artefacts dans Release (Mise en production) ».
Ceci empêche l’exécution correcte de la tâche « Publier les journaux d’analyse de la sécurité » à partir d’un pipeline de mise en production : elle échoue avec un message d’erreur descriptif.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Où les tâches de build téléchargent les outils ? 
Les tâches de build a) téléchargent les packages NuGet pour les outils à partir du [flux de gestion des packages Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) suivant, ou en utilisant le gestionnaire de package de nœud, qui doit être préinstallé sur l’agent de build (exemple : « npm install tslint »).

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Quel sera l’effet de l’installation de l’extension sur mon organisation Azure DevOps ? 

Après l’installation, les tâches de build de sécurité fournies par l’extension peuvent être utilisées par tous les utilisateurs de votre organisation. Lors de la création ou de la modification d’un pipeline Azure, vous pouvez ajouter ces tâches à partir de la liste des collections de tâches de build. À part cela, l’installation de l’extension dans votre organisation Azure DevOps n’a aucun impact. Elle ne modifie pas les paramètres ou les pipelines d’un compte ou d’un projet.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>L’installation de l’extension va-t-elle modifier mes pipelines Azure existants ? 

Non. L’installation de l’extension rend les tâches de build de sécurité disponibles pour être ajoutées à vos pipelines Azure. Les utilisateurs doivent néanmoins toujours ajouter ou mettre à jour les définitions de build pour intégrer les outils dans votre processus de build.

## <a name="task-specific-faqs"></a>Questions fréquentes spécifiques aux tâches

Les questions fréquentes spécifiques aux tâches de build sont listées dans cette section.

### <a name="credential-scanner-faqs"></a>Questions fréquentes sur Credential Scanner

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Quels sont les scénarios et les exemples de suppression courants ? 
Deux des scénarios de suppression les plus courants sont détaillés ci-dessous :
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Supprimer toutes les occurrences d’un secret donné dans le chemin spécifié 
La clé de hachage du secret provenant du fichier de sortie de Credential Scanner est nécessaire, comme le montre l’exemple ci-dessous.
   
        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> La clé de hachage est générée par une partie de la valeur correspondante ou du contenu correspondant du fichier. Toute révision du code source peut modifier la clé de hachage et désactiver la règle de suppression. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Pour supprimer tous les secrets d’un fichier spécifié (ou pour supprimer le fichier de secrets lui-même) 
L’expression pour le fichier peut être un nom de fichier ou une partie suffixée du chemin/nom du fichier complet. Les caractères génériques ne sont pas pris en charge. 

**Exemple** 

Fichier à supprimer : [chemin_entrée]\src\JS\lib\angular.js 

Règles de suppression valides : 
- [chemin_entrée] \src\JS\lib\angular.js : supprimer le fichier dans le chemin spécifié
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js : supprimer les fichiers portant le même nom

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Tous les secrets futurs ajoutés au fichier seront également supprimés automatiquement. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Quelles sont les recommandations pour la gestion des secrets ? 
Si détecter au bon moment des secrets codés en dur et réduire ainsi les risques est utile, il est encore mieux de pouvoir empêcher tout simplement l’enregistrement de secrets. À cet égard, Microsoft a publié CredScan Code Analyzer dans le cadre de l’[extension Microsoft DevLabs](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) pour Visual Studio. Si elle n’en est qu’à sa première préversion, elle offre aux développeurs une expérience inline pour la détection des secrets potentiels placés dans leur code, ce qui leur donne la possibilité de résoudre ces problèmes en temps réel. Pour plus d’informations, consultez [ce](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) blog sur la gestion sécurisée des secrets dans le cloud. Voici quelques ressources supplémentaires pour vous aider à gérer les secrets et à accéder de façon sécurisée à des informations sensibles à partir de vos applications : 
 - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [Azure AD Managed Service Identity](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identité du service administré (MSI) pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [Azure Managed Service Identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [Bibliothèque AppAuthentication](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>Puis-je écrire mes propres chercheurs personnalisés ?

Credential Scanner s’appuie sur un ensemble de chercheurs de contenu généralement définis dans le fichier **buildsearchers.xml**. Le fichier contient un tableau d’objets sérialisés XML qui représentent un objet ContentSearcher. Le programme est distribué avec un ensemble de chercheurs qui ont été bien testés, mais il vous permet également d’implémenter vos propres chercheurs personnalisés. 

Un chercheur de contenu est défini comme suit : 

- **Name** : Nom descriptif du chercheur à utiliser dans le fichier de sortie de Credential Scanner. Il est recommandé d’utiliser la convention de nommage avec une casse mixte pour les noms des chercheurs. 
- **RuleId** : ID opaque stable du chercheur. 
    - Les chercheurs par défaut de Credential Scanner reçoivent des RuleId comme CSCAN0010, CSCAN0020, CSCAN0030, etc. Le dernier chiffre est réservé à la fusion ou à la division du groupe d’expressions régulières potentielles du chercheur.
    - Un RuleId pour les chercheurs personnalisés doit avoir son propre espace de noms au format : CSCAN-{espace_de_noms}0010, CSCAN-{espace_de_noms}0020, CSCAN-{espace_de_noms}0030, etc.
    - Le nom complet du chercheur est la combinaison de RuleId et du nom du chercheur. Par exemple : CSCAN0010.KeyStoreFiles, CSCAN0020.Base64EncodedCertificate, etc.
- **ResourceMatchPattern** : Expression régulière des extensions de fichier à vérifier pour le chercheur
- **ContentSearchPatterns** : Tableau de chaînes contenant les déclarations d’expressions régulières à mettre en correspondance. Si aucun modèle de recherche n’est défini, tous les fichiers correspondant au modèle de correspondance des ressources sont retournés.
- **ContentSearchFilters** : Tableau de chaînes contenant des déclarations d’expressions régulières pour filtrer les faux positifs spécifiques au chercheur.
- **Matchdetails** : Message descriptif et/ou instructions d’atténuation à ajouter pour chaque correspondance du chercheur.
- **Recommendation** : Fournit le contenu du champ des suggestions pour une correspondance avec le format de rapport PREfast.
- **Severity** : Nombre entier reflétant la gravité du problème (la plus élevée = 1).
![Configuration de Credential Scanner](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Questions fréquentes sur les analyseurs Roslyn

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Quelles sont les erreurs les plus courantes lors de l’utilisation de la tâche Analyseurs Roslyn ?

**Erreur : Le projet a été restauré en utilisant Microsoft.NETCore.App version x.x.x, mais avec les paramètres actuels, la version y.y.y est utilisée à la place. Pour résoudre ce problème, vérifiez que les mêmes paramètres sont utilisés pour la restauration et pour les opérations qui suivent, comme la génération ou la publication. En général, ce problème peut se produire si la propriété RuntimeIdentifier est définie lors de la génération ou de la publication, mais pas lors de la restauration :**

Les analyseurs Roslyn s’exécutent dans le cadre de la compilation : l’arborescence source sur la machine de build doit donc être dans un état permettant une build. Une étape (probablement « dotnet.exe publish ») entre votre build principale et les analyseurs Roslyn peut avoir mis l’arborescence source dans un état ne permettant pas une build. Il est possible que la duplication de l’étape qui effectue la restauration NuGet, juste avant l’étape Analyseurs Roslyn, replace l’arborescence source dans un état permettant une build.

**« csc. exe » s’est terminé avec le code d’erreur 1 : impossible de créer une instance de l’analyseur AAAA à partir de C:\BBBB.dll : Impossible de charger le fichier ou l’assembly « Microsoft.CodeAnalysis, Version=X.X.X.X, Culture=neutral, PublicKeyToken=31bf3856ad364e35 » ou une de ses dépendances. Le système ne peut pas trouver le fichier spécifié.**

Vérifiez que votre compilateur prend en charge les analyseurs Roslyn. « csc.exe/version » doit indiquer au moins v2.6. x. Dans certains cas, les fichiers .csproj individuels peuvent remplacer l’installation de Visual Studio de l’ordinateur de build, en référençant un package à partir de Microsoft.Net.Compilers. Si l’utilisation d’une version spécifique du compilateur n’était pas intentionnelle, supprimez les références à Microsoft.Net.Compilers. Dans le cas contraire, vérifiez que le package référencé est également au moins v2.6.x. Essayez d’obtenir le journal des erreurs, que vous trouverez dans le paramètre /errorlog: à partir de la ligne de commande csc.exe (trouvée dans le journal de la tâche de build Roslyn). Il peut se présenter comme ceci : /errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif

**Le compilateur C# n’est pas assez récent (il doit être >= 2.6)**

Les dernières versions du compilateur C# sont publiées ici : https://www.nuget.org/packages/Microsoft.Net.Compilers Pour connaître la version installée que vous utilisez, exécutez `C:\>csc.exe /version` à partir de l’invite de commandes. Vérifiez que vous n’avez pas de référence à un package NuGet Microsoft.Net.Compilers < v2.6.

**Journaux MSBuild/VSBuild introuvables**

En raison de la façon dont la tâche fonctionne, elle doit interroger Azure DevOps pour le journal MSBuild à partir de la tâche de build MSBuild. Si cette tâche s’exécute immédiatement après la tâche de build MSBuild, le journal n’est pas encore disponible : placez les autres tâches de build, notamment les tâches de build SecDevTools (comme Binskim, Antimalware Scan, etc.) entre la tâche de build MSBuild et la tâche de build Analyseurs Roslyn. 

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, notez que le support de Microsoft Security Code Analysis est disponible du lundi au vendredi, de 9h00 à 17h00 (Heure standard du Pacifique)

  - Intégration : Contactez vos responsables de comptes techniques pour commencer. 
  >[!NOTE] 
  >Si vous n’avez pas encore de relation de support payant avec Microsoft ou si vous disposez d’une offre de support qui ne vous permet pas d’acheter des services à partir du catalogue Phoenix, visitez notre [page d’accueil des services de support technique](https://www.microsoft.com/enterprise/services/support) pour plus d’informations.

  - Support technique : Envoyez un e-mail à notre équipe au [Support de Microsoft Security Code Analysis](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)