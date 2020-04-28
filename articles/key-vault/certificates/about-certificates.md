---
title: À propos des certificats Azure Key Vault - Azure Key Vault
description: Vue d’ensemble de l’interface REST Azure Key Vault et des certificats.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5e014634ecb251f05710de16daee30d72dae619e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685910"
---
# <a name="about-azure-key-vault-certificates"></a>À propos des certificats Azure Key Vault

La prise en charge des certificats Key Vault permet de gérer vos certificats x509 et les comportements suivants :  

-   Permet à un propriétaire de certificat de créer un certificat via un processus de création de Key Vault ou l’importation d’un certificat existant. Concerne à la fois les certificats auto-signés et ceux générés par une autorité de certification.
-   Permet à un propriétaire de certificat Key Vault d’implémenter le stockage sécurisé et la gestion des certificats X509 sans interaction avec des éléments de clé privée.  
-   Permet à un propriétaire de certificat de créer une stratégie qui ordonne à Key Vault de gérer le cycle de vie d’un certificat.  
-   Permet aux propriétaires de certificat de fournir des informations de contact pour les notifications concernant des événements d’expiration du cycle de vie et le renouvellement de certificat.  
-   Prend en charge le renouvellement automatique avec des émetteurs sélectionnés : fournisseurs de certificats X509 de partenaire Key Vault/autorités de certification.

>[!Note]
>Les fournisseurs/autorités non partenaires sont également autorisés, mais ils ne prendront pas en charge la fonctionnalité de renouvellement automatique.

## <a name="composition-of-a-certificate"></a>Composition d’un certificat

Lorsqu’un certificat Key Vault est créé, une clé et un secret adressables sont également créés avec le même nom. La clé Key Vault permet d’exécuter des opérations sur les clés et le secret Key Vault permet de récupérer la valeur du certificat en tant que secret. Un certificat Key Vault contient également des métadonnées de certificat x509 publiques.  

L’identificateur et la version de certificats sont similaires à ceux de clés et de secrets. Une version spécifique d’une clé et d’un secret adressables créés avec la version du certificat Key Vault est disponible dans la réponse de certificat Key Vault.
 
