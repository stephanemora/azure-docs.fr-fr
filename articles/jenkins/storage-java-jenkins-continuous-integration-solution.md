---
title: Utilisation de Stockage Azure avec une solution d’intégration continue Jenkins
description: Ce didacticiel décrit l’utilisation du service blob Azure comme référentiel d’artefacts de build créés par une solution d’intégration continue Jenkins.
keywords: Jenkins, Azure, DevOps, Stockage, CI/CD
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624484"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Utilisation de Stockage Azure avec une solution d’intégration continue Jenkins

Cet article explique comment utiliser Stockage Blob comme référentiel pour les artefacts de build créés par une solution d’intégration continue (CI) Jenkins ou comme source de fichiers téléchargeables dans un processus de génération. Cela peut s’avérer utile dans plusieurs scénarios, notamment quand vous codez dans un environnement de développement agile (avec Java ou d’autres langages), que les builds s’exécutent sur la base d’une intégration continue et que vous avez besoin d’un référentiel pour vos artefacts de build, de manière à pouvoir par exemple les partager avec d’autres membres de l’organisation ou avec vos clients, ou conserver une archive. Il existe un autre scénario dans lequel votre tâche de build proprement dite requiert d'autres fichiers, comme des dépendances à télécharger dans le cadre de l'entrée de génération.

Dans ce tutoriel, vous allez utiliser le plug-in Stockage Azure pour Jenkins CI mis à disposition par Microsoft.

## <a name="jenkins-overview"></a>Vue d’ensemble de Jenkins

Jenkins rend possible l’intégration continue d’un projet de logiciel en permettant aux développeurs d’intégrer aisément les modifications de leur code, et de créer automatiquement et fréquemment des builds, d’où une productivité accrue. Les versions des builds sont gérées et les artefacts de build peuvent être téléchargés dans divers référentiels. Cet article montre comment utiliser Stockage Blob Azure comme référentiel des artefacts de build. Vous allez également apprendre à télécharger des dépendances depuis le stockage d'objets blob Azure.

Pour plus d'informations sur Jenkins, consultez la page de [présentation de Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Avantages de l’utilisation du service BLOB

L'utilisation du service BLOB pour héberger vos artefacts de build dans un environnement agile présente les avantages suivants :

* Haute disponibilité de vos artefacts de build et/ou dépendances téléchargeables.
* Performances lorsque votre solution Jenkins CI télécharge vos artefacts de build.
* Performances lorsque vos clients et partenaires téléchargent vos artefacts de build.
* Contrôle sur les stratégies d'accès utilisateur, avec choix entre accès anonyme, accès par signature d'accès partagé basé sur l'expiration, accès privé, etc.

## <a name="prerequisites"></a>Prérequis

* Une solution d’intégration continue Jenkins.
  
    Si vous ne disposez pas d'une solution Jenkins CI, vous pouvez en exécuter une à l'aide de la technique suivante :
  
  1. Sur un ordinateur compatible Java, téléchargez le fichier jenkins.war à l’adresse <https://jenkins-ci.org>.
  2. Accédez au dossier contenant le fichier jenkins.war, ouvrez une invite de commandes et exécutez la commande suivante :
     
      `java -jar jenkins.war`

  3. Dans votre navigateur, ouvrez `http://localhost:8080/` pour accéder au tableau de bord Jenkins, que vous allez utiliser pour installer et configurer le plug-in Stockage Azure.
     
      Une solution Jenkins CI type serait configurée pour s'exécuter en tant que service, mais l'exécution du fichier .war Jenkins depuis la ligne de commande est suffisante pour les besoins de ce didacticiel.
