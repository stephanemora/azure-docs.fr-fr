---
title: Sécuriser l’accès hybride avec Datawiza
titleSuffix: Azure AD
description: Ce tutoriel explique comment intégrer Datawiza avec Azure AD pour un accès hybride sécurisé
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d527db391185c899bed3137c3783aa81f8a00c63
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067026"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>Tutoriel : Configurer Datawiza avec Azure Active Directory pour un accès hybride sécurisé

Cet exemple de tutoriel explique comment intégrer Azure Active Directory (Azure AD) avec [Datawiza](https://www.datawiza.com/) pour un accès hybride sécurisé.

La solution [Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) de Datawiza étend Azure AD pour activer l’authentification unique (SSO) et des contrôles d’accès précis afin de protéger des applications locales et hébergées dans le cloud, telles que Oracle E-Business Suite, Microsoft IIS et SAP.

Cette solution permet aux entreprises de passer rapidement d’un gestionnaire d’accès web (Web Access Manager, WAM) hérité, tel que Symantec SiteMinder, NetIQ, Oracle et IBM, à Azure AD sans réécriture d’applications. Les entreprises peuvent également utiliser Datawiza comme solution sans code ou à faible code pour intégrer de nouvelles applications à Azure AD. Cela permet d’économiser du temps d’ingénierie, de réduire considérablement les coûts et de fournir le projet de façon sécurisée.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d\'aucun abonnement, vous pouvez obtenir un [compte d’essai](https://azure.microsoft.com/free/).

- Un [locataire Azure AD](../fundamentals/active-directory-access-create-new-tenant.md) lié à votre abonnement Azure.

- [Docker](https://docs.docker.com/get-docker/) et [Docker Compose](https://docs.docker.com/compose/install/) sont requis pour exécuter DAB. Vos applications peuvent s’exécuter sur n’importe quelle plateforme telle qu’une machine virtuelle ou du matériel nu.

- Une application locale que vous allez faire passer d’un système d’identité hérité à Azure AD. Dans cet exemple, DAB est déployé sur le même serveur que l’application. L’application s’exécute sur localhost : 3001 et DAB dirige par proxy le trafic vers l’application via localhost : 9772. Le trafic vers l’application atteint d’abord DAB, puis est dirigé par proxy vers l’application.

## <a name="scenario-description"></a>Description du scénario

L’intégration Datawiza inclut les composants suivants :

- [Azure AD](../fundamentals/active-directory-whatis.md) : service de gestion des identités et des accès basé sur le cloud de Microsoft, qui permet aux utilisateurs de se connecter et d’accéder à des ressources internes et externes.

- Datawiza Access Broker (DAB) : l’utilisateur du service se connecte et transmet de manière transparente l’identité aux applications via les en-têtes HTTP.

- Datawiza Cloud Management Console (DCMC) : console de gestion centralisée qui gère DAB. La console DCMC fournit aux administrateurs une interface utilisateur et des API RESTful pour gérer les configurations de DAB et ses stratégies de contrôle d’accès.

Le diagramme d’architecture suivant illustre l’implémentation.

![Image montrant le diagramme d’architecture](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|Étapes| Description|
|:----------|:-----------|
|  1. | L’utilisateur effectue une demande d’accès à l’application locale ou hébergée dans le cloud. DAB dirige par proxy la demande de l’utilisateur vers l’application.|
| 2. |DAB vérifie l’état d’authentification de l’utilisateur. S’il ne reçoit pas de jeton de session ou si le jeton de session fourni n’est pas valide, il achemine l’utilisateur vers Azure AD à des fins d’authentification.|
| 3. | Azure AD envoie la demande de l’utilisateur au point de terminaison spécifié lors de l’inscription de l’application DAB dans le locataire Azure AD.|
| 4. | DAB évalue les stratégies d’accès et calcule les valeurs d’attribut à inclure dans les en-têtes HTTP transférés à l’application. Au cours de cette étape, DAB peut appeler le fournisseur d’identité afin de récupérer les informations nécessaires pour définir correctement les valeurs d’en-tête. DAB définit les valeurs d’en-tête et envoie la demande à l’application. |
| 5. |  L’utilisateur est maintenant authentifié et a accès à l’application.|

## <a name="onboard-with-datawiza"></a>Intégration avec Datawiza

Pour intégrer votre application locale ou hébergée dans le cloud avec Azure AD, connectez-vous à [Datawiza Cloud Management Console](https://console.datawiza.com/) (DCMC).

## <a name="create-an-application-on-dcmc"></a>Créer une application sur la console DCMC

[Créez une application](https://docs.datawiza.com/step-by-step/step2.html) et générez une paire de clés de `PROVISIONING_KEY` et `PROVISIONING_SECRET` pour cette application sur la console DCMC.

Pour Azure AD, Datawiza offre une [intégration en un clic](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html). Cette méthode d’intégration d’Azure AD avec la console DCMC peut créer pour vous une inscription d’application dans votre locataire Azure AD.

![Image montrant la configuration du fournisseur d’identité](./media/datawiza-with-azure-active-directory/configure-idp.png)

Au lieu de cela, si vous souhaitez utiliser une application web existante dans votre locataire Azure AD, vous pouvez désactiver l’option et remplir les champs du formulaire. Vous aurez besoin de l’ID de locataire, de l’ID client et de la clé secrète client. [Créez une application web et récupérez ces valeurs dans votre locataire](https://docs.datawiza.com/idp/azure.html).

![Image montrant la configuration du fournisseur d’identité à l’aide d’un formulaire](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>Exécuter DAB avec une application basée sur un en-tête

1. Vous pouvez utiliser Docker ou Kubernetes pour exécuter DAB. L’image Docker est nécessaire pour que les utilisateurs puissent créer un exemple d’application basée sur un en-tête. [Configurez DAB et l’intégration de l’authentification unique](https://docs.datawiza.com/step-by-step/step3.html). [Déployer DAB avec Kubernetes](https://docs.datawiza.com/tutorial/web-app-AKS.html). Un fichier `docker-compose.yml` d’exemple d’image Docker est fourni, que vous pouvez télécharger et utiliser. [Connectez-vous au registre de conteneurs](https://docs.datawiza.com/step-by-step/step3.html#important-step) pour télécharger les images de DAB et de l’application basée sur un en-tête.

    ```yaml
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. Après l’exécution de `docker-compose -f docker-compose.yml up`, l’authentification unique avec Azure AD doit être activée pour l’application basée sur un en-tête. Ouvrez un navigateur et saisissez `http://localhost:9772/`.

3. Une page de connexion Azure AD s’affiche.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Passer des attributs utilisateur à l’application basée sur un en-tête

1. DAB obtient les attributs utilisateur auprès du fournisseur d’identité et peut transmettre les attributs utilisateur à l’application via un en-tête ou un cookie. Consultez les instructions sur la façon de [passer des attributs utilisateur](https://docs.datawiza.com/step-by-step/step4.html), comme l’adresse de messagerie, le prénom et le nom, à l’application basée sur un en-tête.

2. Une fois les attributs utilisateur correctement configurés, une coche verte doit s’afficher en regard de chaque attribut utilisateur.

   ![Image montrant la page d’accueil de l’application Datawiza](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>Tester le flux

1. Accédez à l’URL de l’application.

2. DAB doit rediriger vers la page de connexion Azure AD.

3. Une fois l’authentification réussie, vous devez être redirigé vers DAB.

4. DAB évalue les stratégies, calcule les en-têtes et envoie l’utilisateur à l’application en amont.

5. L’application demandée doit s’afficher.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer Datawiza avec Azure AD B2C](../../active-directory-b2c/partner-datawiza.md)

- [Documentation Datawiza](https://docs.datawiza.com)