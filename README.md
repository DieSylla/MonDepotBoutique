# Boutique Diayma

### 2. Projets de la solution
- Diayma

### 3. Version SDK .NET utilisée
Version du SDK utilisée : .NET Core 2.1

### 4. Installation du SDK
<img width="562" height="39" alt="image" src="https://github.com/user-attachments/assets/2a7b9498-78f3-48d3-bb23-59121790639c" />

### 5. Dépôt GitHub personnel

### 6. Bugs repérés dans l’application
#### Traduction en espagnol non fonctionnelle :
Le changement de langue vers l’espagnol n’a aucun effet sur l’interface.

#### Le stock des produits n’est pas mis à jour après une commande
Lorsque une commande est validée, la quantité disponible du produit n’est pas décrémentée

### 5. Namespaces, classes et méthodes visités avant l’affichage des produits sur l’écran d’accueil

J'ai placé un point d'arrêt à la ligne 20 de Startup.cs (dans le constructeur : Configuration = configuration;) comme indiqué dans le TP, et lancé le débogage avec F5. L'exécution s'est arrêtée au démarrage de l'application, avant toute requête navigateur. J'ai ensuite utilisé les modes de pas à pas pour tracer le flux initial (bootstrap de l'app). Voici la séquence détaillée observée, basée sur les captures d'écran. 

### Flux au démarrage de l'application :
### - Premier hit après F5 :
- Namespace : P2FixAnAppDotNetCode
- Classe : Startup
- Méthode : Startup (constructeur) – À la ligne 20 : Configuration = configuration;.
 - Mode utilisé : Démarrage automatique via breakpoint.
- Observation : La variable configuration est injectée comme un {Microsoft.Extensions.Configuration.ChainedConfigurationRoot} 
- La pile des appels montre : Startup..ctor (ligne 20) appelé par [Code externe] (probablement WebHostBuilder), puis Program.BuildWebHost (ligne 14), et Program.Main (ligne 10). Cela initialise la configuration de l'app avant d'ajouter les services.

<img width="604" height="268" alt="image" src="https://github.com/user-attachments/assets/e75516bd-5533-4c71-89e8-8752173206e6" />

### Après F10 (Pas à pas principal) :
- Namespace : P2FixAnAppDotNetCode
- Classe : Startup
- Méthode : Startup (constructeur) – Avance à la ligne 21 : } (fin du constructeur). 
- Mode utilisé : F10 pour avancer ligne par ligne sans entrer dans des appels (ici, simple assignment, pas d'appel profond). 
- Observation : Aucune nouvelle variable ; la pile des appels reste identique. Cela termine l'initialisation du Startup, prêt pour ConfigureServices. Temps écoulé : 1 ms comme indiqué.

<img width="606" height="279" alt="image" src="https://github.com/user-attachments/assets/236a250e-4e9b-4405-8d20-886604be34a7" />

### Après F11 (Pas à pas détaillé) :
- Namespace : P2FixAnAppDotNetCode
- Classe : Startup
- Méthode : ConfigureServices – À la ligne 27 : services.AddLocalization(opts => { opts.ResourcesPath = "Resources"; });. 
- Mode utilisé : F11 pour entrer en profondeur (depuis le constructeur, il step into le code système qui appelle ConfigureServices via le WebHostBuilder). 

- Observation : Entré dans l'ajout de services (DI container). 

- La pile des appels montre maintenant : Startup.ConfigureServices (ligne 27), [Code externe] (Microsoft.AspNetCore.Hosting interne), Program.BuildWebHost (ligne 14), Program.Main (ligne 10). 

Cela configure la localisation (pour i18n, chemin "Resources"), avant d'ajouter d'autres services comme AddSingleton<ILanguageService>, AddTransient<IProductService>, etc. 

<img width="603" height="276" alt="image" src="https://github.com/user-attachments/assets/78530e7b-00fa-464d-853f-433356dacec5" />


### Après Shift+F11 (Pas à pas sortant) :
- Namespace : P2FixAnAppDotNetCode
- Classe : Program
- Méthode : BuildWebHost – À la ligne 14 : WebHost.CreateDefaultBuilder(args). 
- Mode utilisé : Shift+F11 pour sortir de la méthode actuelle (ConfigureServices) et revenir au caller supérieur. 
- Observation : Retour au builder de l'host web. 
- Variables : args est un tableau vide (string[0]). La pile des appels simplifiée : Program.BuildWebHost (ligne 14), Program.Main (ligne 10). 
Cela prépare l'host avec defaults (Kestrel, IIS, configuration, logging), et appelle UseStartup<Startup>() implicitement (ligne 15 non visible, mais inférée). Temps écoulé : 777 ms pour le build. Après ça, .Build() et .Run() démarrent le serveur, menant aux requêtes comme ProductController.List pour l'affichage produits.


<img width="605" height="262" alt="image" src="https://github.com/user-attachments/assets/305cce1a-19e4-4d56-aff8-c322b31f8b19" />

### 6. Déploiement sous forme d’exécutable Windows
- Publication réussie le 26/11/2025 à 16:15 
- Configuration : Release | autonome | win-x64
- Dossier publié : bin\Release\net8.0\publish\

<img width="1006" height="449" alt="image" src="https://github.com/user-attachments/assets/2cc35d61-f6a9-44d8-88ac-759b15c4b123" />

### 7. Lien vers l’exécutable

https://drive.google.com/file/d/1IOf-UVwqwP8u9sepoa-nwfG9KiIZudga/view?usp=drive_link

