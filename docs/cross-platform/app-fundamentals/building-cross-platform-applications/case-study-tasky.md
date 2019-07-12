---
title: 'Étude de cas d’application multiplateforme : Tasky'
description: Ce document décrit comment l’exemple d’application Tasky Portable a été conçu et généré sous la forme d’une application mobile multiplateforme. Il aborde l’application exigences, interface, modèle de données, fonctionnalités principales, implémentation et bien plus encore.
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 7bea80c22f6931858d0629382f6882203dfd374f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829939"
---
# <a name="cross-platform-app-case-study-tasky"></a>Étude de cas d’application multiplateforme : Tasky

*Tasky* *Portable* est une application de liste de tâches simple. Ce document explique comment il a été conçu et généré, suivre les instructions de la [création d’Applications inter-plateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) document. La discussion couvre les domaines suivants :

<a name="Design_Process" />

## <a name="design-process"></a>Processus de conception

Il est recommandé de créer un de la feuille de route pour ce que vous voulez atteindre avant de commencer à coder. Cela est particulièrement vrai pour le développement multiplateforme, où vous générez des fonctionnalités qui seront exposées de plusieurs façons. En commençant par une idée précise de ce que vous créez gagner du temps et des efforts plus loin dans le cycle de développement.

 <a name="Requirements" />

### <a name="requirements"></a>Spécifications

La première étape de la conception d’une application consiste à identifier les fonctionnalités souhaitées. Il peut s’agir des objectifs de haut niveau ou détaillés des cas d’usage. Tasky a des exigences fonctionnelles simples :

-  Afficher la liste des tâches
-  Ajouter, modifier et supprimer des tâches
-  Définir le statut d’une tâche à « terminé »

Vous devez envisager l’utilisation de fonctionnalités spécifiques aux plateformes.  Tasky bénéficient de gardiennage virtuel iOS ou Windows Phone des vignettes dynamiques ? Même si vous n’utilisez pas les fonctionnalités spécifiques à la plateforme dans la première version, vous devez planifier à l’avance pour vous assurer que les couches de données et de votre entreprise peuvent prendre en charge les.

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>Conception de l'interface utilisateur

