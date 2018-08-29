---
title: Acheter et configurer un certificat SSL pour votre service Azure App Service | Microsoft Docs
description: Découvrez comment acheter un certificat App Service et le lier à votre application App Service
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 21dde5b0199345e9e8faffc6feac4854a3aa7350
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42145630"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Acheter et configurer un certificat SSL pour votre service Azure App Service

Ce didacticiel vous montre comment sécuriser votre application web en achetant un certificat SSL pour votre **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, en le stockant dans [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) de manière sécurisée et en l’associant à un domaine personnalisé.

## <a name="step-1---sign-in-to-azure"></a>Étape 1 : se connecter à Azure

Connectez-vous au portail Azure sur http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Étape 2 : Passer une commande de certificat SSL

Vous pouvez passer une commande de certificat SSL en créant un [Certificat App Service](https://portal.azure.com/#create/Microsoft.SSL) dans le **portail Azure**.

![Création du certificat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Entrez un **nom** convivial pour votre certificat SSL et entrez le **nom de domaine**.

> [!NOTE]
> Cette étape est l’un des points les plus cruciaux du processus d’achat. Veillez à entrer un nom d’hôte correct (domaine personnalisé) que vous voulez protéger avec ce certificat. N’**AJOUTEZ PAS** le nom d’hôte devant avec WWW. 
>

Sélectionnez votre **abonnement**, **groupe de ressources** et **référence (SKU) de certificat**

> [!TIP]
> Les certificats App Service peuvent être utilisés pour n’importe quel service Azure ou non Azure et ne sont pas limités aux App Services. Pour ce faire, vous pouvez créer une copie PFX locale d’un certificat App Service afin de pouvoir l’utiliser où vous voulez. Pour plus d’informations, lisez [Création d’une copie PFX locale d’un certificat App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Étape 3 : Stocker le certificat dans Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) est un service Azure qui permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud.
>

Une fois l’achat du certificat SSL terminé, vous devez ouvrir la page [Certificats App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders).

![insérer une image de prêt à stocker dans KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

L’état du certificat est **« Émission en attente »**, car vous devez effectuer quelques étapes supplémentaires avant de commencer à utiliser ce certificat.

Cliquez sur **Configuration du certificat** dans la page Propriétés du certificat, puis sur **Étape 1 : Stocker** pour stocker ce certificat dans Azure Key Vault.

Dans la page **État de Key Vault**, cliquez sur **Référentiel Key Vault** pour choisir un coffre de clés existant et stocker ce certificat OU sur **Créer un coffre de clés** pour créer un coffre de clés dans les mêmes abonnement et groupe de ressources.

> [!NOTE]
> Azure Key Vault engendre peu de frais pour le stockage de ce certificat.
> Pour plus d’informations, consultez **[Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Une fois que le référentiel Key Vault où stocker le certificat est sélectionné, l’option **Stocker** doit indiquer que l’opération a réussi.

![insérer une image de réussite du stockage dans KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Étape 4 : Vérifier la propriété du domaine

Dans la même page **Configuration du certificat** utilisée à l’étape 3, cliquez sur **Étape 2 : Vérifier**.

Choisissez la méthode de vérification de domaine par défaut. 

Il existe quatre types de vérification de domaine pris en charge par App Service Certificates : la vérification App Service, la vérification de domaine et la vérification manuelle. Ces types de vérification sont décrits plus en détail dans la [section Avancé](#advanced).

> [!NOTE]
> La **Vérification App Service** est l’option la plus pratique lorsque le domaine à vérifier est déjà mappé à une application App Service dans le même abonnement. Elle profite du fait que l’application App Service a déjà vérifié la propriété du domaine.
>

Cliquez sur le bouton **Vérifier** pour terminer cette étape.

![insérer une image de vérification du domaine](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Après avoir cliqué sur **Vérifier**, utilisez le bouton **Actualiser** jusqu’à ce que l’option **Vérifier** indique que l’opération a réussi.

![insérer une image de réussite de la vérification dans KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Étape 5 : Attribuer un certificat à une application App Service

> [!NOTE]
> Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre application. Pour plus d’informations, consultez la page **[Configuration d’un nom de domaine personnalisé pour une application web.](app-service-web-tutorial-custom-domain.md)**
>

Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur l’option **App Service** sur le côté gauche de la page.

Cliquez sur le nom de votre application à laquelle vous voulez attribuer ce certificat.

Dans les **Paramètres**, cliquez sur **Paramètres SSL**.

Cliquez sur **Importer un certificat App Service**, puis sélectionnez le certificat que vous venez d’acheter.

![insérer une image d’importation de certificat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Dans la section **Liaisons SSL**, cliquez sur **Ajouter des liaisons** et utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser à l’aide du protocole SSL, ainsi que le certificat à utiliser. Vous pouvez également indiquer si vous voulez utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.

![insérer une image de liaisons SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Cliquez sur **Ajouter une liaison** pour enregistrer les modifications et activer SSL.

> [!NOTE]
> Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes. Elles sont décrites plus en détail dans la [section Avancé](#Advanced).

À ce stade, vous devez être en mesure de visiter votre application en utilisant `HTTPS://` à la place de `HTTP://` pour vérifier que le certificat a été configuré correctement.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Étape 6 : Tâches de gestion

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avancé

### <a name="verifying-domain-ownership"></a>Vérifier la propriété du domaine

Il existe deux autres types de vérification de domaine pris en charge par App Service Certificates : la vérification de domaine et la vérification manuelle.

#### <a name="domain-verification"></a>Vérification de domaine

Choisissez cette option uniquement pour [un domaine App Service que vous avez acheté sur Azure](custom-dns-web-site-buydomains-web-app.md). Azure ajoute automatiquement l’enregistrement TXT de vérification pour vous et effectue le processus.

#### <a name="manual-verification"></a>Vérification manuelle

> [!IMPORTANT]
> Vérification de page web HTML (fonctionne uniquement avec la référence du certificat standard)
>

1. Créez un fichier HTML nommé **« starfield.html »**

1. Le contenu de ce fichier doit être le nom exact du jeton de vérification du domaine. (Vous pouvez copier le jeton à partir de la page d’état de la vérification du domaine.)

1. Chargez ce fichier à la racine du serveur web qui héberge votre domaine `/.well-known/pki-validation/starfield.html`

1. Cliquez sur **« Actualiser »** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.

> [!TIP]
> Effectuez la vérification dans un terminal utilisant `curl -G http://<domain>/.well-known/pki-validation/starfield.html`. La réponse doit contenir le `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Vérification d’enregistrement TXT DNS

1. À l’aide de votre Gestionnaire DNS, créez un enregistrement TXT sur le sous-domaine `@` avec une valeur égale au jeton de vérification du domaine.
1. Cliquez sur **« Actualiser »** pour mettre à jour l’état du certificat une fois la vérification terminée.

> [!TIP]
> Vous devez créer un enregistrement TXT sur `@.<domain>` avec la valeur `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Attribuer un certificat à une application App Service

Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes :

Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Vous trouverez cette adresse IP sur la page des **domaines personnalisés** sous les paramètres de votre application, juste au-dessus de la section **Noms d’hôtes**. Elle est répertoriée en tant qu’**Adresse IP externe**

![insérer une image d’IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Cette adresse IP est différente de celle, virtuelle, utilisée précédemment pour configurer l’enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n’utilisez pas SSL, aucune adresse n’est indiquée pour cette entrée.

À l’aide des outils fournis par votre bureau d’enregistrement, modifiez l’enregistrement A de votre nom de domaine personnalisé de manière à ce qu’il pointe vers l’adresse IP spécifiée lors de l’étape précédente.

## <a name="rekey-and-sync-the-certificate"></a>Renouveler la clé du certificat et le synchroniser

Si vous avez besoin de renouveler la clé de votre certificat, sélectionnez l’option **Recréer la clé et synchroniser** dans la page **Propriétés du certificat**.

Cliquez sur le bouton **Renouveler la clé** pour lancer le processus. Ce processus peut prendre de 1 à 10 minutes.

![insérer une image de renouvellement de clé SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Le renouvellement de la clé de votre certificat remplace le certificat par un nouveau certificat émis par l’autorité de certification.

## <a name="renew-the-certificate"></a>Renouveler le certificat

Pour activer le renouvellement automatique de votre certificat à tout moment, cliquez sur **Paramètres de renouvellement automatique** dans la page de gestion des certificats. Sélectionnez **Activé** et cliquez sur **Enregistrer**. Les certificats peuvent démarrer automatiquement le renouvellement 90 jours avant l’expiration si vous avez activé le renouvellement automatique.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Pour renouveler manuellement le certificat, cliquez sur **Renouvellement manuel**. Vous pouvez demander le renouvellement manuellement de votre certificat 60 jours avant expiration.

> [!NOTE]
> Le certificat renouvelé n’est pas automatiquement lié à votre application, qu’il ait été renouvelé manuellement ou automatiquement. Pour lier le certificat à votre application, consultez l’article [Renouveler les certificats](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

<a name="notrenewed"></a>
## <a name="why-is-my-certificate-not-auto-renewed"></a>Pourquoi mon certificat n’est-il pas renouvelé automatiquement ?

Si votre certificat SSL est configuré pour le renouvellement automatique et qu’il n’est pas automatiquement renouvelé, vous avez peut-être une vérification de domaine en attente. Notez les points suivants : 

- GoDaddy, qui génère des certificats App Service, nécessite une vérification de domaine une fois tous les deux ans. L’administrateur de domaine reçoit un e-mail une fois tous les trois ans pour vérifier le domaine. Si vous ignorez cet e-mail ou si vous ne vérifiez pas votre domaine, vous bloquez le renouvellement automatique du certificat App Service. 
- Suite à une modification de la politique de GoDaddy, pour tous les certificats App Service émis avant le 1er mars 2017, une revérification du domaine est obligatoire au moment du renouvellement suivant (même si le renouvellement automatique est activé pour le certificat). Prenez connaissance de cet e-mail et procédez à cette vérification de domaine ponctuelle pour continuer le renouvellement automatique du certificat App Service. 

## <a name="more-resources"></a>Autres ressources

* [Appliquer le protocole HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Appliquer le protocole TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [Utiliser un certificat SSL dans votre code d’application dans Azure App Service](app-service-web-ssl-cert-load.md)
* [Questions fréquentes : App Service Certificates](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
