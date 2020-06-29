---
title: S’authentifier auprès d’Azure Key Vault
description: Apprendre à s’authentifier auprès d’Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: aaaeec3a20088cad447ab0e8034d12ef3268fae3
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793228"
---
# <a name="authenticate-to-azure-key-vault"></a>S’authentifier auprès d’Azure Key Vault

## <a name="overview"></a>Vue d’ensemble

Azure Key Vault est une solution de gestion de secrets qui vous permet de centraliser le stockage des secrets d’applications et de contrôler leur distribution. Azure Key Vault élimine la nécessité de stocker les informations d’identification dans les applications. Votre application peut s’authentifier auprès du coffre de clés pour récupérer les informations d’identification nécessaires. Ce document aborde les principes de base de l’authentification auprès d’un coffre de clés.

Ce document vous aide à comprendre le fonctionnement de l’authentification de coffre de clés. Ce document explique le déroulement de l’authentification, vous montre comment accorder un accès à votre coffre de clés et contient un tutoriel pour récupérer un secret stocké dans le coffre de clés à partir d’un exemple d’application Python.

Ce document aborde les points suivants :

* Concepts clés
* Inscription d’un principal de sécurité
* Compréhension du déroulement de l’authentification Key Vault
* Accorder un accès à Key Vault à un principal de service
* Tutoriel (Python)

## <a name="key-concepts"></a>Concepts clés

### <a name="azure-active-directory-concepts"></a>Concepts Azure Active Directory

* Azure Active Directory (AAD) – Azure Active Directory (Azure AD) est le service de gestion des accès et des identités basé sur le cloud de Microsoft. Il permet à vos employés de se connecter et d’accéder aux ressources

* Définition de rôle – Une définition de rôle est une collection d’autorisations.  AAD possède des rôles standard (Propriétaire, Contributeur ou Lecteur) qui contiennent des niveaux d’autorisations permettant d’effectuer des opérations de lecture, d’écriture et de suppression sur une ressource Azure. Les rôles peuvent aussi être des définitions personnalisées créées par des utilisateurs disposant d’autorisations granulaires spécifiques.

* Inscription d’application – Quand vous inscrivez une application Azure AD, deux objets sont créés dans votre locataire Azure AD : un objet application et un objet principal de service. Considérez l’objet application comme la représentation globale de votre application pour une utilisation dans tous les locataires et le principal de service comme la représentation locale pour une utilisation dans un locataire spécifique.

### <a name="security-principal-concepts"></a>Concepts du principal de sécurité

* Principal de sécurité – Un principal de sécurité est un objet qui représente un utilisateur, un groupe, un principal de service ou une identité managée qui demande un accès à des ressources Azure.

* Utilisateur : personne disposant d’un profil dans Azure Active Directory.

* Groupe : ensemble d’utilisateurs créés dans Azure Active Directory. Lorsque vous attribuez un rôle à un groupe, vous l’attribuez également à tous les utilisateurs de ce groupe.

* Principal de service : identité de sécurité utilisée par des applications ou des services permettant d’accéder aux ressources Azure spécifiques. Vous pouvez le considérer comme une identité d’utilisateur (nom d’utilisateur/mot de passe ou certificat) pour une application.

* Identité managée – Identité dans Azure Active Directory qui est gérée automatiquement par Azure.

* ID d’objet (ID client) – Identificateur unique généré par Azure AD qui est lié à un principal de service pendant son provisionnement initial.

## <a name="security-principal-registration"></a>Inscription d’un principal de sécurité

1. L’administrateur inscrit un utilisateur ou une application (principal de service) dans Azure Active Directory.

2. L’administrateur crée un coffre de clés Azure et configure des stratégies d’accès (ACL).

3. (Facultatif) L’administrateur configure le pare-feu Azure Key Vault.

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Comprendre le déroulement de l’authentification Key Vault

1. Un principal de service effectue un appel pour s’authentifier auprès d’AAD, ce qui peut se produire de différentes façons :
    * Un utilisateur peut se connecter au portail Azure avec un nom d’utilisateur et un mot de passe.
    * Une application utilise un ID client et présente une clé secrète client ou un certificat client à AAD.
    * Une ressource Azure telle qu’une machine virtuelle dispose d’une identité MSI qui lui a été affectée et contacte le point de terminaison REST IMDS pour obtenir un jeton d’accès.

