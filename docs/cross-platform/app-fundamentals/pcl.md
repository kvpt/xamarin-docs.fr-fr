---
title: Présentation des bibliothèques de classes portables (PCL)
description: Cet article présente les projets de bibliothèque de classes portables (PCL) et vous guide dans la création et l’utilisation de projets PCL dans Visual Studio pour Mac et Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a1672937b8c7c0ce429b82aee34ecf45aa59d4c8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457417"
---
# <a name="portable-class-libraries-pcl"></a>Bibliothèques de classes portables (PCL)

> [!TIP]
> Les bibliothèques de classes portables (classes portables) sont considérées comme déconseillées dans les dernières versions de Visual Studio.
> Bien que vous puissiez toujours ouvrir, modifier et compiler des classes portables, pour les nouveaux projets, il est recommandé d’utiliser des [bibliothèques .net standardes](~/cross-platform/app-fundamentals/net-standard.md) pour accéder à une plus grande surface d’exposition de l’API.

Un composant clé de la création d’applications multiplateformes est la possibilité de partager du code entre différents projets spécifiques à la plateforme. Toutefois, cela est compliqué par le fait que les différentes plateformes utilisent souvent un sous-ensemble différent de la bibliothèque de classes de base .NET (BCL) et, par conséquent, sont réellement créées dans un autre profil de bibliothèque .NET Core. Cela signifie que chaque plateforme peut uniquement utiliser des bibliothèques de classes qui sont ciblées vers le même profil, de sorte qu’elles semblent exiger des projets de bibliothèque de classes distincts pour chaque plateforme.

Il existe trois approches majeures du partage de code qui traitent de ce problème : les **projets .NET standard**, les projets de **ressources partagées**et les **projets de bibliothèque de classes portable (PCL)**.

