---
title: Envoyer des notifications Microsoft Azure Notification Hubs aux applications Android et iOS
description: Découvrez les capacités multiplateformes de Microsoft Azure Notification Hubs.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: template-concept
ms.openlocfilehash: 7158fa486483f1ff26599e47e43ee3219a23045a
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082653"
---
# <a name="send-notifications-to-android-and-ios-applications"></a>Envoyer des notifications aux applications Android et iOS

Cet article fournit une vue d’ensemble de l’exemple d’application Azure Notification Hubs créé pour illustrer les capacités d’Azure Notification Hubs sur plusieurs plateformes. L’application utilise un scénario d’arpentage dans lequel l’application de bureau **Contoso Land Survey** envoie des notifications que les applications Android et iOS de Contoso peuvent recevoir.

Vous pouvez télécharger l’[exemple complet sur GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/main/NotificationHubSample).

## <a name="prerequisites"></a>Prérequis

Pour générer l’exemple, vous avez besoin des prérequis suivants :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Microsoft Visual Studio 2019 ou version ultérieure. Cet exemple utilise [Visual Studio 2019](https://www.visualstudio.com/products).
- Visual Studio 2019 avec les charges de travail suivantes installées :
  - Kit SDK .NET 5.0
  - Développement web et ASP.NET
  - Développement Azure
  - Développement Node.js
  - [Outils de développement d’applications UWP](/windows/uwp/get-started/get-set-up)
- Compte Firebase pour activer les notifications Push pour Android.
- Compte de développeur Apple pour activer les notifications Push pour iOS.
- Une instance de base de données SQL Server, hébergée sur Azure.
- Un hub et un espace de noms Azure Notification Hubs.

## <a name="sample-architecture"></a>Exemple d’architecture

La solution comprend les composants suivants :

- Instance Azure Notification Hubs : un espace de noms et un hub ANH configurés sur le [portail Azure](https://portal.azure.com).
- Base de données SQL Server : une instance de base de données SQL Server configurée sur le [portail Azure](https://portal.azure.com).
- Back end d’application ASP.NET : un back end d’API web reposant sur .NET 5.0, qui se connecte au hub de notification et est hébergé en tant qu’Azure App Service.
- Application UWP Windows : une application UWP créée à l’aide de React Native et agissant comme une application « manager » qui dispense des nouvelles et des informations d’enquête à divers utilisateurs et groupes d’enquête. L’application permet également de créer de nouveaux utilisateurs et de modifier les groupes auxquels un utilisateur est affecté.
- Applications clientes Android et iOS : applications « d’arpentage » générées à l’aide de React Native. Ces applications montrent aux utilisateurs les informations diffusées par l’application manager UWP.

## <a name="sample-folder-structure"></a>Exemple de structure de dossier

L’exemple d’application sur GitHub contient les dossiers suivants :

- **NotificationHub.Sample.API** : solution d’API web ASP.NET Visual Studio 2019 qui agit comme un back end.
- **app** : application React Native multiplateforme qui permet de distribuer des notifications avec une connexion de manager, puis de recevoir des notifications avec une connexion d’utilisateur d’enquête.
- **azure-template** : modèles Azure Resource Manager (`parameters.json` et `template.json`) que vous pouvez utiliser pour déployer toutes les ressources nécessaires à la configuration de ce déploiement dans votre abonnement Azure. Pour plus d’informations sur le déploiement de modèles Resource Manager, consultez [Créer et déployer des modèles ARM à l’aide du portail Azure](/azure/azure-resource-manager/templates/quickstart-create-templates-use-the-portal).

## <a name="sample-overview"></a>Vue d’ensemble de l’exemple

Les sections suivantes fournissent une vue d’ensemble des composants que comprend l’exemple.

### <a name="controllers"></a>Controllers

#### <a name="authentication"></a>Authentification

Les méthodes suivantes dans AuthenticateController.cs sont utilisées pour authentifier un utilisateur connecté :

```cs
[HttpPost]
[Route("login")]
public async Task<IActionResult> Login([FromBody] LoginModel model)
{
   var user = await userManager.FindByNameAsync(model.Username);
    if (user != null && await userManager.CheckPasswordAsync(user, model.Password))
    {
        var userRoles = await userManager.GetRolesAsync(user);

        var authClaims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, user.UserName),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
        };

        foreach (var userRole in userRoles)
        {
            authClaims.Add(new Claim(ClaimTypes.Role, userRole));
        }

        var authSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["JWT:Secret"]));

        var token = new JwtSecurityToken(
            issuer: _configuration["JWT:ValidIssuer"],
            audience: _configuration["JWT:ValidAudience"],
            expires: DateTime.Now.AddHours(3),
            claims: authClaims,
            signingCredentials: new SigningCredentials(authSigningKey, SecurityAlgorithms.HmacSha256)
            );

        UserDetails userDetails = new UserDetails();
        userDetails.FirstName = model.Username;
        userDetails.LastName = model.Username;
        userDetails.UserName = model.Username;

        return Ok(new
        {
            token = new JwtSecurityTokenHandler().WriteToken(token),
            expiration = token.ValidTo,
            username = model.Username,
            email = user.Email,
            role = userRoles != null ? userRoles[0] : "Site-Manager",
            user = userDetails
        });
    }
    return Unauthorized();
}

[HttpPost]
[Route("register")]
public async Task<IActionResult> Register([FromBody] RegisterModel model)
{
    var userExists = await userManager.FindByNameAsync(model.Username);
    if (userExists != null)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User already exists!" });

    ApplicationUser user = new ApplicationUser()
    {
        Email = model.Email,
        SecurityStamp = Guid.NewGuid().ToString(),
        UserName = model.Username
    };
    var result = await userManager.CreateAsync(user, model.Password);
    if (!result.Succeeded)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User creation failed! Please check user details and try again." });

    if (!await roleManager.RoleExistsAsync(UserRoles.SiteManager))
        await roleManager.CreateAsync(new IdentityRole(UserRoles.SiteManager));

    if (await roleManager.RoleExistsAsync(UserRoles.SiteManager))
    {
        await userManager.AddToRoleAsync(user, UserRoles.SiteManager);
    }

    return Ok(new Response { Status = "Success", Message = "User created successfully!" });
}
```

### <a name="dashboard"></a>tableau de bord

Le contrôleur de tableau de bord dans DashboardController.cs renvoie toutes les informations de notification :

```cs
public class DashboardController : ControllerBase
{
    private readonly ApplicationDbContext _db;
    private readonly INotificationService _notificationService;

    public DashboardController(ApplicationDbContext dbContext, INotificationService notificationService)
    {
        _db = dbContext;
        _notificationService = notificationService;
    }

    [HttpGet("insights")]
    public async Task<IActionResult> GetDashboardInsight(string duration)
    {
        DashboardInsight dashboardInsight = new DashboardInsight();

        dashboardInsight.DeviceTrends = await _notificationService.GetAllRegistrationInfoAsync();

        var notificationMessages = _db.NotificationMessages.ToList();

        switch (duration)
        {
            case "Daily":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key.ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Weekly":
                {
                    dashboardInsight.NotificationTrends = notificationMessages
                                                            .GroupBy(m => WeekNumber(m.SentTime.Date))
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = FirstDateOfWeekISO8601(DateTime.Now.Year, m.Key).ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Monthly":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date.Month)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key + "-" + DateTime.Now.Year,
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            default:
                break;
        }

        dashboardInsight.TotalGroups = _db.SurveyGroups.Count();
        dashboardInsight.TotalUsers = _db.Users.Count();
        dashboardInsight.TotalNotificationsSent = _db.NotificationMessages.Count();

        return Ok(dashboardInsight);
    }
```

### <a name="front-end"></a>Frontend

Pour appeler une API back-end, l’exemple crée le service notification.service.js, qui effectue l’appel d’API. Ce code se trouve dans app\data\services\notification.service.js :

```nodejs
export const sendNotificationAPI = async (userInfo) => {
  let url = `${api}notification/send`;
  let authHeader = await getAuthHeaders();
  return await post(url, userInfo, { ...authHeader });
};

export const getNotificationsAPI = async () => {
  let url = `${api}notification/get`;
  let authHeader = await getAuthHeaders();
  return await get(url, { ...authHeader });
};
```

### <a name="manager-application"></a>Application manager

Cet exemple comporte une application UWP créée à l’aide de React Native, qui agit comme une application « manager » qui dispense des nouvelles et des informations d’enquête à divers utilisateurs et groupes d’enquête. L’application permet également de créer de nouveaux utilisateurs et de modifier les groupes auxquels un utilisateur est affecté.

Pour la connexion du manager, utilisez le point de terminaison et le corps POST suivants pour générer les informations d’identification d’utilisateur de votre choix. Vous pouvez utiliser n’importe quel client HTTP REST de votre choix :

#### <a name="endpoint"></a>Point de terminaison

```http
POST {{endpoint}}api/authenticate/register-admin
```

#### <a name="body"></a>Corps

```http
{
  "username": "<USER_NAME>",
  "email": "<EMAIL>",
  "password": "<PASSWORD>"
}
```

Une fois inscrit, vous devez être en mesure de vous connecter à l’application UWP avec les mêmes informations d’identification.

### <a name="notification-controller"></a>Contrôleur de notification

Le code suivant, dans NotificationController.cs, reçoit et envoie des notifications :

```cs
[Produces("application/json")]
[Consumes("application/json")]
[HttpPost("send")]
public async Task<ActionResult> SendNotification([FromBody] NotificationMessage notificationMessage)
{
    try
    {
        List<string> tags = new List<string>();

        // attach survey group and user information with notificationMessage
        notificationMessage.SurveyGroupTags.ForEach(surveyGroupId =>
        {
            var group = _db.SurveyGroups.Where(g => g.Id == surveyGroupId).FirstOrDefault();
            if (group != null)
            {
                notificationMessage.SurveyGroups.Add(group);
                tags.Add($"group:{group.GroupName.Replace(' ', '-')}");
            }
        });

        notificationMessage.UserTags.ForEach(userId =>
        {
            var user = _db.Users.Where(u => u.Id == userId).FirstOrDefault();
            if (user != null)
            {
                notificationMessage.Users.Add(user);
                tags.Add($"username:{user.UserName}");
            }
        });
        _db.NotificationMessages.Add(notificationMessage);

        // send template notification
        var notification = new Dictionary<string, string>();
        notification.Add("title", notificationMessage.NotificationTitle);
        notification.Add("message", notificationMessage.NotificationDescription);

        var res = await _notificationService.RequestNotificationAsync(notificationMessage, tags, HttpContext.RequestAborted);

        await _db.SaveChangesAsync();
        return Ok(notificationMessage);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.Message);
    }
}

[Produces("application/json")]
[HttpGet("get")]
public async Task<ActionResult> Get()
{
    try
    {
        var surveyGroups = _db.NotificationMessages.Include(message => message.SurveyGroups).Include(message => message.Users).ToList();
        return Ok(surveyGroups);
    }
    catch (Exception ex)
    {
        return BadRequest();
    }
}
```

### <a name="notification-service"></a>Service de notification

Le service de notification situé dans **NotificationHub.Sample.API/NotificationHub.Sample.API/Services/Notifications/INotificationService.cs** a des méthodes pour créer et supprimer l’installation. Il existe également une méthode pour envoyer des notifications à tous les utilisateurs inscrits et pour obtenir toutes les informations d’inscription :

```cs
public interface INotificationService
{
   Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken cancellationToken);
   Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken cancellationToken);
   Task<bool> RequestNotificationAsync(NotificationMessage notificationMessage, IList<string> tags, CancellationToken cancellationToken);
   Task<List<DeviceTrend>> GetAllRegistrationInfoAsync();
}
```

## <a name="deploy-the-solution"></a>Déployer la solution

Pour exécuter l’exemple, les prérequis suivants sont indispensables :

- Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte Firebase pour configurer les notifications Push pour Android.
- Un compte de développeur Apple pour configurer les notifications Push pour iOS.
- Un ordinateur Windows sur lequel Visual Studio 2019 est installé, avec prise en charge du développement d’applications UWP et d’ASP.NET Core.
- Android SDK et Android Studio sur votre machine de développement si vous exécutez l’application Android.
- Un ordinateur Mac OSX avec XCode et le Kit de développement logiciel (SDK) iOS le plus récent, si vous exécutez l’application iOS.

### <a name="deploy-resources"></a>Déployer des ressources

- **[Instance Azure Notification Hubs](#create-resource-notification-hub) :** un hub de notification configuré sur Azure.
- **[Instance de base de données SQL Server](#create-resource-sql-database) :** une base de données SQL Server hébergée sur Azure.
- **Back end de l’application ASP.NET :** un back end d’API web reposant sur .NET 5.0, qui se connecte à Azure Notification Hubs hébergé en tant que Azure App Service. Pour plus d’informations, consultez [Démarrage rapide : Déployer une application web ASP.NET](/azure/app-service/quickstart-dotnetcore?tabs=net50&pivots=development-environment-vs).

Si vous ne souhaitez pas déployer toutes les ressources manuellement, vous pouvez utiliser le fichier template.json d’Azure Resource Manager fourni dans le référentiel GitHub pour déployer toutes les instances nécessaires en une seule étape. Le fichier de modèle est disponible dans le référentiel dans /azure-template. Pour plus d’informations sur l’utilisation des modèles Resource Manager, consultez [Tutoriel : Utiliser les modèles de démarrage rapide Azure](/azure/azure-resource-manager/templates/template-tutorial-quickstart-template?tabs=azure-powershell).

### <a name="set-up-notifications-for-android-and-ios"></a>Configurer des notifications pour Android et iOS

- **Firebase :** Pour recevoir des notifications sur Android, configurez le service Firebase et connectez-le à votre instance Azure Notification Hubs. Pour plus d’informations, consultez [Configurer les paramètres de Google Firebase](configure-google-firebase-cloud-messaging.md).
- **Apple Push Notification Service (APNS) :** Pour recevoir des notifications sur iOS, configurez le service APNS à l’aide de votre compte de développeur Apple et connectez-le à votre instance Azure Notification Hubs. Pour plus d’informations, consultez [Configurer les paramètres d’Apple Push Notification Service](configure-apple-push-notification-service.md).

## <a name="build-the-solution"></a>Générez la solution.

Pour générer l’exemple, suivez ces étapes.

### <a name="create-resource-sql-database"></a>Créer une ressource : base de données SQL

[Créez une instance de base de données SQL Server](/azure/azure-sql/database/single-database-create-quickstart?tabs=azure-portal) dans le portail Azure. Par exemple :

:::image type="content" source="media/uwp-react/resources-sql.png" alt-text="Ressources d’une instance SQL":::

:::image type="content" source="media/uwp-react/template-screenshot.png" alt-text="Modèle de déploiement":::

### <a name="create-resource-notification-hub"></a>Créer une ressource : hub de notification

Créez un hub de notification dans le portail Azure comme suit :

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-backend"></a>Configurer le back end

Pour configurer le back end d’application, localisez le fichier **/NotificationHub.Sample.API/appsettings.json** et configurez la chaîne de connexion SQL Server :

```json
"ConnectionStrings": {
    "SQLServerConnectionString": "Server=tcp:<SERVER_NAME>,1433;Initial Catalog=<DB_NAME>;Persist Security Info=False;User ID=<DB_USER_NAME>;Password=<PASSWORD>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
  },
```

Remplacez `<SERVER_NAME>` par le nom de votre serveur SQL, `<DB_NAME>` par l’URL de votre base de données déployée, `<DB_USER_NAME>` par le nom d’utilisateur configuré et `<PASSWORD>` par le mot de passe configuré.

Vous pouvez exécuter la solution d’API localement ou sur n’importe quel serveur IIS ou la déployer en tant que service d’application web Azure. Conservez l’URL de l’API à portée de main.

### <a name="build-the-notificationhubsampleapi-application"></a>Générer l’application NotificationHub.Sample.API

1. Dans Visual Studio, chargez la solution /NotificationHubSample/NotificationHub.Sample.API/NotificationHub.Sample.API.sln.
2. Dans le menu **Générer**, sélectionnez **Générer la solution**.
3. Publiez la solution sur Azure : dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **NotificationHub.Sample.API**, puis sélectionnez **Publier**.
4. Dans la boîte de dialogue **Publier**, sélectionnez **Azure** (première option), puis cliquez sur **Suivant**.
5. Sélectionnez **Azure App Service (Windows)** , puis sélectionnez **Suivant**.
6. Vous êtes redirigé vers la boîte de dialogue suivante, à partir de laquelle vous pouvez sélectionner le nom d’abonnement et les instances AppService appropriés. Votre back end sera déployé sur Azure, et vous serez redirigé vers une nouvelle URL.

   :::image type="content" source="media/uwp-react/publish.png" alt-text="Publier le projet":::

7. Une fois le back end publié, ajoutez l’URL générée dans **config.js**, situé dans le dossier **/app**. Veillez à ajouter `/api/` après l’URL.

## <a name="run-react-native-frontend-application-for-windows"></a>Exécuter une application frontale React Native pour Windows

L’application requiert l’exécution simultanée de l’application mobile (Android ou iOS) et de l’application manager UWP. Procédez comme suit pour exécuter les deux :

Ouvrez le dossier **app** dans la fenêtre de votre terminal ou de votre interpréteur de commandes par défaut. Ensuite, procédez comme suit :

### <a name="windows"></a>Windows

1. Exécutez `npm install` pour installer toutes les dépendances du package.
2. Exécutez `npm run start` pour démarrer le serveur metro dans une fenêtre de console.
3. Ouvrez une autre fenêtre de terminal et exécutez `npx react-native run-windows` pour exécuter l’application UWP.
4. Si le déploiement ou la génération échoue, consultez le guide de résolution des problèmes.

### <a name="android"></a>Android

1. Configurez Firebase sur votre projet React Native pour vous assurer que vous êtes en mesure d’utiliser les capacités de notification.
2. Une fois que vous avez correctement configuré le projet Firebase, téléchargez le fichier **google-services.json** à partir du portail Firebase.
3. Remplacez **./app/android/app/google-services.json** par ce nouveau fichier. Assurez-vous que le nom du package d’application correspond à celui configuré dans Firebase. Le nom du package est configuré dans le fichier **AndroidManifest.xml**.
4. Pour plus d’informations sur la configuration des notifications dans une application React Native, consultez [Tutoriel : Envoyer des notifications Push à React Native](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native).
5. Une fois les notifications configurées, exécutez `npm run start` pour démarrer le serveur metro dans une fenêtre de console. Si votre application Windows est déjà en cours d’exécution, vous pouvez ignorer cette étape.
6. Dans une nouvelle fenêtre de console, exécutez `npx react-native run-android` pour exécuter l’application Android.

### <a name="ios"></a>iOS

1. Configurez APNS sur votre projet React Native pour vous assurer que vous êtes en mesure d’utiliser les capacités de notification.
2. Dans iOS, les notifications ne peuvent être reçues que par les applications signées et installées par le biais d’App Store ou de TestFlight. Vous devez créer une application dans votre compte de développeur Apple. L’identificateur d’offre groupée de l’application configuré dans votre compte de développeur Apple doit être configuré dans les fichiers **Info.plist** et **Entitlements.plist** de votre application.
3. Pour plus d’informations sur la configuration des notifications dans une application React Native, consultez [Tutoriel : Envoyer des notifications Push à React Native](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native).
4. Une fois les notifications configurées, exécutez `npm run start` pour démarrer le serveur metro dans une fenêtre de console. Si votre application Windows est déjà en cours d’exécution, vous pouvez ignorer cette étape.
5. Dans une nouvelle fenêtre de console, exécutez `npx react-native run-ios` pour exécuter l’application iOS. Comme mentionné précédemment, les notifications ne fonctionneront pas sur iOS si elles sont déployées localement. Vous ne pouvez pas recevoir de notifications dans le simulateur iOS.

## <a name="troubleshooting"></a>Dépannage

Vous pouvez obtenir l’erreur suivante lors de l’exécution de l’application React Native pour Windows :

`error MSB4057: The target "Deploy" does not exist in the project`

Il s’agit d’un problème connu avec React Native pour Windows. Pour résoudre ce problème, déchargez le fichier **.csproj** pour les projets **CheckboxWindows** et **ReactNativeAsyncStorage** dans Visual Studio après avoir ouvert **app.sln** dans le dossier **app/windows**. Ensuite, dans le fichier **app.csproj**, ajoutez la ligne suivante juste avant `...</Project>`, puis rechargez le projet :

```xml
<Target Name="Deploy"/>
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Envoyer des notifications Push vers des applications React Native avec Microsoft Azure Notification Hubs](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)
- [Tutoriel : Envoyer des notifications Push à des appareils Android à l’aide du Kit de développement logiciel (SDK) Firebase](android-sdk.md)
- [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [React Native pour Windows](https://microsoft.github.io/react-native-windows/)
