---
title: Architecture d’OPC Vault – Azure | Microsoft Docs
description: Architecture du service de gestion de certificats OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995818"
---
# <a name="opc-vault-architecture"></a>Architecture d’OPC Vault

Cet article offre une vue d’ensemble du **microservice OPC Vault** et du **module IoT Edge OPC Vault**.

## <a name="overview"></a>Vue d'ensemble

Les applications OPC UA utilisent des certificats d’instance d’application pour assurer une sécurité au niveau de l’application. Une connexion sécurisée est établie à l’aide du chiffrement asymétrique, pour lequel les certificats d’application fournissent la paire de clés publique et privée. Les certificats peuvent être auto-signés ou signés par une autorité de certification.

Une application OPC UA dispose d’une liste de certificats approuvés qui représentent les applications de confiance. Ces certificats peuvent être auto-signés, signés par une autorité de certification ou être eux-mêmes une autorité de certification racine ou une sous-autorité de certification. Si un certificat approuvé fait partie d’une chaîne de certificats plus grande, l’application fait confiance à tous les certificats liés au certificat dans la liste de confiance, à condition que la chaîne de certificats complète puisse être validée.

La principale différence entre l’approbation de certificats auto-signés et l’approbation d’un certificat d’autorité de certification est l’effort d’installation nécessaire pour déployer et maintenir la confiance et l’effort supplémentaire pour héberger une autorité de certification propre à une entreprise. 

Pour distribuer l’approbation de certificats auto-signés pour n serveurs avec une seule application cliente, tous les certificats d’application des n serveurs doivent être installés dans la liste de confiance de l’application cliente et le certificat de l’application cliente doit être installé dans toutes les listes de confiance des applications serveur. Ce travail administratif assez fastidieux le devient plus encore quand la durée de vie des certificats doit être prise en compte et que les certificats sont renouvelés.

L’utilisation d’une autorité de certification propre à l’entreprise simplifie considérablement la gestion des approbations en présence de plusieurs serveurs et clients. Dans ce cas, l’administrateur génère un certificat d’instance d’application signé par une autorité de certification une fois pour chaque client et serveur utilisé. Par ailleurs, le certificat d’autorité de certification est installé dans chaque liste de confiance d’applications, sur tous les serveurs et clients. Avec cette approche, seuls les certificats arrivés à expiration doivent être renouvelés et remplacés pour les applications concernées.

Le service de gestion de certificats OPC UA d’Azure Industrial IoT est la solution à laquelle nous avons recours pour gérer une autorité de certification propre à une entreprise pour les applications OPC UA basées sur le microservice OPC Vault.

OPC Vault propose un microservice destiné à héberger une autorité de certification propre à une entreprise dans un cloud sécurisé, adossé à des services sécurisés Azure AD avec Azure Key Vault et les modules de sécurité matérielle associés, Cosmos DB et éventuellement IoT Hub en tant que magasin d’applications.

Le microservice OPC Vault a été conçu pour prendre en charge un workflow basé sur des rôles, où le personnel OT demande des certificats d’application signés et où les administrateurs de sécurité et les approbateurs dotés de droits de signature dans Azure Key Vault approuvent ou rejettent ces demandes.

