---
title: 'Tutoriel : Application Linux Ruby avec Postgres'
description: Découvrez comment faire fonctionner une application Linux Ruby dans Azure App Service en établissant une connexion à une base de données PostgreSQL dans Azure. Rails est utilisé dans le tutoriel.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom: seodec18
ms.openlocfilehash: 3238de3f1d3416964af05db031b5ecec20e4f140
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720189"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Générer une application Ruby et Postgres dans Azure App Service sur Linux

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce tutoriel vous explique comment créer une application Ruby et comment la connecter à une base de données PostgreSQL. Une fois terminé, vous disposerez d’une application [Ruby on Rails](https://rubyonrails.org/) s’exécutant dans App Service sur Linux.

![Application Ruby on Rails s’exécutant dans Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connexion d’une application Ruby on Rails à PostgreSQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

* [Installez Git](https://git-scm.com/)
* [Installez Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [Installez Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Préparation du Postgres local

Dans cette étape, vous allez créer une base de données dans votre serveur Postgres local qui vous sera utile dans ce tutoriel.

### <a name="connect-to-local-postgres-server"></a>Se connecter au serveur Postgres local

Ouvrez la fenêtre de terminal et exécutez `psql` pour vous connecter à votre serveur Postgres local.

```bash
sudo -u postgres psql
```

Si la connexion est établie, cela signifie que votre base de données Postgres est en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données Postgres est démarrée en suivant les étapes décrites dans la page [Téléchargements - Distribution principale de PostgreSQL](https://www.postgresql.org/download/).

Saisissez `\q` pour quitter le client Postgres. 

Créez un utilisateur Postgres qui peut créer des bases de données en exécutant la commande suivante, à l’aide de votre nom d’utilisateur de connexion à Linux.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Créer une application Ruby on Rails localement
Dans cette étape, vous allez créer un exemple d’application Ruby on Rails, configurer sa connexion à la base de données et l’exécuter localement. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.

Exécutez la commande suivante pour cloner l’exemple de référentiel :

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` vers votre répertoire cloné. Installez les packages requis.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Télécharger l’exemple localement

Exécutez les [migrations de Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) pour créer les tables requises par l’application. Pour identifier les tables créées dans les migrations, consultez le répertoire _db/migrate_ dans le dépôt Git.

```bash
rake db:create
rake db:migrate
```

Exécutez l'application.

```bash
rails server
```

Dans un navigateur, accédez à `http://localhost:3000`. Ajoutez quelques tâches dans la page.

![Ruby on Rails se connecte correctement à Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Créer Postgres dans Azure

Dans cette étape, vous allez créer une base de données Postgres dans [Azure Database pour PostgreSQL](/azure/postgresql/). Vous configurerez ensuite l’application Ruby on Rails pour la connexion à cette base de données.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Créer un serveur Postgres

Créez un serveur PostgreSQL avec la commande [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).

Exécutez la commande suivante dans Cloud Shell et substituez un nom de serveur unique à l’espace réservé *\<postgres-server-name>* . Le nom du serveur doit être unique parmi tous les serveurs dans Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Lorsque le serveur de base de données Azure pour PostgreSQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres-server-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres-server-name>",
  "location": "westeurope",
  "name": "<postgres-server-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Dans Cloud Shell, créez une règle de pare-feu pour votre serveur Postgres afin d’autoriser les connexions client à l’aide de la commande [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create). Lorsque les adresses IP de début et de fin sont définies sur 0.0.0.0, le pare-feu est ouvert uniquement pour les autres ressources Azure. Substituez un nom de serveur unique à l’espace réservé *\<postgres-server-name>* .

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Vous pouvez être encore plus restrictif dans votre règle de pare-feu en [choisissant uniquement les adresses IP sortantes que votre application utilise](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Se connecter au serveur de production Postgres localement

Dans Cloud Shell, connectez-vous au serveur Postgres dans Azure. Utilisez la valeur spécifiée précédemment pour les espaces réservés _&lt;postgres-server-name>_ .

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Quand une invite de mot de passe apparaît, utilisez _My5up3r$tr0ngPa$w0rd!_ , que vous avez spécifié au cours de la création du serveur de base de données.

### <a name="create-a-production-database"></a>Création d’une base de données de production

À l’invite `postgres`, créez une base de données.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données nommé _railsappuser_ et accordez-lui tous les privilèges dans la base de données `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Quittez la connexion au serveur en tapant `\q`.

## <a name="connect-app-to-azure-postgres"></a>Connexion de l’application à Azure Postgres

Dans cette étape, vous allez connecter l’application Ruby on Rails à la base de données Postgres que vous avez créée dans Azure Database pour PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurer la connexion à la base de données

Dans le dépôt, ouvrez _config/database.yml_. En bas du fichier, remplacez les variables de production par le code suivant. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Enregistrez les modifications.

### <a name="test-the-application-locally"></a>Tester localement l’application

De retour dans le terminal local, définissez les variables d’environnement suivantes :

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

Exécutez les migrations de base de données Rails avec les valeurs de production que vous venez de configurer pour créer les tables dans votre base de données Postgres dans Azure Database pour PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Durant l’exécution dans l’environnement de production, l’application Rails nécessite des ressources précompilées. Générez les ressources nécessaires à l’aide de la commande suivante :

```bash
rake assets:precompile
```

L’environnement de production Rails utilise également des secrets pour gérer la sécurité. Générez une clé secrète.

```bash
rails secret
```

Enregistrez la clé secrète dans les variables respectives utilisées par l’environnement de production Rails. Pour plus de commodité, utilisez la même clé pour les deux variables.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Activez l’environnement de production Rails pour traiter les fichiers CSS et JavaScript.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Exécutez l’exemple d'application dans l'environnement de production.

```bash
rails server -e production
```

Accédez à `http://localhost:3000`. Si la page se charge sans erreur, l’application Ruby on Rails se connecte à la base de données Postgres dans Azure.

Ajoutez quelques tâches dans la page.

![Ruby on Rails se connecte correctement à Azure Database pour PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

### <a name="commit-your-changes"></a>Validation de vos modifications

Exécutez les commandes Git suivantes pour valider vos modifications :

```bash
git add .
git commit -m "database.yml updates"
```

Votre application est prête à être déployée.

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application Rails connectée à Postgres dans Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configuration des paramètres de la base de données

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell.

La commande Cloud Shell suivante configure les paramètres d’application `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` et `DB_PASSWORD`. Remplacez les espaces réservés _&lt;appname>_ et _&lt;postgres-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configuration des variables d’environnement Rails

Dans le terminal local, [générez un nouveau secret](configure-language-ruby.md#set-secret_key_base-manually) pour l’environnement de production Rails dans Azure.

```bash
rails secret
```

Configurez les variables requises par l’environnement de production Rails.

Dans la commande Cloud Shell suivante, remplacez les deux espaces réservés _&lt;output-of-rails-secret>_ par la nouvelle clé secrète que vous avez générée dans le terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indique le conteneur Ruby par défaut à utiliser pour précompiler les ressources à chaque déploiement Git. Pour plus d’informations, consultez [Précompiler les ressources](configure-language-ruby.md#precompile-assets) et [Traiter les ressources statiques](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Dans la fenêtre du terminal local, ajoutez un référentiel distant Azure à votre dépôt Git local.

```bash
git remote add azure <paste-copied-url-here>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer l’application Ruby on Rails. Le mot de passe que vous avez fourni précédemment dans le cadre de la création de l’utilisateur du déploiement vous est demandé.

```bash
git push azure master
```

Au cours du déploiement, Azure App Service communique sa progression avec Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à `http://<app-name>.azurewebsites.net` et ajoutez quelques tâches à la liste.

![Application Ruby on Rails s’exécutant dans Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Félicitations, vous exécutez une application Ruby on Rails orientée données dans Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Mettre à jour et redéployer un modèle localement

Dans cette étape, vous allez apporter une modification simple au modèle de données `task` et à l’application web, puis publier la mise à jour sur Azure.

Pour le scénario des tâches, vous modifiez l’application afin de pouvoir marquer une tâche comme terminée.

### <a name="add-a-column"></a>Ajout d’une colonne

Dans le terminal, accédez à la racine du référentiel Git.

Générez une nouvelle migration qui ajoute une colonne booléenne appelée `Done` à la table `Tasks` :

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Cette commande génère un nouveau fichier de migration dans le répertoire _db/migrate_.


Dans le terminal, exécutez les migrations de base de données Rails pour apporter la modification dans la base de données locale.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Mise à jour de la logique d’application

Ouvrez le fichier *app/controllers/tasks_controller.rb*. Ajoutez la ligne suivante à la fin du fichier :

```rb
params.require(:task).permit(:Description)
```

Modifiez cette ligne pour inclure le nouveau paramètre `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Mise à jour des vues

Ouvrez le fichier *app/views/tasks/_form.html.erb*, qui est le formulaire d’édition.

Recherchez la ligne `<%=f.error_span(:Description) %>` et insérez le code suivant juste en dessous :

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Ouvrez le fichier *app/views/tasks/show.html.erb*, qui est la page de vue à enregistrement unique. 

Recherchez la ligne `<dd><%= @task.Description %></dd>` et insérez le code suivant juste en dessous :

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Ouvrez le fichier *app/views/tasks/index.html.erb*, qui est la page d’index pour tous les enregistrements.

Recherchez la ligne `<th><%= model_class.human_attribute_name(:Description) %></th>` et insérez le code suivant juste en dessous :

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Dans le même fichier, recherchez la ligne `<td><%= task.Description %></td>` et insérez le code suivant juste en dessous :

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Test des modifications en local

Dans le terminal local, exécutez le serveur Rails.

```bash
rails server
```

Pour afficher le changement d’état de la tâche, accédez à `http://localhost:3000`, puis ajoutez ou modifiez des éléments.

![Case à cocher ajoutée à la tâche](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans le terminal, exécutez les migrations de base de données Rails pour l’environnement de production afin d’apporter la modification dans la base de données Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Une fois le `git push` terminé, accédez à l’application Azure et testez la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Si vous avez ajouté des tâches, celles-ci sont conservées dans la base de données. Les mises à jour appliquées au schéma de données n’affectent pas les données existantes.

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Gérer l’application Azure

Accédez au [portail Azure](https://portal.azure.com) pour gérer l’application que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/tutorial-php-mysql-app/access-portal.png)

La page Vue d’ensemble de votre application s’affiche. Ici, vous pouvez effectuer des tâches de gestion de base (arrêter, démarrer, redémarrer, parcourir et supprimer).

Le menu de gauche fournit des pages vous permettant de configurer votre application.

![Page App Service du Portail Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créez une base de données Postgres dans Azure
> * Connexion d’une application Ruby on Rails à Postgres
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application Ruby](configure-language-ruby.md)
