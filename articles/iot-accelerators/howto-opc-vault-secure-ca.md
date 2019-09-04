---
title: Comment exécuter le service de gestion de certificats OPC Vault en toute sécurité – Azure | Microsoft Docs
description: Décrit comment exécuter le service de gestion de certificats OPC Vault en toute sécurité dans Azure et fournit d’autres instructions de sécurité à prendre en compte.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997658"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>Comment exécuter le service de gestion de certificats OPC Vault en toute sécurité

Cet article explique comment exécuter le service de gestion de certificats OPC Vault en toute sécurité dans Azure et fournit d’autres instructions de sécurité à prendre en compte.

## <a name="roles"></a>contrôleur

### <a name="trusted-and-authorized-roles"></a>Rôles approuvés et autorisés

Le microservice OPC Vault est configuré pour autoriser des rôles distincts à accéder aux différentes parties du service.

> [!IMPORTANT]
> Pendant le déploiement, le script ajoute uniquement l’utilisateur qui exécute le script de déploiement en tant qu’utilisateur pour tous les rôles.
> Cette attribution de rôle doit être examinée pour un déploiement de production et reconfigurée correctement en suivant les instructions ci-dessous.
> Cette tâche nécessite une attribution manuelle de rôles et de services dans le portail Applications d’entreprise Azure AD.

### <a name="certificate-management-service-roles"></a>Rôles de service de gestion des certificats

Le microservice OPC Vault définit les rôles suivants :

- **Lecteur** : Par défaut, tout utilisateur authentifié dans le locataire dispose d’un accès en lecture. 
  - Accès en lecture aux applications et aux demandes de certificat. Peut répertorier et interroger les applications et demandes de certificat. Les informations de découverte d’appareil et les certificats publics sont également accessibles avec un accès en lecture.
- **Rédacteur** : Le rôle Rédacteur est attribué à un utilisateur appelé à ajouter des autorisations d’écriture pour certaines tâches. 
  - Accès en lecture/écriture aux applications et aux demandes de certificat. Peut inscrire, mettre à jour et annuler l’inscription des applications. Peut créer des demandes de certificat et obtenir des clés privées et des certificats approuvés. Peut également supprimer des clés privées.
- **Approbateur** : Le rôle Approbateur est attribué à un utilisateur appelé à approuver ou à rejeter des demandes de certificat. Il n’inclut aucun autre rôle.
  - Pour être en mesure de signer les certificats, en plus du rôle Approbateur pour accéder au microservice OPC Vault, l’utilisateur doit également disposer de l’autorisation de signature de clé dans Key Vault.
  - Les rôles Rédacteur et Approbateur doit être attribué à des utilisateurs distincts.
  - Le rôle principal de l’Approbateur est l’approbation de la génération et du rejet des demandes de certificat.
- **Administrateur** : Le rôle Administrateur est attribué à un utilisateur appelé à gérer les groupes de certificats. Le rôle ne prend pas en charge le rôle Approbateur, mais il comprend le rôle Rédacteur.
  - L’administrateur peut gérer les groupes de certificats, modifier la configuration et révoquer des certificats d’application en émettant une nouvelle liste de révocation de certificats.
  - Idéalement, les rôles Rédacteur, Approbateur et Administrateur sont attribués à des utilisateurs distincts. Pour renforcer la sécurité, un utilisateur auquel est attribué le rôle Approbateur ou Administrateur a également besoin de l’autorisation de signature de clé dans keyVault pour émettre des certificats ou renouveler un certificat d’autorité de certification émettrice.
  - En plus du rôle d’administration du microservice, le rôle inclut notamment aussi les attributions suivantes :
    - Administration de l’implémentation des pratiques de sécurité de l’autorité de certification.
    - Gestion de la génération, de la révocation et de la suspension des certificats. 
    - Gestion du cycle de vie des clés de chiffrement (par exemple, renouvellement des clés de l’autorité de certification émettrice).
    - Installation, configuration et maintenance des services qui utilisent l’autorité de certification.
    - Conduite au quotidien des services. 
    - Sauvegarde et récupération de base de données et d’autorité de certification.

### <a name="other-role-assignments"></a>Autres attributions de rôles

Les rôles suivants doivent également être pris en compte et affectés lors de l’exécution du service :

