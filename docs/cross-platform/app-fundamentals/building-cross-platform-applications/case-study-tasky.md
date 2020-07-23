---
title: 'Étude de cas d’application multiplateforme : Tasky'
description: Ce document décrit comment l’exemple d’application portable Tasky a été conçu et créé comme une application mobile multiplateforme. Il traite des exigences, de l’interface, du modèle de données, de la fonctionnalité de base, de l’implémentation de l’application, etc.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 87ba471dad102059788695f3fe50633bc1a3de0c
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930180"
---
# <a name="cross-platform-app-case-study-tasky"></a>Étude de cas d’application multiplateforme : Tasky

La *tâche* *portable* est une application de liste de tâches simple. Ce document explique comment il a été conçu et créé, en suivant les instructions du document [Building multiplateforme applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) . La discussion couvre les sujets suivants :

<a name="Design_Process"></a>

## <a name="design-process"></a>Processus de conception

Il est recommandé de créer un plan routier pour ce que vous souhaitez obtenir avant de commencer le codage. Cela est particulièrement vrai pour le développement multiplateforme, où vous créez des fonctionnalités qui seront exposées de plusieurs façons. En commençant par une idée claire de ce que vous créez, vous gagnez du temps et des efforts plus tard dans le cycle de développement.

 <a name="Requirements"></a>

### <a name="requirements"></a>Spécifications

La première étape de la conception d’une application consiste à identifier les fonctionnalités souhaitées. Il peut s’agir d’objectifs de haut niveau ou de cas d’usage détaillés. Tasky a des exigences fonctionnelles simples :

- Afficher une liste de tâches
- Ajouter, modifier et supprimer des tâches
- Définir l’état d’une tâche sur « terminé »

Vous devez prendre en compte votre utilisation des fonctionnalités spécifiques à la plateforme.  Les tâches peuvent-elles tirer parti de la délimitation iOS ou Windows Phone des vignettes dynamiques ? Même si vous n’utilisez pas de fonctionnalités spécifiques à la plateforme dans la première version, vous devez vous assurer que votre entreprise & couches de données peuvent les gérer.

 <a name="User_Interface_Design"></a>

### <a name="user-interface-design"></a>Conception de l'interface utilisateur