2. Si l’authentification à AAD réussit, le principal de service se voit accorder un jeton OAuth.
3. Le principal de service adresse un appel à Key Vault.
4. Le pare-feu Azure Key Vault détermine si l’appel doit être autorisé.
    * Scénario 1 : le pare-feu Key Vault est désactivé, le point de terminaison public (URI) du coffre de clés est accessible depuis l’Internet public. L’appel est autorisé.
    * Scénario 2 : l’appelant est un service approuvé par Azure Key Vault. Certains services Azure peuvent contourner le pare-feu Key Vault si l’option est sélectionnée. [Liste des services approuvés par Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scénario 3 : l’appelant est listé dans le pare-feu Azure Key Vault par adresse IP, réseau virtuel ou point de terminaison de service.
    * Scénario 4 : l’appelant peut accéder à Azure Key Vault via une connexion de liaison privée configurée.
    * Scénario 5 : l’appelant n’est pas autorisé et une réponse d’interdiction est retournée.
5. Key Vault adresse un appel à AAD pour valider le jeton d’accès du principal de service.
6. Key Vault vérifie si le principal de service dispose d’autorisations de stratégie d’accès suffisantes pour effectuer l’opération demandée (en l’occurrence, une opération d’obtention de secret).
7. Key Vault communique le secret au principal de service.

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Accorder un accès à Key Vault à un principal de service

1. Créez un principal de service si vous n’en possédez pas déjà un. [Créer un principal de service](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Ajoutez une attribution de rôle à votre principal de service dans les paramètres de gestion des identités et des accès (IAM) d’Azure Key Vault. Vous pouvez ajouter les rôles préattribués Propriétaire, Contributeur ou Lecteur. Vous pouvez aussi créer des rôles personnalisés pour votre principal de service. Vous devez suivre le principe du moindre privilège et accorder uniquement l’accès minimal nécessaire à votre principal de service. 
3.  Configurez le pare-feu Key Vault. Vous pouvez laisser le pare-feu Key Vault désactivé et accorder l’accès depuis l’Internet public (moins sécurisé, plus facile à configurer). Vous pouvez aussi limiter l’accès à des plages d’adresses IP, des points de terminaison de service, des réseaux virtuels ou des points de terminaison privés spécifiques (plus sécurisé).
4.  Ajoutez une stratégie d’accès pour votre principal de service ; il s’agit de la liste des opérations que votre principal de service peut effectuer sur le coffre de clés. Vous devez utiliser le principe du moindre privilège et limiter les opérations que peut effectuer le principal de service. Cependant, si vous ne fournissez pas d’autorisations suffisantes, votre principal de service se verra refuser l’accès.

## <a name="tutorial"></a>Tutoriel

Dans ce tutoriel, vous allez découvrir comment configurer un principal de service de telle sorte qu’il s’authentifie auprès d’un coffre de clés et récupère un secret. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Première partie :  Créer un principal de service sur le portail Azure

1. Connectez-vous au portail Azure.
1. Recherchez Azure Active Directory.
1. Cliquez sur l’onglet « Inscriptions des applications ».
1. Cliquez sur « + Nouvelle inscription ».
1. Créez un nom pour le principal de service.
1. Sélectionnez Inscrire.

À ce stade, vous avez inscrit un principal de service. Vous pouvez le vérifier en sélectionnant « Inscriptions des applications ». Un GUID d’ID client est maintenant attribué à votre principal de service ; il s’agit en quelque sorte du « nom d’utilisateur » de votre principal de service. À présent, vous devez créer le « mot de passe » de votre principal de service ; vous pouvez utiliser une clé secrète client ou un certificat client. Notez que l’authentification avec une clé secrète client n’est pas sécurisée et que vous ne devez l’employer qu’à des fins de test. Ce tutoriel vous montre comment utiliser un certificat client.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Deuxième partie : Créer un certificat client pour le principal de service

1. Créer un certificat

    * Option 1 : Créez un certificat en utilisant [OpenSSL](https://www.openssl.org/) (à des fins de test uniquement ; n’utilisez pas de certificats autosignés en production)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Option n°2 : Créez un certificat à l’aide de Key Vault. [Créer un certificat dans Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Téléchargez le certificat au format PEM.
1. Connectez-vous au portail Azure et accédez à Azure Active Directory.
1. Cliquez sur « Inscriptions des applications ».
1. Sélectionnez le principal de service que vous avez créé dans la première partie.
1. Cliquez sur l’onglet « Certificats et secrets » de votre principal de service.
1. Chargez le certificat à l’aide du bouton « Charger le certificat ».

### <a name="part-3-configure-an-azure-key-vault"></a>Troisième partie : Configurer un coffre de clés Azure

1. Créez un coffre de clés Azure ([lien](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)).

2. Configurez des autorisations de gestion des identités et des accès (IAM) Key Vault.
    1. Accédez à votre coffre de clés.
    1. Sélectionnez l’onglet « Contrôle d’accès (IAM) ».
    1. Cliquez sur Ajouter une attribution de rôle.
    1. Sélectionnez le rôle « Contributeur » dans la liste déroulante.
    1. Entrez le nom ou l’ID client du principal de service que vous avez créé.
    1. Cliquez sur « Afficher les attributions de rôles » pour vérifier que votre principal de service est listé.

3. Configurez des autorisations de stratégie d’accès de coffre de clés.
    1. Accédez à votre coffre de clés.
    1. Sélectionnez l’onglet « Stratégies d’accès » sous « Paramètres ».
    1. Sélectionnez le lien « Ajouter une stratégie d’accès ».
    1. Sous la liste déroulante Autorisations du secret, cochez les autorisations « Obtenir » et « Lister ».
    1. Sélectionnez votre principal de service par nom ou ID client.
    1. Sélectionnez « Ajouter ».
    1. Sélectionnez « Enregistrer ».

4. Créez un secret dans votre coffre de clés.
    1. Accédez à votre coffre de clés.
    1. Cliquez sur l’onglet « Secrets » en dessous de Paramètres.
    1. Cliquez sur « + Générer/Importer ».
    1. Créez un nom pour le secret (dans cet exemple, nous le nommerons « test »).
    1. Créez une valeur pour le secret (nous définirons ici la valeur « password123 »).

Désormais, quand vous exécuterez le code sur votre ordinateur local, vous pourrez vous authentifier auprès du coffre de clés en obtenant un jeton d’accès en présentant l’ID client et un chemin d’accès au certificat.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Partie 4 : Récupérer le secret à partir de votre coffre de clés Azure dans une application (Python)

Utilisez l’exemple de code suivant pour tester si votre application peut récupérer un secret à partir de votre coffre de clés en utilisant le principal de service que vous avez configuré. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>Étapes suivantes

1. Découvrez comment résoudre les erreurs d’authentification de coffre de clés. [Guide de résolution des problèmes liés à Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
