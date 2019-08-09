---
title: Acheter et configurer un certificat SSL d’Azure | Microsoft Docs
description: Découvrez comment acheter un certificat App Service et le lier à votre application App Service
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: e7768eb29caf66fd8f666a9475ac0787826a47e0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618896"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Acheter et configurer un certificat SSL pour Azure App Service

Ce tutoriel vous montre comment sécuriser votre [application App Service](https://docs.microsoft.com/azure/app-service/) ou votre [Function App](https://docs.microsoft.com/azure/azure-functions/) en créant (en achetant) un certificat App Service dans [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), puis comment lier ce certificat à une application App Service.

> [!TIP]
> Les certificats App Service peuvent être utilisés pour n’importe quel service Azure ou non Azure et ne sont pas limités aux App Services. Pour cela, vous devez créer une copie PFX locale d’un certificat App Service afin de pouvoir l’utiliser où vous voulez. Pour plus d’informations, consultez [Création d’une copie PFX locale d’un certificat App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce guide pratique, vous devez au préalable :

- [Création d’une application App Service](/azure/app-service/)
- [Mapper un nom de domaine à votre application](app-service-web-tutorial-custom-domain.md) ou [acheter et configurer un nom de domaine dans Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Commander un certificat

Commandez un certificat App Service dans la <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">page de création App Service Certificate</a>.

![Création du certificat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Aidez-vous du tableau suivant pour configurer le certificat. Lorsque vous avez terminé, cliquez sur **Créer**.

| Paramètre | Description |
|-|-|
| Nom | Nom convivial de votre certificat App Service. |
| Nom d’hôte de domaine nu | Si vous spécifiez ici le domaine racine, vous obtenez un certificat qui sécurise *à la fois* le domaine racine et le sous-domaine `www`. Pour sécuriser un sous-domaine uniquement, indiquez ici son nom de domaine complet (par exemple, `mysubdomain.contoso.com`). |
| Subscription | Centre de données dans lequel l’application web est hébergée. |
| Resource group | Groupe de ressources qui contient le certificat. Vous pouvez utiliser un nouveau groupe de ressources, ou sélectionner le même groupe de ressources que votre application App Service, par exemple. |
| Référence (SKU) de certificat | Détermine le type de certificat à créer (certificat standard ou [certificat générique](https://wikipedia.org/wiki/Wildcard_certificate)). |
| Termes et conditions | Cliquez pour confirmer que vous acceptez les termes et conditions. Les certificats sont obtenus auprès de GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Stocker dans Azure Key Vault

Après l’achat du certificat, il vous reste quelques étapes à effectuer avant de commencer à utiliser ce certificat. 

Sélectionnez le certificat dans la page [App Service Certificates](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders), puis cliquez sur **Configuration du certificat** > **Étape 1 : Stocker**.

![insérer une image de prêt à stocker dans KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) est un service Azure qui permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. C’est le stockage recommandé pour les certificats App Service.

Dans la page **État de Key Vault**, cliquez sur **Référentiel Key Vault** pour créer un coffre ou choisir un coffre existant. Si vous créez un coffre, configurez le nouveau coffre en vous aidant du tableau ci-dessous, puis cliquez sur Créer. Voir comment créer un coffre de clés dans le même abonnement et dans le même groupe de ressources.

| Paramètre | Description |
|-|-|
| Nom | Nom unique composé de caractères alphanumériques et de tirets. |
| Resource group | Nous vous conseillons de choisir le même groupe de ressources que votre certificat App Service. |
| Location | Choisissez le même emplacement que votre application App Service. |
| Niveau tarifaire | Pour obtenir des informations sur les tarifs, consultez [Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Stratégies d’accès| Définit les applications et l’accès autorisé aux ressources du coffre. Vous pouvez configurer ce paramètre ultérieurement, en suivant les étapes décrites dans [Accorder à plusieurs applications l’autorisation d’accéder à un coffre de clés](../key-vault/key-vault-group-permissions-for-apps.md). |
| Accès au réseau virtuel | Limitez l’accès au coffre à certains réseaux virtuels Azure. Vous pouvez configurer ce paramètre ultérieurement, en suivant les étapes décrites dans [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](../key-vault/key-vault-network-security.md) |

Une fois que vous avez sélectionné le coffre, fermez la page **Référentiel Key Vault**. L’option **Stocker** affiche normalement une coche verte de réussite. Gardez cette page ouverte pour l’étape suivante.

## <a name="verify-domain-ownership"></a>Vérifier la propriété du domaine

Dans la même page **Configuration du certificat** utilisée à l’étape précédente, cliquez sur **Étape 2 : Vérifier**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Sélectionnez **Vérification App Service**. Étant donné que vous avez déjà mappé le domaine à votre application web (voir la section [Prérequis](#prerequisites)), le domaine a déjà été vérifié. Cliquez simplement sur **Vérifier** pour terminer cette étape. Cliquez sur le bouton **Actualiser** jusqu’à ce que le message **Le domaine du certificat a été vérifié** s’affiche.

> [!NOTE]
> Quatre types de méthodes de vérification du domaine sont pris en charge : 
> 
> - **App Service** : option la plus pratique quand le domaine a déjà été mappé à une application App Service dans le même abonnement. Elle profite du fait que l’application App Service a déjà vérifié la propriété du domaine.
> - **Domaine** : permet de vérifier un [domaine App Service que vous avez acheté sur Azure](manage-custom-dns-buy-domain.md). Azure ajoute automatiquement l’enregistrement TXT de vérification pour vous et effectue le processus.
> - **E-mail** : permet de vérifier le domaine en envoyant un e-mail à l’administrateur de domaine. Quand vous sélectionnez cette option, des instructions supplémentaires s’affichent.
> - **Manuelle** : permet de vérifier le domaine à l’aide d’une page HTML (certificat **Standard** uniquement) ou d’un enregistrement TXT DNS. Quand vous sélectionnez cette option, des instructions supplémentaires s’affichent.

## <a name="bind-certificate-to-app"></a>Lier le certificat à l’application

Dans le **[portail Azure](https://portal.azure.com/)** , dans le menu de gauche, sélectionnez **App Services** >  **\<votre_app>** .

Dans le panneau de navigation de gauche de votre application, sélectionnez **Paramètres SSL** > **Certificats privés (.pfx)**  > **Importer un App Service Certificate**.

![insérer une image d’importation de certificat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Sélectionnez le certificat que vous venez d’acheter.

Maintenant que le certificat est importé, vous devez le lier à un nom de domaine mappé dans votre application. Sélectionnez **Liaisons** > **Ajouter une liaison SSL**. 

![insérer une image d’importation de certificat](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Dans la boîte de dialogue **Liaisons SSL**, configurez la liaison en vous aidant du tableau suivant, puis cliquez sur **Ajouter une liaison**.

| Paramètre | Description |
|-|-|
| Nom d’hôte | Nom du domaine pour lequel ajouter une liaison SSL. |
| Empreinte numérique du certificat privé | Certificat à lier. |
| Type SSL | <ul><li>**SNI SSL** : plusieurs liaisons SNI SSL peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Après avoir configuré la liaison, effectuez les étapes décrites dans [Remapper un enregistrement pour IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Vérifier l’accès HTTPS

Accédez à votre application en utilisant `HTTPS://<domain_name>` au lieu de `HTTP://<domain_name>` pour vérifier que le certificat a été configuré correctement.

## <a name="rekey-certificate"></a>Recréer la clé du certificat

Si vous pensez que la clé privée de votre certificat est compromise, vous pouvez recréer la clé de votre certificat. Sélectionnez le certificat dans la page [App Service Certificates](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders), puis sélectionnez **Recréer la clé et synchroniser** dans le volet de navigation de gauche.

Cliquez sur le bouton **Recréer la clé** pour lancer le processus. Ce processus peut prendre de 1 à 10 minutes.

![insérer une image de renouvellement de clé SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Le renouvellement de la clé de votre certificat remplace le certificat par un nouveau certificat émis par l’autorité de certification.

Une fois l’opération de recréation de clé terminée, cliquez sur **Synchronisation**. L’opération de synchronisation met à jour automatiquement les liaisons de nom d’hôte pour le certificat dans App Service sans perturber le fonctionnement de vos applications.

> [!NOTE]
> Si vous ne cliquez pas sur **Synchronisation**, App Service synchronise automatiquement votre certificat sous 48 heures.

## <a name="renew-certificate"></a>Renouvellement de certificat

Pour activer le renouvellement automatique de votre certificat à tout moment, sélectionnez le certificat dans la page [App Service Certificates](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders), puis cliquez sur **Paramètres de renouvellement automatique** dans le volet de navigation de gauche.

Sélectionnez **Activé** et cliquez sur **Enregistrer**. Les certificats peuvent être automatiquement renouvelés 60 jours avant leur expiration si vous avez activé le renouvellement automatique.

![renouveler le certificat automatiquement](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Pour renouveler manuellement le certificat, cliquez sur **Renouvellement manuel**. Vous pouvez demander le renouvellement manuellement de votre certificat 60 jours avant expiration.

Une fois l’opération de renouvellement terminée, cliquez sur **Synchronisation**. L’opération de synchronisation met à jour automatiquement les liaisons de nom d’hôte pour le certificat dans App Service sans perturber le fonctionnement de vos applications.

> [!NOTE]
> Si vous ne cliquez pas sur **Synchronisation**, App Service synchronise automatiquement votre certificat sous 48 heures.

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

### <a name="azure-cli"></a>D’Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Autres ressources

* [Appliquer le protocole HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Appliquer le protocole TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Utiliser un certificat SSL dans votre code d’application dans Azure App Service](app-service-web-ssl-cert-load.md)
* [FORUM AUX QUESTIONS : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
