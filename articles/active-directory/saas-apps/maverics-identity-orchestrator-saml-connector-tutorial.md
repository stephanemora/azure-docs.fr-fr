---
title: 'Tutoriel : Intégrer l’authentification unique Azure Active Directory à Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585092"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Intégrer l’authentification unique Azure AD avec Maverics Identity Orchestrator SAML Connector

Maverics Identity Orchestrator de Strata offre un moyen simple d’intégrer des applications locales à Azure Active Directory (Azure AD) pour l’authentification et le contrôle d’accès. Maverics Orchestrator est capable de moderniser l’authentification et l’autorisation pour les applications qui s’appuient actuellement sur des en-têtes, des cookies et d’autres méthodes d’authentification propriétaires. Les instances Maverics Orchestrator peuvent être déployées localement ou dans le cloud. 

Ce tutoriel sur l’accès hybride montre comment migrer une application web locale qui est actuellement protégée par un produit de gestion de l’accès web hérité afin d’utiliser Azure AD pour l’authentification et le contrôle d’accès. Les étapes de base sont les suivantes :

1. Configurer Maverics Orchestrator
1. Traiter une application par proxy
1. Inscrire une application d’entreprise dans Azure AD
1. S’authentifier par le biais d’Azure et autoriser l’accès à l’application
1. Ajouter des en-têtes pour un accès fluide aux applications
1. Travailler avec plusieurs applications

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Maverics Identity Orchestrator SAML Connector pour lequel l’authentification unique est activée. Pour obtenir le logiciel Maverics, contactez l’[équipe commerciale Strata](mailto:sales@strata.io).
* Au moins une application qui utilise l’authentification basée sur l’en-tête. Les exemples fonctionnent sur une application appelée Sonar, qui est hébergée sur https://app.sonarsystems.com, et une application appelée Connectulum, hébergée sur https://app.connectulum.com.
* Une machine Linux pour héberger Maverics Orchestrator
  * Système d’exploitation : REHL 7.7 ou version ultérieure, CentOS 7+
  * Disque : >= 10 Go
  * Mémoire : >= 4 Go
  * Ports : 22 (SSH/SCP), 443, 7474
  * Accès racine pour les tâches d’installation/d’administration
  * Sortie réseau à partir du serveur hébergeant Maverics Identity Orchestrator vers votre application protégée

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Étape 1 : Configurer Maverics Orchestrator

### <a name="install-maverics"></a>Installer Maverics

1. Obtenez le dernier RPM Maverics. Copiez le package sur le système sur lequel vous souhaitez installer le logiciel Maverics.

1. Installez le package Maverics, en substituant le nom de votre fichier à `maverics.rpm`.

   `sudo rpm -Uvf maverics.rpm`

   Une fois Maverics installé, il s’exécute en tant que service sous `systemd`. Pour vérifier que le service est en cours d’exécution, exécutez la commande suivante :

   `sudo systemctl status maverics`

1. Pour redémarrer Orchestrator et suivre les journaux, vous pouvez exécuter la commande suivante :

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Après l’installation de Maverics, le fichier `maverics.yaml` par défaut est créé dans le répertoire `/etc/maverics`. Avant que vous ne modifiiez votre configuration pour inclure `appgateways` et `connectors`, votre fichier de configuration se présente comme suit :

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Configurer DNS

DNS vous sera utile pour vous éviter d’avoir à mémoriser l’ IP du serveur Orchestrator.

Modifiez le fichier hosts de l’ordinateur du navigateur (celui de votre portable) en utilisant une IP Orchestrator hypothétique 12.34.56.78. Sur les systèmes d’exploitation Linux, ce fichier se trouve dans `/etc/hosts`. Sur Windows, il se trouve dans `C:\windows\system32\drivers\etc`.

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Pour confirmer que le DNS est configuré comme prévu, vous pouvez effectuer une demande au point de terminaison d’état d’Orchestrator. Dans votre navigateur, demandez http://sonar.maverics.com:7474/status.

### <a name="configure-tls"></a>Configurer TLS

