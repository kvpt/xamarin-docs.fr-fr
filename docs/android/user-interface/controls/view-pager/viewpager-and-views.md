---
title: ViewPager avec des vues
description: ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter une interface utilisateur balayable avec ViewPager et PagerTabStrip, à l’aide de vues comme pages de données (un guide suivant explique comment utiliser des fragments pour les pages).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 7413fbe3f08988cfdb7c7b4e5237539aca250772
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292646"
---
# <a name="viewpager-with-views"></a>ViewPager avec des vues

_ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter une interface utilisateur balayable avec ViewPager et PagerTabStrip, à l’aide de vues comme pages de données (un guide suivant explique comment utiliser des fragments pour les pages)._

## <a name="overview"></a>Vue d’ensemble

Ce guide est une procédure pas à pas qui fournit une démonstration pas à pas de l’utilisation de `ViewPager` pour implémenter une galerie d’images d’arbres à feuilles de feuilles et de feuilles à feuilles de feuilles. Dans cette application, l’utilisateur glisse vers la gauche et vers la droite dans un « catalogue d’arborescence » pour afficher les images d’arborescence. En haut de chaque page du catalogue, le nom de l’arborescence est répertorié dans un`PagerTabStrip`, et une image de l’arborescence est affichée dans une `ImageView`. Un adaptateur est utilisé pour l’interface du `ViewPager` au modèle de données sous-jacent. Cette application implémente un adaptateur dérivé de `PagerAdapter`. 