- Propriétaire du contrat d’approvisionnement de certificat conclu avec l’autorité de certification racine externe (dans le cas où le propriétaire achète des certificats à une autorité de certification externe ou utilise une autorité de certification subordonnée à une autorité de certification externe).
- Développement et validation de l’autorité de certification.
- Révision des enregistrements d’audit.
- Le personnel qui contribue au support de l’autorité de certification ou à la gestion des installations physiques et cloud, mais qui n’est pas directement approuvé pour effectuer des opérations d’autorité de certification, est défini comme faisant partir du rôle autorisé. L’ensemble des tâches que les personnes auxquelles est attribué le rôle autorisé peuvent effectuer doivent également être documentées.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Les appartenances des rôles approuvés et autorisés doivent être révisées annuellement

L’appartenance aux rôles approuvés et autorisés doit être vérifiée au moins une fois par trimestre pour s’assurer que le nombre des personnes (pour les processus manuels) ou des identités de service (pour les processus automatisés) auxquelles chaque rôle est attribué est réduit au minimum.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Le processus d’émission de certificat doit appliquer le principe de la séparation des rôles entre le demandeur et l’approbateur de certificat.

Le processus d’émission de certificat doit appliquer le principe de la séparation des rôles de demandeur et d’approbateur de certificat (personnes ou systèmes automatisés). L’émission de certificat doit être autorisée par un rôle d’approbateur de certificat qui vérifie que le demandeur de certificat est autorisé à obtenir des certificats. Les personnes qui détiennent le rôle d’approbateur de certificat doivent être formellement autorisées.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>La gestion des rôles privilégiés doit limiter l’accès et être révisée trimestriellement

L’attribution de rôles privilégiés, tels que l’autorisation de l’appartenance aux groupes Administrateurs et Approbateurs, doit être limitée à un ensemble limité de personnes autorisées. Toute modification de rôle privilégié doit avoir un accès révoqué dans les 24 heures. Enfin, les attributions de rôles privilégiées doivent être révisées sur une base trimestrielle et toutes les affectations inutiles ou expirées doivent être supprimées.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Les rôles privilégiés doivent utiliser une authentification à deux facteurs

Une authentification multifacteur (authentification à deux facteurs, MFA ou TFA) doit être utilisée pour les connexions interactives d’approbateurs et d’administrateurs au service.

## <a name="certificate-service-operation-guidelines"></a>Instructions opérationnelles concernant le service de certificats

### <a name="operational-contacts"></a>Contacts opérationnels

Le service de certificats doit disposer d’un plan de réponse de sécurité à jour consigné dans un fichier contenant des contacts détaillés pour la réponse aux incidents opérationnels.

### <a name="security-updates"></a>Mises à jour de sécurité

Tous les systèmes doivent être surveillés et mis à jour en permanence avec les mises à jour et correctifs de sécurité et de conformité les plus récents.

> [!IMPORTANT]
> Le dépôt GitHub du service OPC Vault est continuellement mis à jour avec des correctifs de sécurité. Les mises à jour disponibles sur GitHub doivent être surveillées et appliquées au service à intervalles réguliers.

### <a name="security-monitoring"></a>Surveillance de la sécurité

Souscrivez ou implémentez une analyse de sécurité appropriée, par exemple en vous abonnant à une solution de surveillance centralisée (telle qu’Azure Security Center, la solution de surveillance O365), et configurez-la de manière appropriée pour vous assurer que les événements de sécurité lui sont transmis.

