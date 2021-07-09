---
title: 'Tutoriel : Mapper un nom DNS personnalisé existant'
description: Découvrez comment ajouter un nom de domaine DNS (domaine personnel) à une application web, au back-end d’une application mobile ou à une application API dans Azure App Service.
keywords: app service, azure app service, mappage de domaine, nom de domaine, domaine existant, nom d’hôte, domaine personnel
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bfd3429531f1473e099e3296ebfebd50c6d4d864
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787905"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutoriel : Mapper un nom DNS personnalisé existant à Azure App Service

Ce tutoriel vous montre comment mapper un <abbr title="Nom de domaine que vous avez acheté auprès d’un bureau d’enregistrement de domaines, comme GoDaddy, ou sous-domaine de votre domaine acheté.">nom de domaine DNS personnalisé</abbr> to <abbr title="Service HTTP pour l’hébergement d’applications web, d’API REST et d’applications back-end mobiles.">Azure App Service</abbr>.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mapper un sous-domaine à l’aide d’un <abbr title="Un enregistrement de nom canonique DNS mappe un seul nom de domaine à un autre.">Enregistrement CNAME</abbr>.
> * Mapper un domaine racine à l’aide d’un <abbr title="Dans DNS, un enregistrement d’adresse mappe un nom d’hôte à une adresse IP.">Enregistrement A</abbr>.
> * Mapper un domaine générique à l’aide d’un enregistrement CNAME
> * Rediriger l’URL par défaut vers un répertoire personnalisé

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

* [Créez une application App Service](./index.yml), ou utilisez une application créée pour un autre didacticiel.
* Vérifiez que vous pouvez modifier les enregistrements DNS de votre domaine personnalisé. Si vous n’avez pas encore de domaine personnalisé, vous pouvez [acheter un domaine App Service](manage-custom-dns-buy-domain.md).

    <details>
        <summary>De quoi ai-je besoin pour modifier les enregistrements DNS ?</summary>
        Accès nécessaire au registre DNS de votre fournisseur de domaine, comme GoDaddy. Par exemple, pour ajouter des entrées DNS pour <code>contoso.com</code> et <code>www.contoso.com</code>, vous devez pouvoir configurer les paramètres DNS du domaine racine <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Préparer l’application