Bien que les applications basées sur les `ViewPager`soient souvent implémentées avec `Fragment`, il existe des cas d’utilisation relativement simples où la complexité supplémentaire des `Fragment`s n’est pas nécessaire. Par exemple, l’application de la Galerie d’images de base illustrée dans cette procédure pas à pas ne nécessite pas l’utilisation de `Fragment`s. Étant donné que le contenu est statique et que l’utilisateur ne fait que passer d’une image à l’autre, l’implémentation peut être simplifiée à l’aide de vues et de mises en page Android standard. 

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Créez un nouveau projet Android appelé **TreePager** (consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) pour plus d’informations sur la création de nouveaux projets Android). Ensuite, lancez le gestionnaire de package NuGet. (Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Rechercher et installer la **bibliothèque de prise en charge Android v4**: 

[Capture d’écran ![de la prise en charge de la V4 NuGet sélectionnée dans le gestionnaire de package NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Cette opération installe également tous les packages supplémentaires réinstallés par **Android Support Library v4**.

## <a name="add-an-example-data-source"></a>Ajouter un exemple de source de données

Dans cet exemple, la source de données du catalogue d’arborescence (représentée par la classe `TreeCatalog`) fournit le `ViewPager` avec le contenu de l’élément. 
`TreeCatalog` contient une collection prête à l’emploi d’images et de titres d’arborescence que l’adaptateur utilisera pour créer des `View`s. Le constructeur `TreeCatalog` ne requiert aucun argument :

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

La collection d’images dans `TreeCatalog` est organisée de telle sorte que chaque image soit accessible par un indexeur. Par exemple, la ligne de code suivante récupère l’ID de ressource d’image pour la troisième image de la collection : 

```csharp
int imageId = treeCatalog[2].imageId;
```

Étant donné que les détails d’implémentation de `TreeCatalog` ne sont pas pertinents pour comprendre `ViewPager`, le code de `TreeCatalog` n’est pas répertorié ici. Le code source à `TreeCatalog` est disponible sur [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Téléchargez ce fichier source (ou copiez et collez le code dans un nouveau fichier **TreeCatalog.cs** ) et ajoutez-le à votre projet. Vous pouvez également télécharger et décompresser les [fichiers image](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) dans votre dossier **Resources/drawic** et les inclure dans le projet. 

## <a name="create-a-viewpager-layout"></a>Créer une disposition ViewPager

Ouvrez **Resources/layout/main. AXML** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>
```

Ce code XML définit un `ViewPager` qui occupe tout l’écran. Notez que vous devez utiliser le nom complet **Android. support. v4. View. ViewPager** , car `ViewPager` est empaqueté dans une bibliothèque de prise en charge. `ViewPager` n’est disponible qu’à partir de la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); elle n’est pas disponible dans le Android SDK. 

## <a name="set-up-viewpager"></a>Configurer ViewPager

Modifiez **MainActivity.cs** et ajoutez l’instruction `using` suivante :

```csharp
using Android.Support.V4.View;
```

Remplacez la méthode `OnCreate` par le code suivant :

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

Ce code effectue les actions suivantes :

1. Définit la vue à partir de la ressource de disposition **main. AXML** .

2. Récupère une référence au `ViewPager` à partir de la disposition.

3. Instancie un nouveau `TreeCatalog` comme source de données.

Lorsque vous générez et exécutez ce code, vous devez voir un affichage qui ressemble à la capture d’écran suivante : 

[![capture d’écran de l’application affichant un ViewPager vide](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

À ce stade, le `ViewPager` est vide, car il ne dispose pas d’un adaptateur pour accéder au contenu dans **TreeCatalog**. Dans la section suivante, un **PagerAdapter** est créé pour connecter le `ViewPager` au **TreeCatalog**. 

## <a name="create-the-adapter"></a>Créer l’adaptateur

`ViewPager` utilise un objet contrôleur d’adaptateur qui se situe entre le `ViewPager` et la source de données (Voir l’illustration dans l' [adaptateur](~/android/user-interface/controls/view-pager/index.md#adapter)). Pour accéder à ces données, `ViewPager` devez fournir un adaptateur personnalisé dérivé de `PagerAdapter`. Cet adaptateur remplit chaque page de `ViewPager` avec le contenu de la source de données. Étant donné que cette source de données est spécifique à l’application, l’adaptateur personnalisé est le code qui comprend comment accéder aux données. À mesure que l’utilisateur parcourt les pages de la `ViewPager`, l’adaptateur extrait des informations de la source de données et les charge dans les pages du `ViewPager` à afficher. 

Lorsque vous implémentez un `PagerAdapter`, vous devez remplacer les éléments suivants :

- **InstantiateItem** &ndash; crée la page (`View`) pour une position donnée et l’ajoute à la collection de vues de l' `ViewPager`. 

- **DestroyItem** &ndash; supprime une page d’une position donnée.

- **Nombre** &ndash; propriété en lecture seule qui retourne le nombre de vues (pages) disponibles. 

- **IsViewFromObject** &ndash; détermine si une page est associée à un objet clé spécifique. (Cet objet est créé par la méthode `InstantiateItem`.) Dans cet exemple, l’objet clé est le `TreeCatalog` objet de données.

Ajoutez un nouveau fichier appelé **TreePagerAdapter.cs** et remplacez son contenu par le code suivant : 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Ce code élimine l’implémentation de `PagerAdapter` essentielle. Dans les sections suivantes, chacune de ces méthodes est remplacée par du code de travail. 

### <a name="implement-the-constructor"></a>Implémenter le constructeur

Lorsque l’application instancie le `TreePagerAdapter`, elle fournit un contexte (le `MainActivity`) et un `TreeCatalog`instancié. Ajoutez les variables membres et le constructeur suivants au début de la classe `TreePagerAdapter` dans **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

L’objectif de ce constructeur est de stocker le contexte et l’instance de `TreeCatalog` que le `TreePagerAdapter` utilisera. 

### <a name="implement-count"></a>Nombre d’implémentations

L’implémentation de `Count` est relativement simple : elle retourne le nombre d’arborescences dans le catalogue d’arborescence. Remplacez `Count` par le code suivant :

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

La propriété `NumTrees` de `TreeCatalog` retourne le nombre d’arbres (nombre de pages) dans le jeu de données.

### <a name="implement-instantiateitem"></a>Implémenter InstantiateItem

La méthode `InstantiateItem` crée la page pour une position donnée. Il doit également ajouter la vue nouvellement créée à la collection de vues de `ViewPager`. Pour rendre cela possible, le `ViewPager` se transforme en tant que paramètre Container. 

Remplacez la méthode `InstantiateItem` par le code suivant :

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

Ce code effectue les actions suivantes :

1. Instancie un nouveau `ImageView` pour afficher l’image de l’arborescence à la position spécifiée. Le `MainActivity` de l’application est le contexte qui sera passé au constructeur `ImageView`.

2. Définit le `ImageView` ressource sur l’ID de ressource d’image `TreeCatalog` à la position spécifiée.

3. Convertit le conteneur passé `View` en une référence `ViewPager`.
    Notez que vous devez utiliser `JavaCast<ViewPager>()` pour effectuer correctement ce Cast (cela est nécessaire pour qu’Android effectue une conversion de type vérifiée au moment de l’exécution).

4. Ajoute le `ImageView` instancié à l' `ViewPager` et retourne le `ImageView` à l’appelant.

Lorsque l' `ViewPager` affiche l’image à `position`, elle affiche ce `ImageView`. Initialement, `InstantiateItem` est appelé deux fois pour remplir les deux premières pages avec des vues. À mesure que l’utilisateur fait défiler, il est rappelé pour gérer les affichages juste après et avant l’élément actuellement affiché. 

### <a name="implement-destroyitem"></a>Implémenter DestroyItem

La méthode `DestroyItem` supprime une page de la position donnée. Dans les applications où la vue à une position donnée peut changer, `ViewPager` devez disposer d’un moyen de supprimer une vue obsolète à cette position avant de la remplacer par une nouvelle vue. Dans l’exemple de `TreeCatalog`, la vue à chaque position ne change pas. par conséquent, une vue supprimée par `DestroyItem` sera simplement rajoutée lorsque `InstantiateItem` est appelé pour cette position. (Pour une meilleure efficacité, il est possible d’implémenter un pool pour recycler `View`s qui seront réaffichés à la même position.) 

Remplacez la méthode `DestroyItem` par le code suivant : 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Ce code effectue les actions suivantes :

1. Convertit le conteneur passé `View` en une référence `ViewPager`.

2. Convertit l’objet Java passé (`view`) en C# `View` (`view as View`);

3. Supprime la vue de la `ViewPager`. 

### <a name="implement-isviewfromobject"></a>Implémenter IsViewFromObject

À mesure que l’utilisateur fait défiler les pages de contenu vers la gauche et vers la droite, `ViewPager` appelle `IsViewFromObject` pour vérifier que les `View` enfants à la position donnée sont associés à l’objet de l’adaptateur pour cette même position (par conséquent, l’objet de l’adaptateur est appelé une *clé d’objet*). Pour les applications relativement simples, l’Association est l’une des identités &ndash; la clé d’objet de l’adaptateur au niveau de cette instance est la vue qui a été précédemment retournée à la `ViewPager` via `InstantiateItem`. Toutefois, pour d’autres applications, la clé d’objet peut être une autre instance de classe spécifique à l’adaptateur qui est associée (mais pas la même que) à la vue enfant qui `ViewPager` s’affiche à cette position. Seul l’adaptateur sait si la vue et la clé d’objet passées sont associées. 

`IsViewFromObject` doit être implémentée pour que `PagerAdapter` fonctionne correctement. Si `IsViewFromObject` retourne `false` pour une position donnée, `ViewPager` n’affichera pas la vue à cette position. Dans l’application `TreePager`, la clé d’objet retournée par `InstantiateItem` *est* le `View` de page d’une arborescence, de sorte que le code doit uniquement vérifier l’identité (la clé d’objet et la vue en sont une et la même). Remplacez `IsViewFromObject` par le code suivant : 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>Ajouter l’adaptateur au ViewPager

Maintenant que la `TreePagerAdapter` est implémentée, il est temps de l’ajouter au `ViewPager`. Dans **MainActivity.cs**, ajoutez la ligne de code suivante à la fin de la méthode `OnCreate` :

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Ce code instancie le `TreePagerAdapter`, en passant le `MainActivity` comme contexte (`this`). Le `TreeCatalog` instancié est passé dans le deuxième argument du constructeur. La propriété `Adapter` de l' `ViewPager`est définie sur l’objet `TreePagerAdapter` instancié ; Cela connecte le `TreePagerAdapter` dans le `ViewPager`. 

L’implémentation de base est maintenant terminée &ndash; générer et exécuter l’application. La première image du catalogue d’arborescences doit s’afficher sur l’écran, comme indiqué à gauche dans la capture d’écran suivante. Balayez vers la gauche pour afficher plus d’arborescences, puis faites défiler vers la droite pour revenir en arrière dans le catalogue d’arborescence : 

[![captures d’écran de l’application TreePager balayer les images d’arborescence](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Ajouter un indicateur de pagineur

Cette implémentation minimale de `ViewPager` affiche les images du catalogue d’arborescence, mais elle ne fournit aucune indication quant à l’emplacement de l’utilisateur dans le catalogue. L’étape suivante consiste à ajouter un `PagerTabStrip`. L' `PagerTabStrip` informe l’utilisateur de la page qui s’affiche et fournit un contexte de navigation en affichant un indicateur des pages précédentes et suivantes. `PagerTabStrip` est destiné à être utilisé comme indicateur pour la page actuelle d’un `ViewPager`; elle fait défiler et met à jour à mesure que l’utilisateur fait défiler chaque page. 

Ouvrez **ressources/mise en page/main. AXML** et ajoutez une `PagerTabStrip` à la disposition :

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

les `ViewPager` et les `PagerTabStrip` sont conçus pour fonctionner ensemble. Lorsque vous déclarez un `PagerTabStrip` à l’intérieur d’une disposition de `ViewPager`, le `ViewPager` trouve automatiquement le `PagerTabStrip` et le connecte à l’adaptateur. Lorsque vous générez et exécutez l’application, le `PagerTabStrip` vide doit s’afficher en haut de chaque écran : 

[capture d’écran ![gros plan d’un PagerTabStrip vide](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>Afficher un titre

Pour ajouter un titre à chaque onglet de page, implémentez la méthode `GetPageTitleFormatted` dans la classe dérivée de `PagerAdapter`. `ViewPager` appelle `GetPageTitleFormatted` (s’il est implémenté) pour obtenir la chaîne de titre qui décrit la page à la position spécifiée. Ajoutez la méthode suivante à la classe `TreePagerAdapter` dans **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Ce code récupère la chaîne de légende de l’arborescence à partir de la page spécifiée (position) dans le catalogue d’arborescence, le convertit en `String`Java et la retourne à l' `ViewPager`. Quand vous exécutez l’application avec cette nouvelle méthode, chaque page affiche la légende de l’arborescence dans la `PagerTabStrip`. Vous devez voir le nom de l’arborescence en haut de l’écran sans souligné : 

[![des captures d’écran des pages avec des onglets PagerTabStrip remplis de texte](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Vous pouvez faire défiler vers l’arrière pour afficher chaque image de l’arborescence sous-titrage dans le catalogue. 

### <a name="pagertitlestrip-variation"></a>Variation PagerTitleStrip

`PagerTitleStrip` est très similaire à `PagerTabStrip` sauf que `PagerTabStrip` ajoute un soulignement pour l’onglet actuellement sélectionné. Vous pouvez remplacer `PagerTabStrip` par `PagerTitleStrip` dans la mise en page ci-dessus et réexécuter l’application pour voir comment elle se présente avec `PagerTitleStrip`: 

[![PagerTitleStrip avec des traits de soulignement supprimés du texte](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Notez que le soulignement est supprimé lors de la conversion en `PagerTitleStrip`. 

## <a name="summary"></a>Résumé

Cette procédure pas à pas a fourni un exemple pas à pas de la création d’une application de base `ViewPager`sans utiliser `Fragment`s. Il a présenté un exemple de source de données contenant des images et des chaînes de légende, une disposition `ViewPager` pour afficher les images et une sous-classe `PagerAdapter` qui connecte la `ViewPager` à la source de données. Pour aider l’utilisateur à naviguer dans le jeu de données, vous trouverez des instructions qui expliquent comment ajouter un `PagerTabStrip` ou `PagerTitleStrip` pour afficher la légende de l’image en haut de chaque page. 

## <a name="related-links"></a>Liens connexes

- [TreePager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
