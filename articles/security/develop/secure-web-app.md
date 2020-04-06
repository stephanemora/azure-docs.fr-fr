---
title: Développer une application web sécurisée | Microsoft Docs
description: Cet exemple d’application simple met en œuvre les meilleures pratiques de sécurité qui améliorent votre application, de même que le niveau de sécurité de votre organisation lorsque vous développez sur Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 75890efebc42b74c56fb95ed1803152b516588b9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385212"
---
# <a name="develop-a-secure-web-app"></a>Développer une application web sécurisée

Cet exemple est une application Python simple qui affiche une page web contenant des liens vers des ressources de sécurité pour développer des applications sur Azure. L’application met en œuvre les meilleures pratiques en matière de sécurité pour vous aider à améliorer votre application et le niveau de sécurité de votre organisation lorsque vous développez des applications sur Azure.

Vous devez suivre les étapes décrites dans cet article de manière séquentielle pour vous assurer de la bonne configuration des composants de l’application. La base de données, Azure App Service, l'instance Azure Key Vault et l'instance Azure Application Gateway dépendent les uns des autres.

Les scripts de déploiement configurent l’infrastructure. Une fois les scripts de déploiement exécutés, vous devez procéder à une configuration manuelle dans le portail Azure pour lier les composants et services.

L’exemple d’application est destiné aux débutants qui développent des applications sur Azure et souhaitent implémenter des mesures de sécurité dans leurs applications.

Lors du développement et du déploiement de cette application, vous allez apprendre à :