Pour des raisons de compatibilité avec les solutions OT GDS OPC UA existantes, les services incluent la prise en charge d’un module de périphérie adossé au microservice OPC Vault, qui implémente l’interface de *gestion de certificats et de serveur de détection globale OPC UA* pour distribuer les certificats et les listes de confiance conformément à la partie 12 de la spécification. Toutefois, à notre connaissance, l’utilisation de cette interface de serveur GDS n’est pas encore très répandue et ses fonctionnalités sont encore limitées (rôle de lecteur). [Nous améliorerons l’expérience à la demande des clients (*)](#yet-unsupported-features).

## <a name="architecture"></a>Architecture

L’architecture s’appuie sur le microservice OPC Vault avec un module IoT Edge OPC Vault pour le réseau de fabrique et un exemple d’expérience utilisateur web pour contrôler le workflow :

![Architecture d’OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microservice OPC Vault

Le microservice OPC Vault est constitué des interfaces suivantes pour implémenter le workflow de distribution et de gestion d’une autorité de certification propre à une entreprise pour les applications OPC UA :

### <a name="application"></a>Application 
- Une « application OPC UA » peut être un serveur, un client ou les deux à la fois. Dans ce cas, OPC Vault fait office d’autorité d’inscription d’applications. 
- À côté des opérations de base d’inscription, de mise à jour et de désinscription d’applications, il existe aussi des interfaces permettant de rechercher des applications et de les interroger avec des expressions de recherche. 
- Pour être traitées, les demandes de certificat doivent faire référence à une application valide. Un certificat signé est alors émis avec toutes les extensions propres à OPC UA. 
- Le service d’application est adossé à une base de données CosmosDB ou au [registre d’appareils OpcTwin (*)](#yet-unsupported-features), selon la configuration du client.

### <a name="certificate-group"></a>Groupe de certificats
- Un groupe de certificats est une entité qui stocke un certificat d’autorité de certification racine ou de sous-autorité de certification, avec la clé privée destinée à signer les certificats. 
- La longueur de clé RSA, la longueur du hachage SHA-2 et les durées de vie peuvent être configurées pour l’autorité de certification émettrice et les certificats d’application signés. 
- Un même service peut héberger plusieurs groupes en prévision d’extensions futures avec des groupes de certificats d’algorithme https, user ou ECC [(*)](#yet-unsupported-features). 
- Les certificats d’autorité de certification sont stockés dans Azure Key Vault, qui s’appuie sur des modules de sécurité matérielle (HSM) FIPS 140-2 de niveau 2. La clé privée ne quitte jamais le stockage sécurisé, car la signature est assurée par une opération Key Vault sécurisée par Azure AD. 
- Les certificats d’autorité de certification peuvent être renouvelés au fil du temps et rester stockés en lieu sûr grâce à l’historique Key Vault. 
- La liste de révocation de chaque certificat d’autorité de certification est aussi stockée dans Key Vault en tant que secret. Une fois qu’une application est désinscrite, le certificat d’application est également révoqué dans la liste de révocation de certificats par un administrateur.
- La révocation de certificats par lots et la révocation de certificats uniques sont toutes deux prises en charge.

### <a name="certificate-request"></a>Demande de certificat
Une demande de certificat implémente le workflow chargé de générer une nouvelle paire de clés ou un certificat signé en utilisant une « demande de signature de certificat » pour une application OPC UA. 
- La demande est stockée dans une base de données avec des informations connexes comme l’Objet ou une demande de signature de certificat ainsi qu’une référence à l’application OPC UA. 
- La logique métier du service valide la demande par rapport aux informations stockées dans la base de données d’application. Par exemple, l’URI d’application contenu dans la base de données doit correspondre à l’URI d’application figurant dans la demande de signature de certificat.
- Un administrateur de la sécurité disposant de droits de signature (rôle d’approbateur) approuve ou rejette la demande. Si la demande est approuvée, une nouvelle paire de clés et/ou un certificat signé sont générés. La nouvelle clé privée est stockée de manière sécurisée dans KeyVault, alors que le nouveau certificat public signé est stocké dans la base de données des demandes de certificat.
- Le demandeur peut consulter l’état de la demande jusqu’à ce qu’elle soit approuvée ou révoquée. Si la demande a été approuvée, la clé privée et le certificat peuvent être téléchargés et installés dans le magasin de certificats de l’application OPC UA.
- Le demandeur peut maintenant accepter la demande de suppression des informations inutiles de la base de données des demandes. 

Pendant la durée de vie d’un certificat signé, il peut arriver qu’une application soit supprimée ou qu’une clé devienne compromise. Dans ce cas, le gestionnaire d’une autorité de certification peut :
- Supprimer une application, ce qui a aussi pour effet de supprimer toutes les demandes de certificat en attente et approuvées de l’application. 
- Une autre option consiste à supprimer une seule demande de certificat si le renouvellement ou la compromission concerne une seule clé.
- Les demandes de certificat approuvées et acceptées compromises sont maintenant marquées comme étant supprimées.
- Un gestionnaire peut procéder à un renouvellement régulier de la liste de révocation de certificats de l’autorité de certification émettrice. Au moment du renouvellement, toutes les demandes de certificat supprimées sont révoquées et les numéros de série des certificats sont ajoutés à la liste de révocation de la liste de révocation de certificats. Les demandes de certificat révoquées sont marquées comme étant révoquées.
- Dans les situations d’urgence, les demandes de certificat uniques peuvent aussi être révoquées.
- Enfin, les listes de révocation de certificats mises à jour peuvent être distribuées aux clients et serveurs OPC UA participants.

Pour plus d’informations sur l’API de microservice OPC Vault, consultez la documentation Swagger du microservice.

## <a name="opc-vault-iot-edge-module"></a>Module IoT Edge OPC Vault
Pour prendre en charge le serveur de découverte globale d’un réseau de fabrique, le module OPC Vault peut être déployé en périphérie, s’exécuter en tant qu’application .Net Core locale ou être démarrée dans un conteneur Docker. Faute de prise en charge de l’authentification Auth2 dans la pile .Net Standard d’OPC UA, les fonctionnalités du module de périphérie OPC Vault sont limitées à un rôle de lecteur, car un utilisateur ne peut pas faire l’objet d’un emprunt d’identité entre le module de périphérie et le microservice utilisant l’interface standard GDS d’OPC UA. À ce stade, seules sont autorisées les opérations qui ne nécessitent pas de rôle d’auteur, d’administrateur ou d’approbateur [(*)](#yet-unsupported-features). 

## <a name="yet-unsupported-features"></a>Fonctionnalités pas encore prises en charge

**(*)** pas encore pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que l’architecture OPC Vault n’a plus de secrets pour vous, voici l’étape suivante que nous suggérons :

> [!div class="nextstepaction"]
> [Générer et déployer OPC Vault](howto-opc-vault-deploy.md)
