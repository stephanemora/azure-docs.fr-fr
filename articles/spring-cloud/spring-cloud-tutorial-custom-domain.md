---
title: Tutoriel – Mapper un domaine personnalisé existant à Azure Spring Cloud
description: Comment mapper un nom DNS (Distributed Name Service) personnalisé existant à Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: ff38f923f7b33c4bc893246970c1e47d33e59269
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780400"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapper un domaine personnalisé existant à Azure Spring Cloud
Le service DNS (Distributed Name Service) est une technique de stockage des noms de nœuds réseau à l’échelle d’un réseau. Ce tutoriel mappe un domaine, tel que www.contoso.com, à l’aide d’un enregistrement CNAME. Il sécurise le domaine personnalisé avec un certificat et montre comment mettre en œuvre le protocole TLS (Transport Layer Security), également connu sous le nom de protocole SSL (Secure Sockets Layer). 

Les certificats chiffrent le trafic web. Ces certificats TLS/SSL peuvent être stockés dans Azure Key Vault. 

## <a name="prerequisites"></a>Prérequis
* Une application déployée dans Azure Spring Cloud (consultez [Démarrage rapide : Lancer une application Azure Spring Cloud existante à partir du portail Azure](spring-cloud-quickstart-launch-app-portal.md) ou utilisez une application existante).
* Un nom de domaine avec un accès au registre DNS pour le fournisseur de domaine, par exemple GoDaddy.
* Un certificat privé (autrement dit, votre certificat auto-signé) d’un fournisseur tiers. Le certificat doit correspondre au domaine.
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

Pour accorder à Azure Spring Cloud l’accès à votre coffre de clés avant d’importer le certificat :
1. Accédez à votre instance de coffre de clés.
1. Dans le volet de navigation gauche, cliquez sur **Stratégies d’accès**.
1. Dans le menu supérieur, cliquez sur **Ajouter une stratégie d’accès**.
1. Renseignez les informations, cliquez sur le bouton **Ajouter**, puis **enregistrez** la stratégie d’accès.

| Autorisation de secret | Autorisation de certificat | Sélectionner le principal |
|--|--|--|
| Obtenir, Lister | Obtenir, Lister | Azure Spring Cloud Domain-Management |

![Importation du certificat 2](./media/custom-dns-tutorial/import-certificate-b.png)

Vous pouvez utiliser Azure CLI pour accorder à Azure Spring Cloud un accès au coffre de clés.

Récupérez l’ID de l’objet à l’aide de la commande suivante.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

À l’aide de la commande suivante, accordez à Azure Spring Cloud un accès en lecture au coffre de clés (remplacez l’ID de l’objet).
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Pour importer le certificat dans Azure Spring Cloud :
1. Accédez à votre instance de service. 
1. Dans le volet de navigation gauche de votre application, sélectionnez **Paramètres TLS/SSL**.
1. Cliquez ensuite sur **Importer un certificat Key Vault**.

    ![Importation d’un certificat](./media/custom-dns-tutorial/import-certificate.png)

Ou bien, vous pouvez utiliser Azure CLI pour importer le certificat :

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Veillez à accorder à Azure Spring Cloud l’accès à votre coffre de clés avant d’exécuter la commande précédente d’importation du certificat. Si vous ne l’avez pas fait, vous pouvez exécuter la commande suivante pour accorder les droits d’accès.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Une fois votre certificat importé, il apparaît dans la liste des **certificats à clé privé**.

![Certificat à clé privée](./media/custom-dns-tutorial/key-certificates.png)

Ou bien, vous pouvez utiliser Azure CLI pour afficher une liste de certificats :

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
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
5. Cliquez sur **Add**.

    ![Ajouter un domaine personnalisé](./media/custom-dns-tutorial/add-custom-domain.png)

Ou bien, vous pouvez utiliser Azure CLI pour ajouter un domaine personnalisé :
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Une application peut avoir plusieurs domaines, mais seul un domaine peut être mappé à une même application. Une fois que vous avez mappé votre domaine personnalisé à l’application, il apparaît dans le tableau du domaine personnalisé.

![Tableau du domaine personnalisé](./media/custom-dns-tutorial/custom-domain-table.png)

Ou bien, vous pouvez utiliser Azure CLI pour afficher une liste de domaines personnalisés :
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Si votre domaine personnalisé présente le libellé **Non sécurisé**, cela signifie que n’est pas encore lié à un certificat SSL. Toute requête HTTPS d’un navigateur à votre domaine personnalisé générera une erreur ou un avertissement.

## <a name="add-ssl-binding"></a>Ajouter une liaison SSL
Dans le tableau du domaine personnalisé, sélectionnez **Ajouter une liaison SSL** comme illustré dans la figure précédente.  
1. Sélectionnez votre **Certificat** ou importez-le.
1. Cliquez sur **Enregistrer**.

    ![Ajouter une liaison SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Ou bien, vous pouvez utiliser Azure CLI pour **ajouter une liaison SSL** :
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Une fois la liaison SSL ajoutée, le domaine passe à un état sécurisé : **Intègre**. 

![Ajouter une liaison SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Appliquer le protocole HTTPS
Par défaut, n’importe qui peut accéder à votre application en HTTP, mais vous pouvez rediriger toutes les requêtes HTTP vers le port HTTPS.

Dans la page de votre application, dans le volet de navigation gauche, sélectionnez **Domaine personnalisé**. Ensuite, définissez **HTTPS uniquement** sur *Vrai*.

![Ajouter une liaison SSL](./media/custom-dns-tutorial/enforce-http.png)

Ou bien, vous pouvez utiliser Azure CLI pour appliquer le protocole HTTPS :
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Une fois l’opération terminée, accédez à une des URL HTTPS qui pointent vers votre application. Notez que les URL HTTP ne fonctionnent pas.

## <a name="see-also"></a>Voir aussi
* [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importer un certificat](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Lancer votre application Spring Cloud en utilisant Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