Pour mapper un nom DNS personnalisé à une application, le <abbr title="Spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application.">Plan App Service</abbr> de l’application doit correspondre à un niveau de service payant (pas au niveau <abbr title="Niveau Azure App Service dans lequel votre application s’exécute sur les mêmes machines virtuelles que d’autres applications, notamment les applications d’autres clients. Ce niveau est destiné au développement et aux tests.">**Gratuit (F1)**</abbr>). Pour plus d’informations, consultez [Vue d’ensemble des plans Azure App Service](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Sélectionner l’application dans le portail Azure

1. Recherchez et sélectionnez **App Services**.

   ![Capture d’écran montrant la sélection d’App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Dans la page **App Services**, sélectionnez le nom de votre application Azure.

   ![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    La page de gestion de l’application App Service s’affiche.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

1. Dans le volet gauche de la page de l’application, faites défiler jusqu’à la section **Paramètres**, puis sélectionnez **Effectuer un scale-up (plan App Service)** .

   ![Capture d’écran montrant le menu Effectuer un scale-up (plan App Service).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Le niveau actuel de l’application est encadré d’un rectangle bleu. Vérifiez que l’application ne se trouve pas dans le niveau **F1**. Les DNS personnalisés ne sont pas disponibles dans le niveau **F1**.

   ![Capture d’écran montrant les niveaux tarifaires recommandés.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Si le plan App Service ne relève pas du niveau **F1**, fermez la page **Effectuer un scale-up**, puis passez à [3. Obtenir l’ID de vérification du domaine](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Monter en puissance le plan App Service

1. Sélectionnez un niveau payant (**D1**, **B1**, **B2**, **B3** ou n’importe quel niveau dans la catégorie **Production**). Pour obtenir des options supplémentaires, sélectionnez **Afficher d’autres options**.

1. Sélectionnez **Appliquer**.

   ![Capture d’écran montrant la vérification du niveau tarifaire.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Lorsque la notification suivante s’affiche, cela signifie que l’opération est terminée.

   ![Capture d’écran montrant la confirmation de l’opération de mise à l’échelle](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Obtenir l’ID de vérification d’un domaine

Pour ajouter un domaine personnalisé à votre application, vous devez vérifier votre propriété du domaine, en ajoutant un ID de vérification sous forme d’enregistrement TXT avec votre fournisseur de domaine. 

1. Dans le volet gauche de la page de votre application, sélectionnez **Domaines personnalisés**. 
1. Pour l’étape suivante, copiez l’ID du champ **ID de vérification des domaines personnalisés** dans la page **Domaines personnalisés**.

    ![Capture d’écran montrant l’ID dans le champ ID de vérification des domaines personnalisés.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Pourquoi en ai-je besoin ?</summary>
        L’ajout d’ID de vérification de domaines à votre domaine personnalisé peut empêcher les entrées DNS non résolues et éviter les acquisitions de sous-domaine. Pour les domaines personnalisés que vous avez précédemment configurés sans cet ID de vérification, vous devez les protéger du même risque en ajoutant l’ID de vérification à votre enregistrement DNS. Pour plus d’informations sur cette menace critique courante, consultez <a href="/azure/security/fundamentals/subdomain-takeover">Acquisition de sous-domaine</a>.
    </details>
    
<a name="info"></a>

3. **(Enregistrement A uniquement)** Pour mapper un <abbr title="Dans DNS, un enregistrement d’adresse mappe un nom d’hôte à une adresse IP.">Enregistrement A</abbr>, vous avez besoin de l’adresse IP externe de l’application. Dans la page **Domaines personnalisés**, copiez la valeur d’**Adresse IP**.

   ![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. Créer les enregistrements DNS

1. Connectez-vous au site web de votre fournisseur de domaine.

    <details>
        <summary>Puis-je gérer les enregistrements DNS de mon fournisseur de domaine avec Azure ?</summary>
        Si vous le souhaitez, vous pouvez utiliser Azure DNS pour gérer les enregistrements DNS de votre domaine et configurer un nom DNS personnalisé pour Azure App Service. Pour plus d’informations, consultez <a href="/azure/dns/dns-delegate-domain-azure-dns">Tutoriel : Héberger votre domaine dans Azure DNS></a>.
    </details>

1. Trouvez la page de gestion des enregistrements DNS. 

    <details>
        <summary>Comment trouver la page ?</summary>
        <p>Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions <strong>Nom de domaine</strong>, <strong>DNS</strong> ou <strong>Gestion du nom de serveur</strong>.</p>
        <p>Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme <strong>Mes domaines</strong>. Accédez à cette page, puis recherchez un lien nommé comme <strong>Fichier de zone</strong>, <strong>Enregistrements DNS</strong>, ou <strong>Configuration avancée</strong>.</p>
    </details>

   La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

   ![Capture d’écran montrant un exemple de page d’enregistrements DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Sélectionnez **Ajouter** ou le widget approprié pour créer un enregistrement. 

1. Sélectionnez le type d’enregistrement à créer et suivez les instructions. Vous pouvez utiliser un <abbr title="Un enregistrement de nom canonique dans DNS mappe un seul nom de domaine (alias) à un autre (nom canonique).">Enregistrement CNAME</abbr> ou un <abbr title="Dans DNS, un enregistrement d’adresse mappe un nom d’hôte à une adresse IP.">Enregistrement A</abbr> pour mapper un nom DNS personnalisé à App Service. 

    <details>
        <summary>Quel enregistrement dois-je choisir ?</summary>
        <div>
            <ul>
            <li>Pour mapper le domaine racine (par exemple, <code>contoso.com</code>), utilisez un enregistrement A. N’utilisez pas l’enregistrement CNAME pour l’enregistrement racine (pour plus d’informations, consultez l’<a href="https://en.wikipedia.org/wiki/CNAME_record">entrée Wikipédia</a>).</li>
            <li>Pour mapper un sous-domaine (par exemple, <code>www.contoso.com</code>), utilisez un enregistrement CNAME.</li>
            <li>Vous pouvez mapper un sous-domaine à l’adresse IP de l’application directement avec un enregistrement A, mais il est possible que <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">l’adresse IP change</a>. L’enregistrement CNAME est quant à lui mappé au nom d’hôte de l’application, ce qui offre moins de risque de changement.</li>
            <li>Pour mapper un <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">domaine générique</a> (par exemple, <code>*.contoso.com</code>), utilisez un enregistrement CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Pour un sous-domaine comme `www` dans `www.contoso.com`, créez deux enregistrements conformément au tableau suivant :

| Type d’enregistrement | Host | Valeur | Commentaires |
| - | - | - |
| CNAME | `<subdomain>` (par exemple, `www`) | `<app-name>.azurewebsites.net` | Le mappage de domaine lui-même. |
| TXT | `asuid.<subdomain>` (par exemple, `asuid.www`) | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid.<subdomain>` pour vérifier votre propriété du domaine personnalisé. |

![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Pour un domaine racine comme `contoso.com`, créez deux enregistrements conformément au tableau suivant :

| Type d’enregistrement | Host | Valeur | Commentaires |
| - | - | - |
| Un | `@` | Adresse IP de [Copier l’adresse IP de l’application](#3-get-a-domain-verification-id) | Le mappage de domaine lui-même (`@` représente généralement le domaine racine). |
| TXT | `asuid` | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid.<subdomain>` pour vérifier votre propriété du domaine personnalisé. Pour le domaine racine, utilisez `asuid`. |

![Capture d’écran montrant une page d’enregistrements DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Que se passe-t-il si je souhaite mapper un sous-domaine à un enregistrement A ?</summary>
Pour permettre le mappage d’un sous-domaine tel que `www.contoso.com` à un enregistrement A au lieu d’un enregistrement CNAME recommandé, votre enregistrement A et votre enregistrement TXT doivent plutôt se présenter comme dans le tableau suivant :

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tableau 3</caption>
<thead>
<tr>
<th>Type d’enregistrement</th>
<th>Host</th>
<th>Valeur</th>
</tr>
</thead>
<tbody>
<tr>
<td>Un</td>
<td><code>&lt;subdomain&gt;</code> (par exemple, <code>www</code>)</td>
<td>Adresse IP de <a href="#info" data-linktype="self-bookmark">Copier l’adresse IP de l’application</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (par exemple, <code>asuid.www</code>)</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">L’ID de vérification que vous avez obtenu précédemment</a>.</td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Générique (CNAME)](#tab/wildcard)

Pour un nom générique comme `*` dans `*.contoso.com`, créez deux enregistrements conformément au tableau suivant :

| Type d’enregistrement | Host | Valeur | Commentaires |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Le mappage de domaine lui-même. |
| TXT | `asuid` | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid` pour vérifier votre propriété du domaine personnalisé. |

![Capture d’écran montrant la navigation vers une application Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Mes changements sont effacés, une fois que j’ai quitté la page.</summary>
<p>Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien <strong>Enregistrer les modifications</strong> distinct.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Activer le mappage dans votre application

1. Dans le volet gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**.

    ![Capture d’écran montrant le menu Domaines personnalisés.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant l’élément Ajouter un nom d’hôte.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Tapez le nom de domaine complet que vous avez ajouté pour un enregistrement CNAME, tel que `www.contoso.com`.

1. Sélectionnez **Valider**. La page **Ajouter un domaine personnalisé** s’affiche.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **CNAME (www\.exemple.com ou tout sous-domaine)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant le bouton Ajouter un domaine personnalisé.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    ![Capture d’écran montrant l’ajout de l’enregistrement CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Qu’est-ce que l’avertissement <strong>Non sécurisé</strong> ?</summary>
        Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez <a href="/azure/app-service/configure-ssl-bindings">Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service</a>.
    </details>

    Si vous avez raté une étape ou si vous avez fait une faute de frappe, une erreur de vérification s’affichera au bas de la page.

    ![Capture d’écran montrant une erreur de vérification.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement A, tel que `contoso.com`. 

1. Sélectionnez **Valider**. La page **Ajouter un domaine personnalisé** s’affiche.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant l’ajout d’un nom DNS à l’application.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    ![Capture d’écran montrant l’ajout d’un enregistrement A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Qu’est-ce que l’avertissement <strong>Non sécurisé</strong> ?</summary>
        Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez <a href="/azure/app-service/configure-ssl-bindings">Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service</a>.
    </details>
    
    Si vous avez raté une étape ou si vous avez fait une faute de frappe, une erreur de vérification s’affichera au bas de la page.
    
    ![Capture d’écran montrant une erreur de vérification.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Générique (CNAME)](#tab/wildcard)

3. Tapez un nom de domaine complet qui correspond au domaine générique. Par exemple, pour `*.contoso.com`, vous pouvez utiliser `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com` ou toute autre chaîne qui correspond au modèle générique. Ensuite, sélectionnez **Valider**.

    Le bouton **Ajouter un domaine personnalisé** est activé.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (www\.exemple.com ou tout sous-domaine)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran qui montre l’ajout d’un nom DNS à l’application.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    <details>
        <summary>Qu’est-ce que l’avertissement <strong>Non sécurisé</strong> ?</summary>
        Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez <a href="/azure/app-service/configure-ssl-bindings">Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Test dans un navigateur

Accédez aux noms DNS que vous avez configurés précédemment.

![Capture d’écran montrant la navigation vers une application Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>J’obtiens une erreur HTTP 404 (introuvable).</summary>
<ul>
<li>Un enregistrement A et/ou un enregistrement CNAME est manquant dans le domaine personnalisé configuré.  Vérifiez si les enregistrements DNS sont exposés en utilisant un outil de <a href="https://www.nslookup.io/">recherche DNS en ligne</a>.</li>
<li>Le client du navigateur a mis en cache l'ancienne adresse IP de votre domaine. Effacez le cache et testez à nouveau la résolution DNS. Sur une machine Windows, effacez le cache avec <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Migrer un domaine actif

Pour migrer un site actif et son nom de domaine DNS vers App Service sans interruption de service, voir [Migrer un nom DNS actif vers Azure App Service](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Rediriger vers un répertoire personnalisé

<details>
<summary>En ai-je besoin ?</summary>
<p>Cela dépend de votre application. Par défaut, App Service dirige les demandes web au répertoire racine du code de votre application. Mais certains frameworks web ne démarrent pas dans le répertoire racine. Par exemple, <a href="https://laravel.com/">Laravel</a> démarre dans le sous-répertoire <code>public</code>. Pour continuer avec l’exemple DNS <code>contoso.com</code>, une telle application est accessible à partir de <code>http://contoso.com/public</code>, mais il est préférable de diriger <code>http://contoso.com</code> vers le répertoire <code>public</code>. </p>
</details>

Même si ce scénario est courant, il n’implique en fait pas de mappage de domaine personnalisé, mais il a trait à la personnalisation du répertoire virtuel au sein de votre application.

1. Sélectionnez **Paramètres d’application** dans le volet gauche de la page de votre application web.

1. En bas de la page, le répertoire virtuel racine `/` pointe par défaut vers `site\wwwroot`, qui correspond au répertoire racine de votre code d'application. Modifiez-le afin qu'il pointe vers `site\wwwroot\public`, par exemple, puis enregistrez vos modifications.

    ![Capture d’écran montrant la personnalisation d’un répertoire virtuel.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Une fois l’opération terminée, vérifiez en accédant au chemin racine de votre application dans le navigateur (par exemple, `http://contoso.com` ou `http://<app-name>.azurewebsites.net`).

<hr/> 

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des domaines personnalisés à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Azure CLI

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application web](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Pour plus d’informations, consultez [Attribuer un domaine personnalisé à une application web](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment lier un certificat TLS/SSL personnalisé à une application web.

> [!div class="nextstepaction"]
> [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md)
