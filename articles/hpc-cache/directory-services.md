---
title: Utiliser des groupes étendus dans Azure HPC Cache
description: Configuration des services d’annuaire pour l’accès client aux cibles de stockage dans Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 9db7576cb2278cc2ef0d8b93ef04bb633962cd0e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255747"
---
# <a name="configure-directory-services"></a>Configurer les services d’annuaire

Les paramètres des **services d’annuaire** permettent à votre Azure HPC Cache d’utiliser une source externe pour authentifier les utilisateurs qui souhaitent accéder au stockage back-end.

Vous devrez peut-être activer les **groupes étendus** si votre flux de travail comprend des cibles de stockage NFS et des clients qui sont membres de plus de 16 groupes.

Après avoir cliqué sur le bouton pour activer les groupes étendus, vous devez choisir la source qu’Azure HPC Cache utilisera pour obtenir les informations d’identification de l’utilisateur et du groupe.

* [Active Directory](#configure-active-directory) : obtenir les informations d’identification à partir d’un serveur Active Directory externe. Vous ne pouvez pas utiliser Azure Active Directory pour cette opération.
* [Fichier plat](#configure-file-download) : télécharger les fichiers `/etc/group` et `/etc/passwd` à partir d’un emplacement réseau.
* [LDAP](#configure-ldap) : obtenir les informations d’identification à partir d’une source compatible avec LDAP.

> [!NOTE]
> Vérifiez que votre cache peut accéder à sa source d’informations sur le groupe depuis son sous-réseau sécurisé.<!-- + details/examples -->

Le champ **Nom d’utilisateur téléchargé** indique l’état des informations sur le groupe téléchargées les plus récentes.

![capture d’écran de la page des paramètres de la page des services d’annuaire dans le portail, avec l’option Oui sélectionnée pour les groupes étendus et le menu déroulant intitulé Télécharger la source](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Configurer Active Directory

Cette section décrit comment configurer le cache pour obtenir les informations d’identification de l’utilisateur et du groupe à partir d’un serveur Active Directory (AD) externe.

Sous **Informations Active Directory**, indiquez ces valeurs :

* **DNS principal** : indiquez l’adresse IP d’un serveur de noms de domaine que le cache peut utiliser pour résoudre le nom de domaine AD.

* **DNS secondaire** (facultatif) : entrez l’adresse d’un serveur de noms à utiliser si le serveur principal est indisponible.

* **Nom de domaine DNS AD** : indiquez le nom de domaine complet du serveur AD que le cache contacte pour obtenir les informations d’identification.

* **Nom du serveur du cache (compte de l’ordinateur)**  : définissez le nom attribué à ce cache HPC lorsqu’il contacte le domaine AD. Spécifiez un nom qui permette d’identifier facilement ce cache. Le nom peut comporter 15 caractères maximum, des lettres minuscules ou majuscules, des chiffres et des traits d’union (-).

Dans la section **Informations d’identification**, indiquez un nom d’utilisateur et un mot de passe d’administrateur AD qu’Azure HPC Cache peut utiliser pour accéder au serveur AD. Ces informations sont chiffrées lorsqu’elles sont stockées et ne peuvent pas être interrogées.

Cliquez sur le bouton situé en haut de la page pour enregistrer les paramètres.

![capture d’écran de la section Détails du téléchargement avec les valeurs Active Directory renseignées](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Configurer le téléchargement de fichiers

Ces valeurs sont obligatoires si vous souhaitez télécharger des fichiers avec vos informations d’utilisateur et de groupe. Les fichiers doivent être au format Linux/UNIX standard `/etc/group` et `/etc/passwrd`.

* **URI du fichier d’utilisateur** : entrez l’URI complet du fichier `/etc/passwrd`.
* **URI du fichier de groupe** : entrez l’URI complet du fichier `/etc/group`.

![capture d’écran de la section Détails du téléchargement pour un téléchargement de fichier plat](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Configurer LDAP

Renseignez ces valeurs si vous souhaitez utiliser une source LDAP autre qu’Active Directory pour obtenir les informations d’identification de l’utilisateur et du groupe. Contactez votre administrateur LDAP si vous avez besoin d’aide sur ces valeurs.

* **Serveur LDAP** : entrez le nom de domaine complet ou l’adresse IP du serveur LDAP à utiliser. <!-- only one, not up to 3 -->

* **ND de base LDAP** : spécifiez le nom distinctif de base du domaine LDAP au format DN. Contactez votre administrateur LDAP si vous ne connaissez pas votre ND de base.

Le serveur et le ND de base sont les seuls paramètres obligatoires pour que LDAP fonctionne, mais les options supplémentaires sécurisent davantage votre connexion.

![capture d’écran de la zone de configuration LDAP de la page des paramètres de la page des services d’annuaire](media/group-download-details-ldap.png)

Dans la section **Accès sécurisé**, vous pouvez activer le chiffrement et la validation des certificats pour la connexion LDAP. Après avoir cliqué sur **Oui** pour activer le chiffrement, vous disposez des options suivantes :

* **Valider le certificat** : lorsque cette valeur est définie, le certificat du serveur LDAP est vérifié par rapport à l’autorité de certification définie dans le champ URI ci-dessous.

* **URI du certificat d’autorité de certification** : spécifiez le chemin d’accès au certificat faisant autorité. Il peut s’agir d’un lien vers un certificat validé par l’autorité de certification ou vers un certificat auto-signé. Ce champ est obligatoire pour utiliser le paramètre Certificats validés en externe.

* **Télécharger automatiquement le certificat** : choisissez **Oui** si vous souhaitez essayer de télécharger un certificat dès que vous envoyez ces paramètres.

Renseignez la section **Informations d’identification** si vous souhaitez utiliser des informations d’identification statiques pour la sécurité LDAP. Ces informations sont chiffrées lorsqu’elles sont stockées et ne peuvent pas être interrogées.

* **Nom unique de la liaison** : entrez le nom unique de la liaison à utiliser pour s’authentifier au serveur LDAP. (Utilisez le format DN.)
* **Mot de passe de la liaison** : indiquez le mot de passe du nom unique de la liaison.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’accès client, consultez [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* Si vos informations d’identification ne sont pas téléchargées correctement, contactez l’administrateur pour obtenir la source des informations d’identification. Si nécessaire, ouvrez un [ticket de support](hpc-cache-support-ticket.md).
