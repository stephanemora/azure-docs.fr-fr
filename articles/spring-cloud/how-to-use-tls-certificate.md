---
title: Utilisation de certificats TLS/SSL dans une application dans Azure Spring Cloud
titleSuffix: Azure Spring Cloud
description: Utilisez des certificats TLS/SSL dans une application.
author: karlerickson
ms.author: jieshe
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
ms.openlocfilehash: 44ea16782bb3bfdd51e89fd140f123f34fe4b44d
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067467"
---
# <a name="use-a-tlsssl-certificate-in-your-application-in-azure-spring-cloud"></a>Utilisation d’un certificat TLS/SSL dans une application dans Azure Spring Cloud

Cet article vous explique comment utiliser des certificats publics dans Azure Spring Cloud pour votre application. Votre application peut jouer le rôle d’un client et accéder à un service externe qui exige une authentification par certificat. Elle peut également être amenée à effectuer des tâches de chiffrement.  

Lorsque vous confiez la gestion de vos certificats TLS/SSL à Azure Spring Cloud, vous pouvez conserver les certificats et le code de votre application séparément pour protéger vos données sensibles. Le code de votre application a accès aux certificats publics que vous ajoutez à votre instance Azure Spring Cloud.

> [!NOTE]
> Un support et des exemples Azure CLI et Terraform seront bientôt disponibles dans cet article.

## <a name="prerequisites"></a>Prérequis

- Une application déployée dans Azure Spring Cloud. Consultez [Démarrage rapide : Déploiement d’une première application dans Azure Spring Cloud](./quickstart.md) ou utilisez une application existante.
- Un fichier de certificat dotée de l’extension *.crt*, *.cer*, *.pem* ou *.der*, ou une instance d’Azure Key Vault déployée avec un certificat privé.

## <a name="import-a-certificate"></a>Importation d’un certificat

Vous pouvez choisir d’importer votre certificat dans votre instance Azure Spring Cloud à partir de Key Vault ou d’utiliser un fichier de certificat local.

### <a name="import-a-certificate-from-key-vault"></a>Importer un certificat à partir de Key Vault

Avant d’importer le certificat, vous devez accorder à Azure Spring Cloud un accès à votre coffre de clés. Pour cela, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Coffres de clés**, puis le coffre de clés à partir duquel vous allez importer votre certificat.
1. Dans le volet de navigation gauche, sélectionnez **Stratégies d’accès**, puis **+ Ajouter des stratégies d’accès**.
1. Sélectionnez **Autorisations du certificat**, puis **Get** (Obtenir) et **List** (Lister).
1. Sous **Sélectionner un principal**, sélectionnez **Aucune sélection**, puis votre principal de sécurité.
1. Sélectionnez **Sélectionner**, puis **Ajouter**.

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="Capture d’écran de l’emplacement auquel l’accès au coffre de clés est accordé avant l’importation d’un certificat" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

Une fois que vous avez accordé l’accès à votre coffre de clés, vous pouvez importer votre certificat. Pour cela, procédez comme suit :

1. Accédez à votre instance de service.
1. Dans le volet de navigation gauche de votre instance, sélectionnez **Paramètres TLS/SSL**.
1. Sélectionnez **Importer un certificat de coffre de clés** dans la section **Certificats à clé publique**.
1. Sélectionnez votre coffre de clés dans **Coffre de clés** et le certificat dans **Certificat**, puis **Sélectionner** et **Appliquer**.
1. Une fois importé, votre certificat apparaît dans la liste des certificats à clé privé.

### <a name="import-a-local-certificate-file"></a>Importation d’un fichier de certificat local

Pour importer un fichier de certificat stocké localement, procédez comme suit :

1. Accédez à votre instance de service.
1. Dans le volet de navigation gauche de votre instance, sélectionnez **Paramètres TLS/SSL**.
1. Sélectionnez **Charger un certificat public** dans la section **Certificats à clé publique**.
1. Une fois importé, votre certificat apparaît dans la liste des certificats à clé privé.

## <a name="load-a-certificate"></a>Chargement d’un certificat

Pour charger un certificat dans votre application dans Azure Spring Cloud, commencez par suivre cette procédure :

1. Accédez à votre instance d’application.
1. Dans le volet de navigation gauche de votre application, sélectionnez **Gestion des certificats**.
1. Sélectionnez **Ajouter un certificat** pour choisir les certificats accessibles à l’application.

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="Capture d’écran de l’emplacement auquel un certificat est chargé dans l’application" lightbox="media/use-tls-certificates/load-certificate.png":::

### <a name="load-a-certificate-from-code"></a>Chargement d’un certificat à partir du code

Vos certificats chargés sont disponibles dans le dossier */etc/azure-spring-cloud/certs/public*. Utilisez le code Java suivant pour charger un certificat public dans une application dans Azure Spring Cloud.

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### <a name="load-a-certificate-into-the-trust-store"></a>Chargement d’un certificat dans le magasin de confiance

Dans le cas d’une application Java, vous pouvez choisir **Charger dans le magasin de confiance** pour le certificat sélectionné. Le certificat est automatiquement ajouté au magasin Java TrustStores par défaut pour authentifier un serveur suivant le protocole SSL.

Le journal suivant de l’application montre que le certificat a bien été chargé.

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## <a name="next-steps"></a>Étapes suivantes

* [Activer le protocole TLS de bout en bout](./how-to-enable-end-to-end-tls.md)
* [Accéder à Config Server et Service Registry](./how-to-access-data-plane-azure-ad-rbac.md)