Démarrez avec une conception de haut niveau qui peut être implémentée sur les plateformes cibles. Prenez soin de contraintes d’interface utilisateur Remarque spécifique à la plateforme. Par exemple, un `TabBarController` dans iOS peuvent afficher plus de cinq boutons, tandis que l’équivalent Windows Phone peut afficher jusqu'à quatre.
Dessinez le flux d’écran à l’aide de l’outil de votre choix (livre fonctionne).

 [![](case-study-tasky-images/taskydesign.png "Dessinez le flux d’écran à l’aide de l’outil de vos travaux de papier de choix")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>Modèle de données

En sachant quelles données doivent être stockées pour déterminer quel mécanisme de persistance à utiliser. Consultez [accès aux données de Cross-Platform](~/cross-platform/app-fundamentals/index.md) pour plus d’informations sur les mécanismes de stockage disponibles et déterminer plus facilement entre eux. Pour ce projet, nous allons utiliser SQLite.NET.

Tasky a besoin de stocker trois propriétés pour chaque « TaskItem » :

- **Nom** : chaîne
- **Notes de publication** : chaîne
- **Fait** : booléen

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>Fonctionnalité de base

Envisagez de l’API de l’interface utilisateur sera devront l’utiliser pour répondre aux exigences. Une liste de tâches requiert les fonctions suivantes :

- **Liste de toutes les tâches** : permet d’afficher la liste de l’écran principal de toutes les tâches disponibles
- **Obtenir une tâche** – lorsqu’une ligne de tâche est touchée.
- **Enregistrer une tâche** – lorsqu’une tâche est modifiée.
- **Supprimer une tâche** – lorsqu’une tâche est supprimée.
- **Créer une tâche vide** – lorsqu’une tâche est créée.

Pour obtenir la réutilisation du code, cette API doit être implémentée qu’une seule fois dans le *bibliothèque de classes Portable*.

 <a name="Implementation" />

### <a name="implementation"></a>Implémentation

Une fois que la conception de l’application a été acceptée, pensez comment elle peut être implémentée comme une application multiplateforme. Cela deviendra l’architecture de l’application. Suivre les instructions de la [création d’Applications inter-plateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) document, le code d’application doit être rompu vers le bas dans les sections suivantes :

- **Code commun** – un projet commun qui contient le code de nouveau utilisable pour stocker les données de tâche ; expose une classe de modèle et une API pour gérer l’enregistrement et chargement des données.
- **Code spécifique à la plateforme** – projets spécifiques à la plateforme qui implémentent une interface utilisateur native pour chaque système d’exploitation, utilisant le code commun en tant que serveur principal.

[![](case-study-tasky-images/taskypro-architecture.png "Projets propres à la plateforme implémentent une interface utilisateur native pour chaque système d’exploitation, utilisant le code commun en tant que le serveur principal")](case-study-tasky-images/taskypro-architecture.png#lightbox)

Ces deux parties sont décrits dans les sections suivantes.

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>Code de (bibliothèque de classes portable) commun

Tasky Portable utilise la stratégie de la bibliothèque de classes portables pour partager du code commun. Consultez le [Sharing Code Options](~/cross-platform/app-fundamentals/code-sharing.md) document pour obtenir une description des options de partage de code.

Tout le code commun, y compris la couche d’accès aux données, de code de base de données et de contrats, est placé dans le projet de bibliothèque.

Le projet de bibliothèque de classes portable complet est illustré ci-dessous. Tout le code dans la bibliothèque portable est compatible avec chaque plateforme ciblée. Lors du déploiement, chaque application native fait référence à cette bibliothèque.

![](case-study-tasky-images/portable-project.png "Lors du déploiement, chaque application native fait référence à cette bibliothèque")

Le diagramme de classes ci-dessous montre les classes regroupées par couche. Le `SQLiteConnection` classe correspond à du code à partir du package Sqlite-NET. Le reste des classes sont code personnalisé pour Tasky. Le `TaskItemManager` et `TaskItem` classes représentent l’API est exposée aux applications spécifiques à la plateforme.

 [![](case-study-tasky-images/classdiagram-core.png "Les classes TaskItemManager et TaskItem représentent l’API est exposée aux applications spécifiques à la plateforme")](case-study-tasky-images/classdiagram-core.png#lightbox)

À l’aide des espaces de noms pour séparer les couches permet de gérer les références entre chaque couche. Les projets spécifiques à la plateforme ne doit inclure un `using` instruction pour la couche métier. La couche d’accès aux données et la couche données doivent être encapsulé par l’API est exposée par `TaskItemManager` dans la couche métier.

 <a name="References" />

### <a name="references"></a>Références

Bibliothèques de classes portables doivent être utilisables sur plusieurs plateformes, chacune avec différents niveaux de prise en charge des fonctionnalités de plate-forme et infrastructure. Pour cette raison, il existe les limitations sur lequel les packages et bibliothèques de framework peuvent être utilisés. Par exemple, Xamarin.iOS ne prend pas en charge le langage c# `dynamic` mot clé, une bibliothèque de classes portable ne pouvez pas utiliser n’importe quel package dépend de code dynamique, même si ce code fonctionne sur Android. Visual Studio pour Mac vous empêchera d’ajouter des packages incompatibles et les références, mais vous voudrez garder limitations à l’esprit afin d’éviter les surprises par la suite.

Remarque : Vous verrez que vos projets de référencent des bibliothèques de framework que vous n’avez pas utilisé. Ces références sont inclus dans le cadre des modèles de projet Xamarin. Lorsque les applications sont compilées, le processus de liaison supprimer le code non référencé, par conséquent, même si `System.Xml` a été référencé, il ne pas être inclus dans l’application finale, car nous n’utilisons pas toutes les fonctions Xml.

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>Couche données (DL)

La couche données contient le code qui effectue le stockage physique des données, que ce soit sur une base de données, fichiers plats ou un autre mécanisme. La couche données Tasky se compose de deux parties : la bibliothèque de SQLite-NET et le code personnalisé est ajouté à le connecter.

Tasky s’appuie sur le package nuget de Sqlite-net (publié par Frank Kreuger) pour incorporer du code SQLite-NET qui fournit une interface de base de données de mappage objet-relationnel (ORM). Le `TaskItemDatabase` hérite de la classe `SQLiteConnection` et ajoute le requise création, lecture, mise à jour, les méthodes de suppression (CRUD) pour lire et écrire des données dans SQLite. C’est une implémentation simple réutilisable de méthodes CRUD génériques qui peuvent être réutilisées dans d’autres projets.

Le `TaskItemDatabase` est un singleton, en garantissant que tous les accès se fait par rapport à la même instance. Un verrou est utilisé pour empêcher les accès simultanés à partir de plusieurs threads.

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>SQLite sur Windows Phone

Tout iOS et Android sont fournis avec SQLite en tant que partie du système d’exploitation, Windows Phone n’inclut pas d’un moteur de base de données compatible. Pour partager du code entre les trois plateformes, une version de téléphone native de Windows de SQLite est nécessaire. Consultez [fonctionne avec une base de données locale](~/xamarin-forms/data-cloud/data/databases.md) pour plus d’informations sur la configuration de votre projet Windows Phone pour Sqlite.

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>À l’aide d’une Interface pour généraliser l’accès aux données

La couche de données dépendant de `BL.Contracts.IBusinessIdentity` afin qu’elle peut implémenter des méthodes d’accès de données abstrait qui nécessitent une clé primaire. N’importe quelle classe de couche métier qui implémente l’interface peut ensuite être conservé dans la couche données.

L’interface spécifie simplement une propriété entière pour agir en tant que la clé primaire :

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

La classe de base implémente l’interface et ajoute les attributs de SQLite-NET pour la marquer comme une clé primaire auto-incrémentée. N’importe quelle classe dans la couche métier qui implémente cette classe de base peut ensuite être conservé dans la couche données :

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

S’agit-il d’un exemple de méthodes génériques dans la couche données qui utilisent l’interface `GetItem<T>` méthode :

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>Le verrouillage pour empêcher les accès simultanés

Un [verrou](https://msdn.microsoft.com/library/c5kehkcz(v=vs.100).aspx) est implémenté dans le `TaskItemDatabase` classe pour empêcher les accès simultanés à la base de données. Il s’agit pour garantir l’accès simultané à partir de différents threads est sérialisé (sinon, un composant d’interface utilisateur peut tenter de lire la base de données en même temps un thread d’arrière-plan est la mise à jour). Voici un exemple de la façon dont le verrou est implémenté ici :

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

La plupart du code de couche données peut être réutilisée dans d’autres projets. Le code uniquement spécifiques à l’application dans la couche est le `CreateTable<TaskItem>` appeler dans le `TaskItemDatabase` constructeur.

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>Couche d’accès aux données (DAL)

Le `TaskItemRepository` classe encapsule le mécanisme de stockage de données avec une API fortement typée qui permet de `TaskItem` objets doit être créé, supprimé, récupérés et mis à jour.

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>À l’aide de la Compilation conditionnelle

La classe utilise la compilation conditionnelle pour définir l’emplacement du fichier : il s’agit d’un exemple d’implémentation de Divergence de plateforme. La propriété qui retourne le chemin d’accès se compile en un code différent sur chaque plateforme. Le code et les directives du compilateur spécifiques à la plateforme sont affichés ici :

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

Selon la plateforme, la sortie sera «<app
path>/Library/TaskDB.db3 » pour iOS, «<app
path>/Documents/TaskDB.db3 » pour Android ou simplement « TaskDB.db3 » pour Windows Phone.

### <a name="business-layer-bl"></a>Couche métier (BL)

La couche métier implémente les classes de modèle et une Façade pour les gérer.
Dans Tasky le modèle est le `TaskItem` classe et `TaskItemManager` implémente le modèle de Façade pour fournir une API de gestion de `TaskItems`.

 <a name="Façade" />

#### <a name="faade"></a>Façade

 `TaskItemManager` encapsule le `DAL.TaskItemRepository` pour assurer la Get, enregistrer et supprimer des méthodes qui seront référencées par l’Application et les couches d’interface utilisateur.

Logique et des règles d’entreprise seraient de placer ici si nécessaire : par exemple les règles de validation qui doivent être satisfaites avant l’enregistrée d’un objet.

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>API pour le Code spécifique à la plateforme

Une fois que le code commun a été écrit, l’interface utilisateur doit être généré pour collecter et afficher les données exposées par celui-ci. Le `TaskItemManager` classe implémente le modèle de Façade pour fournir une API simple pour le code d’application à accéder.

Le code écrit dans chaque projet spécifique à la plateforme sera généralement être étroitement couplé avec le Kit de développement natif de cet appareil et accéder uniquement le code commun à l’aide de l’API définie par le `TaskItemManager`. Cela inclut les méthodes et entreprise classes il expose, telles que `TaskItem`.

Les images ne sont pas partagés entre les plateformes mais ajoutées indépendamment à chaque projet. Ceci est important, car chaque plateforme gère les images différemment, à l’aide de solutions, des répertoires et des noms de fichiers différents.

Les sections restantes décrivent les détails d’implémentation spécifique à la plateforme de l’interface utilisateur Tasky.

 <a name="iOS_App" />

## <a name="ios-app"></a>Application iOS

Il existe quelques classes nécessaires pour implémenter l’iOS application Tasky à l’aide de projet de bibliothèque PCL commun pour stocker et récupérer des données. Le projet Xamarin.iOS iOS complet est indiqué ci-dessous :

 ![](case-study-tasky-images/taskyios-solution.png "projet iOS est illustré ici")

Les classes sont indiquées dans ce diagramme, regroupé en couches.

 [![](case-study-tasky-images/classdiagram-android.png "Les classes sont indiquées dans ce diagramme, regroupé en couches")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Références

L’application iOS référence les bibliothèques SDK spécifique à la plateforme – par exemple. Xamarin.iOS et MonoTouch.Dialog-1.

Il doit également faire référence à la `TaskyPortableLibrary` projet de bibliothèque de classes portable.
La liste des références est illustrée ici :

 ![](case-study-tasky-images/taskyios-references.png "La liste de références est illustrée ici")

La couche Application et la couche d’Interface utilisateur sont implémentés dans ce projet à l’aide de ces références.

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Couche d’application (AL)

La couche d’Application contient des classes spécifiques à la plateforme requises pour « lier » les objets exposés par la bibliothèque PCL pour l’interface utilisateur. L’application spécifique à iOS a deux classes pour afficher des tâches :

- **EditingSource** : cette classe est utilisée pour lier des listes de tâches à l’interface utilisateur. Étant donné que `MonoTouch.Dialog` a été utilisé pour la liste des tâches, nous devons implémenter ce programme d’assistance pour activer les fonctionnalités de balayage pour suppression dans le `UITableView` . Balayez-delete est courant sur iOS, mais pas sur Android ou Windows Phone, par conséquent, le projet spécifique iOS est le seul qui l’implémente.
- **TaskDialog** : cette classe est utilisée pour lier une seule tâche à l’interface utilisateur. Il utilise le `MonoTouch.Dialog` API de réflexion pour « encapsuler » le `TaskItem` objet avec une classe qui contient les attributs corrects pour autoriser l’écran d’entrée à mettre en forme correctement.

Le `TaskDialog` classe utilise `MonoTouch.Dialog` attributs pour créer un écran basé sur les propriétés d’une classe. La classe se présente comme suit :

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

Notez que le `OnTap` attributs nécessitent un nom de méthode : ces méthodes doivent exister dans la classe où le `MonoTouch.Dialog.BindingContext` est créé (dans ce cas, la `HomeScreen` classe décrit dans la section suivante).

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>Couche d’Interface utilisateur (IU)

La couche d’Interface utilisateur se compose des classes suivantes :

1.   **AppDelegate** – contient des appels à l’API d’apparence de style des polices et couleurs utilisées dans l’application. Tasky étant une application simple sans d’autres tâches d’initialisation en cours d’exécution `FinishedLaunching` .
2.   **Écrans** – sous-classes de `UIViewController` qui définissent chaque écran et son comportement. Écrans relier l’interface utilisateur avec des classes de couche d’Application et l’API courantes ( `TaskItemManager` ). Dans cet exemple, les écrans sont créés dans le code, mais elles pourraient ont été conçues à l’aide Interface Builder de Xcode ou le Concepteur de plan conceptuel.
3.   **Images** – éléments visuels sont une partie importante de toutes les applications. Tasky inclut démarrage écran et l’icône des images, qui doivent être fournis en Normal et de résolution de la rétine pour iOS.

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Écran d’accueil

L’écran d’accueil est un `MonoTouch.Dialog` écran qui affiche une liste de tâches à partir de la base de données SQLite. Il hérite de `DialogViewController` et implémente le code pour définir le `Root` de contenir une collection de `TaskItem` objets pour l’affichage.

 [![](case-study-tasky-images/ios-taskylist.png "Il hérite de DialogViewController et implémente le code permettant de définir la racine pour contenir une collection d’objets TaskItem pour l’affichage")](case-study-tasky-images/ios-taskylist.png#lightbox)

Les deux méthodes principales liées à afficher et interagir avec la liste des tâches sont :

1.   **PopulateTable** – utilise la couche métier `TaskManager.GetTasks` méthode pour récupérer une collection de `TaskItem` objets à afficher.
2.   **Sélectionné** – lorsqu’une ligne est touchée, affiche la tâche dans un nouvel écran.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Écran de détails de tâche

Détails de la tâche est un écran d’entrée qui permet aux tâches d’être modifié ou supprimé.

Utilise tasky `MonoTouch.Dialog`d’API de réflexion pour afficher l’écran, il y a donc aucun `UIViewController` implémentation. Au lieu de cela, le `HomeScreen` classe instancie et affiche un `DialogViewController` à l’aide de la `TaskDialog` classe à partir de la couche d’Application.

Cette capture d’écran montre un écran vide qui illustre le `Entry` attribut à définir le texte de filigrane dans le **nom** et **Notes** champs :

 [![](case-study-tasky-images/ios-taskydetail.png "Cette capture d’écran montre un écran vide qui montre l’attribut d’entrée définissant le texte de filigrane dans les champs nom et les notes de publication")](case-study-tasky-images/ios-taskydetail.png#lightbox)

Les fonctionnalités de la **détails de la tâche** écran (par exemple, l’enregistrement ou la suppression d’une tâche) doit être implémentée dans le `HomeScreen` classe, car c’est là où le `MonoTouch.Dialog.BindingContext` est créé. Ce qui suit `HomeScreen` méthodes prennent en charge de l’écran de détails de la tâche :

1.   **ShowTaskDetails** – crée un `MonoTouch.Dialog.BindingContext` pour afficher un écran. Il crée l’écran d’entrée à l’aide de la réflexion pour récupérer les noms de propriété et les types à partir de la `TaskDialog` classe. Des informations supplémentaires, telles que le texte de filigrane pour les zones de saisie sont implémentées avec des attributs sur les propriétés.
2.   **SaveTask** – cette méthode est référencée dans le `TaskDialog` classe un `OnTap` attribut. Elle est appelée lorsque **enregistrer** est enfoncée et utilise un `MonoTouch.Dialog.BindingContext` pour récupérer les données entrées par l’utilisateur avant d’enregistrer les modifications apportées à l’aide de `TaskItemManager` .
3.   **DeleteTask** – cette méthode est référencée dans le `TaskDialog` classe un `OnTap` attribut. Il utilise `TaskItemManager` pour supprimer les données à l’aide de la clé primaire (propriété d’ID).

 <a name="Android_App" />

## <a name="android-app"></a>Application Android

Le projet Xamarin.Android complet est illustré ci-dessous :

 ![](case-study-tasky-images/taskyandroid-solution.png "Projet Android est illustrée ici")

Le diagramme de classes, avec les classes regroupées par couche :

 [![](case-study-tasky-images/classdiagram-android.png "Le diagramme de classes, avec les classes regroupées par couche")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>Références

Le projet d’application Android doit référencer l’assembly de Xamarin.Android spécifique à la plateforme pour accéder aux classes du Kit Android SDK.

Il doit également référencer le projet de bibliothèque de classes portable (par exemple). TaskyPortableLibrary) pour accéder au code de couche métier et de données courantes.

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary pour accéder au code de couche métier et de données courants")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Couche d’application (AL)

Similaire à la version iOS que nous avons vu précédemment, la couche Application dans la version Android contient les classes spécifiques à la plate-forme requis pour « lier » les objets exposés par le noyau à l’interface utilisateur.

 **TaskListAdapter** : permet d’afficher une liste<T> d’objets, nous devons implémenter un adaptateur pour afficher des objets personnalisés dans un `ListView`. L’adaptateur de contrôle de disposition est utilisée pour chaque élément dans la liste : dans ce cas le code utilise une disposition Android intégrée `SimpleListItemChecked`.

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface utilisateur (IU)

Couche d’Interface utilisateur de l’application Android est une combinaison de code et le balisage XML.

- **Ressources/disposition** – conception implémentée en tant que fichiers AXML de cellule de dispositions d’écran et la ligne. Le AXML peut être écrit à la main ou disposé que visuellement à l’aide du Concepteur d’interface utilisateur Xamarin pour Android.
- **Ressources/Drawable** – images (icônes) et un bouton personnalisé.
- **Écrans** – sous-classes d’activité qui définissent chaque écran et son comportement. Relie l’interface utilisateur avec des classes de couche d’Application et l’API courantes (`TaskItemManager`).

 <a name="Home_Screen" />

#### <a name="home-screen"></a>Écran d’accueil

L’écran d’accueil se compose d’une sous-classe de l’activité `HomeScreen` et `HomeScreen.axml` fichier qui définit la disposition (position de la liste de bouton et de la tâche). L’écran ressemble à ceci :

 [![](case-study-tasky-images/android-taskylist.png "L’écran ressemble à ceci")](case-study-tasky-images/android-taskylist.png#lightbox)

Le code de l’écran d’accueil définit les gestionnaires pour en cliquant sur le bouton et en cliquant sur les éléments dans la liste, ainsi que pour remplir la liste dans le `OnResume` (méthode) (afin qu’il reflète les modifications apportées dans l’écran de détails de tâche). Chargement des données à l’aide de la couche métier `TaskItemManager` et `TaskListAdapter` à partir de la couche d’Application.

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>Écran de détails de tâche

L’écran de détails de tâche est également constitué d’un `Activity` sous-classe et un fichier de disposition AXML. La disposition détermine l’emplacement des contrôles d’entrée et la C# classe définit le comportement pour charger et enregistrer `TaskItem` objets.

 [![](case-study-tasky-images/android-taskydetail.png "La classe définit le comportement pour charger et enregistrer les objets TaskItem")](case-study-tasky-images/android-taskydetail.png#lightbox)

Toutes les références à la bibliothèque PCL sont effectuent via le `TaskItemManager` classe.

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Application du Windows Phone
Le projet Windows Phone :

 ![](case-study-tasky-images/taskywp7-solution.png "Application du Windows Phone l’ensemble du projet Windows Phone")

Le diagramme ci-dessous présente les classes regroupées en couches :

 [![](case-study-tasky-images/classdiagram-wp7.png "Ce diagramme présente les classes regroupées en couches")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>Références

Le projet spécifique à la plateforme doit référencer les bibliothèques spécifiques à la plateforme requises (tel que `Microsoft.Phone` et `System.Windows`) pour créer une application Windows Phone valide.

Il doit également référencer le projet de bibliothèque de classes portable (par exemple). `TaskyPortableLibrary`) pour utiliser le `TaskItem` classe et la base de données.

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary d’utiliser la classe de TaskItem et de la base de données")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>Couche d’application (AL)

Là encore, comme avec les versions iOS et Android, la couche d’application se compose des éléments non visuels permettant de lier des données à l’interface utilisateur.

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

ViewModels encapsuler des données à partir de la bibliothèque PCL ( `TaskItemManager`) et il présente de manière qui peut être utilisé par la liaison de données Silverlight/XAML. Il s’agit d’un exemple de comportement spécifique à la plateforme (comme indiqué dans le document des Applications multiplateformes).

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>Interface utilisateur (IU)

XAML comprend une fonctionnalité de liaison de données unique qui peut être déclarée dans le balisage et de réduire la quantité de code nécessaire pour afficher les objets suivants :

1.   **Pages** – les fichiers XAML et son code-behind définissent l’interface utilisateur et les ViewModels et le projet de bibliothèque de classes portable pour afficher et de collecter des données de référence.
2.   **Images** – images d’écran, en arrière-plan et icône de démarrage sont une partie essentielle de l’interface utilisateur.

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

La classe MainPage utilise le `TaskListViewModel` pour afficher des données à l’aide des fonctionnalités de liaison de données du XAML. La page `DataContext` est définie sur le modèle de vue, qui est rempli de façon asynchrone. Le `{Binding}` syntaxe dans le XAML détermine la façon dont les données sont affichées.

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

Chaque tâche s’affiche en liant la `TaskViewModel` pour le XAML défini dans le TaskDetailsPage.xaml. Les données de tâche sont récupérées la `TaskItemManager` dans la couche métier.

 <a name="Results" />

## <a name="results"></a>Résultats

Les applications qui en résulte se présenter comme suit sur chaque plateforme :

 <a name="iOS" />

#### <a name="ios"></a>iOS

L’application utilise la conception de l’interface utilisateur iOS-standard, tels que le bouton « Ajouter » qui est positionné dans la barre de navigation et à l’aide intégrée **plus (+)** icône. Il utilise également la valeur par défaut `UINavigationController` bouton « arrière » le comportement et prend en charge balayez-à-delete dans la table.

 [![](case-study-tasky-images/ios-taskylist.png "Il utilise le comportement du bouton précédent de UINavigationController par défaut et prend en charge le balayage à supprimer dans la table d’également")](case-study-tasky-images/ios-taskylist.png#lightbox) [![](case-study-tasky-images/ios-taskylist.png "il utilise également la valeur par défaut UINavigationController sauvegarder le comportement du bouton et prend en charge le balayage à supprimer dans la table")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

L’application Android utilise des contrôles intégrés, y compris la mise en page intégré pour les lignes qui nécessitent une graduation affichée. Le comportement précédent du matériel/système est prise en charge en plus d’un bouton à l’écran précédent.

 [![](case-study-tasky-images/android-taskylist.png "Le comportement précédent du matériel/système est prise en charge en plus d’un bouton à l’écran précédent")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "le comportement précédent du matériel/système est prise en charge en plus d’un à l’écran bouton précédent")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

L’application Windows Phone utilise la mise en forme standard, remplissage de la barre des applications en bas de l’écran au lieu d’une barre de navigation en haut.

 [![](case-study-tasky-images/wp-taskylist.png "L’application Windows Phone utilise la mise en page standard, remplissage de la barre des applications au bas de l’écran au lieu d’une barre de navigation en haut")](case-study-tasky-images/wp-taskylist.png#lightbox) [![](case-study-tasky-images/wp-taskylist.png "de Windows Phone l’application utilise le standard mise en page, remplissage de la barre des applications au bas de l’écran au lieu d’une barre de navigation en haut")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Récapitulatif

Ce document a fourni une explication détaillée de la façon dont les principes de conception de l’application en couches ont été appliqués à une application simple pour faciliter la réutilisation de code sur trois plateformes mobiles : iOS, Android et Windows Phone.

Il a décrit le processus utilisé pour concevoir les couches application et décrit ce que le code &amp; fonctionnalité a été implémentée dans chaque couche.

Le code peut être téléchargé à partir de [github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable).

## <a name="related-links"></a>Liens associés

- [Création d’Applications inter-plateformes (document principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky Portable exemple d’application (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