- Les **projets .NET standard** sont l’approche préférée pour le partage de code .net, en savoir plus sur [les projets .NET standard et les Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- Les **projets d’actifs partagés** utilisent un ensemble unique de fichiers et offrent un moyen simple et rapide de partager du code dans une solution et utilise généralement des directives de compilation conditionnelle pour spécifier des chemins de code pour les différentes plateformes qui l’utiliseront (pour plus d’informations, consultez l' [article projets partagés](~/cross-platform/app-fundamentals/shared-projects.md)).
- Les projets **PCL** ciblent des profils spécifiques qui prennent en charge un jeu connu de classes ou de fonctionnalités BCL. Toutefois, le côté vers PCL est qu’ils nécessitent souvent des efforts architecturaux supplémentaires pour séparer le code spécifique à un profil dans ses propres bibliothèques.

Cette page explique comment créer un projet **PCL** qui cible un profil spécifique, qui peut ensuite être référencé par plusieurs projets spécifiques à la plateforme.

## <a name="what-is-a-portable-class-library"></a>Qu’est-ce qu’une bibliothèque de classes portables ?

Lorsque vous créez un projet d’application ou un projet de bibliothèque, la DLL résultante est limitée à l’utilisation de la plateforme spécifique pour laquelle elle est créée. Cela vous empêche d’écrire un assembly pour une application Windows, puis de le réutiliser sur Xamarin. iOS et Xamarin. Android.

Toutefois, lorsque vous créez une bibliothèque de classes portable, vous pouvez choisir une combinaison de plateformes sur lesquelles vous souhaitez que votre code s’exécute. Les choix de compatibilité que vous effectuez lors de la création d’une bibliothèque de classes portable sont traduits en un identificateur « profil », qui décrit les plateformes prises en charge par la bibliothèque.

Le tableau ci-dessous présente certaines des fonctionnalités qui varient en fonction de la plateforme .NET. Pour écrire un assembly PCL dont l’exécution est garantie sur des appareils/plateformes spécifiques, vous choisissez simplement la prise en charge requise lorsque vous créez le projet.

|Caractéristique|.NET Framework|Applications UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Core|O|O|O|O|O|
|LINQ|O|O|O|O|O|
|IQueryable|O|O|O|7,5 +|O|
|Sérialisation|O|O|O|O|O|
|Annotations de données|4.0.3 +|O|O||O|

La colonne Xamarin reflète le fait que Xamarin. iOS et Xamarin. Android prennent en charge tous les profils fournis avec Visual Studio, et la disponibilité des fonctionnalités dans les bibliothèques que vous créez est limitée uniquement par les autres plateformes que vous choisissez de prendre en charge.

Cela comprend les profils qui sont des combinaisons de :

- .NET 4 ou .NET 4,5
- Silverlight 5
- Windows Phone 8
- Applications UWP

Pour en savoir plus sur les différentes fonctionnalités de profil sur le [site Web de Microsoft](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library) , consultez le [Résumé du profil PCL](https://portablelibraryprofiles.stephencleary.com/) d’un autre membre de la Communauté, qui comprend des informations sur le Framework prises en charge et d’autres remarques.

**Avantages**

1. Partage de code centralisé : écrivez et testez du code dans un projet unique qui peut être consommé par d’autres bibliothèques ou applications.
2. Les opérations de refactorisation affectent tout le code chargé dans la solution (bibliothèque de classes portable et projets spécifiques à la plateforme).
3. Le projet PCL peut être facilement référencé par d’autres projets dans une solution, ou l’assembly de sortie peut être partagé pour permettre à d’autres utilisateurs de faire référence à leurs solutions.

**Inconvénients**

1. Étant donné que la même bibliothèque de classes portable est partagée entre plusieurs applications, les bibliothèques spécifiques à la plateforme ne peuvent pas être référencées (par exemple, Community. CsharpSqlite. WP7).
2. Le sous-ensemble de la bibliothèque de classes portable peut ne pas inclure des classes qui, autrement, seraient disponibles à la fois dans les interfaces monochrome et mono pour Android (comme DllImport ou System. IO. file).

> [!NOTE]
> Les bibliothèques de classes portables ont été dépréciées dans la dernière version de Visual Studio et [.NET standard bibliothèques](net-standard.md) sont recommandées à la place.

Dans une certaine mesure, les deux inconvénients peuvent être contournés à l’aide du modèle de fournisseur ou de l’injection de dépendances pour coder l’implémentation réelle dans les projets de plateforme sur une interface ou une classe de base définie dans la bibliothèque de classes portables.

Ce diagramme illustre l’architecture d’une application multiplateforme à l’aide d’une bibliothèque de classes portable pour partager du code, mais également à l’aide de l’injection de dépendances pour passer des fonctionnalités dépendantes de la plateforme :

[![Ce diagramme illustre l’architecture d’une application multiplateforme à l’aide d’une bibliothèque de classes portable pour partager du code, mais également à l’aide de l’injection de dépendances pour passer des fonctionnalités dépendantes de la plateforme.](pcl-images/image1.png)](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio pour Mac procédure pas à pas

Cette section explique comment créer et utiliser une bibliothèque de classes portable à l’aide de Visual Studio pour Mac. Reportez-vous à la section exemple to PCL pour une implémentation complète.

### <a name="creating-a-pcl"></a>Création d’une bibliothèque de classes portable

L’ajout d’une bibliothèque de classes portable à votre solution est très similaire à l’ajout d’un projet de bibliothèque standard.

1. Dans la boîte de dialogue **nouveau projet** , sélectionnez la **bibliothèque de > multiplateformes > option bibliothèque portable** :

    ![Créer un projet PCL](pcl-images/image2.png)

1. Lorsqu’un PCL est créé dans Visual Studio pour Mac il est automatiquement configuré avec un profil qui fonctionne pour Xamarin. iOS et Xamarin. Android. Le projet PCL s’affiche comme indiqué dans cette capture d’écran :

    ![Projet PCL dans le panneau solutions](pcl-images/image3.png)

La bibliothèque PCL est maintenant prête pour l’ajout de code. Elle peut également être référencée par d’autres projets (projets d’application, projets de bibliothèque et même d’autres projets PCL).

### <a name="editing-pcl-settings"></a>Modification des paramètres PCL

Pour afficher et modifier les paramètres PCL de ce projet, cliquez avec le bouton droit sur le projet, puis choisissez **Options > générer > général** pour voir l’écran illustré ici :

[![Options de projet PCL pour définir le profil](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Cliquez sur **modifier** pour modifier le profil cible de cette bibliothèque de classes portable.

Si le profil est modifié une fois que le code a déjà été ajouté à la bibliothèque PCL, il est possible que la bibliothèque ne soit plus compilée si le code fait référence à des fonctionnalités qui ne font pas partie du profil nouvellement sélectionné.

## <a name="working-with-a-pcl"></a>Utilisation d’une bibliothèque de classes portable

Lorsque le code est écrit dans une bibliothèque PCL, l’éditeur de Visual Studio pour Mac reconnaît les limitations du profil sélectionné et ajuste les options de saisie semi-automatique en conséquence. Par exemple, cette capture d’écran montre les options de saisie semi-automatique pour System.IO à l’aide du profil par défaut (Profile136) utilisé dans Visual Studio pour Mac – Notez la barre de défilement qui indique la moitié des classes disponibles sont affichées (en fait, seules 14 classes sont disponibles).

[![Liste IntelliSense de 14 classes dans la classe System.IO d’une bibliothèque de classes portable](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Comparez-le à la saisie semi-automatique System.IO dans un projet Xamarin. iOS ou Xamarin. Android : 40 classes sont disponibles, y compris les classes couramment utilisées comme `File` et `Directory` qui ne sont pas dans un profil PCL.

[![Liste IntelliSense des classes 40 dans .NET Framework espace de noms System.IO](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Cela reflète le compromis sous-jacent de l’utilisation de la bibliothèque de classes portable : la possibilité de partager du code en toute transparence sur de nombreuses plateformes, car certaines API ne sont pas disponibles car elles n’ont pas d’implémentations comparables sur toutes les plateformes possibles.

### <a name="using-pcl"></a>Utilisation de PCL

Une fois qu’un projet PCL a été créé, vous pouvez y ajouter une référence à partir de n’importe quel projet d’application ou de bibliothèque compatible, de la même façon que vous ajoutez normalement des références. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur le nœud Références, puis choisissez **modifier les références..** ., puis basculez vers l’onglet **projets** comme indiqué :

[![Ajouter une référence à une bibliothèque de classes portable par le biais de l’option modifier les références](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

La capture d’écran suivante montre le panneau solution de l’exemple d’application TaskyPortable, affichant la bibliothèque PCL en bas et une référence à cette bibliothèque PCL dans le projet Xamarin. iOS.

[![Exemple de solution TaskyPortable avec un projet PCL](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

La sortie d’une bibliothèque de classes portable (c’est-à-dire, la DLL d’assembly qui en résulte) peut également être ajoutée en tant que référence à la plupart des projets. La méthode PCL est ainsi idéale pour envoyer des composants et des bibliothèques multiplateforme.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Procédure pas à pas Visual Studio

Cette section explique comment créer et utiliser une bibliothèque de classes portable à l’aide de Visual Studio. Reportez-vous à la section exemple to PCL pour une implémentation complète.

### <a name="creating-a-pcl"></a>Création d’une bibliothèque de classes portable

L’ajout d’une bibliothèque de classes portable à votre solution dans Visual Studio est légèrement différent pour l’ajout d’un projet normal :

1. Dans l’écran **Ajouter un nouveau projet** , sélectionnez l’option **bibliothèque de classes (portable hérité)** . Notez que la description à droite recommande que ce type de projet soit déconseillé.

    [![Fenêtre nouveau projet pour créer une bibliothèque de classes portables](pcl-images/image10-sml.png "Bibliothèque de classes portable")](pcl-images/image10.png#lightbox)

2. Visual Studio s’affiche immédiatement avec la boîte de dialogue suivante afin que le profil puisse être configuré.
 Cochez les plateformes que vous devez prendre en charge et appuyez sur OK.

    [![Sélectionner les plateformes cibles pour la bibliothèque](pcl-images/image11-sml.png "Cochez les plateformes que vous devez prendre en charge, puis appuyez sur OK")](pcl-images/image11.png#lightbox)

3. Le projet PCL s’affiche comme indiqué dans le Explorateur de solutions &ndash; le texte **(portable)** s’affiche en regard du nom du projet pour indiquer qu’il s’agit d’un PCL :

    ![.NET Framework défini par le profil PCL](pcl-images/image12.png ".NET Framework défini par le profil PCL")

La bibliothèque PCL est maintenant prête pour l’ajout de code. Il peut également être référencé par d’autres projets (projets d’application, projets de bibliothèque et même d’autres projets PCL).

### <a name="editing-pcl-settings"></a>Modification des paramètres PCL

Les paramètres PCL peuvent être affichés et modifiés en cliquant avec le bouton droit sur le projet et en sélectionnant **propriétés > bibliothèque** , comme illustré dans cette capture d’écran :

[![Modifier les cibles de la plateforme](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Si le profil est modifié une fois que le code a déjà été ajouté à la bibliothèque PCL, il est possible que la bibliothèque ne soit plus compilée si le code fait référence à des fonctionnalités qui ne font pas partie du profil nouvellement sélectionné.

> [!TIP]
> Il y a également un message le avertissant **. Netstandard est la méthode recommandée pour le partage de code**. Cela indique que bien que les classes portables soient toujours pris en charge, il est recommandé de procéder à une mise à niveau vers .NET Standard.

### <a name="working-with-a-pcl"></a>Utilisation d’une bibliothèque de classes portable

Lorsque le code est écrit dans une bibliothèque PCL, Visual Studio reconnaît les limites du profil sélectionné et ajuste les options IntelliSense en conséquence. Par exemple, cette capture d’écran montre les options de saisie semi-automatique pour System.IO à l’aide du profil par défaut (Profile136). Notez la barre de défilement qui indique que la moitié des classes disponibles sont affichées (en fait, seules 14 classes sont disponibles).

[![Réduction du nombre de classes d’e/s disponibles dans une bibliothèque de classes portable](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Comparez-le à la saisie semi-automatique System.IO dans un projet normal : il existe 40 classes disponibles, y compris les classes couramment utilisées comme `File` et `Directory` qui ne sont pas dans un profil PCL.

[![De nombreuses classes d’e/s supplémentaires sont disponibles dans le .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Cela reflète le compromis sous-jacent de l’utilisation de la bibliothèque de classes portable : la possibilité de partager du code en toute transparence sur de nombreuses plateformes, car certaines API ne sont pas disponibles car elles n’ont pas d’implémentations comparables sur toutes les plateformes possibles.

> [!TIP]
> .NET Standard 2,0 représente une plus grande surface d’exposition de l’API que classes portables, y compris l’espace de noms System.IO. Pour les nouveaux projets, .NET Standard est recommandé sur PCL.

### <a name="using-pcl"></a>Utilisation de PCL

Une fois qu’un projet PCL a été créé, vous pouvez y ajouter une référence à partir de n’importe quel projet d’application ou de bibliothèque compatible, de la même façon que vous ajoutez normalement des références. Dans Visual Studio, cliquez avec le bouton droit sur le nœud Références, puis choisissez `Add Reference...` basculer vers l’onglet **solution > projets** comme indiqué ci-dessous :

[![Ajouter une référence à une bibliothèque de classes portable via l’onglet ajouter des projets de référence](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

La capture d’écran suivante montre le volet solution pour l’exemple d’application TaskyPortable, en affichant la bibliothèque PCL en bas et une référence à cette bibliothèque PCL dans le projet Xamarin. iOS.

[![Exemple de solution TaskyPortable présentant une bibliothèque PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

La sortie d’une bibliothèque de classes portable (c’est-à-dire, la DLL d’assembly qui en résulte) peut également être ajoutée en tant que référence à la plupart des projets.
La méthode PCL est ainsi idéale pour envoyer des composants et des bibliothèques multiplateforme.

-----

## <a name="pcl-example"></a>Exemple PCL

L’exemple d’application [TaskyPortable](/samples/xamarin/mobile-samples/taskyportable/) montre comment une bibliothèque de classes portable peut être utilisée avec Xamarin.
Voici quelques captures d’écran des applications qui s’exécutent sur iOS et Android :

[![Voici quelques captures d’écran des applications qui s’exécutent sur iOS, Android et Windows Phone](pcl-images/image18.png)](pcl-images/image18.png#lightbox)

Il partage un certain nombre de classes de données et de logiques qui sont du code purement portable, et il montre également comment incorporer des exigences spécifiques à la plateforme à l’aide de l’injection de dépendances pour l’implémentation de base de données SQLite.

La structure de la solution est indiquée ci-dessous (dans Visual Studio pour Mac et Visual Studio, respectivement) :

[![La structure de la solution est présentée ici dans Visual Studio pour Mac et Visual Studio, respectivement](pcl-images/image19.png)](pcl-images/image19.png#lightbox)

Étant donné que le code SQLite-NET possède des éléments spécifiques à la plateforme (pour travailler avec les implémentations SQLite sur chaque système d’exploitation) à des fins de démonstration, il a été refactorisé dans une classe abstraite qui peut être compilée dans une bibliothèque de classes portables, et le code réel implémenté en tant que sous-classes dans les projets iOS et Android.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

La bibliothèque de classes portables est limitée dans les fonctionnalités .NET qu’elle peut prendre en charge. Étant donné qu’il est compilé pour s’exécuter sur plusieurs plateformes, il ne peut pas utiliser `[DllImport]` les fonctionnalités utilisées dans SQLite-net. Au lieu de cela, SQLite-NET est implémenté en tant que classe abstraite, puis référencé par le reste du code partagé. Un extrait de l’API abstraite est illustré ci-dessous :

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

Le reste du code partagé utilise la classe abstraite pour « stocker » et « récupérer » des objets de la base de données. Dans toute application qui utilise cette classe abstraite, nous devons passer une implémentation complète qui fournit la fonctionnalité de base de données réelle.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid et TaskyiOS

Les projets d’application iOS et Android contiennent l’interface utilisateur et d’autres codes spécifiques à la plateforme, utilisés pour connecter le code partagé dans la bibliothèque de classes portable.

Ces projets contiennent également une implémentation de l’API de base de données abstraite qui fonctionne sur cette plateforme. Sur iOS et Android, le moteur de base de données SQLite est intégré au système d’exploitation. par conséquent, l’implémentation peut utiliser `[DllImport]` comme indiqué pour fournir l’implémentation concrète de la connectivité de base de données. Un extrait du code d’implémentation spécifique à la plateforme est présenté ici :

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

L’implémentation complète peut être consultée dans l’exemple de code.

## <a name="summary"></a>Résumé

Cet article a brièvement abordé les avantages et les pièges des bibliothèques de classes portables, a démontré comment créer et consommer des classes portables à partir de l’intérieur de Visual Studio pour Mac et de Visual Studio. Enfin, il a présenté un exemple d’application complet – TaskyPortable – qui montre un PCL en action.

## <a name="related-links"></a>Liens connexes

- [TaskyPortable (exemple)](/samples/xamarin/mobile-samples/taskyportable/)
- [Création d’applications multiplateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Visual Basic portable](~/cross-platform/platform/visual-basic/index.md)
- [Projets partagés](~/cross-platform/app-fundamentals/shared-projects.md)
- [Options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md)
- [Développement multiplateforme avec le .NET Framework (Microsoft)](/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)