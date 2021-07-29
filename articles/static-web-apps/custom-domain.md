---
title: Configurer un domaine personnalisé dans Azure Static Web Apps
description: Apprenez à mapper un domaine personnalisé avec un certificat SSL/TLS gratuit sur Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: buhollan
ms.openlocfilehash: fd8df4e162b33aef8a0e929da818e8b961953d9b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066109"
---
# <a name="set-up-a-custom-domain-with-free-certificate-in-azure-static-web-apps"></a>Configurez un domaine personnalisé avec un certificat gratuit dans Azure Static Web Apps

Par défaut, Azure Static Web Apps fournit un nom de domaine généré automatiquement. Cet article explique comment mapper un nom de domaine personnalisé avec une application Azure Static Web Apps.

## <a name="free-ssltls-certificate"></a>Certificat SSL/TLS gratuit

Azure Static Web Apps fournit automatiquement un certificat SSL/TLS gratuit pour le nom de domaine généré automatiquement et les domaines personnalisés que vous pouvez ajouter.

## <a name="walkthrough-video"></a>Vidéo de la procédure pas à pas

> [!VIDEO https://channel9.msdn.com/Shows/5-Things/Configuring-a-custom-domain-with-Azure-Static-Web-Apps/player?format=ny]

## <a name="prerequisites"></a>Prérequis

- Un nom de domaine acheté
- Accéder aux propriétés de configuration DNS de votre domaine

## <a name="dns-configuration-options"></a>Options de configuration DNS

Il existe plusieurs types de configurations DNS disponibles pour une application.

| Scénario                                                                                 | Exemple                                | Méthode de validation de domaine | Type d’enregistrement DNS |
| ---------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------ | --------------- |
| [Ajouter un domaine racine/apex](#add-domain-using-txt-record-validation)                        | `mydomain.com`, `example.co.uk`        | TXT                      | ALIAS           |
| [Ajouter un sous-domaine](#add-domain-using-cname-record-validation)                             | `www.mydomain.com`, `foo.mydomain.com` | CNAME                    | CNAME           |
| [Transférer un sous-domaine en cours d’utilisation](#add-domain-using-txt-record-validation) | `www.mydomain.com`, `foo.mydomain.com` | TXT                      | CNAME           |

## <a name="add-domain-using-cname-record-validation"></a>Ajouter un domaine à l’aide de la validation d’enregistrement CNAME

La validation d’enregistrement CNAME est la méthode recommandée pour ajouter un domaine personnalisé. Toutefois, elle ne fonctionne que pour les sous-domaines. Si vous souhaitez ajouter un domaine racine (`mydomain.com`), passez à [Ajouter un domaine à l’aide de la validation d’enregistrement TXT](#add-domain-using-txt-record-validation), puis [créez un enregistrement ALIAS](#create-an-alias-record).

> [!IMPORTANT]
> Si votre sous-domaine est actuellement associé à un site actif et que vous n’êtes pas prêt à le transférer vers votre application web statique, utilisez la [validation d’enregistrement TXT](#add-domain-using-txt-record-validation).

### <a name="enter-your-subdomain"></a>Entrer votre sous-domaine

1. Ouvrez votre application web statique dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Domaines personnalisés** dans le menu.

1. Sélectionnez le bouton **Ajouter**.

1. Dans le champ _Nom de domaine_, entrez votre sous-domaine. Veillez à le saisir sans aucun protocole. Par exemple : `www.mydomain.com`.

   :::image type="content" source="media/custom-domain/add-subdomain.png" alt-text="Écran Ajouter un domaine présentant le sous-domaine personnalisé dans la zone d’entrée":::

1. Sélectionnez le bouton **Suivant** pour passer à l’étape _Valider + configurer_.

### <a name="configure-cname-with-your-domain-provider"></a>Configurer CNAME avec votre fournisseur de domaine

Vous devez configurer un enregistrement CNAME auprès de votre fournisseur de domaine. Azure DNS est recommandé, mais ces étapes fonctionnent avec n’importe quel fournisseur de domaine.

# <a name="azure-dns"></a>[DNS Azure](#tab/azure-dns)

1. Vérifiez que **CNAME** est sélectionné dans la liste déroulante _Type d’enregistrement de nom d’hôte_.

1. Copiez la valeur dans le champ _Valeur_ dans le presse-papiers en sélectionnant l’icône de **copie**.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="Écran Valider + ajouter montrant l’option CNAME sélectionnée et l’icône de copie délimitée":::

1. Dans un(e) autre fenêtre ou onglet de navigateur, ouvrez votre zone Azure DNS dans le portail Azure.

1. Sélectionnez le bouton **+ Jeu d’enregistrements**.

1. Créez un jeu d’enregistrements **CNAME** avec les valeurs suivantes.

   | Paramètre          | Value                                     |
   | ---------------- | ----------------------------------------- |
   | Nom             | Votre sous-domaine, par exemple `www`             |
   | Type             | CNAME                                     |
   | Jeu d’enregistrements d’alias | No                                        |
   | TTL              | Laisser la valeur par défaut                    |
   | Unité TTL         | Laisser la valeur par défaut                    |
   | Alias            | Coller le nom de domaine à partir de votre presse-papiers |

1. Sélectionnez **OK**.

   :::image type="content" source="media/custom-domain/azure-dns-cname.png" alt-text="Écran du jeu d’enregistrements Azure DNS avec les champs nom, type et alias mis en surbrillance":::

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

# <a name="other-dns"></a>[Autre DNS](#tab/other-dns)

1. Vérifiez que **CNAME** est sélectionné dans la liste déroulante _Type d’enregistrement de nom d’hôte_.

1. Copiez la valeur dans le champ _Valeur_ dans le presse-papiers en sélectionnant l’icône de **copie**.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="Écran Valider + ajouter montrant l’option CNAME sélectionnée et l’icône de copie délimitée":::

1. Dans un(e) autre fenêtre ou onglet de navigateur, connectez-vous au site web de votre fournisseur de domaine.

1. Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.

1. Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Accédez à cette page et recherchez un lien nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

   La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

   :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Exemple de configuration de fournisseur DNS":::

1. Créez un enregistrement **CNAME** avec les valeurs suivantes.

   | Paramètre             | Valeur                                     |
   | ------------------- | ----------------------------------------- |
   | Type                | CNAME                                     |
   | Host                | Votre sous-domaine, par exemple `www`             |
   | Value               | Coller le nom de domaine à partir de votre presse-papiers |
   | TTL (le cas échéant) | Laisser la valeur par défaut                    |

1. Enregistrez les modifications avec votre fournisseur DNS.

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

---

## <a name="add-domain-using-txt-record-validation"></a>Ajouter un domaine à l’aide de la validation d’enregistrement TXT

Azure utilise un enregistrement TXT pour confirmer que vous êtes propriétaire d’un domaine. Cela est utile lorsque vous voulez effectuer l’une des opérations suivantes...

1. Vous souhaitez configurer un domaine racine (par exemple, `mydomain.com`). Valider que vous être propriétaire du domaine est nécessaire pour créer un enregistrement ALIAS qui configure le domaine racine.

1. Vous souhaitez transférer un sous-domaine sans temps d’arrêt. La méthode de validation d’enregistrement TXT vous permet de vérifier que vous êtes propriétaire du domaine. Cela permet aux applications web statiques d’effectuer le processus pour vous émettre un certificat pour ce domaine. Vous pouvez ensuite basculer votre domaine pour qu’il pointe vers votre application web statique à tout moment avec un enregistrement CNAME.

#### <a name="enter-your-domain"></a>Entrer votre domaine

1. Ouvrez votre application web statique dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Domaines personnalisés** dans le menu.

1. Sélectionnez le bouton **Ajouter**.

1. Dans le champ _Nom de domaine_, entrez votre domaine racine (c’est-à-dire `mydomain.com`) ou votre sous-domaine (c’est-à-dire `www.mydomain.com`).

   :::image type="content" source="media/custom-domain/add-domain.png" alt-text="Écran Ajouter un domaine présentant le domaine personnalisé dans la zone d’entrée":::

1. Cliquez sur le bouton **Suivant** pour passer à l’étape _Valider + configurer_.

#### <a name="configure-txt-record-with-your-domain-provider"></a>Configurer un enregistrement TXT avec votre fournisseur de domaine

Vous devez configurer un enregistrement TXT auprès de votre fournisseur de domaine. Azure DNS est recommandé, mais ces étapes fonctionnent avec n’importe quel fournisseur de domaine.

# <a name="azure-dns"></a>[DNS Azure](#tab/azure-dns)

1. Vérifiez que la liste déroulante « Type d’enregistrement de nom d’hôte » est définie sur « TXT ».

1. Sélectionnez le bouton **Générer le code**.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="Écran Ajouter un domaine personnalisé avec le bouton Générer le code mis en surbrillance":::

   Cette action génère un code unique, qui peut prendre jusqu’à une minute à traiter.

1. Sélectionnez l’icône du presse-papiers en regard du code pour copier la valeur dans le presse-papiers.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="Écran Ajouter un domaine personnalisé avec le bouton Copier le code mis en surbrillance":::

1. Dans un(e) autre fenêtre ou onglet de navigateur, ouvrez votre zone Azure DNS dans le portail Azure.

1. Sélectionnez le bouton **+ Jeu d’enregistrements**.

1. Créez un jeu d’enregistrements **TXT** avec les valeurs suivantes.

   | Paramètre  | Value                                       |
   | -------- | ------------------------------------------- |
   | Nom     | `@` pour le domaine racine ou entrez le sous-domaine |
   | Type     | TXT                                         |
   | TTL      | Laisser la valeur par défaut                      |
   | Unité TTL | Laisser la valeur par défaut                      |
   | Value    | Coller le code à partir de votre presse-papiers          |

1. Sélectionnez **OK**.

   :::image type="content" source="media/custom-domain/azure-dns-txt.png" alt-text="Écran du jeu d’enregistrements Azure DNS avec les champs nom, type et valeur mis en surbrillance":::

[!INCLUDE [validate TXT record](../../includes/static-web-apps-validate-txt.md)]

# <a name="other-dns"></a>[Autre DNS](#tab/other-dns)

1. Vérifiez que la liste déroulante « Type d’enregistrement de nom d’hôte » est définie sur « TXT ».

1. Sélectionnez le bouton **Générer le code**.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="Écran Ajouter un domaine personnalisé avec le bouton Générer le code mis en surbrillance":::

   Cette action génère un code unique, qui peut prendre jusqu’à une minute à traiter.

1. Sélectionnez l’icône du presse-papiers en regard du code pour copier la valeur dans le presse-papiers.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="Écran Ajouter un domaine personnalisé avec le bouton Copier le code mis en surbrillance":::

1. Dans un(e) autre fenêtre ou onglet de navigateur, connectez-vous au site web de votre fournisseur de domaine.

1. Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.

   > [!NOTE]
   > Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Accédez à cette page et recherchez un lien nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

1. Créez un enregistrement **TXT** avec les valeurs suivantes...

   | Paramètre             | Valeur                                       |
   | ------------------- | ------------------------------------------- |
   | Type                | TXT                                         |
   | Hôte                | `@` pour le domaine racine ou entrez le sous-domaine |
   | Value               | Coller le code à partir de votre presse-papiers          |
   | TTL (le cas échéant) | Laisser la valeur par défaut                      |

> [!NOTE]
> Certains fournisseurs DNS remplaceront automatiquement « @ » par votre domaine racine (par exemple, mydomain.com). Cela est attendu et le processus de validation fonctionne toujours.

[!INCLUDE [create repository from template](../../includes/static-web-apps-validate-txt.md)]

---

## <a name="create-an-alias-record"></a>Créer un enregistrement ALIAS

Un enregistrement ALIAS mappe un domaine à un autre. Il est utilisé spécifiquement pour les domaines racine (par exemple, `mydomain.com`). Dans cette section, vous allez créer un enregistrement ALIAS qui mappe votre domaine racine à l’URL générée automatiquement de votre application web statique.

# <a name="azure-dns"></a>[DNS Azure](#tab/azure-dns)

> [!IMPORTANT]
> Votre zone Azure DNS doit se trouver dans le même abonnement que votre application web statique.

1. Ouvrez la zone Azure DNS de votre domaine dans le portail Azure.

1. Sélectionnez le bouton **+ Jeu d’enregistrements**.

1. Créez un jeu d’enregistrements **A** avec les valeurs suivantes.

   | Paramètre          | Value                              |
   | ---------------- | ---------------------------------- |
   | Nom             | @                                  |
   | Type             | A - Enregistrement Alias sur l’adresse IPv4   |
   | Jeu d’enregistrements d’alias | Yes                                |
   | Type d’alias       | Ressource Azure                     |
   | Abonnement     | \<Your Subscription>               |
   | Ressource Azure   | \<Your Static Web App>             |
   | TTL              | Laisser la valeur par défaut             |
   | Unité TTL         | Laisser la valeur par défaut             |

1. Sélectionnez **OK**.

   :::image type="content" source="media/custom-domain/azure-dns-alias.png" alt-text="Écran du jeu d’enregistrements Azure DNS avec les champs type, alias et ressource mis en surbrillance":::

À présent que le domaine racine est configuré, le fournisseur DNS peut prendre plusieurs heures pour propager les modifications dans le monde entier.

# <a name="other-dns"></a>[Autre DNS](#tab/other-dns)

> [!IMPORTANT]
> Votre fournisseur de domaine doit prendre en charge les enregistrements [ALIAS](../dns/dns-alias.md) ou ANAME, ou l’aplanissement CNAME.

1. Ouvrez votre application web statique dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Domaine personnalisé** dans le menu.

1. Copiez l’URL générée automatiquement de votre application web statique à partir de l’écran de domaine personnalisé.

   :::image type="content" source="media/custom-domain/auto-generated.png" alt-text="Page de vue d’ensemble d’une application web statique avec l’icône Copier l’URL en surbrillance":::

1. Connectez-vous au site web de votre fournisseur de domaine.

1. Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.

   > [!NOTE]
   > Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Accédez à cette page et recherchez un lien nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

1. Créez un enregistrement **ALIAS** avec les valeurs suivantes...

   | Paramètre             | Valeur                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | Type                | ALIAS ou ANAME (utilisez CNAME si ALIAS n’est pas disponible)                    |
   | Host                | @                                                              |
   | Valeur               | Coller le nom de domaine à partir de votre presse-papiers                      |
   | TTL (le cas échéant) | Laisser la valeur par défaut                                         |

> [!IMPORTANT]
> Si votre fournisseur de domaine n’offre pas de type d’enregistrement ALIAS ou ANAME, utilisez un type CNAME à la place. De nombreux fournisseurs offrent les mêmes fonctionnalités que le type d’enregistrement ALIAS via le type d’enregistrement CNAME et une fonctionnalité appelée « Aplanissement CNAME ».

À présent que le domaine racine est configuré, le fournisseur DNS peut prendre plusieurs heures pour propager les modifications dans le monde entier.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer les paramètres d’application](application-settings.md)
