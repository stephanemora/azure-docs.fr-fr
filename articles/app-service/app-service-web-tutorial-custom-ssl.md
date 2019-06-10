---
title: Charger et lier un certificat SSL - Azure App Service | Microsoft Docs
description: Découvrez comment lier un certificat SSL personnalisé à votre application web, un backend d’application mobile ou une application API dans Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e0ee1e7c652ddf4126fc9658bf17d3e919d7a5c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475359"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Didacticiel : Charger et lier un certificat SSL dans Azure App Service

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce tutoriel montre comment sécuriser un domaine personnalisé dans App Service à l’aide d’un certificat acheté auprès d’une autorité de certification approuvée. Il explique également comment charger les certificats privés et publics dont votre application a besoin. Quand vous aurez terminé, vous pourrez accéder à votre application au niveau du point de terminaison HTTPS de votre domaine DNS personnalisé.

![Application Web avec certificat SSL personnalisé](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour le niveau de tarification de votre application
> * Sécuriser un domaine personnalisé à l’aide d’un certificat
> * Téléchargement d’un certificat privé
> * Téléchargement d’un certificat public
> * Renouveler les certificats
> * Appliquer le protocole HTTPS
> * Appliquer le protocole TLS 1.1/1.2
> * Automatiser la gestion TLS avec des scripts

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

- [Création d’une application App Service](/azure/app-service/)
- [Mapper un nom DNS personnalisé à votre application App Service](app-service-web-tutorial-custom-domain.md) (dans le cadre de la sécurisation d’un domaine personnalisé)
- Acquérir un certificat auprès d’une autorité de certification approuvée
- Ayez à disposition la clé privée que vous avez utilisée pour signer la demande de certificat (pour les certificats privés)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Préparer un certificat privé

Pour sécuriser un domaine, le certificat doit remplir toutes les exigences suivantes :

* Être configuré pour l’authentification serveur
* Être signé par une autorité de certification approuvée
* Être exporté sous la forme d’un fichier PFX protégé par mot de passe
* Contenir une clé privée d’au moins 2048 bits de long
* Contenir tous les certificats intermédiaires dans la chaîne de certificats

> [!TIP]
> Si vous avez besoin d’un certificat SSL personnalisé, vous pouvez en obtenir un directement dans le portail Azure et l’importer dans votre application. Suivez le [didacticiel Certificats App Service](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> Les **certificats de chiffrement à courbe elliptique (ECC)** sont compatibles avec App Service, mais ce sujet sort du cadre de cet article. Consultez votre autorité de certification sur les étapes à suivre pour créer des certificats ECC.

Une fois que vous avez obtenu un certificat de votre fournisseur de certificats, effectuez les étapes décrites dans cette section afin de préparer le certificat pour App Service.

### <a name="merge-intermediate-certificates"></a>Fusionner les certificats intermédiaires

Si votre autorité de certification vous donne plusieurs certificats dans la chaîne, vous devez les fusionner dans l’ordre.

Pour ce faire, ouvrez chaque certificat reçu dans un éditeur de texte.

Créez un fichier pour le certificat fusionné, appelé _mergedcertificate.crt_. Dans un éditeur de texte, copiez le contenu de chaque certificat dans ce fichier. L’ordre de vos certificats devrait suivre l’ordre dans la chaîne d’approbation, commençant par votre certificat et finissant par le certificat racine. Cela ressemble à l’exemple suivant :

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportation du certificat vers PFX

Exportez votre certificat SSL fusionné avec la clé privée ayant servi à générer votre demande de certificat.

Si vous avez généré votre demande de certificat à l’aide d’OpenSSL, vous avez créé un fichier de clé privée. Pour exporter votre certificat au format PFX, exécutez la commande suivante : Remplacez les espaces réservés  _&lt;fichier de clé privée>_ et _&lt;fichier de certificat fusionné>_ par les chemins d’accès menant à votre clé privée et à votre fichier de certificat fusionné.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Lorsque vous y êtes invité, définissez un mot de passe d’exportation. Vous allez utiliser ce mot de passe lors du chargement de votre certificat SSL dans App Service.

Si vous avez utilisé IIS ou _Certreq.exe_ pour générer votre demande de certificat, installez le certificat sur votre ordinateur local, puis [exportez le certificat au format PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Vous êtes maintenant prêt à charger le certificat dans App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Sécuriser un domaine personnalisé

> [!TIP]
> Si vous avez besoin d’un certificat SSL personnalisé, vous pouvez en obtenir un directement dans le portail Azure et le lier à votre application. Suivez le [didacticiel Certificats App Service](web-sites-purchase-ssl-web-site.md).

Pour sécuriser un [domaine personnalisé](app-service-web-tutorial-custom-domain.md) avec un certificat tiers, vous devez charger le [certificat privé préparé](#prepare-a-private-certificate), puis le lier au domaine personnalisé. Ce processus est facilité par App Service. Effectuez également les étapes suivantes :

Cliquez sur **Domaines personnalisés** dans le volet de navigation gauche dans votre application, puis cliquez sur **Ajouter une liaison** pour le domaine à sécuriser. Si vous ne voyez pas l’option **Ajouter une liaison** pour un domaine, cela signifie que ce domaine est déjà sécurisé et doit avoir l’état SSL **Sécurisé**.

![Ajouter une liaison au domaine](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Cliquez sur **Charger le certificat**.

Dans **Fichier de certificat PFX**, sélectionnez votre fichier PFX. Dans **Mot de passe du certificat**, tapez le mot de passe que vous avez créé lors de l’exportation du fichier PFX.

Cliquez sur **Télécharger**.

![Charger le certificat pour le domaine](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Attendez qu’Azure ait fini de charger votre certificat et d’ouvrir la boîte de dialogue Liaisons SSL.

Dans la boîte de dialogue Liaisons SSL, sélectionnez le certificat que vous avez chargé ainsi que le type SSL, puis cliquez sur **Ajouter une liaison**.

> [!NOTE]
> Les types SSL suivants sont pris en charge :
>
> - **[SSL basé sur SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)**  : plusieurs liaisons SSL basées sur SNI peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Pour sécuriser plusieurs domaines, vous devez tous les sécuriser en utilisant le même certificat SSL. Cette option est sélectionnée par défaut pour la liaison SSL.

![Liaison SSL au domaine](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

L’état SSL du domaine doit maintenant être **Sécurisé**.

![Domaine sécurisé](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Quand un domaine présente l’état **Sécurisé** dans **Domaines personnalisés**, cela indique qu’il est sécurisé par un certificat. Toutefois, App Service ne vérifie pas si le certificat est auto-signé ou arrivé à expiration, par exemple, ce qui peut également provoquer l’affichage d’une erreur ou d’un avertissement dans les navigateurs.

## <a name="remap-a-record-for-ip-ssl"></a>Nouveau mappage d’un enregistrement pour SSL IP

Si vous n’utilisez pas SSL basé sur IP dans votre application, passez à [Tester HTTPS pour votre domaine personnalisé](#test).

Par défaut, votre application utilise une adresse IP publique partagée. Dès que vous liez un certificat avec SSL basé sur IP, App Service crée une adresse IP dédiée pour votre application.

Si vous avez mappé un enregistrement A à votre application, mettez à jour votre registre de domaine avec cette nouvelle adresse IP dédiée.

La page **Domaine personnalisé** de votre application est mise à jour avec la nouvelle adresse IP dédiée. [Copiez cette adresse IP](app-service-web-tutorial-custom-domain.md#info), puis [mappez à nouveau l’enregistrement A](app-service-web-tutorial-custom-domain.md#map-an-a-record) à cette nouvelle adresse IP.

<a name="test"></a>

## <a name="test-https"></a>Test du protocole HTTPS

Il ne reste plus maintenant qu’à vous assurer que HTTPS fonctionne pour votre domaine personnalisé. Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour vérifier qu’il fournit votre application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Si votre application permet de voir les erreurs de validation de certificat, vous utilisez probablement un certificat auto-signé.
>
> Si ce n’est pas le cas, vous pouvez avoir oublié des certificats intermédiaires lorsque vous avez exporté votre certificat vers le fichier PFX.

## <a name="renew-certificates"></a>Renouveler les certificats

Votre adresse IP entrante peut être modifiée lorsque vous supprimez une liaison, même si cette liaison repose sur une adresse IP. Cela est particulièrement important lorsque vous renouvelez un certificat qui se trouve déjà dans une liaison reposant sur une adresse IP. Pour éviter une modification de l’adresse IP de votre application, suivez ces étapes dans l’ordre :

1. Chargez le nouveau certificat.
2. Liez le nouveau certificat au domaine personnalisé souhaité, sans supprimer l’ancien. Cette action remplace la liaison plutôt que de supprimer l’ancienne.
3. Supprimez l’ancien certificat. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Appliquer le protocole HTTPS

Par défaut, n’importe qui peut encore accéder à votre application avec HTTP. Vous pouvez rediriger toutes les demandes HTTP sur le port HTTPS.

Dans le volet de navigation gauche de la page de votre application, sélectionnez **Paramètres SSL**. Ensuite, dans **HTTPS uniquement**, sélectionnez **Activer**.

![Appliquer le protocole HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Lorsque l’opération est terminée, accédez à une des URL HTTP pointant vers votre application. Par exemple :

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Appliquer des versions TLS

Votre application autorise la version [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 par défaut, qui constitue le niveau TLS recommandé par les normes du secteur, telles que [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pour appliquer d’autres versions TLS, procédez comme suit :

Dans le volet de navigation gauche de la page de votre application, sélectionnez **Paramètres SSL**. Ensuite, dans **Version TLS**, sélectionnez la version minimale de TLS souhaitée. Ce paramètre contrôle uniquement les appels entrants. 

![Appliquer le protocole TLS 1.1 ou 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Une fois l’opération terminée, votre application rejette toutes les connexions effectuées avec des versions antérieures de TLS.

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser les liaisons SSL de votre application à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

La commande suivante charge un fichier PFX exporté et obtient l’empreinte.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

La commande suivante ajoute une liaison SSL basée sur SNI à l’aide de l’empreinte de la commande précédente.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

La commande suivante met en œuvre la version minimale de TLS 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La commande suivante charge un fichier PFX exporté et ajoute une liaison SSL basée sur SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Utiliser des certificats dans votre code

Si votre application doit se connecter à des ressources distantes, et qu’une ressource distante exige l’authentification par certificat, vous pouvez charger des certificats publics ou privés dans votre application. Vous n’avez pas besoin de lier ces certificats à un domaine personnalisé dans votre application. Pour plus d’informations, consultez [Utiliser un certificat SSL dans votre code d’application dans Azure App Service](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Mettre à jour le niveau de tarification de votre application
> * Lier votre certificat personnalisé à App Service
> * Renouveler les certificats
> * Appliquer le protocole HTTPS
> * Appliquer le protocole TLS 1.1/1.2
> * Automatiser la gestion TLS avec des scripts

Passez au didacticiel suivant pour découvrir comment utiliser un réseau de distribution de contenu Azure.

> [!div class="nextstepaction"]
> [Ajouter un réseau de distribution de contenu (CDN) à un Azure App Service](../cdn/cdn-add-to-web-app.md)

Pour plus d’informations, consultez [Utiliser un certificat SSL dans votre code d’application dans Azure App Service](app-service-web-ssl-cert-load.md).