- Créer une instance Azure Key Vault, stocker et récupérer des secrets depuis cette instance.
- Déployer Azure Database pour PostgreSQL, configurer des mots de passe sécurisés et en autoriser l’accès.
- Exécuter un conteneur Alpine Linux sur Azure Web Apps pour Linux et activer des identités managées pour les ressources Azure.
- Créer et configurer une instance Azure Application Gateway avec un pare-feu utilisant les [10 principaux ensembles de règles OWASP](https://coreruleset.org/).
- Activer le chiffrement des données en transit comme au repos à l’aide des services Azure.

Après avoir développé et déployé cette application, vous aurez configuré l’exemple d’application web suivant avec la configuration et les mesures de sécurité décrites.

![Exemple d’application web](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architecture

L’application est une application multiniveau type présentant trois niveaux. Le serveur frontal, le back end et la couche de base de données avec composants de surveillance et de gestion des secrets intégrés sont présentés ici :

![Architecture d’application](./media/secure-web-app/architecture.png)

L’architecture est constituée des composants suivants :

- [Azure Application Gateway](../../application-gateway/index.yml) : Fournit la passerelle et le pare-feu pour l'architecture de l'application.
- [Azure Web Apps sur Linux](../../app-service/containers/app-service-linux-intro.md). Fournit le runtime de conteneur pour exécuter l’application Python dans un environnement Linux.
- [Azure Key Vault](../../key-vault/index.yml). Stocke et chiffre les secrets de l’application et gère la création des stratégies d’accès qui s'y rapportent.
- [Azure Database pour PostgreSQL](https://azure.microsoft.com/services/postgresql/). Stocke en toute sécurité les données de l’application.
- [Azure Security Center](../../security-center/index.yml) et [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Fournit une surveillance et des alertes sur le fonctionnement de l'application.

## <a name="threat-model"></a>Modèle de menace

La modélisation des menaces est le processus d’identification des menaces de sécurité potentielles pour votre entreprise et votre application, puis de vérification qu'un plan d'atténuation approprié, est en place.

Cet exemple a utilisé [Microsoft Threat Modeling Tool](threat-modeling-tool.md) pour implémenter la modélisation des menaces pour l’exemple d’application sécurisée. En reconstituant les composants et les flux de données, vous pouvez identifier les problèmes et menaces dès le début du processus de développement. Cela permet d’économiser ensuite du temps et de l’argent.

Voici le modèle de menace de l’exemple d’application :

![Modèle de menace](./media/secure-web-app/threat-model.png)

Certains exemples de menaces et de vulnérabilités potentielles générés par l'outil de modélisation sont illustrés dans la capture d'écran suivante. Le modèle de menace offre une vue d’ensemble de la surface d’attaque exposée et invite les développeurs à réfléchir à la manière d’atténuer les problèmes.

![Sortie du modèle de menace](./media/secure-web-app/threat-model-output.png)

Par exemple, l’injection de code SQL dans la sortie du modèle de menace précédente est atténuée en nettoyant les entrées utilisateur et en utilisant des fonctions stockées dans Azure Database pour PostgreSQL. Cette atténuation empêche l’exécution arbitraire des requêtes lors des lectures et écritures de données.

Les développeurs améliorent la sécurité globale du système en atténuant chacune des menaces dans la sortie du modèle de menace.

## <a name="deployment"></a>Déploiement

Les options suivantes vous permettent d’exécuter Linux sur Azure App Service :

- Choisissez un conteneur dans la liste des conteneurs Microsoft prédéfinis sur Azure et créés avec des technologies prises en charge (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Utilisez un conteneur personnalisé. Sélectionnez vos propres registres de conteneurs en tant que source d’image et appuyez-vous sur les nombreuses technologies disponibles prenant en charge HTTP.

Dans cet exemple, vous allez exécuter le script de déploiement qui déploiera l'application web sur App Service et créera les ressources.

L’application peut utiliser les différents modèles de déploiement indiqués ci-dessous :

![Diagramme de flux de données de déploiement](./media/secure-web-app/deployment.png)

Il existe de nombreuses manières de déployer des applications sur Azure, notamment :

- Modèles Microsoft Azure Resource Manager
- PowerShell
- Azure CLI
- Portail Azure
- Azure DevOps

Cette application a utilisé :

- [Docker](https://docs.docker.com/) pour créer et générer les images de conteneur.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour le déploiement.
- [Docker Hub](https://hub.docker.com/) en tant que registre de conteneurs.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

### <a name="network"></a>Réseau

L’exemple d’application utilise le chiffrement SSL de bout en bout pour le flux de données en transit vers et depuis le réseau. La passerelle est configurée avec un certificat auto-signé.
> [!IMPORTANT]
> Un certificat auto-signé est utilisé dans cette démonstration. Dans un environnement de production, vous devez obtenir les certificats auprès d’une autorité de certification vérifiée(CA).

Le pare-feu d’application inspecte également le trafic entrant et avertit les administrateurs en cas de détection de trafic malveillant dans le trafic réseau.
Application Gateway atténue les possibles menaces par déni de service distribué et injection de code SQL dans le modèle de menace.

### <a name="identity"></a>Identité

Pour se connecter au portail, l’exemple d’application utilise Multi-Factor Authentication pour les administrateurs Azure Active Directory (Azure AD) qui ont accès aux ressources.
L’exemple d’application utilise des identités managées pour être autorisée à lire et à récupérer les secrets à partir d'Azure Key Vault, ce qui lui évite de devoir recourir à des informations d’identification et jetons codés en dur. Azure AD crée automatiquement les principaux de service dont l’application a besoin pour lire et modifier les secrets lorsque des identités managées sont utilisées.

Grâce aux identités managées pour ressources Azure et MFA, les pirates ont plus de mal à accéder aux privilèges et à élever leurs privilèges dans le système. Cette menace a été signalée dans le modèle de menace.
L’application utilise OAuth, qui permet aux utilisateurs inscrits dans l’application OAuth de se connecter à l’application.

### <a name="storage"></a>Stockage

Les données de la base de données PostgreSQL sont automatiquement chiffrées au repos par Azure Database pour PostgreSQL. La base de données autorise les adresses IP App Service. Ainsi, seule l'application web App Service déployée peut accéder aux ressources de base de données avec les informations d’identification appropriées.

### <a name="logging-and-auditing"></a>Journalisation et audit

L’application implémente la journalisation à l’aide d'Application Insights pour suivre les métriques, les journaux et les exceptions qui se produisent. Cette journalisation fournit suffisamment de métadonnées d’application pour informer les développeurs et les membres de l’équipe des opérations de l’état de l’application. Elle fournit également suffisamment de données pour revenir en arrière en cas d’incidents de sécurité.

## <a name="cost-considerations"></a>Considérations relatives au coût

Si vous n'avez pas encore de compte Azure, vous pouvez en créer un gratuitement. Pour commencer, accédez à la [page de compte gratuit](https://azure.microsoft.com/free/), voyez ce que vous pouvez faire avec un compte Azure gratuit et découvrez les produits gratuits pendant 12 mois.

Pour déployer les ressources dans l’exemple d’application avec les fonctionnalités de sécurité, vous devez payer certaines fonctionnalités Premium. À mesure que l'application évolue et que les niveaux et essais gratuits offerts par Azure doivent être mis à niveau pour répondre aux besoins de l’application, les coûts sont susceptibles d'augmenter. Utiliser la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts.

## <a name="deploy-the-solution"></a>Déployer la solution

### <a name="prerequisites"></a>Prérequis

Pour permettre le bon fonctionnement de l'application, vous devez installer les outils suivants :

- Un éditeur de code pour modifier et afficher le code de l’application. [Visual Studio Code](https://code.visualstudio.com/) est une option open source.
- [Azure CLI](/cli/azure/install-azure-cli) sur votre ordinateur de développement.
- [Git](https://git-scm.com/) sur votre système. Git est utilisé pour cloner le code source localement.
- [jq](https://stedolan.github.io/jq/) est un outil UNIX pour interroger JSON de manière conviviale.

Vous devez disposer d’un abonnement Azure pour déployer les ressources de l’exemple d’application. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/) pour tester l'exemple d'application.

Après avoir installé ces outils, vous êtes prêt à déployer l’application sur Azure.

### <a name="environment-setup"></a>Configuration de l’environnement

Exécutez les scripts de déploiement pour configurer l’environnement et l’abonnement :

1. Pour cloner le référentiel de code source, utilisez la commande git suivante :

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Pour vous déplacer dans le répertoire, utilisez la commande suivante :

   ```shell
   cd tutorial-project/scripts
   ```

3. Le dossier des scripts contient des fichiers spécifiques à la plateforme que vous utilisez (Windows ou Linux). L’interface de ligne de commande Azure étant déjà installée, connectez-vous au compte Azure à l’invite de commandes en exécutant la commande Azure CLI suivante :

   ```azurecli-interactive
   az login
   ```

Le navigateur s’ouvre. Connectez-vous à l'aide de vos informations d’identification. Une fois connecté, vous pouvez commencer à déployer les ressources à partir de l’invite de commandes.

Les scripts de déploiement `deploy-powershell.ps1` et `deploy-bash.sh` contiennent du code qui déploie l’application dans son intégralité.
Pour déployer la solution :

1. Si vous utilisez PowerShell, exécutez le fichier `deploy-powershell.ps1` en entrant `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` pour remplacer la région et le nom du groupe de ressources par les régions Azure et le nom du groupe de ressources qui conviennent
2. Si vous utilisez Linux, exécutez le fichier `deploy-bash.sh` en entrant `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`. Vous devrez peut-être rendre le fichier exécutable en entrant `chmod +x deploy-bash.sh`

Les exemples suivants montrent les extraits de code des composants clés. Vous pouvez déployer les exemples individuellement ou avec le reste des composants en exécutant les fichiers de déploiement.

### <a name="implementation-guidance"></a>Conseils d’implémentation

Le script de déploiement peut être divisé en quatre phases. Chaque phase déploie et configure une ressource Azure présente dans le [diagramme d'architecture](#architecture).

Ces quatre phases sont les suivantes :

- Déployer Azure Key Vault.
- Déployer Azure Database pour PostgreSQL.
- Déployer Azure Web Apps sur Linux.
- Déployer Application Gateway avec le pare-feu d’applications web.

Chaque phase s’appuie sur la précédente en utilisant la configuration des ressources précédemment déployées.

Pour mener à bien la procédure d’implémentation, vérifiez que vous avez installé les outils répertoriés sous [Conditions préalables](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Déployer Azure Key Vault

Dans cette section, vous allez créer et déployer une instance Azure Key Vault utilisée pour stocker les secrets et certificats.

Au terme du déploiement, vous disposerez d’une instance Azure Key Vault déployée sur Azure.

Pour déployer Azure Key Vault à l’aide d'Azure CLI :

1. Déclarez les variables pour Azure Key Vault.
2. Inscrivez le fournisseur Azure Key Vault.
3. Créez le groupe de ressources de l'instance.
4. Créez l’instance Azure Key Vault dans le groupe de ressources créé à l’étape 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

Il est recommandé d’utiliser des identités managées pour les ressources Azure des applications utilisant Key Vault afin d'accéder aux ressources. Votre niveau de sécurité est renforcé lorsque les clés d’accès Key Vault ne sont pas stockées dans le code ou la configuration.

#### <a name="deploy-azure-database-for-postgresql"></a>Déployer Azure Database pour PostgreSQL

Azure Database pour PostgreSQL fonctionne de la manière suivante ; commencez par créer le serveur de base de données, puis la base de données dans laquelle stocker le schéma et les données.

Une fois le déploiement terminé, vous disposez d’un serveur et d’une base de données PostgreSQL s’exécutant sur Azure.

Pour déployer Azure Database pour PostgreSQL à l’aide d’Azure CLI :

1. Ouvrez un terminal avec Azure CLI et la configuration de votre abonnement Azure.
2. Générez une combinaison sécurisée de nom d’utilisateur et mot de passe utilisée pour accéder à la base de données. (Ils doivent être stockés dans Azure Key Vault pour les applications qui les utilisent.)
3. Créez l'instance de serveur PostgreSQL.
4. Créez une base de données sur l’instance de serveur que vous avez créée à l’étape 3.
5. Exécutez les scripts PostgreSQL sur l’instance PostgreSQL.

Le code ci-dessous s’appuie sur les secrets PGUSERNAME et PGPASSWORD stockés dans Azure Key Vault lors de l'étape de déploiement de KeyVault ci-dessus.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Après avoir déployé la base de données, vous devez stocker ses informations d’identification et sa chaîne de connexion dans Azure Key Vault.
Dans le dossier des scripts, vous trouverez un `functions.sql` contenant le code PL/pgSQL à utiliser pour créer les fonctions stockées. L’exécution de ce fichier permet de paramétrer les entrées pour limiter l’injection de code SQL.

PostgreSQL est fourni avec un outil appelé `psql` utilisé à des fins de connexion à la base de données. Pour exécuter `functions.sql`, vous devez vous connecter à l’instance Azure Database pour PostgreSQL à partir de votre ordinateur local et l’exécuter à partir de là. L’installation de l’outil psql est incluse dans l’installation par défaut pour PostgreSQL sur chaque système d’exploitation.
Pour plus d’informations, consultez la [documentation psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell comprend également l'outil `psql`. Vous pouvez utiliser Cloud Shell directement à partir du portail Azure en sélectionnant l’icône Cloud Shell.

Pour activer l’accès à distance à l’instance PostgreSQL, vous devez autoriser l’adresse IP dans PostgreSQL.
Pour ce faire, accédez à l'onglet **Sécurité de la connexion**, sélectionnez **Ajouter une adresse IP cliente** et enregistrez les nouveaux paramètres.

![Autoriser l’adresse IP cliente](./media/secure-web-app/add-client-ip-postgres.png)

Si vous utilisez Cloud Shell plutôt que l'outil psql local, sélectionnez **Autoriser l’accès aux services Azure** et définissez sa valeur sur **ACTIVÉ** pour autoriser l'accès Cloud Shell.

Connectez-vous ensuite à l’instance en exécutant la commande psql ci-dessous avec les paramètres de chaîne de connexion de l’onglet **Chaînes de connexion** de l’instance PostgreSQL sur le portail Azure.
Remplacez les accolades vides par les paramètres du panneau Chaîne de connexion de la base de données et le mot de passe par le mot de passe Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Exécutez le script PL/pgSQL suivant après avoir vérifié que vous êtes bien connecté à la base de données. Le script crée les fonctions stockées utilisées pour insérer des données dans la base de données.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Pour plus d’informations sur la configuration de la vérification SSL et de l’autorité de certification pour PostgreSQL, consultez [Configurer la connectivité SSL dans Azure Database pour PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Un certificat racine est inclus dans le conteneur. Pour obtenir le certificat, procédez comme suit :

1. Téléchargez le fichier de certificat émis par l’[autorité de certification](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Téléchargez et installez OpenSSL sur l'ordinateur](/azure/postgresql/concepts-ssl-connection-security).
3. Décodez le fichier de certificat :

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Pour plus d’informations sur la configuration de la sécurité SSL pour PostgreSQL, consultez [Configurer la sécurité de la connexion SSL](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Déployer Azure Web Apps sur Linux

Vous pouvez facilement créer des services Linux sur Azure App Service car Azure fournit plusieurs conteneurs et images prédéfinis destinés aux langages courants tels que Python, Ruby, C# et Java. Azure prend également en charge les conteneurs personnalisés, qui permettent l'exécution de la plupart des langages de programmation sur la plateforme Azure App Service.

L’application déployée est une application Python simple qui s’exécute sur la dernière distribution Ubuntu Linux. Elle se connecte aux instances Azure Key Vault et PostgreSQL créées dans les sections précédentes, respectivement pour la gestion des informations d’identification et le stockage des données.

Le fichier Docker suivant est fourni dans le dossier racine de l’application :

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Le fichier Docker ci-dessus est utilisé pour générer le conteneur hébergé dans Azure Container Registry à l'adresse `mcr.microsoft.com/samples/basic-linux-app`.

Le code ci-dessous :

1. Déclare les variables et les noms pour l’instance App Service.
2. Crée le groupe de ressources pour le plan App Service.
3. Approvisionne Azure Web Apps sur une instance de conteneurs Linux.
4. Active la journalisation pour le conteneur d’application web.
5. Définit plusieurs configurations d'application dans les paramètres d’application du conteneur.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Ce script crée une identité attribuée pour l'instance App Service et celle-ci peut être utilisée avec MSI pour interagir avec Azure Key Vault, sans coder en dur les secrets dans le code ou la configuration.

Dans le portail, accédez à l’instance Azure Key Vault pour autoriser l’identité attribuée dans l'onglet Stratégie d’accès. Sélectionnez **Ajouter une nouvelle stratégie d’accès**. Sous **Sélectionner le principal**, recherchez le nom de l’application (similaire au nom de l'instance App Service créée).
Un principal de service joint à l’application doit être visible. Sélectionnez-le et enregistrez la page de stratégie d’accès, comme illustré dans la capture d’écran suivante.

L’application devant uniquement récupérer des clés, sélectionnez l'autorisation **Get** dans les options de secrets pour autoriser l’accès tout en limitant les privilèges accordés.

![Stratégie d’accès à Key Vault](./media/secure-web-app/kv-access-policy.png)

*Créer une stratégie d’accès à Key Vault*

Enregistrez la stratégie d’accès, puis enregistrez la nouvelle modification dans l'onglet **Stratégies d'accès** pour mettre à jour les stratégies.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Déployer Application Gateway avec le pare-feu d’applications web

Dans les applications web, il est déconseillé d'exposer publiquement les services sur Internet.
L’équilibrage de charge et les règles de pare-feu offrent plus de sécurité et de contrôle sur le trafic entrant et en facilitent la gestion.

Pour déployer une instance Application Gateway :

1. Créez un groupe de ressources où héberger Application Gateway.
2. Approvisionnez un réseau virtuel à joindre à la passerelle.
3. Créez un sous-réseau pour la passerelle dans le réseau virtuel.
4. Approvisionnez une adresse IP publique.
5. Approvisionnez la passerelle Application Gateway.
6. Activez le pare-feu d’applications web sur la passerelle.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Le script précédent :

1. Crée un nouveau certificat auto-signé sur Azure.
2. Télécharge le certificat auto-signé sous forme de fichier codé en Base64.
3. Génère un mot de passe pour le certificat auto-signé.
4. Exporte le certificat sous forme de fichier PFX signé avec le mot de passe.
5. Stocke le mot de passe du certificat dans Azure Key Vault.

Cette section déploie la passerelle Application Gateway :

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Une fois le déploiement terminé, vous disposez d’une passerelle Application Gateway avec pare-feu d’applications web activé.

L’instance de passerelle expose le port 443 pour HTTPS. Cette configuration permet de s'assurer que l'application est uniquement accessible sur le port 443 via HTTPS.

Bloquer les ports non utilisés et limiter la surface d'attaque relève d'une bonne pratique en matière de sécurité.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Ajouter des groupes de sécurité réseau à l’instance App Service

Les instances App Service peuvent être intégrées aux réseaux virtuels. Cette intégration permet de les configurer avec les stratégies de groupe de sécurité réseau qui gèrent le trafic entrant et sortant de l’application.

1. Pour activer cette fonctionnalité, dans le panneau de l'instance du service Azure App, sous **Paramètres**, sélectionnez **Mise en réseau**. Dans le volet droit, sous **Intégration au réseau virtuel**, sélectionnez **Cliquez ici pour configurer**.

   ![Intégration du nouveau réseau virtuel](./media/secure-web-app/app-vnet-menu.png)

    *Intégration d’un nouveau réseau virtuel pour App Service*

1. Sur la page suivante, sélectionnez **Ajouter un réseau virtuel (préversion)** .

1. Dans le menu suivant, sélectionnez le réseau virtuel créé lors du déploiement er commençant par `hello-vnet`. Vous pouvez créer un sous-réseau ou sélectionner un sous-réseau existant.
   Dans ce cas, créez un sous-réseau. Définissez la **Plage d'adresses** sur **10.0.3.0/24** et nommez le sous-réseau **app-subnet**.

   ![Configuration du réseau virtuel App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuration du réseau virtuel pour App Service*

Une fois l'intégration au réseau virtuel activée, vous pouvez ajouter des groupes de sécurité réseau à l'application.

1. Dans la zone de recherche, recherchez les **groupe de sécurité réseau**. Sélectionnez **Groupes de sécurité réseau** dans les résultats.

    ![Rechercher les groupes de sécurité réseau](./media/secure-web-app/nsg-search-menu.png)

    *Rechercher les groupes de sécurité réseau*

2. Dans le menu suivant, sélectionnez **Ajouter**. Entrez le **Nom** du groupe de sécurité réseau et le **Groupe de ressources** dans lequel il doit se trouver. Ce groupe de sécurité réseau sera appliqué au sous-réseau de la passerelle d’application.

    ![Créer un groupe de sécurité réseau](./media/secure-web-app/nsg-create-new.png)

    *Créer un groupe de sécurité réseau*

3. Une fois le groupe de sécurité réseau créé, sélectionnez-le. Dans ce panneau, sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**. Configurez ces paramètres pour autoriser les connexions à la passerelle Application Gateway via le port 443.

   ![Configurer le groupe de sécurité réseau](./media/secure-web-app/nsg-gateway-config.png)

   *Configurer le groupe de sécurité réseau*

4. Dans les règles de trafic sortant du groupe de sécurité réseau de la passerelle, ajoutez une règle autorisant les connexions sortantes vers l’instance App Service en créant une règle qui cible la balise de service `AppService` :

   ![Ajouter les règles de trafic sortant pour le groupe de sécurité réseau](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Ajouter les règles de trafic sortant pour le groupe de sécurité réseau*

    Ajoutez une autre règle de trafic sortant pour autoriser la passerelle à envoyer des règles de trafic sortant à un réseau virtuel.

   ![Ajouter une autre règle de trafic sortant](./media/secure-web-app/nsg-outbound-vnet.png)

    *Ajouter une autre règle de trafic sortant*

5. Dans le panneau des sous-réseaux du groupe de sécurité réseau, sélectionnez **Associer**, sélectionnez le réseau virtuel créé lors du déploiement, puis le sous-réseau de passerelle nommé **gw-subnet**. Le groupe de sécurité réseau est appliqué au sous-réseau.

6. Créez un autre groupe de sécurité réseau comme à l’étape précédente, mais pour l’instance App Service cette fois. Donnez-lui un nom. Ajoutez la règle de trafic entrant pour le port 443 comme vous l’avez fait pour le groupe de sécurité réseau de la passerelle Application Gateway.

   Si une instance App Service est déployée sur une instance de App Service Environment (ce qui n’est pas le cas de cette application), vous pouvez ajouter des règles de trafic entrant pour autoriser les sondes Azure Service Health en ouvrant les ports 454-455 sur les groupes de sécurité entrants de votre groupe de sécurité réseau App Service. Voici la configuration :

   ![Ajouter des règles pour les sondes Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Ajouter des règles pour les sondes Azure Service Health (App Service Environment uniquement)*

7. Dans les règles de sécurité de trafic sortant, créez une règle de sécurité de trafic sortant qui autorise l’instance App Service à communiquer avec la base de données PostgreSQL. Configurez-la comme suit :

   ![Règle autorisant les connexions sortantes PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Ajouter une règle pour autoriser les connexions sortantes PostgreSQL*

Pour limiter la surface d’attaque, modifiez les paramètres réseau App Service afin d'autoriser uniquement la passerelle Application Gateway à accéder à l’application.
Pour ce faire, accédez à l’onglet réseau App Service, sélectionnez l’onglet **Restrictions d’adresse IP**, puis créez une règle qui autorise uniquement l’adresse IP de la passerelle d’application à accéder directement au service.

Vous pouvez récupérer l’adresse IP de la passerelle à partir de sa page Vue d'ensemble. Dans l'onglet **CIDR d'adresse IP**, entrez l’adresse IP au format suivant : `<GATEWAY_IP_ADDRESS>/32`.

![Autoriser uniquement la passerelle](./media/secure-web-app/app-allow-gw-only.png)

*Autoriser uniquement l’adresse IP de la passerelle à accéder à App Service*

#### <a name="implement-azure-active-directory-oauth"></a>Implémenter OAuth Azure Active Directory

Les documents Azure distribués sur la page de l’exemple d’application web correspondent aux ressources de l'application susceptibles de nécessiter une protection. Vous pouvez utiliser Azure Active Directory (Azure AD) pour implémenter l’authentification pour les applications web, mobiles et de bureau à l’aide de différents flux d’authentification.
L’application utilise **Connexion avec Microsoft**, qui lui permet de lire les profils des utilisateurs ajoutés à la liste de l’utilisateur d’un seul locataire Azure AD.

Dans le portail Azure, configurez l’application pour utiliser les informations d’identification requises :

1. Sélectionnez **Azure Active Directory** ou recherchez-le à l’aide de la zone de recherche.

2. Sélectionnez **Nouvelle inscription** :

   ![Créer une inscription](./media/secure-web-app/ad-auth-create.png)

   *Créer une inscription d’application Azure AD*

3. Sur la page suivante, entrez le nom de l'application. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
    Sous **URI de redirection**, entrez le domaine de base sur lequel l’application s’exécutera, de même qu'avec le point de terminaison de jeton. Par exemple : *GATEWAY_HASH*.cloudapp.net/token.

   ![Configurer l’inscription d'application Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Configurer l’inscription d'application Azure AD*

4. L'écran qui s'affiche vous présente l'application inscrite ainsi que les informations qui s'y rapportent. Vous devez ajouter ces informations dans l’instance Azure Key Vault.
   1. Copiez l’ID d’application (client) et enregistrez-le dans Key Vault comme suit : `CLIENTID`.
   2. Copiez l’URI de redirection que vous avez entrée à l’étape précédente et enregistrez-la comme suit : `REDIRECTURI`.
   3. Copiez le nom du répertoire par défaut Azure AD, qui se présente au format *name*.microsoftonline.com, puis enregistrez-le dans Key Vault comme suit : `TENANT`.
   4. Accédez à l'onglet **Certificats et secrets** de l'application Azure AD créée précédemment et sélectionnez **Nouveau secret client**, comme illustré dans la capture d’écran suivante. Définissez une date d’expiration, puis copiez la valeur générée et enregistrez-la dans Key Vault comme suit : `CLIENTSECRET`.

      ![Secret d’autorisation Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Secret d’autorisation Azure AD*

   5. Générez une clé secrète aléatoire sécurisée à l’aide de la ligne de commande/outil en ligne de votre choix. Enregistrez-la dans Key Vault comme suit : `FLASKSECRETKEY`. L’infrastructure d’application utilise cette clé pour créer des sessions.
        Pour savoir comment générer une clé secrète, consultez [Sessions Flask](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Une fois la connexion configurée, vous devez ajouter des utilisateurs au lien Azure AD pour leur permettre de se connecter à la ressource. Pour ce faire, accédez à l'onglet **Utilisateurs** dans Azure AD, sélectionnez **Tous les utilisateurs**, puis **Nouvel utilisateur** ou **Nouvel utilisateur invité**. À des fins de test, vous pouvez ajouter un utilisateur invité et l'inviter dans le répertoire. Vous pouvez également ajouter un nouvel utilisateur si le domaine exécuté par l'application a été validé. Dans cet exemple, seuls les utilisateurs inscrits dans le locataire Azure AD peuvent être inscrits à des fins d'accès. Pour plus d’informations sur l’accès à la connexion multi-locataire, consultez la documentation.

   ![Ajouter des utilisateurs au domaine par défaut](./media/secure-web-app/ad-auth-add-user.png)

   *Ajouter des utilisateurs au domaine Azure Active Directory par défaut*

Après avoir ajouté la configuration et les secrets Azure AD à Key Vault, les utilisateurs peuvent être authentifiés dans l’application à l’aide de l’authentification OAuth Azure.
Dans le code d’application, cela est géré par la Bibliothèque d'authentification Azure Active Directory (ADAL).

Lorsque les secrets se trouvent dans Key Vault et que l’application a accès aux secrets et à la base de données, le service d’application est accessible via l'URL d'application de la passerelle (https://GATEWAY_HASH.cloudapp.net), que vous pouvez obtenir dans le panneau correspondant).

Lorsque vous vous connectez à Azure AD, si un message d'erreur indique « L’utilisateur n’est pas inscrit dans le répertoire auquel vous essayez d'accéder », vous devez ajouter cet utilisateur. Pour ce faire, accédez à l'onglet **Utilisateurs** d'Azure AD et ajoutez l’utilisateur manuellement en entrant ses détails ou en l'invitant à entrer son adresse e-mail en tant qu'utilisateur invité Azure AD dans le panneau **Inviter**.

#### <a name="deploy-application-insights"></a>Déployer Application Insights
Maintenant que l’application est déployée et fonctionnelle, vous devez gérer les erreurs qui s'y produisent, ainsi que la journalisation et la collecte des données de suivi.
La journalisation et la collecte de données de suivi vous offrent une vue d'ensemble des événements d’audit qui se produisent dans l’application.

Application Insights collecte les journaux et ceux-ci peuvent être générés par les utilisateurs ou le système.

Pour créer une instance Application Insights :

1. Recherchez **Application Insights** à l’aide de la zone de recherche du portail Azure.
2. Sélectionnez **Application Insights**. Fournissez les informations indiquées ici pour créer une instance.

   ![Créer une instance Application Insights](./media/secure-web-app/app-insights-data.png)

Au terme du déploiement, vous disposez d’une instance Application Insights.

Une fois l’instance Application Insights créée, vous devez informer l'application de l'existence d'une d'instrumentation qui lui permet d’envoyer des journaux dans le cloud. Pour ce faire, récupérez la clé Application Insights et utilisez-la dans les bibliothèques d’applications fournies par Azure pour Application Insights. Une bonne pratique consiste à stocker les clés et les secrets dans Azure Key Vault pour assurer leur sécurité.

Pour l’exemple d’application de base, après avoir créé l’instance Application Insights, vous devez informer l’application de l'existence d'une clé d’instrumentation qui lui permet d’envoyer des journaux dans le cloud.
Dans Key Vault, définissez un secret `APPINSIGHTSKEY` et définissez sa valeur en tant que clé d’instrumentation. Ainsi, l'application sera autorisée à envoyer des journaux et des métriques à Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implémenter Multi-Factor Authentication pour Azure Active Directory

Les administrateurs doivent s’assurer que les comptes d’abonnement du portail sont protégés. L'abonnement gérant les ressources que vous créez, il est vulnérable aux attaques. Pour protéger l’abonnement, activez Multi-Factor Authentication dans l'onglet **Azure Active Directory** correspondant.

Azure AD fonctionne selon les stratégies appliquées à un utilisateur ou à des groupes d’utilisateurs répondant à certains critères.
Azure crée une stratégie par défaut qui spécifie que les administrateurs doivent utiliser une authentification à deux facteurs pour se connecter au portail.
Après avoir activé cette stratégie, vous pouvez être invité à vous déconnecter puis à vous reconnecter au portail Azure.

Pour activer l’authentification multifacteur pour les connexions administrateur :

1. Dans le portail Azure, accédez à l'onglet **Azure Active Directory**.
2. Sous la catégorie Sécurité, sélectionnez Accès conditionnel. L'écran suivant s'affiche :

   ![Accès conditionnel - Stratégies](./media/secure-web-app/ad-mfa-conditional-add.png)

Si n'êtes pas en mesure de créer une stratégie :

1. Accédez à l'onglet **MFA**.
2. Sélectionnez le lien **Essai gratuit** Azure AD Premium pour vous abonner à l'essai gratuit.

   ![Essai gratuit Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Revenez à l'écran Accès conditionnel.

1. Sélectionnez l’onglet Nouvelle stratégie.
2. Entrez le nom de la stratégie.
3. Sélectionnez les utilisateurs ou les groupes pour lesquels vous souhaitez activer l’authentification multifacteur.
4. Sous **Contrôle d'accès**, sélectionnez l'onglet **Octroi**, puis **Exiger une authentification multifacteur** (et d'autres paramètres, si vous le souhaitez).

   ![Exiger une authentification multifacteur](./media/secure-web-app/ad-mfa-conditional-add.png)

Pour activer la stratégie, cochez la case située en haut de l'écran ou utilisez l'onglet **Accès conditionnel**. Une fois la stratégie activée, les utilisateurs doivent utiliser l’authentification multifacteur pour se connecter au portail.

Une stratégie de base requiert l’authentification multifacteur pour tous les administrateurs Azure. Vous pouvez l’activer immédiatement dans le portail. L’activation de cette stratégie peut invalider la session active et vous obliger à vous reconnecter.

Si la stratégie de ligne de base n’est pas activée :

1. Sélectionnez **Demander l’authentification multifacteur pour les administrateurs**.
2. Sélectionnez **Utiliser la stratégie immédiatement**.

   ![Sélectionner Utiliser la stratégie immédiatement](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Utiliser Azure Sentinel pour surveiller les applications et les ressources

À mesure qu'une application se développe, il devient difficile de regrouper tous les signaux et métriques de sécurité reçus à partir des ressources et de les utiliser dans le cadre d'une approche orientée action.

Azure Sentinel est conçu pour collecter les données, détecter les types de menaces possibles et fournir une visibilité sur les incidents de sécurité.
En attendant une intervention manuelle, Azure Sentinel peut utiliser des playbooks pré-écrits pour déclencher des alertes et processus de gestion des incidents.

L’exemple d’application se compose de plusieurs ressources qu'Azure Sentinel peut surveiller.
Pour configurer Azure Sentinel, vous devez d’abord créer un espace de travail Log Analytics afin d'y stocker toutes les données issues des différentes ressources.

Pour créer cet espace de travail :

1. Dans la zone de recherche du portail Azure, recherchez **Log Analytics**. Sélectionnez **Espaces de travail Log Analytics**.

   ![Rechercher des espaces de travail Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Rechercher des espaces de travail Log Analytics*

2. Sur la page suivante, sélectionnez **Ajouter**, puis indiquez un nom, un groupe de ressources et l'emplacement de l'espace de travail.
   ![Créer un espace de travail Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Créer un espace de travail Log Analytics*

3. Utilisez la zone de recherche pour rechercher **Azure Sentinel**.

   ![Rechercher Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Rechercher Azure Sentinel*

4. Sélectionnez **Ajouter**, puis l'espace de travail Log Analytics que vous avez sélectionné précédemment.

   ![Ajouter un espace Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Ajouter un espace de travail Log Analytics*

5. Dans la page **Azure Sentinel - Connecteurs de données**, sous **Configuration**, sélectionnez **Connecteurs de données**. Vous pouvez lier le tableau des services Azure qui s'affiche à l’instance de stockage Log Analytics à des fins d'analyse dans Azure Sentinel.

   ![Connecteurs de données Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Ajouter un connecteur de données à Azure Sentinel*

   Par exemple, pour connecter la passerelle Application Gateway, procédez comme suit :

   1. Ouvrez le panneau de l'instance Azure Application Gateway.
   2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
   3. Sélectionnez **Ajouter le paramètre de diagnostic**.

      ![Ajouter des diagnostics Application Gateway](./media/secure-web-app/sentinel-gateway-connector.png)

      *Ajouter des diagnostics Application Gateway*

   4. Dans la page **Paramètres de diagnostic**, sélectionnez l’espace de travail log Analytics que vous avez créé, puis toutes les métriques que vous souhaitez collecter et envoyer à Azure Sentinel. Sélectionnez **Enregistrer**.

        ![Paramètres du connecteur Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Paramètres du connecteur Azure Sentinel*

  Les métriques de la ressource se trouvent dans Azure Sentinel, où vous pouvez les interroger et les examiner.

   Ajoutez les mêmes métriques aux paramètres de diagnostic pour Azure Key Vault, l’adresse IP publique, Azure Database pour PostgreSQL et tous les services prenant en charge les journaux de diagnostic à votre compte.

Une fois les métriques configurées, Azure Sentinel dispose de données à analyser.

## <a name="evaluate-and-verify"></a>Évaluer et vérifier

Après avoir développé et déployé l’architecture, vous devez vous assurer que le code et les services déployés répondent aux normes de sécurité. Voici quelques étapes à suivre pour vérifier le logiciel :

- Analyse du code statique
- Analyse des vulnérabilités
- Rechercher et corriger les vulnérabilités des dépendances d’application

Il s’agit des blocs de construction de base relevant des meilleures pratiques en matière développement sécurisé.

### <a name="static-code-analysis"></a>Analyse du code statique

Pour l’exemple d’application, une vérification avec les outils d'analyse statique consiste à détecter les vulnérabilités du code d'application à l’aide de techniques telles que la vérification de teinte et l'analyse de flux de données. Les outils d’analyse statique Python vous rassurent sur le fait que votre application est sécurisée.

**Linting**

PyFlakes, une bibliothèque de linting Python, vous permet de supprimer le code mort et les fonctions non utilisées des applications, comme illustré ici :

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting propose des conseils et modifications pour vous permettre d'obtenir un code plus propre et moins sujet aux erreurs lors de son exécution.

**PyLint**

PyLint s'est avéré particulièrement utile pour ce projet. Il procède à différentes vérifications de code, recherche les erreurs et formule des conseils de refactorisation pour garantir la sécurité du code qui s'exécute sur le serveur. En utilisant PyLint pour mettre à jour votre code, vous pouvez éliminer les bogues et améliorer l’évaluation de PyLint, comme le montrent les images suivantes.

![Avant PyLint](./media/secure-web-app/before-pylint.png)

*Avant PyLint*

Après avoir corrigé certaines erreurs de code détectées par les outils de linting, vous avez davantage l'assurance que le code n'est pas sujet aux erreurs. La correction des erreurs réduit considérablement les risques de sécurité susceptibles de se manifester lorsque le code est déployé dans des environnements de production.

![Après Pylint](./media/secure-web-app/after-pylint.png)

*Après PyLint*

### <a name="vulnerability-scanning"></a>Analyse des vulnérabilités

L'outil [ZAP d'OWASP](https://www.zaproxy.org/) est un analyseur de vulnérabilité destiné aux applications web open source que vous pouvez utiliser pour rechercher des vulnérabilités dans l'exemple d'application. L’exécution de cet outil sur l’exemple d’application révèle de possibles erreurs et vecteurs d’attaque.

![Outil ZAP](./media/secure-web-app/zap-tool.png)

*Outil ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Rechercher et corriger les vulnérabilités dans les dépendances d’application

Pour rechercher et corriger les dépendances d'application, vous pouvez utiliser l'outil [Vérification de dépendance OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

Safety est une application similaire qui vérifie les dépendances. Elle est disponible sur [GitHub](https://github.com/pyupio/safety). Safety analyse les vulnérabilités détectées par rapport à des bases de données de vulnérabilités bien connues.

![Safety](./media/secure-web-app/pysafety.png)

*Safety*

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants peuvent vous aider à concevoir, développer et déployer des applications sécurisées.

- [Conception](secure-design.md)
- [Développement](secure-develop.md)
- [Déployer](secure-deploy.md)