> [!IMPORTANT]
> Par défaut, le service OPC Vault est déployé avec [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) en guise de solution de surveillance. L’ajout d’une solution de sécurité telle qu’[Azure Security Center](https://azure.microsoft.com/services/security-center/) est fortement recommandé.

### <a name="assess-security-of-open-source-software-components"></a>Évaluer la sécurité de composants logiciels open source

Tous les composants open source utilisés au sein d’un produit ou service doivent être exempts de failles de sécurité modérées ou importantes.

> [!IMPORTANT]
> Le dépôt GitHub du service OPC Vault analyse tous les composants pendant les générations d’intégration continue en lien avec des vulnérabilités. Les mises à jour disponibles sur GitHub doivent être surveillées et appliquées au service à intervalles réguliers.

### <a name="maintain-an-inventory"></a>Tenue d’inventaire

Un inventaire des ressources doit être tenu à jour pour l’ensemble des hôtes de production (y compris les machines virtuelles persistantes), appareils, plages d’adresses IP internes, adresses IP virtuelles et noms de domaine DNS publics. Cet inventaire doit être mis à jour par l’ajout ou la suppression d’un système, d’une adresse IP d’appareils, d’une adresse IP virtuelle ou d’un domaine DNS public dans un délai de 30 jours.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventaire du déploiement de production de microservice Azure OPC Vault par défaut : 

Dans **Azure** :
- **App Service Plan** (Plan App Service) : Plan App service pour les hôtes de service. Par défaut S1.
- **App service** pour microservice : Hôte du service OPC Vault.
- **App service** pour exemple d’application : Exemple d’application hôte d’OPC Vault.
- **KeyVault Standard** : Pour stocker les secrets et les clés Cosmos DB pour les services web.
- **KeyVault Premium** : Pour héberger les clés de l’autorité de certification émettrice, pour le service de signature, pour la configuration du coffre et pour le stockage des clés privées d’application.
- **Cosmos DB** : Base de données pour les demandes d’application et de certificat. 
- **Application Insights** : solution de surveillance (facultative) pour le service web et l’application.
- **Inscription d’application AzureAD** : Inscription de l’exemple d’application, du service et du module Edge.

Pour les services cloud, l’ensemble des noms d’hôte, groupes de ressources, noms de ressource, ID d’abonnement et ID de locataires utilisés pour déployer le service doivent être documentés. 

Dans **IOT Edge** ou un **serveur IOT Edge** local :
- **Module IoT Edge OPC Vault** : Pour prendre en charge un serveur de découverte global OPC UA de réseau de fabrique. 

Pour les appareils IoT Edge, les noms d’hôte et les adresses IP doivent être documentés. 

### <a name="document-the-certification-authorities-cas"></a>Documenter les autorités de certification

La documentation sur la hiérarchie des autorités de certification doit contenir toutes les autorités de certification utilisées, y compris toutes les autorités de certification secondaires, les autorités de certification parentes et les autorités de certification racines, même si elles ne sont pas gérées par le service. Un ensemble exhaustif des certificats d’autorité de certification non expirés peut être fourni à la place d’une documentation formelle.

> [!NOTE]
> L’exemple d’application OPC Vault prend en charge le téléchargement de tous les certificats utilisés et produits dans le service à des fins de documentation.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documenter les certificats émis par toutes les autorités de certification

Un ensemble exhaustif des certificats émis au cours des 12 derniers mois doit être fourni à des fins de documentation.

> [!NOTE]
> L’exemple d’application OPC Vault prend en charge le téléchargement de tous les certificats utilisés et produits dans le service à des fins de documentation.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Documenter la procédure SOP pour supprimer des clés de chiffrement en toute sécurité

Étant donné que la suppression de clé peut ne se produire que rarement pendant la durée de vie d’une autorité de certification, aucun utilisateur ne dispose d’un droit de suppression de certificat keyVault, et aucune API n’est exposée pour supprimer un certificat d’autorité de certification émettrice. La procédure manuelle standard pour la suppression sécurisée des clés de chiffrement d’autorité de certification n’est disponible qu’en accédant directement au KeyVault sur le Portail Azure et en supprimant le groupe de certificats dans le KeyVault. Pour garantir une suppression immédiate, la [suppression réversible du KeyVault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) doit être désactivée.

## <a name="certificates"></a>Certificats

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Les certificats doivent être conformes au profil de certificat minimal

Le service OPC Vault est un service d’autorités de certification en ligne qui émet des certificats d’entité finale pour des abonnés.
Le microservice OPC Vault respect ces directives dans on implémentation par défaut.

- Tous les certificats doivent inclure les champs X.509 suivants, comme indiqué ci-dessous :
  - Le contenu du champ Version doit être v3. 
  - Le contenu du champ serialNumber doit inclure au moins 8 octets d’entropie obtenus auprès d’un générateur de nombres aléatoires certifié conforme à la norme FIPS (Federal Information Processing Standards) 140.<br>
    > [!IMPORTANT]
    > Le numéro de série d’OPC Vault est par défaut de 20 octets et obtenu à partir du générateur de nombres aléatoires de chiffrement de système d’exploitation. Le générateur de nombres aléatoires est certifié conforme à la norme FIPS 140 sur les appareils Windows, mais pas sur les versions Linux. Ce fait doit être pris en compte lors du choix d’un déploiement de service qui utilise des machines virtuelles Linux ou des conteneurs Docker Linux sur lesquels la technologie sous-jacente OpenSSL n’est pas certifiée conforme à la norme FIPS 140.
  - Les champs issuerUniqueID et subjectUniqueID ne doivent pas être présents.
  - Les certificats d’entité finale doivent être identifiés avec l’extension Contraintes de base conformément à la norme IETF RFC 5280.
  - Le champ pathLenConstraint doit avoir la valeur 0 pour le certificat de l’autorité de certification émettrice. 
  - L’extension Utilisation de clé étendue doit être présente et contenir l’ensemble minimal d’identificateurs d’objet (OID) pour l’Utilisation de clé étendue. L’OID anyExtendedKeyUsage (2.5.29.37.0) ne doit pas être spécifié. 
  - L’extension Point de distribution de liste de révocation de certificats (CDP) doit être présente dans le certificat de l’autorité de certification émettrice.<br>
    > [!IMPORTANT]
    > L’extension Point de distribution de liste de révocation de certificats (CDP) est présente dans les certificats d’autorité de certification d’OPC Vault. Toutefois, les appareils OPC UA utilisent des méthodes personnalisées pour distribuer les listes de révocation de certificats.
  - L’extension Accès aux informations de l’autorité doit être présente dans les certificats de l’abonné.<br>
    > [!IMPORTANT]
    > L’extension Accès aux informations de l’autorité  est présente dans les certificats d’abonné d’OPC Vault. Toutefois, les appareils OPC UA utilisent des méthodes personnalisées pour distribuer les informations de l’autorité de certification émettrice.
- Des algorithmes asymétriques approuvés, des longueurs de clé, des fonctions de hachage et des modes de remplissage doivent être utilisés.
  - Les algorithmes **RSA** et **SHA-2** sont les seuls pris en charge (*).
  - L’algorithme RSA peut être utilisé pour le chiffrement, la signature et l’échange de clés.
  - Le chiffrement RSA doit utiliser uniquement les modes de remplissage OAEP, RSA-KEM ou RSA-PSS.
  - Les longueurs de clé doivent être au minimum de 2048 bits.
  - Utilisez la famille d’algorithmes de hachage SHA-2 (SHA256, SHA384 et SHA512).
  - La durée de vie des clés d’autorité de certification racine RSA doit au mois de 20 ans et elles doivent comprendre au minimum 4096 bits.
  - Les clés d’autorité de certification émettrices RSA doivent comporter au moins 2048 bits ; si la date d’expiration du certificat de l’autorité de certification est postérieure à 2030, la clé d’autorité de certification doit comprendre au minimum 4096 bits.
- Durée de vie du certificat
  - Certificats d’autorité de certification racine : La période maximale de validité du certificat pour les autorités de certification racine ne doit pas dépasser 25 ans.
  - Certificats d’autorité de certification d’abonné ou d’autorité de certification émettrice en ligne : La période maximale de validité du certificat pour les autorités de certification qui sont en ligne et n’émettent que des certificats d’abonné ne peut pas dépasser six ans. Pour ces autorités de certification, la clé de signature privée associée ne doit pas être utilisée pendant plus de trois ans pour émettre de nouveaux certificats.<br>
    > [!IMPORTANT]
    > Le certificat d’émetteur tel qu’il est généré dans le microservice OPC Vault par défaut sans autorité de certification racine externe est traité comme une autorité de certification d’abonné en ligne avec des exigences et durées de vie associées. La durée de vie par défaut est définie sur cinq ans, et la longueur de clé minimale est de 2048.
  - Toutes les clés asymétriques doivent avoir une durée de vie maximale de cinq ans et une durée de vie recommandée d’un an.<br>
    > [!IMPORTANT]
    > Par défaut, les durées de vie des certificats d’application émis avec OPC Vault sont de deux ans et ils doivent être remplacées chaque année. 
  - Chaque fois qu’un certificat est renouvelé, il est renouvelé avec une nouvelle clé.
- Extensions spécifiques d’OPC UA dans les certificats d’instance d’application
  - L’extension subjectAltName inclut l’URI de l’application et des noms d’hôte qui peuvent également inclure un nom de domaine complet, ainsi que des adresses IPv4 et IPv6.
  - La valeur keyUsage inclut les informations digitalSignature, nonRepudiation, keyEncipherment et dataEncipherment.
  - La valeur ExtendedKeyUsage inclut les informations serverAuth et/ou AutClient.
  - La valeur authorityKeyIdentifier est spécifiée dans les certificats signés.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Les clés et certificats d’autorité de certification doivent répondre à des exigences minimales

- **Clés privées** : Les clés **RSA** émises doivent comporter au moins 2048 bits ; si la date d’expiration du certificat de l’autorité de certification est postérieure à 2030, la clé d’autorité de certification doit comprendre au minimum 4096 bits.
- **Durée de vie** : La période maximale de validité du certificat pour les autorités de certification qui sont en ligne et n’émettent que des certificats d’abonné ne peut pas dépasser six ans. Pour ces autorités de certification, la clé de signature privée associée ne doit pas être utilisée pendant plus de trois ans pour émettre de nouveaux certificats.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>Les clés d’autorité de certification sont protégées à l’aide de modules de sécurité matériels (HSM)

- OpcVault utilise Azure Key Vault Premium, et les clés sont protégées par des modules de sécurité matériels (HSM) FIPS 140-2 de niveau 2. 

Les modules de chiffrement qu’utilise Key Vault, HSM ou logiciel, sont conformes aux standards FIPS (Federal Information Processing Standards).<br>
Les clés créées ou importées comme clés protégées par module de sécurité matériel (HSM) sont traitées dans un HSM certifié conforme à la norme FIPS 140-2 de niveau 2.<br>
Les clés créées ou importées comme clés protégées par logiciel sont traitées dans des modules de chiffrement certifiés conformes à la norme FIPS 140-2 de niveau 1.

## <a name="operational-practices"></a>Pratiques opérationnelles

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documenter et tenir à jour les pratiques d’infrastructure à clé publique (PKI) opérationnelles standard pour l’inscription de certificat

Documentez et tenez à jour les procédures opérationnelles standard (SOP) régissant la manière dont les autorités de certification (CA) émettent les certificats, à savoir : 
- Mode d’identification et d’authentification de l’abonné 
- Façon dont les demandes de certificat sont traitées et validées (le cas échéant, spécifier également la manière dont les demandes de renouvellement de certificat et de recréation de clé sont traitées) 
- Mode de distribution des certificats émis aux abonnés 

La procédure opérationnelle standard du microservice OPC Vault est décrite dans les documents [Vue d’ensemble](overview-opc-vault-architecture.md) et [Comment gérer](howto-opc-vault-manage.md). Les pratiques suivent la spécification d’architecture unifiée OPC, partie 12 : Services de découverte et globaux.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documenter et tenir à jour les pratiques d’infrastructure à clé publique (PKI) opérationnelles standard pour la révocation de certificat

Le processus de révocation de certificat est décrit dans les documents [Vue d’ensemble](overview-opc-vault-architecture.md) et [Comment gérer](howto-opc-vault-manage.md).
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>Documenter le cérémonial de génération de clé de l’autorité de certification (CA) 

La génération de clé par l’autorité de certification émettrice dans le microservice OPC Vault est simplifiée en raison du stockage sécurisé dans Azure keyVault, et décrite dans la documentation [Comment gérer](howto-opc-vault-manage.md).

Toutefois, quand une autorité de certification racine externe est utilisée, le cérémonial de génération de clé de l’autorité de certification doit répondre aux exigences suivantes :

Le cérémonial de génération de clé de l’autorité de certification doit être conforme à un script documenté spécifiant au moins les points suivants : 
1. Définition des rôles et des responsabilités des participants
2. Approbation de la conduite du cérémonial de génération de clé de l’autorité de certification
3. Matériel de chiffrement et supports d’activation requis pour la cérémonie
4. Préparation du matériel (y compris mise à jour et validation des informations relatives aux ressources et à la configuration)
5. Installation du système d’exploitation
6. Étapes spécifiques effectuées pendant le cérémonial de génération de clé de l’autorité de certification, par exemple : 
7. Installation et configuration de l’application de l’autorité de certification
8. Génération de clé par l’autorité de certification
9. Sauvegarde de la clé de l’autorité de certification
10. Signature du certificat de l’autorité de certification
9. Importation de clés signées dans le module de sécurité matériel (HSM) protégé du service.
11. Arrêt du système par l’autorité de certification
12. Préparation des supports pour le stockage


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer OPC Vault en toute sécurité, voici la prochaine étape suggérée :

> [!div class="nextstepaction"]
> [Sécuriser des appareils OPC UA avec OPC Vault](howto-opc-vault-secure.md)