* Un compte Azure. Vous pouvez vous inscrire pour un compte Azure sur <https://www.azure.com>.
* Un compte de stockage Azure. Si vous ne disposez pas déjà d’un compte de stockage, vous pouvez en créer un en suivant la procédure décrite dans la section [Créer un compte de stockage](../storage/common/storage-account-create.md).
* Une bonne connaissance de la solution Jenkins CI est recommandée, mais pas obligatoire, car le contenu suivant emploiera un exemple élémentaire pour décrire la procédure requise lorsque vous utilisez le service BLOB comme référentiel pour les artefacts de build Jenkins CI.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Utilisation du service BLOB avec Jenkins CI
Pour utiliser le service BLOB avec Jenkins, vous devez installer le plug-in Azure Storage, le configurer pour utiliser votre compte de stockage, puis créer une action post-build qui télécharge vos artefacts de build sur votre compte de stockage. Les étapes de cette procédure sont décrites dans les sections suivantes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Comment installer le plug-in de stockage Azure
1. Dans le tableau de bord Jenkins, sélectionnez **Manage Jenkins (Gérer Jenkins)** .
2. Dans la page **Manage Jenkins** (Gérer Jenkins), sélectionnez **Manage Plugins** (Gérer les plug-ins).
3. Sélectionnez l’onglet **Available** (Disponible).
4. Dans la section **Téléchargeurs d'artefacts**, activez la case à cocher **Plug-in Microsoft Azure Storage**.
5. Sélectionnez **Install without restart** (Installer sans redémarrer) ou **Download now and install after restart** (Télécharger maintenant et installer après redémarrage).
6. Redémarrez Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Comment configurer le plug-in de stockage Azure pour utiliser votre compte de stockage
1. Dans le tableau de bord Jenkins, sélectionnez **Manage Jenkins (Gérer Jenkins)** .
2. Dans la page **Manage Jenkins** (Gérer Jenkins), sélectionnez **Configure System** (Configurer le système).
3. Dans la section **Configuration du compte Microsoft Azure Storage** :
   1. Entrez le nom de votre compte de stockage, que vous pouvez obtenir à partir du [portail Azure](https://portal.azure.com).
   2. Entrez la clé de votre compte de stockage, que vous pouvez obtenir à partir du [portail Azure](https://portal.azure.com).
   3. Utilisez la valeur par défaut pour **Blob Service Endpoint URL** (URL du point de terminaison du service d’objets blob) si vous utilisez le cloud Azure global. Si vous vous servez d’un autre cloud Azure, utilisez le point de terminaison spécifié dans le [portail Azure](https://portal.azure.com) pour votre compte de stockage. 
   4. Sélectionnez **Validate storage credentials** (Valider les informations d’identification de stockage) pour valider votre compte de stockage. 
   5. [Facultatif] Si vous avez d’autres comptes de stockage que vous voulez rendre disponibles pour votre solution Jenkins CI, sélectionnez **Add more Storage Accounts** (Ajouter d’autres comptes de stockage).
   6. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Création d'une action post-build qui télécharge les artefacts de votre build sur votre compte de stockage
Pour les besoins de la formation, vous devez d’abord créer une tâche qui créera plusieurs fichiers, puis ajouter l’action post-build pour charger les fichiers sur votre compte de stockage.

1. Dans le tableau de bord Jenkins, cliquez sur **New Item** (Nouvel élément).
2. Nommez la tâche **MyJob**, sélectionnez **Build a free-style software project** (Créer un projet logiciel de style libre), puis **OK**.
3. Dans la section **Build** (Générer) de la configuration de la tâche, sélectionnez **Add build step** (Ajouter une étape de génération) et sélectionnez **Execute Windows batch command** (Exécuter une commande batch Windows).
4. Dans **Command**, utilisez les commandes suivantes :

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Dans la section **Post-build Actions** (Actions post-build) de la configuration de la tâche, sélectionnez **Add post-build action** (Ajouter une action post-build) et sélectionnez **Upload artifacts to Azure Blob storage** (Charger des artefacts sur Stockage Blob Azure).
6. Dans le champ **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
7. Dans le champ **Nom du conteneur**, indiquez le nom du conteneur. Le conteneur est créé s’il n’existe pas déjà au téléchargement des artefacts de build. Vous pouvez utiliser des variables d’environnement. Dans cet exemple, entrez `${JOB_NAME}` comme nom de conteneur.
   
    **Conseil**
   
    Sous la section **Command** où vous avez entré un script pour **Execute Windows batch command**, un lien permet d'accéder aux variables d'environnement reconnues par Jenkins. Sélectionnez ce lien pour découvrir les noms des variables d’environnement avec leurs descriptions. Les variables d’environnement qui contiennent des caractères spéciaux, comme la variable d’environnement **BUILD_URL**, ne sont pas autorisées comme nom de conteneur ou comme chemin virtuel commun.
8. Sélectionnez **Make new container public by default** (Rendre le nouveau conteneur public par défaut) pour cet exemple. (Si vous voulez utiliser un conteneur privé, vous devez créer une signature d’accès partagé pour autoriser l’accès, ce qui se trouve en dehors de la portée de cet article. Pour en savoir plus sur les signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP)](../storage/common/storage-sas-overview.md).)
9. [Facultatif] Sélectionnez **Clean container before uploading** (Nettoyer le conteneur avant le téléchargement) si vous voulez que le contenu du conteneur soit effacé avant le chargement des artefacts de build (ne sélectionnez pas cette option si vous ne voulez pas effacer le contenu du conteneur).
10. Dans **List of Artifacts to upload** (Liste des artefacts à télécharger), entrez `text/*.txt`.
11. Pour **Common virtual path for uploaded artifacts** (Chemin virtuel commun pour les artefacts chargés), dans le cadre de ce tutoriel, entrez `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.
13. Dans le tableau de bord Jenkins, sélectionnez **Build Now** (Générer maintenant) pour exécuter **MyJob**. Examinez l'état dans la sortie de la console. Les messages d'état du stockage Azure sont inclus dans la sortie de la console lorsque l'action post-build commence à télécharger les artefacts de build.
14. Une fois la tâche terminée correctement, vous pouvez examiner les artefacts de build en ouvrant l’objet blob public.
    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    2. Sélectionnez **Stockage**.
    3. Sélectionnez le nom du compte de stockage que vous avez utilisé pour Jenkins.
    4. Sélectionnez **Conteneurs**.
    5. Sélectionnez le conteneur nommé **myjob**, qui correspond à la version en minuscules du nom de tâche attribué à la création de la tâche Jenkins. Les noms de conteneurs et les noms d’objets blob sont en minuscules (et sensibles à la casse) dans le stockage Azure. La liste d’objets blob du conteneur nommé **myjob** contient normalement **hello.txt** et **date.txt**. Copiez l’URL correspondant à l’un de ces éléments et ouvrez-la dans le navigateur. Le fichier texte qui a été téléchargé apparaît comme un artefact de build.

Une seule action post-build qui télécharge les artefacts dans le stockage d'objet blob Azure peut être créée par tâche. L’action post-build permettant de charger des artefacts sur Stockage Blob Azure peut spécifier différents fichiers (notamment des caractères génériques) et chemins de fichiers dans **List of Artifacts to upload** (Liste des artefacts à charger) grâce à l’ajout d’un point-virgule comme séparateur. Par exemple, si votre build Jenkins produit des fichiers JAR et des fichiers TXT dans le dossier **build** de votre espace de travail et que vous voulez charger ces deux types de fichiers sur Stockage Blob Azure, entrez ce qui suit dans le champ **List of Artifacts to upload** (Liste des artefacts à charger) : `build/\*.jar;build/\*.txt`. Vous pouvez aussi utiliser un double signe deux-points pour indiquer le chemin à utiliser dans le nom de l’objet blob. Par exemple, si vous souhaitez que les fichiers JAR soient téléchargés à l’aide de fichiers **binaires** dans le chemin d’accès des objets blob et que les fichiers TXT soient téléchargés à l’aide de **notices** dans le chemin d’accès des objets blob, utilisez la valeur suivante pour l’option **List of Artifacts to upload** (Liste des artefacts à télécharger) comme suit : `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Création d'une étape de génération pour télécharger des éléments depuis un stockage d'objets blob Azure
Les étapes suivantes expliquent comment configurer une étape de génération pour télécharger des éléments depuis Stockage Blob Azure, ce qui est pratique si vous voulez inclure des éléments dans votre build. Le stockage de fichiers JAR dans Stockage Blob Azure est un exemple d’utilisation de ce modèle.

1. Dans la section **Build** (Générer) de la configuration de la tâche, sélectionnez **Add build step** (Ajouter une étape de génération) et sélectionnez **Download from Azure Blob storage** (Télécharger à partir de Stockage Blob Azure).
2. Dans le champ **Nom du compte de stockage**, sélectionnez le compte de stockage à utiliser.
3. Dans le champ **Nom du conteneur**, indiquez le nom du conteneur dans lequel se trouvent les objets blob que vous souhaitez télécharger. Vous pouvez utiliser des variables d'environnement.
4. Dans le champ **Nom d'objet blob**, indiquez le nom de l'objet blob. Vous pouvez utiliser des variables d'environnement. Vous pouvez aussi utiliser un astérisque comme caractère générique après avoir indiqué la ou les premières lettres du nom de l’objet blob. Par exemple, **projet\\** * désigne tous les objets blob dont le nom commence par **projet**.
5. [Facultatif] Dans le champ **Chemin de téléchargement**, indiquez l'emplacement de l'ordinateur Jenkins où vous souhaitez télécharger les fichiers depuis le stockage d'objets blob Azure. Vous pouvez utiliser des variables d’environnement. Si vous n'entrez rien dans le champ **Chemin de téléchargement**, les fichiers du stockage d'objets blob Azure seront téléchargés dans l'espace de travail de la tâche.

Si vous souhaitez télécharger d’autres éléments depuis le stockage d’objets blob Azure, vous pouvez créer des étapes de génération supplémentaires.

Après avoir exécuté une build, vous pouvez vérifier la sortie de la console d’historique de build ou vérifier dans le dossier de téléchargement si les objets blob attendus ont bien été téléchargés.  

## <a name="components-used-by-the-blob-service"></a>Composants utilisés par le service BLOB
La section suivante présente les composants du service Blob.

* **Compte de stockage** : Tous les accès à Azure Storage passent par un compte de stockage. Un compte de stockage est le plus haut niveau de l’espace de noms permettant d’accéder aux objets blob. Un compte peut contenir un nombre illimité de conteneurs, tant que sa taille totale ne dépasse pas 100 To.
* **Conteneur** : Un conteneur regroupe un ensemble d’objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets blob.
* **Objet blob** : Fichier de tout type et de toute taille. Il existe deux types d’objets blob qui peuvent être enregistrés dans un stockage Azure : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d'objet blob, peuvent avoir une taille maximale de 1 To et sont plus efficaces lorsque les plages d'octets d'un fichier sont fréquemment modifiées. Pour plus d’informations sur les objets blob, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format d’URL** : Les objets blob sont adressables à l’aide du format d’URL suivant :
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Le format ci-dessus s’applique au cloud Azure global. Si vous utilisez un autre cloud Azure, utilisez le point de terminaison dans le [portail Azure](https://portal.azure.com) pour déterminer votre URL de point de terminaison.)
  
    Dans le format ci-dessus, `storageaccount` représente le nom de votre compte de stockage, `container_name` représente le nom de votre conteneur et `blob_name` représente le nom de votre objet blob, respectivement. Le nom du conteneur contient plusieurs chemins d’accès, séparés par une barre oblique ( **/** ). Dans ce tutoriel, nous avons utilisé **MyJob** comme exemple de nom de conteneur et **${BUILD\_ID}/${BUILD\_NUMBER}** comme chemin virtuel commun. L’URL de l’objet blob a donc la forme suivante :
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Dépannage du plug-in Jenkins

Si vous constatez des bogues dans les plug-ins Jenkins, enregistrez un problème dans le [Jenkins JIRA](https://issues.jenkins-ci.org/) du composant en question.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/Jenkins/)