![Les certificats sont des objets complexes](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Clé exportable ou non exportable

Lorsqu’un certificat Key Vault est créé, il peut être récupéré dans le secret adressable avec la clé privée au format PFX ou PEM. La stratégie utilisée pour créer le certificat doit indiquer que la clé est exportable. Si la stratégie indique qu’elle n’est pas exportable, la clé privée ne fait pas partie de la valeur quand elle est récupérée en tant que secret.  

La clé adressable est plus pertinente avec des certificats KV non exportables. Les opérations de la clé KV adressable sont mappées à partir du champ *keyusage* de la stratégie de certificat KV utilisée pour créer le certificat KV.  

Deux types de clés sont pris en charge, *RSA* ou *RSA HSM*, avec les certificats. Exportable est autorisé avec RSA uniquement, il n’est pas pris en charge par RSA HSM.  

## <a name="certificate-attributes-and-tags"></a>Attributs et balises de certificat

En plus des métadonnées de certificat, une clé adressable, un secret adressable et un certificat Key Vault contiennent également des attributs et des balises.  

### <a name="attributes"></a>Attributs

Les attributs de certificat sont reproduits dans des attributs de la clé et du secret adressables créés lors de la création du certificat KV.  

Un certificat Key Vault comprend les attributs suivants :  

-   *enabled* : booléen, facultatif, **true** par défaut. Peut être spécifié pour indiquer si les données du certificat peuvent être récupérées en tant que secret ou utilisables en tant que clé. Également utilisé avec *nbf* et *exp*. Quand une opération se produit entre *nbf* et *exp*, elle est autorisée seulement si enabled a la valeur true. Les opérations en dehors de la fenêtre *nbf* et *exp* sont automatiquement interdites.  

Des attributs supplémentaires en lecture seule sont inclus dans la réponse :

-   *created* : IntDate, indique quand cette version du certificat a été créée.  
-   *updated* : IntDate, indique quand cette version du certificat a été mise à jour.  
-   *exp* : IntDate, contient la valeur de la date d’expiration du certificat X.509.  
-   *nbf* : IntDate, contient la valeur de la date du certificat X.509.  

> [!Note] 
> Si un certificat Key Vault expire, sa clé et son secret adressables ne sont plus utilisables.  

### <a name="tags"></a>Balises

 Dictionnaire de paires clé/valeur spécifié par le client, similaire aux balises dans les clés et les secrets.  

 > [!Note]
> Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet (clés, secrets ou certificats).

## <a name="certificate-policy"></a>Stratégie de certificat

Une stratégie de certificat contient des informations sur la création et la gestion du cycle de vie d’un certificat Key Vault. Lorsqu’un certificat avec une clé privée est importé dans le coffre de clés, une stratégie par défaut est créée en lisant le certificat x509.  

Quand un certificat Key Vault est créé de zéro, une stratégie doit être fournie. La stratégie spécifie comment créer cette version du certificat Key Vault ou la prochaine version. Lorsqu’une stratégie a été définie, des opérations de création successives ne sont pas nécessaires pour les prochaines versions. Il n’existe qu’une seule instance d’une stratégie pour toutes les versions d’un certificat Key Vault.  

Globalement, une stratégie de certificat contient les informations suivantes :  

-   Propriétés du certificat X.509 : contient le nom du sujet, les autres noms du sujet et d’autres propriétés utilisées pour créer une demande de certificat X.509.  
-   Propriétés des clés : contient les champs type de clé, longueur de clé, exportable et réutiliser la clé. Ces champs indiquent au coffre de clés comment générer une clé.  
-   Propriétés du secret : propriétés du secret comme le type de contenu de secret adressable pour générer la valeur du secret, pour récupérer le certificat en tant que secret.  
-   Actions de la durée de vie : actions de la durée de vie du certificat KV. Chaque action de la durée de vie contient :  

     - Déclencheur : spécifié en jours avant l’expiration ou en pourcentage de la durée de vie  

     - Action : spécifie le type d’action, *emailContacts* ou *autoRenew*  

-   Émetteur : paramètres relatifs à l’émetteur de certificat à utiliser pour émettre des certificats X.509.  
-   Attributs de stratégie : attributs liés à la stratégie  

### <a name="x509-to-key-vault-usage-mapping"></a>Mappage d’utilisation de clé X509 avec Key Vault

Le tableau suivant représente le mappage de la stratégie d’utilisation de la clé x509 avec des opérations sur les clés effectives d’une clé créée lors de la création d’un certificat Key Vault.

|**Indicateurs d’utilisation de la clé X509**|**Opérations sur la clé Key Vault**|**Comportement par défaut**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| N/A |
|DecipherOnly|decrypt| N/A  |
|DigitalSignature|sign, verify| Valeur par défaut de Key Vault sans spécification de l’utilisation au moment de la création du certificat | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|sign, verify|N/A|
|KeyEncipherment|wrapKey, unwrapKey| Valeur par défaut de Key Vault sans spécification de l’utilisation au moment de la création du certificat | 
|NonRepudiation|sign, verify| N/A |
|crlsign|sign, verify| N/A |

## <a name="certificate-issuer"></a>Émetteur de certificat

Un objet certificat Key Vault conserve une configuration utilisée pour communiquer avec un fournisseur de l’émetteur de certificat sélectionné pour demander des certificats x509.  

-   Partenaires Key Vault avec les fournisseurs de l’émetteur de certificat suivants pour les certificats TLS/SSL

|**Nom du fournisseur**|**Emplacements**|
|----------|--------|
|DigiCert|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|
|GlobalSign|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|

Avant de pouvoir créer un émetteur de certificat dans un coffre de clés, les étapes préliminaires 1 et 2 suivantes doivent être exécutées.  

1. Intégrer aux fournisseurs d’autorités de certification  

    -   Un administrateur de l’organisation doit intégrer sa société (par ex. Contoso) à au moins un fournisseur d’autorité de certification.  

2. L’administrateur crée des informations d’identification du demandeur pour Key Vault afin d’inscrire (et de renouveler) des certificats TLS/SSL  

    -   Fournit la configuration à utiliser pour créer un objet émetteur du fournisseur dans le coffre de clés  

Pour plus d’informations sur la création d’objets Émetteur à partir du portail Certificats, consultez le [blog de certificats Key Vault](https://aka.ms/kvcertsblog)  

Key Vault permet de créer plusieurs objets émetteurs avec une configuration de fournisseur de l’émetteur différente. Lorsqu’un objet émetteur est créé, son nom peut être référencé dans une ou plusieurs stratégies de certificat. Le référencement de l’objet émetteur indique à Key Vault d’utiliser la configuration telle que spécifiée dans l’objet émetteur lors de la demande du certificat x509 à partir du fournisseur d’autorité de certification lors de la création ou du renouvellement du certificat.  

Les objets émetteur sont créés dans le coffre et ne peuvent être utilisés qu’avec des certificats KV dans le même coffre.  

## <a name="certificate-contacts"></a>Contacts du certificat

Les contacts du certificat contiennent des informations de contact pour l’envoi de notifications déclenchées par des événements de durée de vie de certificat. Les informations de contact sont partagées par tous les certificats dans le coffre de clés. Une notification est envoyée à tous les contacts spécifiés pour un événement pour n’importe quel certificat dans le coffre de clés.  

Si la stratégie d’un certificat est définie sur le renouvellement automatique, une notification est alors envoyée pour les événements suivants.  

- Avant le renouvellement du certificat
- Après le renouvellement du certificat, indiquant si le certificat a été renouvelé, ou si une erreur s’est produite, nécessitant un renouvellement manuel du certificat.  

  Quand la stratégie d’un certificat est définie pour un renouvellement manuel (e-mail uniquement), une notification est envoyée lorsqu’il est temps de renouveler le certificat.  

## <a name="certificate-access-control"></a>Contrôle d’accès aux certificats

 Le contrôle d’accès pour les certificats est géré par Key Vault et fourni par le coffre de clés qui contient ces certificats. La stratégie de contrôle d’accès pour les certificats est différente des stratégies de contrôle d’accès pour les clés et les secrets dans un même coffre de clés. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les certificats afin de maintenir une segmentation et une gestion des certificats appropriées au scénario.  

 Les autorisations suivantes peuvent être utilisées, par principal, dans l’entrée de contrôle d’accès aux secrets sur un coffre de clés, et reflètent précisément les opérations autorisées sur un objet secret :  

- Autorisations pour les opérations de gestion des certificats
  - *get* : obtenir la version actuelle ou n’importe quelle version d’un certificat 
  - *list* : lister les certificats actuels ou les versions d’un certificat  
  - *update* : mettre à jour un certificat
  - *create* : créer un certificat Key Vault
  - *import* : importer les éléments d’un certificat dans un certificat Key Vault
  - *delete* : supprimer un certificat, sa stratégie et toutes ses versions  
  - *recover* : récupérer un certificat supprimé
  - *backup* : sauvegarder un certificat dans un coffre de clés
  - *restore* : restaurer un certificat sauvegardé sur un coffre de clés
  - *managecontacts* : gérer les contacts du certificat Key Vault  
  - *manageissuers* : gérer les autorités/émetteurs du certificat Key Vault
  - *getissuers* : obtenir les autorités/émetteurs d’un certificat
  - *listissuers* : lister les autorités/émetteurs d’un certificat  
  - *setissuers* : créer ou mettre à jour les autorités/émetteurs d’un certificat Key Vault  
  - *deleteissuers* : supprimer les autorités/émetteurs d’un certificat Key Vault  
 
- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) un certificat supprimé

Pour plus d’informations, voir [Informations de référence sur les opérations liées aux certificats dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Étapes suivantes

- [À propos de Key Vault](../general/overview.md)
- [Présentation des clés, des secrets et des certificats](../general/about-keys-secrets-certificates.md)
- [À propos des clés](../keys/about-keys.md)
- [À propos des secrets](../secrets/about-secrets.md)
- [Authentification, requêtes et réponses](../general/authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)