Commencez avec une conception de haut niveau qui peut être implémentée sur les plateformes cibles. Prenez soin de noter les contraintes de l’interface utilisateur de la plateforme spécifique. Par exemple, un `TabBarController` dans iOS peut afficher plus de cinq boutons, tandis que le Windows Phone équivalent peut afficher jusqu’à quatre.
Dessinez le bord de l’écran à l’aide de l’outil de votre choix (papier Works).

 [![Dessinez le circuit d’écran à l’aide de l’outil de votre document Choice Works](case-study-tasky-images/taskydesign.png)](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model"></a>

### <a name="data-model"></a>Modèle de données

Le fait de savoir quelles données doivent être stockées vous aidera à déterminer le mécanisme de persistance à utiliser. Consultez [accès aux données multiplateforme](~/cross-platform/app-fundamentals/index.md) pour obtenir des informations sur les mécanismes de stockage disponibles et vous aider à les choisir. Pour ce projet, nous allons utiliser SQLite.NET.

Tasky doit stocker trois propriétés pour chaque « TaskItem » :

- **Name** : chaîne
- **Remarques** -chaîne
- **Terminé** – booléen

 <a name="Core_Functionality"></a>

### <a name="core-functionality"></a>Fonctionnalité de base

Considérez l’API que l’interface utilisateur devra utiliser pour répondre aux exigences. Une liste de tâches requiert les fonctions suivantes :

- **Répertorier toutes les tâches** : pour afficher la liste de l’écran principal de toutes les tâches disponibles
- **Obtenir une tâche** : quand une ligne de tâche est touchée
- **Enregistrer une tâche** : quand une tâche est modifiée
- **Supprimer une tâche** : quand une tâche est supprimée
- **Créer une tâche vide** – quand une nouvelle tâche est créée

Pour permettre la réutilisation du code, cette API doit être implémentée une seule fois dans la *bibliothèque de classes portable*.

 <a name="Implementation"></a>

### <a name="implementation"></a>Implémentation

Une fois que la conception de l’application a été approuvée, réfléchissez à la façon dont elle peut être implémentée en tant qu’application multiplateforme. Cela deviendra l’architecture de l’application. En suivant les instructions du document [Building multiplateforme applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) , le code de l’application doit être divisé en plusieurs parties :

- **Code commun** : projet courant contenant du code réutilisable pour stocker les données de tâche. exposer une classe de modèle et une API pour gérer l’enregistrement et le chargement des données.
- **Code spécifique** à la plateforme : projets spécifiques à la plateforme qui implémentent une interface utilisateur native pour chaque système d’exploitation, en utilisant le code commun comme « back end ».

[![Les projets spécifiques à la plateforme implémentent une interface utilisateur native pour chaque système d’exploitation, en utilisant le code commun comme back end](case-study-tasky-images/taskypro-architecture.png)](case-study-tasky-images/taskypro-architecture.png#lightbox)

Ces deux parties sont décrites dans les sections suivantes.

 <a name="Common_(PCL)_Code"></a>

## <a name="common-pcl-code"></a>Code courant (PCL)

Une tâche portable utilise la stratégie de la bibliothèque de classes portable pour le partage de code commun. Pour obtenir une description des options de partage de code, consultez le document options de partage de [code](~/cross-platform/app-fundamentals/code-sharing.md) .

Tout le code commun, y compris la couche d’accès aux données, le code de base de données et les contrats, est placé dans le projet de bibliothèque.

Le projet PCL complet est illustré ci-dessous. Tout le code de la bibliothèque portable est compatible avec chaque plateforme ciblée. Une fois déployée, chaque application native fait référence à cette bibliothèque.

![Une fois déployée, chaque application native fait référence à cette bibliothèque.](case-study-tasky-images/portable-project.png)

Le diagramme de classes ci-dessous montre les classes regroupées par couche. La `SQLiteConnection` classe est un code réutilisable à partir du package SQLite-net. Le reste des classes est du code personnalisé pour Tasky. Les `TaskItemManager` `TaskItem` classes et représentent l’API exposée aux applications spécifiques à la plateforme.

 [![Les classes TaskItemManager et TaskItem représentent l’API exposée aux applications spécifiques à la plateforme.](case-study-tasky-images/classdiagram-core.png)](case-study-tasky-images/classdiagram-core.png#lightbox)

L’utilisation d’espaces de noms pour séparer les couches permet de gérer les références entre chaque couche. Les projets spécifiques à la plateforme doivent uniquement inclure une `using` instruction pour la couche métier. La couche d’accès aux données et la couche de données doivent être encapsulées par l’API exposée par `TaskItemManager` dans la couche métier.

 <a name="References"></a>

### <a name="references"></a>References

Les bibliothèques de classes portables doivent être utilisables sur plusieurs plateformes, chacune avec différents niveaux de prise en charge pour les fonctionnalités de plateforme et de Framework. Pour cette raison, il existe des limitations sur les packages et les bibliothèques d’infrastructure qui peuvent être utilisés. Par exemple, Xamarin. iOS ne prend pas en charge le `dynamic` mot clé c#. une bibliothèque de classes portable ne peut donc pas utiliser un package qui dépend du code dynamique, même si ce code fonctionne sur Android. Visual Studio pour Mac vous empêchera d’ajouter des packages et des références incompatibles, mais vous souhaiterez garder les limites à l’esprit afin d’éviter les surprises plus tard.

Remarque : vous verrez que vos projets référencent les bibliothèques d’infrastructure que vous n’avez pas utilisées. Ces références sont incluses dans les modèles de projet Xamarin. Lorsque les applications sont compilées, le processus de liaison supprime le code non référencé, donc même si `System.Xml` a été référencé, il ne sera pas inclus dans l’application finale, car nous n’utilisons pas de fonctions XML.

 <a name="Data_Layer_(DL)"></a>

### <a name="data-layer-dl"></a>Couche de données (DL)

La couche de données contient le code qui effectue le stockage physique des données, qu’il s’agisse d’une base de données, de fichiers plats ou d’un autre mécanisme. La couche de données Tasky se compose de deux parties : la bibliothèque SQLite-NET et le code personnalisé ajouté pour l’associer.

Tasky s’appuie sur le package NuGet SQLite-net (publié par Frank Kreuger) pour incorporer le code SQLite-NET qui fournit une interface de base de données de mappage relationnel objet (ORM). La `TaskItemDatabase` classe hérite de `SQLiteConnection` et ajoute les méthodes de création, de lecture, de mise à jour, de suppression (CRUD) requises pour lire et écrire des données dans sqlite. Il s’agit d’une implémentation simple et standard de méthodes CRUD génériques qui pourraient être réutilisées dans d’autres projets.

`TaskItemDatabase`Est un singleton, garantissant que tous les accès sont exécutés sur la même instance. Un verrou est utilisé pour empêcher l’accès simultané à partir de plusieurs threads.

 <a name="SQLite_on_WIndows_Phone"></a>

#### <a name="sqlite-on-windows-phone"></a>SQLite sur Windows Phone

Tandis que iOS et Android sont livrés avec SQLite dans le cadre du système d’exploitation, Windows Phone n’inclut pas de moteur de base de données compatible. Pour partager du code sur les trois plateformes, une version native de Windows Phone de SQLite est requise. Pour plus d’informations sur la configuration de votre projet Windows Phone pour SQLite, consultez [utilisation d’une base de données locale](~/xamarin-forms/data-cloud/data/databases.md) .

 <a name="Using_an_Interface_to_Generalize_Data_Access"></a>

#### <a name="using-an-interface-to-generalize-data-access"></a>Utilisation d’une interface pour généraliser l’accès aux données

La couche de données dépend de afin de pouvoir `BL.Contracts.IBusinessIdentity` implémenter des méthodes d’accès abstraites aux données qui requièrent une clé primaire. Toute classe de couche métier qui implémente l’interface peut ensuite être rendue persistante dans la couche de données.

L’interface spécifie simplement une propriété entière pour agir en tant que clé primaire :

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La classe de base implémente l’interface et ajoute les attributs SQLite-NET pour la marquer comme une clé primaire à incrémentation automatique. Toute classe de la couche métier qui implémente cette classe de base peut ensuite être rendue persistante dans la couche de données :

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

Voici un exemple des méthodes génériques dans la couche de données qui utilisent l’interface `GetItem<T>` :

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access"></a>

#### <a name="locking-to-prevent-concurrent-access"></a>Verrouillage pour empêcher l’accès simultané

Un [verrou](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) est implémenté dans la `TaskItemDatabase` classe pour empêcher l’accès simultané à la base de données. Cela permet de garantir que l’accès simultané à partir de différents threads est sérialisé (sinon, un composant d’interface utilisateur peut tenter de lire la base de données en même temps qu’un thread d’arrière-plan qui le met à jour). Vous trouverez ci-dessous un exemple de la façon dont le verrou est implémenté :

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

La majeure partie du code de la couche de données peut être réutilisée dans d’autres projets. Le seul code spécifique à l’application dans la couche est l' `CreateTable<TaskItem>` appel dans le `TaskItemDatabase` constructeur.

 <a name="Data_Access_Layer_(DAL)"></a>

### <a name="data-access-layer-dal"></a>Couche d’accès aux données (DAL)

La `TaskItemRepository` classe encapsule le mécanisme de stockage des données avec une API fortement typée qui permet la `TaskItem` création, la suppression, la récupération et la mise à jour des objets.

 <a name="Using_Conditional_Compilation"></a>

#### <a name="using-conditional-compilation"></a>Utilisation de la compilation conditionnelle

La classe utilise la compilation conditionnelle pour définir l’emplacement du fichier. il s’agit d’un exemple d’implémentation de la divergence de plateforme. Propriété qui retourne le chemin d’accès à un code différent sur chaque plateforme. Les directives de compilateur spécifiques au code et à la plateforme sont indiquées ici :

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

Selon la plateforme, la sortie sera « <app
path> /Library/TaskDB.db3 » pour iOS, « <app
path> /documents/TaskDB.db3 » pour Android ou simplement « TaskDB. » pour les Windows Phone.

### <a name="business-layer-bl"></a>Couche métier (BL)

La couche métier implémente les classes de modèle et une façade pour les gérer.
Dans Tasky, le modèle est la `TaskItem` classe et `TaskItemManager` implémente le modèle de façade pour fournir une API pour la gestion `TaskItems` .

 <a name="Façade"></a>

#### <a name="faade"></a>Facade

 `TaskItemManager`encapsule le `DAL.TaskItemRepository` pour fournir les méthodes d’extraction, d’enregistrement et de suppression qui seront référencées par l’application et les couches d’interface utilisateur.

Les règles d’entreprise et la logique seraient placées ici si nécessaire (par exemple, toutes les règles de validation qui doivent être satisfaites avant l’enregistrement d’un objet).

 <a name="API_for_Platform-Specific_Code"></a>

### <a name="api-for-platform-specific-code"></a>API pour le code spécifique à la plateforme

Une fois le code commun écrit, l’interface utilisateur doit être créée pour collecter et afficher les données exposées par celui-ci. La `TaskItemManager` classe implémente le modèle de façade pour fournir une API simple à laquelle le code de l’application doit accéder.

Le code écrit dans chaque projet spécifique à la plateforme est généralement étroitement couplé au kit de développement logiciel (SDK) natif de cet appareil et accède uniquement au code commun à l’aide de l’API définie par le `TaskItemManager` . Cela comprend les méthodes et les classes métier qu’il expose, telles que `TaskItem` .

Les images ne sont pas partagées entre les plateformes, mais ajoutées indépendamment à chaque projet. C’est important, car chaque plateforme gère les images différemment, en utilisant des noms de fichiers, des répertoires et des résolutions différents.

Les sections restantes traitent des détails d’implémentation spécifiques à la plateforme de l’interface utilisateur Tasky.

 <a name="iOS_App"></a>

## <a name="ios-app"></a>Application iOS

Il n’existe qu’une poignée de classes requises pour implémenter l’application Tasky iOS à l’aide du projet PCL commun pour stocker et récupérer des données. Le projet iOS Xamarin. iOS complet est illustré ci-dessous :

 ![le projet iOS est présenté ici](case-study-tasky-images/taskyios-solution.png)

Les classes sont présentées dans ce diagramme, regroupées en couches.

 [![Les classes sont présentées dans ce diagramme, regroupées en couches](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>References

L’application iOS fait référence aux bibliothèques du kit de développement logiciel (SDK) spécifiques à la plateforme, par exemple Xamarin. iOS et MonoTouch. boîte de dialogue-1.

Il doit également référencer le `TaskyPortableLibrary` projet PCL.
La liste Références est présentée ici :

 ![La liste Références est présentée ici](case-study-tasky-images/taskyios-references.png)

La couche application et la couche d’interface utilisateur sont implémentées dans ce projet à l’aide de ces références.

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Couche application (AL)

La couche application contient des classes spécifiques à la plateforme requises pour « lier » les objets exposés par la bibliothèque de classes portable à l’interface utilisateur. L’application spécifique à iOS a deux classes pour faciliter l’affichage des tâches :

- **EditingSource** : cette classe permet de lier des listes de tâches à l’interface utilisateur. Étant donné que `MonoTouch.Dialog` a été utilisé pour la liste des tâches, nous devons implémenter cette application d’assistance pour activer la fonctionnalité de balayage à la suppression dans le `UITableView` . L’opération de balayage à la suppression est courante sur iOS, mais pas sur Android ou Windows Phone. par conséquent, le projet spécifique iOS est le seul qui l’implémente.
- **TaskDialog** : cette classe est utilisée pour lier une tâche unique à l’interface utilisateur. Elle utilise l' `MonoTouch.Dialog` API de réflexion pour « encapsuler » l' `TaskItem` objet avec une classe qui contient les attributs corrects pour permettre au format correct de l’écran de saisie.

La `TaskDialog` classe utilise `MonoTouch.Dialog` des attributs pour créer un écran basé sur les propriétés d’une classe. La classe se présente comme suit :

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

Notez que les `OnTap` attributs requièrent un nom de méthode : ces méthodes doivent exister dans la classe où `MonoTouch.Dialog.BindingContext` est créé (dans ce cas, la `HomeScreen` classe décrite dans la section suivante).

 <a name="User_Interface_Layer_(UI)"></a>

### <a name="user-interface-layer-ui"></a>Couche interface utilisateur (IU)

La couche interface utilisateur est constituée des classes suivantes :

1. **AppDelegate** – contient des appels à l’API Appearance pour appliquer un style aux polices et couleurs utilisées dans l’application. Tasky est une application simple, de sorte qu’aucune autre tâche d’initialisation ne s’exécute dans `FinishedLaunching` .
2. **Écrans** : sous-classes de `UIViewController` qui définissent chaque écran et son comportement. Les écrans lient l’interface utilisateur avec les classes de couche d’application et l’API commune ( `TaskItemManager` ). Dans cet exemple, les écrans sont créés dans le code, mais ils peuvent avoir été conçus à l’aide de l’Interface Builder de Xcode ou du concepteur de storyboards.
3. **Images** : les éléments visuels constituent une partie importante de chaque application. Tasky contient des images d’écran de démarrage et d’icône qui, pour iOS, doivent être fournies dans une résolution normale et retine.

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Écran d’accueil

L’écran d’accueil est un `MonoTouch.Dialog` écran qui affiche la liste des tâches de la base de données SQLite. Il hérite de `DialogViewController` et implémente le code pour définir le `Root` pour qu’il contienne une collection d' `TaskItem` objets à afficher.

 [![Elle hérite de DialogViewController et implémente le code pour définir la racine afin qu’elle contienne une collection d’objets TaskItem à afficher.](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox)

Les deux principales méthodes relatives à l’affichage et à l’interaction avec la liste des tâches sont les suivantes :

1. **PopulateTable** : utilise la méthode de la couche métier `TaskManager.GetTasks` pour récupérer une collection d' `TaskItem` objets à afficher.
2. **Sélectionné** : lorsqu’une ligne est touchée, affiche la tâche dans un nouvel écran.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Écran détails de la tâche

Les détails de la tâche sont un écran d’entrée qui permet de modifier ou de supprimer des tâches.

`MonoTouch.Dialog`L’API de réflexion de utilise Tasky pour afficher l’écran, il n’y a donc aucune `UIViewController` implémentation. Au lieu de cela, la `HomeScreen` classe instancie et affiche une `DialogViewController` à l’aide de la `TaskDialog` classe de la couche application.

Cette capture d’écran montre un écran vide qui montre l' `Entry` attribut définissant le texte de filigrane dans les champs **nom** et **Remarques** :

 [![Cette capture d’écran montre un écran vide qui montre l’attribut d’entrée définissant le texte de filigrane dans les champs nom et remarques.](case-study-tasky-images/ios-taskydetail.png)](case-study-tasky-images/ios-taskydetail.png#lightbox)

La fonctionnalité de l’écran détails de la **tâche** (par exemple, l’enregistrement ou la suppression d’une tâche) doit être implémentée dans la `HomeScreen` classe, car il s’agit de l’emplacement où `MonoTouch.Dialog.BindingContext` est créé. Les `HomeScreen` méthodes suivantes prennent en charge l’écran détails de la tâche :

1. **ShowTaskDetails** : crée un `MonoTouch.Dialog.BindingContext` pour afficher un écran. Il crée l’écran d’entrée à l’aide de la réflexion pour récupérer les noms et les types de propriété de la `TaskDialog` classe. Des informations supplémentaires, telles que le texte de filigrane pour les zones de saisie, sont implémentées avec des attributs sur les propriétés.
2. **SaveTask** : cette méthode est référencée dans la `TaskDialog` classe via un `OnTap` attribut. Elle est appelée lorsque l’utilisateur clique sur l' **enregistrement** et utilise un `MonoTouch.Dialog.BindingContext` pour récupérer les données entrées par l’utilisateur avant d’enregistrer les modifications à l’aide de `TaskItemManager` .
3. **DeleteTask** : cette méthode est référencée dans la `TaskDialog` classe via un `OnTap` attribut. Elle utilise `TaskItemManager` pour supprimer les données à l’aide de la clé primaire (propriété ID).

 <a name="Android_App"></a>

## <a name="android-app"></a>Application Android

Le projet Xamarin. Android complet est illustré ci-dessous :

 ![Image du projet Android ici](case-study-tasky-images/taskyandroid-solution.png)

Le diagramme de classes, avec les classes regroupées par couche :

 [![Diagramme de classes, avec les classes regroupées par couche](case-study-tasky-images/classdiagram-android.png)](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>References

Le projet d’application Android doit référencer l’assembly Xamarin. Android propre à la plateforme pour accéder aux classes à partir de l’Android SDK.

Il doit également référencer le projet PCL (par exemple, TaskyPortableLibrary) pour accéder au code de la couche données et métier courantes.

 ![TaskyPortableLibrary pour accéder au code de couche métier et aux données courantes](case-study-tasky-images/taskyandroid-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Couche application (AL)

À l’instar de la version d’iOS que nous avons examinée précédemment, la couche application dans la version Android contient des classes spécifiques à la plateforme requises pour « lier » les objets exposés par le noyau à l’interface utilisateur.

 **TaskListAdapter** : pour afficher la liste \<T> des objets dont nous avons besoin pour implémenter un adaptateur afin d’afficher des objets personnalisés dans un `ListView` . L’adaptateur contrôle la disposition utilisée pour chaque élément de la liste. dans ce cas, le code utilise une disposition intégrée Android `SimpleListItemChecked` .

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interface utilisateur (IU)

La couche d’interface utilisateur de l’application Android est une combinaison de code et de balisage XML.

- **Ressources/disposition** : dispositions d’écran et conception de cellule de ligne implémentées en tant que fichiers AXML. Le AXML peut être écrit manuellement ou disposé visuellement à l’aide du concepteur d’interface utilisateur Xamarin pour Android.
- **Ressources/dessinables** – images (icônes) et bouton personnalisé.
- **Écrans** : sous-classes d’activité qui définissent chaque écran et son comportement. Associe l’interface utilisateur avec les classes de couche d’application et l’API commune ( `TaskItemManager` ).

 <a name="Home_Screen"></a>

#### <a name="home-screen"></a>Écran d’accueil

L’écran d’accueil se compose d’une sous-classe d’activité `HomeScreen` et du `HomeScreen.axml` fichier qui définit la disposition (position du bouton et de la liste des tâches). L’écran se présente comme suit :

 [![L’écran se présente comme suit](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)

Le code d’écran d’accueil définit les gestionnaires pour cliquer sur le bouton et cliquer sur les éléments de la liste, ainsi que remplir la liste dans la `OnResume` méthode (afin qu’elle reflète les modifications apportées dans l’écran détails de la tâche). Les données sont chargées à l’aide des couches métier `TaskItemManager` et `TaskListAdapter` de la couche application.

 <a name="Task_Details_Screen"></a>

#### <a name="task-details-screen"></a>Écran détails de la tâche

L’écran détails de la tâche se compose également d’une `Activity` sous-classe et d’un fichier de disposition AXML. La disposition détermine l’emplacement des contrôles d’entrée et la classe C# définit le comportement de chargement et d’enregistrement des `TaskItem` objets.

 [![La classe définit le comportement de chargement et d’enregistrement des objets TaskItem](case-study-tasky-images/android-taskydetail.png)](case-study-tasky-images/android-taskydetail.png#lightbox)

Toutes les références à la bibliothèque PCL s’effectuent via la `TaskItemManager` classe.

 <a name="Windows_Phone_App"></a>

## <a name="windows-phone-app"></a>Application Windows Phone
Le projet Windows Phone complet :

 ![Windows Phone l’application Windows Phone projet complet](case-study-tasky-images/taskywp7-solution.png)

Le diagramme ci-dessous présente les classes regroupées en couches :

 [![Ce diagramme présente les classes regroupées en couches.](case-study-tasky-images/classdiagram-wp7.png)](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References"></a>

### <a name="references"></a>References

Le projet spécifique à la plateforme doit faire référence aux bibliothèques requises spécifiques à la plateforme (telles que `Microsoft.Phone` et `System.Windows` ) pour créer une application de Windows Phone valide.

Il doit également référencer le projet PCL (par exemple, `TaskyPortableLibrary`) pour utiliser la `TaskItem` classe et la base de données.

 ![TaskyPortableLibrary pour utiliser la classe TaskItem et la base de données](case-study-tasky-images/taskywp7-references.png)

 <a name="Application_Layer_(AL)"></a>

### <a name="application-layer-al"></a>Couche application (AL)

Là encore, comme avec les versions iOS et Android, la couche d’application se compose des éléments non visuels qui permettent de lier des données à l’interface utilisateur.

 <a name="ViewModels"></a>

#### <a name="viewmodels"></a>ViewModels

ViewModels encapsule les données de la bibliothèque de classes portable ( `TaskItemManager` ) et les présente de manière utilisable par la liaison de données Silverlight/XAML. Il s’agit d’un exemple de comportement spécifique à la plateforme (comme indiqué dans le document applications multiplateformes).

 <a name="User_Interface_(UI)"></a>

### <a name="user-interface-ui"></a>Interface utilisateur (IU)

XAML dispose d’une fonctionnalité de liaison de données unique qui peut être déclarée dans le balisage et réduire la quantité de code requise pour afficher les objets :

1. **Pages** : les fichiers XAML et le codebehind définissent l’interface utilisateur et référencent les ViewModels et le projet PCL pour afficher et collecter des données.
2. **Images** : l’écran de démarrage, les images d’arrière-plan et d’icône sont une partie essentielle de l’interface utilisateur.

 <a name="MainPage"></a>

#### <a name="mainpage"></a>MainPage

La classe MainPage utilise le `TaskListViewModel` pour afficher des données à l’aide des fonctionnalités de liaison de données du langage XAML. La page `DataContext` est définie sur le modèle de vue, qui est rempli de façon asynchrone. La `{Binding}` syntaxe dans le code XAML détermine la façon dont les données sont affichées.

 <a name="TaskDetailsPage"></a>

#### <a name="taskdetailspage"></a>TaskDetailsPage

Chaque tâche est affichée en liant le `TaskViewModel` au XAML défini dans TaskDetailsPage. Xaml. Les données de la tâche sont récupérées par le biais du `TaskItemManager` dans la couche métier.

 <a name="Results"></a>

## <a name="results"></a>Résultats

Les applications qui en résultent ressemblent à ceci sur chaque plateforme :

 <a name="iOS"></a>

### <a name="ios"></a>iOS

L’application utilise la conception d’interface utilisateur standard iOS, telle que le bouton « Ajouter » placé dans la barre de navigation et l’utilisation de l’icône **plus (+)** intégrée. Il utilise également le comportement par défaut du `UINavigationController` bouton « précédent » et prend en charge l’opération « balayer-supprimer » dans la table.

 [ ![ Il utilise également le comportement du bouton précédent par défaut UINavigationController et prend en charge l’opération de balayage à la suppression dans la table](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox) . [ ![ il utilise également le comportement du bouton précédent par défaut UINavigationController et prend en charge l’opération de balayage à la suppression dans la table](case-study-tasky-images/ios-taskylist.png)](case-study-tasky-images/ios-taskylist.png#lightbox) .

 <a name="Android"></a>

### <a name="android"></a>Android

L’application Android utilise des contrôles intégrés, notamment la mise en page intégrée pour les lignes qui nécessitent l’affichage d’un « Tick ». Le comportement de retour matériel/système est pris en charge en plus du bouton de retour à l’écran.

 [ ![ Le comportement de retour matériel/système est pris en charge en plus du bouton de retour](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox)à l’écran.[ ![ le comportement du matériel ou du système est pris en charge en plus du bouton de retour à l’écran](case-study-tasky-images/android-taskylist.png)](case-study-tasky-images/android-taskylist.png#lightbox) .

 <a name="Windows_Phone"></a>

### <a name="windows-phone"></a>Windows Phone

L’application Windows Phone utilise la disposition standard, en remplissant la barre de l’application en bas de l’écran au lieu d’une barre de navigation en haut.

 [ ![ L’application Windows Phone utilise la disposition standard, en remplissant la barre de l’application en bas de l’écran au lieu d’une barre de navigation en haut](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox) de [ ![ l’application Windows Phone utilise la disposition standard, en remplissant la barre de l’application en bas de l’écran au lieu d’une barre de navigation en haut](case-study-tasky-images/wp-taskylist.png)](case-study-tasky-images/wp-taskylist.png#lightbox) .

 <a name="Summary"></a>

## <a name="summary"></a>Résumé

Ce document vous a fourni une explication détaillée de la façon dont les principes de conception d’applications en couches ont été appliqués à une application simple pour faciliter la réutilisation du code sur trois plateformes mobiles : iOS, Android et Windows Phone.

Il a décrit le processus utilisé pour concevoir les couches d’application et décrit les &amp; fonctionnalités de code qui ont été implémentées dans chaque couche.

Le code peut être téléchargé à partir de [GitHub](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Liens associés

- [Création d’applications multiplateformes (document principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Exemple d’application portable Tasky (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