La communication sur des canaux sécurisés pour communiquer avec votre instance Orchestrator est essentielle pour garantir la sécurité. Pour cela, vous pouvez ajouter une paire certificat/clé dans votre section `tls`.

Pour générer un certificat auto-signé et une clé pour le serveur Orchestrator, exécutez la commande suivante à partir du répertoire `/etc/maverics` :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Pour les environnements de production, vous voudrez probablement utiliser un certificat signé par une autorité de certification connue afin d’éviter les avertissements dans le navigateur. [Let’s Encrypt](https://letsencrypt.org/) est une option gratuite appropriée si vous recherchez une autorité de certification approuvée.

À présent, utilisez le certificat et la clé que vous venez de générer pour Orchestrator. Votre fichier de configuration doit maintenant contenir le code suivant :

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Pour vérifier que TLS est configuré comme prévu, redémarrez le service Maverics, puis effectuez une demande auprès du point de terminaison d’état. Dans votre navigateur, demandez https://sonar.maverics.com/status.

## <a name="step-2-proxy-an-application"></a>Étape 2 : Traiter une application par proxy

Configurez ensuite le traitement proxy de base dans Orchestrator en utilisant `appgateways`. Cette étape vous permet de vérifier qu’Orchestrator dispose de la connectivité nécessaire à l’application protégée.

Votre fichier de configuration doit maintenant contenir le code suivant :

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Pour vérifier que le traitement proxy fonctionne comme prévu, redémarrez le service Maverics, puis effectuez une demande auprès de l’application par le biais du proxy Maverics. Dans votre navigateur, demandez https://sonar.maverics.com. Vous pouvez éventuellement effectuer une demande auprès de ressources d’application spécifiques, par exemple `https://sonar.maverics.com/RESOURCE`, où `RESOURCE` est une ressource d’application valide de l’application en amont protégée.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Étape 3 : Inscrire une application d’entreprise dans Azure AD

À présent, créez une application d’entreprise dans Azure AD. Elle sera utilisée pour authentifier les utilisateurs finaux.

> [!NOTE]
> Quand vous utilisez des fonctionnalités Azure AD comme l’accès conditionnel, il est important de créer une application d’entreprise par application locale. Cela permet un accès conditionnel par application, une évaluation des risques par application, des autorisations attribuées par application, etc. En général, une application d’entreprise dans Azure AD mappe à un connecteur Azure dans Maverics.

Pour inscrire une application d’entreprise dans Azure AD :

1. Dans votre locataire Azure AD, sélectionnez **Applications d’entreprise**, puis **Nouvelle application**. Dans la galerie Azure AD, recherchez **Maverics Identity Orchestrator SAML Connector**, puis sélectionnez-le.

1. Dans le volet **Propriétés** de Maverics Identity Orchestrator SAML Connector, définissez **Affectation de l’utilisateur obligatoire ?** sur **Non** afin que l’application fonctionne pour tous les utilisateurs de votre répertoire.

1. Dans le volet **Vue d’ensemble** d’Identity Orchestrator SAML Connector, sélectionnez **Configurer l’authentification unique**, puis **SAML**.

1. Dans Maverics Identity Orchestrator SAML Connector, accédez au volet **Authentification basée sur SAML** et modifiez la **Configuration SAML de base** en sélectionnant le bouton **Modifier** (icône de crayon).

   ![Capture d’écran du bouton Modifier la configuration SAML de base.](common/edit-urls.png)

1. Entrez `https://sonar.maverics.com` comme **ID d’entité**. L’ID d’entité doit être unique dans les applications du locataire, et il peut s’agir d’une valeur arbitraire. Vous utiliserez cette valeur quand vous définirez le champ `samlEntityID` pour votre connecteur Azure dans la prochaine section.

1. Entrez `https://sonar.maverics.com/acs` comme **URL de réponse**. Vous utiliserez cette valeur quand vous définirez le champ `samlConsumerServiceURL` pour votre connecteur Azure dans la prochaine section.

1. Entrez `https://sonar.maverics.com/` comme **URL de connexion**. Ce champ n’est pas utilisé par Maverics, mais il est nécessaire dans Azure AD pour permettre aux utilisateurs d’accéder à l’application par le biais du portail Mes applications d’Azure AD.

1. Sélectionnez **Enregistrer**.

1. Dans la section **Certificat de signature SAML**, sélectionnez le bouton **Copier** pour copier la valeur **URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

   ![Capture d’écran du bouton de copie Certificat de signature SAML.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Étape 4 : S’authentifier par le biais d’Azure et autoriser l’accès à l’application

Placez ensuite l’application d’entreprise que vous venez de créer pour l’utiliser en configurant le connecteur Azure dans Maverics. Cette configuration `connectors` associée au bloc `idps` permet à Orchestrator d’authentifier les utilisateurs.

Votre fichier de configuration doit maintenant contenir le code suivant. Veillez à remplacer `METADATA_URL` par la valeur URL des métadonnées de fédération d’application de l’étape précédente.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Pour vérifier que l’authentification fonctionne comme prévu, redémarrez le service Maverics, puis effectuez une demande auprès d’une ressource d’application par le biais du proxy Maverics. Vous êtes normalement redirigé vers Azure pour l’authentification avant d’accéder à la ressource.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Étapes 5 : Ajouter des en-têtes pour un accès fluide aux applications

Vous n’envoyez pas encore d’en-têtes à l’application en amont. Ajoutons `headers` à la demande quand elle transite par le proxy Maverics pour permettre à l’application en amont d’identifier l’utilisateur.

Votre fichier de configuration doit maintenant contenir le code suivant :

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Pour vérifier que l’authentification fonctionne comme prévu, effectuez une demande auprès d’une ressource d’application par le biais du proxy Maverics. L’application protégée doit maintenant recevoir des en-têtes sur la demande. 

N’hésitez pas à modifier les clés d’en-tête si votre application attend des en-têtes différents. Toutes les revendications revenant d’Azure AD dans le cadre du flux SAML peuvent être utilisées dans les en-têtes. Par exemple, vous pouvez inclure un autre en-tête `secondary_email: azureSonarApp.email`, où `azureSonarApp` est le nom du connecteur et `email` est une revendication retournée par Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Étape 6 : Travailler avec plusieurs applications

Voyons maintenant ce qui est nécessaire pour traiter par proxy plusieurs applications qui se trouvent sur des hôtes différents. Pour effectuer cette étape, configurez une autre passerelle d’application, une autre application d’entreprise dans Azure AD et un autre connecteur.

Votre fichier de configuration doit maintenant contenir le code suivant :

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Vous avez peut-être remarqué que le code ajoute un champ `host` à vos définitions de passerelle d’application. Le champ `host` permet à Maverics Orchestrator de distinguer vers quel hôte en amont diriger le trafic proxy.

Pour vérifier que la passerelle d’application qui vient d’être ajoutée fonctionne comme prévu, effectuez une demande à https://connectulum.maverics.com.

## <a name="advanced-scenarios"></a>Scénarios avancés

### <a name="identity-migration"></a>Migration d’identité

Vous ne supportez pas votre outil de gestion de l’accès web en fin de vie, mais vous n’avez aucun moyen de migrer vos utilisateurs sans réinitialisations de mot de passe de masse ? Maverics Orchestrator prend en charge la migration d’identité à l’aide de `migrationgateways`.

### <a name="web-server-gateways"></a>Passerelles de serveur web

Vous ne voulez pas remanier votre trafic réseau et proxy par le biais de Maverics Orchestrator ? Ce n’est pas un problème. Maverics Orchestrator peut être associé à des passerelles de serveur web (modules) pour offrir les mêmes solutions sans traitement proxy.

## <a name="wrap-up"></a>Synthèse

À ce stade, vous avez installé Maverics Orchestrator, créé et configuré une application d’entreprise dans Azure AD, et configuré Orchestrator pour traiter par proxy une application protégée tout en exigeant une authentification et en appliquant une stratégie. Pour en savoir plus sur la façon dont Maverics Orchestrator peut être utilisé pour les cas d’usage de la gestion distribuée des identités, [contactez Strata](mailto:sales@strata.io).

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)
