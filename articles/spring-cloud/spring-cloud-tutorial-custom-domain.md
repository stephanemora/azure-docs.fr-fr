---
title: Tutoriel – Mapper un domaine personnalisé existant à Azure Spring Cloud
description: Comment mapper un nom DNS (Distributed Name Service) personnalisé existant à Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176834"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapper un domaine personnalisé existant à Azure Spring Cloud
Le service DNS (Distributed Name Service) est une technique de stockage des noms de nœuds réseau à l’échelle d’un réseau. Ce tutoriel mappe un domaine, tel que www.contoso.com, à l’aide d’un enregistrement CNAME. Il sécurise le domaine personnalisé avec un certificat et montre comment mettre en œuvre le protocole TLS (Transport Layer Security), également connu sous le nom de protocole SSL (Secure Sockets Layer). 

Les certificats chiffrent le trafic web. Ces certificats TLS/SSL peuvent être stockés dans Azure Key Vault. 

## <a name="prerequisites"></a>Prérequis
* Une application déployée dans Azure Spring Cloud (consultez [Démarrage rapide : Lancer une application Azure Spring Cloud existante à partir du portail Azure](spring-cloud-quickstart-launch-app-portal.md) ou utilisez une application existante).
* Un nom de domaine avec un accès au registre DNS pour le fournisseur de domaine, par exemple GoDaddy.
* Un certificat privé émanant d’un fournisseur tiers. Le certificat doit correspondre au domaine.
* Une instance déployée d’[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

## <a name="import-certificate"></a>Importation d’un certificat 
La procédure d’importation d’un certificat exige la présence du fichier PEM ou PFX encodé sur le disque, et vous devez disposer de la clé privée. 

Pour charger votre certificat dans le coffre de clés :
1. Accédez à votre instance de coffre de clés.
1. Dans le volet de navigation gauche, cliquez sur **Certificats**.
1. Dans le menu du haut, cliquez sur **Générer/importer**.
1. Dans la boîte de dialogue **Créer un certificat**, en dessous de **Méthode de création de certificat**, sélectionnez `Import`.
1. Sous **Charger le fichier de certificat**, accédez à emplacement du certificat et sélectionnez-le.
1. Sous **Mot de passe**, entrez la clé privée de votre certificat.
1. Cliquez sur **Créer**.

![Importation du certificat 1](./media/custom-dns-tutorial/import-certificate-a.png)

Pour importer le certificat dans Azure Spring Cloud :
1. Accédez à votre instance de service. 
1. Dans le volet de navigation gauche de votre application, sélectionnez **Paramètres TLS/SSL**.
1. Cliquez ensuite sur **Importer un certificat Key Vault**.

![Importation d’un certificat](./media/custom-dns-tutorial/import-certificate.png)

Une fois votre certificat importé, il apparaît dans la liste des **certificats à clé privé**.

![Certificat à clé privée](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
> Pour sécuriser un domaine personnalisé avec ce certificat, vous devez quand même lier le certificat à un domaine spécifique. Suivez les étapes détaillées dans la section **Ajouter une liaison SSL** de ce document.

## <a name="add-custom-domain"></a>Ajouter un domaine personnalisé
Vous pouvez utiliser un enregistrement CNAME pour mapper un nom DNS personnalisé à Azure Spring Cloud. 

> [!NOTE] 
> L’enregistrement A n’est pas pris en charge. 

### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME
Accédez à votre fournisseur DNS et ajoutez un enregistrement CNAME pour mapper votre domaine à <nom_service>.azuremicroservices.io. Ici <nom_service> est le nom de votre instance Azure Spring Cloud. Nous prenons en charge les domaines et sous-domaines génériques. Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant : 

![Page Enregistrements DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapper votre domaine personnalisé à l’application Azure Spring Cloud
Si vous n’avez pas d’application dans Azure Spring Cloud, suivez les instructions du [Démarrage rapide : Lancer une application Azure Spring Cloud existante à partir du portail Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Accédez à la page de l’application.

1. Sélectionner **Domaine personnalisé**.
2. Ensuite, choisissez **Ajouter un domaine personnalisé**. 

![Domaine personnalisé](./media/custom-dns-tutorial/custom-domain.png)

3. Tapez le nom de domaine complet pour lequel vous avez ajouté un enregistrement CNAME, tel que www.contoso.com. Vérifiez que le type d’enregistrement de nom d’hôte défini est CNAME (<nom_service>.azuremicroservices.io).
4. Cliquez sur **Valider** pour activer le bouton **Ajouter**.
5. Cliquez sur **Ajouter**.

![Ajouter un domaine personnalisé](./media/custom-dns-tutorial/add-custom-domain.png)

Une application peut avoir plusieurs domaines, mais seul un domaine peut être mappé à une même application. Une fois que vous avez mappé votre domaine personnalisé à l’application, il apparaît dans le tableau du domaine personnalisé.

![Tableau du domaine personnalisé](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Si votre domaine personnalisé présente le libellé **Non sécurisé**, cela signifie que n’est pas encore lié à un certificat SSL. Toute requête HTTPS d’un navigateur à votre domaine personnalisé générera une erreur ou un avertissement.

## <a name="add-ssl-binding"></a>Ajouter une liaison SSL
Dans le tableau du domaine personnalisé, sélectionnez **Ajouter une liaison SSL** comme illustré dans la figure précédente.  
1. Sélectionnez votre **Certificat** ou importez-le.
1. Cliquez sur **Enregistrer**.

![Ajouter une liaison SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Une fois la liaison SSL ajoutée, le domaine passe à un état sécurisé : **Intègre**. 

![Ajouter une liaison SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Appliquer le protocole HTTPS
Par défaut, n’importe qui peut accéder à votre application en HTTP, mais vous pouvez rediriger toutes les requêtes HTTP vers le port HTTPS.

Dans la page de votre application, dans le volet de navigation gauche, sélectionnez **Domaine personnalisé**. Ensuite, définissez **HTTPS uniquement** sur *Vrai*.

![Ajouter une liaison SSL](./media/custom-dns-tutorial/enforce-http.png)

Une fois l’opération terminée, accédez à une des URL HTTPS qui pointent vers votre application. Notez que les URL HTTP ne fonctionnent pas.

## <a name="see-also"></a>Voir aussi
* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importer un certificat](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Lancer votre application Spring Cloud en utilisant Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

