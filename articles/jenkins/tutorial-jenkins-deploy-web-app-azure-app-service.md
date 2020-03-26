---
title: 'Tutoriel : Effectuer un déploiement sur Azure App Service à partir de GitHub avec Jenkins'
description: Configurez Jenkins pour l’intégration continue (CI) dans GitHub et le déploiement continu (CD) sur Azure App Service pour les applications web Java
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158406"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Tutoriel : Effectuer un déploiement sur Azure App Service à partir de GitHub avec l’intégration continue et le déploiement continu Jenkins

Ce tutoriel explique comment déployer un exemple d’application web Java de GitHub sur [Azure App Service sur Linux](/azure/app-service/containers/app-service-linux-intro), en configurant l’intégration continue (CI) et le déploiement continu (CD) dans Jenkins. Lorsque vous mettez à jour l’application en envoyant (push) des validations à GitHub, Jenkins génère et republie automatiquement votre application dans Azure App Service. L’exemple d’application de ce tutoriel a été développé avec le framework [Spring Boot](https://projects.spring.io/spring-boot/). 

![Vue d’ensemble d’un déploiement entre GitHub et Azure App Service](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Installer les plug-ins Jenkins pour générer l’application à partir de GitHub, la déployer sur Azure App Service et effectuer d’autres tâches associées
> * Dupliquer (fork) le dépôt d’exemples GitHub pour disposer d’une copie de travail
> * Connecter Jenkins à GitHub
> * Créer un principal de service Azure pour que Jenkins puisse accéder à Azure sans utiliser vos informations d’identification
> * Ajouter votre principal de service dans Jenkins
> * Créer le pipeline Jenkins qui génère et déploie l’exemple d’application chaque fois que vous mettez à jour l’application dans GitHub
> * Créer des fichiers de génération et de déploiement pour votre pipeline Jenkins
> * Diriger votre pipeline Jenkins vers le script de génération et de déploiement
> * Déployer votre exemple d’application dans Azure en exécutant une génération manuelle
> * Envoyer (push) une mise à jour d’application dans GitHub, ce qui pousse Jenkins à générer et à redéployer l’application dans Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour les besoins de ce tutoriel, vous devez disposer des éléments suivants :

* Un serveur [Jenkins](https://jenkins.io/) sur lequel sont installés le kit de développement Java (JDK) et les outils Maven, sur une machine virtuelle Linux Azure

  Si vous n’avez pas de serveur Jenkins, suivez ces étapes dans le portail Azure : [Créer un serveur Jenkins sur une machine virtuelle Linux Azure](/azure/jenkins/install-jenkins-solution-template)

* Un compte [GitHub](https://github.com) afin d’obtenir une copie de travail (duplication fork) de l’exemple d’application web Java. 

* [Azure CLI](/cli/azure/install-azure-cli), que vous pouvez exécuter à partir de votre ligne de commande locale ou d’[Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Installer les plug-ins Jenkins

1. Connectez-vous à votre console web Jenkins à l’emplacement suivant :

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Dans la page principale de Jenkins, sélectionnez **Manage Jenkins** > **Manage Plugins**.

   ![Gérer les plug-ins Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Sous l’onglet **Available** (Disponible), sélectionnez ces plug-ins :

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - [Plug-in Environment Injector](https://plugins.jenkins.io/envinject) Jenkins
   - Des [informations d’identification Azure](https://plugins.jenkins.io/azure-credentials)

   Si les plug-ins n’apparaissent pas, vérifiez sous l’onglet **Installed** qu’ils ne sont pas déjà installés.

1. Pour installer les plug-ins sélectionnés, sélectionnez **Download now and install after restart** (Télécharger maintenant et installer après le redémarrage).

1. Une fois terminé, dans le menu Jenkins, sélectionnez **Manage Jenkins** afin de retourner à la page de gestion de Jenkins pour les étapes suivantes.

## <a name="fork-sample-github-repo"></a>Dupliquer (fork) le dépôt d’exemples GitHub

1. [Connectez-vous au dépôt GitHub pour obtenir l’exemple d’application Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. En haut à droite de GitHub, sélectionnez **Fork** (Dupliquer).

   ![Dupliquer le dépôt d’exemples de GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Suivez les invites pour sélectionner votre compte GitHub et terminer la duplication.

Ensuite, configurez Jenkins avec vos informations d’identification GitHub.

## <a name="connect-jenkins-to-github"></a>Connecter Jenkins à GitHub

Pour que Jenkins supervise GitHub et réponde quand de nouvelles validations sont envoyées (push) à votre application web dans votre duplication (fork) GitHub, activez [GitHub webhooks](https://developer.github.com/webhooks/) dans Jenkins.

> [!NOTE]
> 
> Les étapes suivantes créent des informations d’identification de jeton d’accès personnel pour que Jenkins puisse utiliser GitHub avec votre nom d’utilisateur et de votre mot de passe GitHub. 
> Toutefois, si votre compte GitHub utilise l’authentification à deux facteurs, vous devrez créer votre jeton dans GitHub et configurer Jenkins pour qu’il utilise ce jeton. 
> Pour plus d’informations, consultez la documentation du [plug-in Jenkins GitHub](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. Dans la page **Manage Jenkins**, sélectionnez **Configure System**. 

   ![Configurer le système dans Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Dans la section **GitHub**, fournissez les informations concernant votre serveur GitHub. Dans la liste **Add GitHub Server** (Ajouter un serveur GitHub), sélectionnez **GitHub Server**. 

   ![Ajouter un serveur GitHub dans Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Si la propriété **Manage hooks** (Gérer les crochets) n’est pas sélectionnée, sélectionnez-la. Sélectionnez **Advanced** pour spécifier d’autres paramètres. 

   ![Spécifier les paramètres avancés Jenkins pour le serveur GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Dans la liste **Manage additional GitHub actions** (Gérer d’autres actions GitHub), sélectionnez **Convert login and password to token** (Convertir le nom d’utilisateur et le mot de passe en jeton).

   ![Convertir le nom d’utilisateur et le mot de passe en jeton pour GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Sélectionnez **From login and password** (À partir du nom d’utilisateur et du mot de passe), puis entrez votre nom d’utilisateur et votre mot de passe GitHub. Lorsque vous avez terminé, sélectionnez **Create token credentials** (Créer des informations d’identification de jeton) pour créer un [jeton d’accès personnel (PAT) GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Créer un jeton PAT GitHub à partir du nom d’utilisateur et du mot de passe](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Dans la section **GitHub Server**, dans la liste **Credentials** (Informations d’identification), sélectionnez votre nouveau jeton. Vérifiez que l’authentification fonctionne en choisissant **Test connection**.

   ![Vérifier la connexion au serveur GitHub avec le nouveau jeton d’accès personnel](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Ensuite, créez le principal de service Azure que Jenkins utilise pour l’authentification et l’accès aux ressources Azure.

## <a name="create-service-principal"></a>Créer un principal du service

Dans une prochaine section, vous créerez un travail de pipeline Jenkins pour générer votre application à partir de GitHub et déployer votre application dans Azure App Service. Pour permettre à Jenkins d’accéder à Azure sans entrer vos informations d’identification, créez un [principal de service](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) dans Azure Active Directory pour Jenkins. Un principal de service est une identité distincte que Jenkins peut utiliser pour authentifier l’accès aux ressources Azure. Pour créer ce principal de service, exécutez la commande Azure CLI [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) à partir de votre ligne de commande locale ou d’Azure Cloud Shell. Par exemple : 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Veillez à placer le nom du principal de service entre guillemets. Vous devez également créer un mot de passe fort en suivant les [règles et restrictions relatives aux mots de passe Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Si vous ne fournissez pas de mot de passe, Azure CLI en crée un pour vous. 

Voici la sortie générée par la commande **`create-for-rbac`**  : 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Si vous disposez déjà d’un principal de service, vous pouvez réutiliser cette identité.
> Lorsque vous fournissez des valeurs de principal de service pour l’authentification, utilisez les valeurs de propriété `appId`, `password` et `tenant`. 
> Lorsque vous recherchez un principal de service existant, utilisez la valeur de propriété `displayName`.

## <a name="add-service-principal-to-jenkins"></a>Ajouter un principal de service dans Jenkins

1. Dans la page principale de Jenkins, sélectionnez **Credentials (Informations d’identification)**  > **System**. 

1. Dans la page **System** sous **Domain**, sélectionnez **Global credentials (unrestricted)** (Informations d’identification globales (sans restrictions)).

1. Dans le menu de gauche, sélectionnez **Add Credentials** (Ajouter des informations d’identification).

1. Dans la liste **Kind** (Type), sélectionnez **Azure Service Principal**.

1. Fournissez les informations sur votre principal de service et votre abonnement Azure dans les propriétés du tableau suivant :

   ![Ajouter les informations d’identification du principal de service](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Propriété | Valeur | Description | 
   |----------|-------|-------------| 
   | **Identifiant d’abonnement** | <*yourAzureSubscription-ID*> | Valeur GUID de votre abonnement Azure <p>**Conseil** : Si vous ne connaissez pas votre ID d’abonnement Azure, exécutez cette commande Azure CLI à partir de la ligne de commande ou dans Cloud Shell, puis utilisez la valeur GUID `id` : <p>`az account list` | 
   | **ID client** | <*yourAzureServicePrincipal-ID*> | Valeur GUID `appId` générée précédemment pour votre principal de service Azure | 
   | **Clé secrète client** | <*yourSecurePassword*> | Valeur ou « secret » `password` que vous avez fourni(e) pour votre principal de service Azure | 
   | **Tenant ID** | <*yourAzureActiveDirectoryTenant-ID*> | Valeur GUID `tenant` de votre locataire Azure Active Directory | 
   | **Identifiant** | <*yourAzureServicePrincipalName*> | Valeur `displayName` de votre principal de service Azure | 

1. Pour vérifier que votre principal de service fonctionne, sélectionnez **Verify Service Principal** (Vérifier le principal du service). Quand vous avez terminé, sélectionnez **OK**.

Ensuite, créez le pipeline Jenkins qui génère et déploie votre application.

## <a name="create-jenkins-pipeline"></a>Créer un pipeline Jenkins

Dans Jenkins, créez le travail de pipeline pour générer et déployer votre application.

1. Retournez à la page d’accueil de Jenkins, puis sélectionnez **New Item** (Nouvel élément). 

   ![Créer un pipeline Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Fournissez un nom pour votre travail de pipeline, par exemple, « My-Java-Web-App », puis sélectionnez **Pipeline**. En bas, sélectionnez **OK**.  

   ![Nommer le travail de pipeline Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Configurez Jenkins avec votre principal de service pour que Jenkins puisse effectuer le déploiement sur Azure sans utiliser vos informations d’identification.

   1. Sous l’onglet **General**, sélectionnez **Prepare an environment for the run** (Préparer un environnement pour l’exécution). 

   1. Dans la zone **Properties Content** (Contenu des propriétés) qui s’affiche, ajoutez ces variables d’environnement et leurs valeurs. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Préparer un environnement pour l’exécution et définir des variables d’environnement](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Quand vous avez terminé, sélectionnez **Enregistrer**.

Ensuite, créez les scripts de génération et de déploiement pour Jenkins.

## <a name="create-build-and-deployment-files"></a>Créer des fichiers de génération et de déploiement

À présent, créez les fichiers que Jenkins doit utiliser pour générer et déployer votre application.

1. Dans le dossier `src/main/resources/` de votre duplication (fork) GitHub, créez un fichier de configuration d’application nommé `web.config`, contenant le code XML suivant, mais où `gs-spring-boot-0.1.0.jar` remplace `$(JAR_FILE_NAME)` :

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Dans le dossier racine de votre duplication (fork) GitHub, créez un script de génération et de déploiement nommé `Jenkinsfile`, contenant le texte suivant ([voir la source dans GitHub](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)) :

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Validez les fichiers `web.config` et `Jenkinsfile` dans votre duplication (fork) GitHub, puis envoyez (push) vos modifications.

## <a name="point-pipeline-at-script"></a>Diriger le pipeline vers le script

Spécifiez le script de génération et de déploiement que Jenkins doit utiliser.

1. Dans Jenkins, sélectionnez le travail de pipeline créé précédemment. 

   ![Sélectionner le travail de pipeline Jenkins de votre application web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. Dans le menu de gauche, sélectionnez **Configure**.

1. Sous l’onglet **Pipeline**, dans la liste **Definition**, sélectionnez **Pipeline script from SCM** (Script de pipeline SCM).

   1. Dans la zone **SCM** qui s’affiche, sélectionnez **Git** comme contrôle de code source. 

   1. Sous la section **Repositories** (Dépôts), dans **Repository URL** (URL du dépôt), entrez l’URL de votre duplication GitHub. Par exemple : 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Pour **Credentials** (Informations d’identification), sélectionnez le jeton d’accès personnel GitHub créé précédemment.

   1. Dans la zone **Script Path** (Chemin du script), ajoutez le chemin de votre script « Jenkinsfile ».

   Une fois que vous avez terminé, votre définition de pipeline ressemble à cet exemple : 

   ![Faire pointer le pipeline Jenkins vers le script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Quand vous avez terminé, sélectionnez **Enregistrer**.

Ensuite, générez et déployez votre application dans Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Générer et déployer dans Azure

1. Avec Azure CLI, à partir de la ligne de commande ou d’Azure Cloud Shell, créez une [application web Azure App Service sur Linux](/azure/app-service/containers/app-service-linux-intro), que Jenkins doit déployer une fois la génération terminée. Vérifiez que votre application web a un nom unique.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Pour plus d’informations sur ces commandes Azure CLI, consultez les pages suivantes :

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Dans Jenkins, sélectionnez votre travail de pipeline, puis sélectionnez **Build Now** (Générer maintenant).

   Une fois la génération terminée, Jenkins déploie votre application qui se trouve maintenant sur Azure sous l’URL de publication définie. Par exemple : 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Afficher votre application déployée dans Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Envoyer (push) les modifications et redéployer

1. Dans votre navigateur web, accédez à l’emplacement suivant de la duplication (fork) GitHub de votre application web :

   `complete/src/main/java/Hello/Application.java`
   
1. En haut à droite de GitHub, sélectionnez **Edit this file** (Modifier ce fichier).

1. Apportez la modification suivante à la méthode `commandLineRunner()`, puis validez-la dans la branche `master` du dépôt. Dans la branche `master`, cette validation démarre une génération dans Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Une fois la génération terminée et le redéploiement effectué par Jenkins dans Azure, actualisez votre application pour voir la version mise à jour.

   ![Afficher votre application déployée dans Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Résolution des problèmes liés aux plug-ins Jenkins

Si vous constatez un bogue dans les plug-ins Jenkins, signalez le problème dans [Jenkins JIRA](https://issues.jenkins-ci.org/) en sélectionnant le composant concerné.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser des machines virtuelles Azure comme agents de build](/azure/jenkins/jenkins-azure-vm-agents)
