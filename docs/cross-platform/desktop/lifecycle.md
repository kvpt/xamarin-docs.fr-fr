---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Cycle de vie des applications WPF et Xamarin. Forms
description: Ce document compare les similitudes et les différences entre le cycle de vie de l’application pour Xamarin. Forms et les applications WPF. Il examine également l’arborescence d’éléments visuels, les graphiques, les ressources et les styles.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 6e959156aada0451efa13f6b67b6637f4e5deaa5
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281233"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Cycle de vie des applications WPF et Xamarin. Forms

Xamarin. Forms s’appuie sur un grand nombre de conseils de conception des infrastructures XAML qui ont été antérieures, en particulier WPF. Toutefois, dans d’autres cas, il s’écarte considérablement, ce qui peut être un point collant pour les personnes qui tentent de migrer. Ce document tente d’identifier certains de ces problèmes et fournit des conseils dans la mesure du possible pour relier les connaissances WPF à Xamarin. Forms.

## <a name="app-lifecycle"></a>Cycle de vie d’application

Le cycle de vie de l’application entre WPF et Xamarin. Forms est similaire. Les deux démarrent dans le code externe (plateforme) et lancent l’interface utilisateur par le biais d’un appel de méthode. La différence réside dans le fait que Xamarin. Forms démarre toujours dans un assembly spécifique à la plateforme qui initialise ensuite et crée l’interface utilisateur pour l’application.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> La `Main` méthode est, par défaut, générée automatiquement et n’est pas visible dans le code.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash;`MainActivity > App > ContentPage`
- **UWP** &ndash;`Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe Application

WPF et Xamarin. Forms ont tous deux une `Application` classe qui est créée en tant que singleton. Dans la plupart des cas, les applications dérivent de cette classe pour fournir une application personnalisée, bien que cela ne soit pas strictement requis dans WPF. Les deux exposent une `Application.Current` propriété pour localiser le singleton créé.

### <a name="global-properties--persistence"></a>Propriétés globales + persistance

WPF et Xamarin. Forms ont tous deux un `Application.Properties` dictionnaire disponible dans lequel vous pouvez stocker des objets de niveau application globaux accessibles n’importe où dans l’application. La principale différence réside dans le fait que Xamarin. Forms _conserve_ tous les types primitifs stockés dans la collection lorsque l’application est suspendue, puis les recharge lorsqu’elle est relancée. WPF ne prend pas automatiquement en charge ce comportement, mais la plupart des développeurs s’appuient sur le stockage isolé, ou ont utilisé la `Settings` prise en charge intégrée.

## <a name="defining-pages-and-the-visual-tree"></a>Définition des pages et de l’arborescence d’éléments visuels

WPF utilise `Window` comme élément racine pour tout élément visuel de niveau supérieur. Cela définit un HWND dans le monde Windows pour afficher des informations. Vous pouvez créer et afficher autant de fenêtres que vous le souhaitez dans WPF.

Dans Xamarin. Forms, le visuel de niveau supérieur est toujours défini par la plateforme. par exemple, sur iOS, il s’agit d’un `UIWindow` . Xamarin. Forms restitue son contenu dans ces représentations de plateforme natives à l’aide d’une `Page` classe. Chaque `Page` dans Xamarin. Forms représente une « page » unique dans l’application, où une seule est visible à la fois.

WPFs `Window` et Xamarin. Forms `Page` incluent une `Title` propriété pour influencer le titre affiché, et les deux ont une `Icon` propriété pour afficher une icône spécifique pour la page ( **Notez** que le titre et l’icône ne sont pas toujours visibles dans Xamarin. Forms). En outre, vous pouvez modifier les propriétés visuelles communes sur les deux, telles que la couleur ou l’image d’arrière-plan.

Il est techniquement possible d’effectuer un rendu sur deux vues de plateforme distinctes (par exemple, définir deux `UIWindow` objets et faire en sorte que le second soit rendu dans un affichage externe ou AirPlay), il requiert du code spécifique à la plateforme pour le faire et n’est pas une fonctionnalité directement prise en charge de Xamarin. Forms.

### <a name="views"></a>Les vues

La hiérarchie visuelle pour les deux Frameworks est similaire. WPF est un peu plus profond en raison de sa prise en charge des documents WYSIWYG.

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>Cycle de vie de l’affichage

Xamarin. Forms est principalement axé sur les scénarios mobiles. Par conséquent, les applications sont _activées_ , _suspendues_ et _réactivées_ à mesure que l’utilisateur interagit avec elles. Cela revient à cliquer à l’extérieur du `Window` dans une application WPF et il existe un ensemble de méthodes et d’événements correspondants que vous pouvez remplacer ou raccorder pour surveiller ce comportement.

| Objectif | Méthode WPF | Xamarin. Forms, méthode |
|--- |--- |--- |
|Activation initiale|ctor + fenêtre. OnLoaded|ctor + page. OnStart|
|Démontré|Fenêtre. IsVisibleChanged|Page. apparaît|
|Hidden|Fenêtre. IsVisibleChanged|Page. disparition|
|Suspendre/perdre le focus|Fenêtre. OnDeactivated|Page. OnSleep|
|Focus activé/reçu|Fenêtre. OnActivated|Page. OnResume|
|Fermé|Window. OnClosing + Window. OnClosed|n/a|

Les deux prennent en charge le masquage/l’affichage des contrôles enfants, dans WPF, il s’agit d’une propriété à trois États `IsVisible` (visible, masquée et réduite). Dans Xamarin. Forms, il est simplement visible ou masqué par la `IsVisible` propriété.

### <a name="layout"></a>Layout

La mise en page se produit dans le même 2-passe (mesure/réorganisation) qui se produit dans WPF. Vous pouvez vous connecter à la mise en page en remplaçant les méthodes suivantes dans la classe Xamarin. Forms `Page` :

| Méthode | Objectif |
|--- |--- |
|OnChildMeasureInvalidated|La taille par défaut d’un enfant a changé.|
|OnSizeAllocated|Une largeur/hauteur a été affectée à la page.|
|Événement LayoutChanged|La disposition/taille de la page a été modifiée.|

Il n’y a pas d’événement de disposition global qui est appelé aujourd’hui, et il n’y a pas d’événement global tel qu’il est `CompositionTarget.Rendering` trouvé dans WPF.

#### <a name="common-layout-properties"></a>Propriétés de disposition communes

WPF et Xamarin. Forms prennent tous les deux en charge `Margin` pour contrôler l’espacement autour d’un élément et `Padding` pour contrôler l’espacement _dans_ un élément. En outre, la plupart des vues de la disposition Xamarin. Forms ont des propriétés pour contrôler l’espacement (par exemple, ligne ou colonne).

De plus, la plupart des éléments ont des propriétés qui influencent leur position dans le conteneur parent :

| WPF | Xamarin.Forms | Objectif |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Options gauche/centre/droite/Stretch|
|VerticalAlignment|VerticalOptions|Options haut/Centre/bas/Stretch|

> [!NOTE]
> L’interprétation réelle de ces propriétés dépend du conteneur parent.

#### <a name="layout-views"></a>Vues de la disposition

WPF et Xamarin. Forms utilisent tous deux des contrôles de disposition pour positionner des éléments enfants. Dans la plupart des cas, ceux-ci sont très proches les uns des autres en termes de fonctionnalités.

| WPF | Xamarin.Forms | Style de disposition |
|--- |--- |--- |
|StackPanel|StackLayout|Empilement infinie de gauche à droite ou de haut en bas|
|Grille|Grille|Format tabulaire (lignes et colonnes)|
|DockPanel|n/a|Ancrer sur les bords de la fenêtre|
|Canevas|AbsoluteLayout|Positionnement de pixel/coordonnée|
|WrapPanel|n/a|Pile d’habillage|
|n/a|RelativeLayout|Positionnement relatif basé sur des règles|

> [!NOTE]
> Xamarin. Forms ne prend pas en charge `GridSplitter` .

Les deux plateformes utilisent des _propriétés attachées_ pour affiner les enfants.

### <a name="rendering"></a>Rendu

Le mécanisme de rendu pour WPF et Xamarin. Forms est radicalement différent. Dans WPF, les contrôles que vous créez affichent directement le contenu sur les pixels de l’écran. WPF gère deux graphiques d’objets ( _arborescences_ ) pour représenter ce-l' _arborescence logique_ représente les contrôles tels qu’ils sont définis dans le code ou XAML, et l’arborescence d’éléments _visuels_ représente le rendu réel qui se produit sur l’écran, qui est exécuté directement par l’élément visuel (via une méthode Draw virtuelle), ou par le biais d’un XAML défini `ControlTemplate` qui peut être remplacé ou personnalisé. En règle générale, l’arborescence d’éléments visuels est plus complexe, car elle comprend des éléments tels que des bordures autour des contrôles, des étiquettes pour le contenu implicite, etc. WPF comprend un ensemble d’API ( `LogicalTreeHelper` et `VisualTreeHelper` ) pour examiner ces deux graphiques d’objets.

Dans Xamarin. Forms, les contrôles que vous définissez dans un `Page` sont en réalité simplement des objets de données simples. Ils sont similaires à la représentation de l’arborescence logique, mais n’affichent jamais leur propre contenu. Au lieu de cela, il s’agit du _modèle de données_ qui influence le rendu des éléments. Le rendu réel est effectué par un [ensemble distinct de _convertisseurs visuels_ mappés à chaque type de contrôle](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Ces convertisseurs sont enregistrés dans chacun des projets spécifiques à la plateforme par des assemblys Xamarin. Forms spécifiques à la plateforme. Vous pouvez voir une liste [ici](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). En plus de remplacer ou d’étendre le convertisseur, Xamarin. Forms prend également en charge les [effets](~/xamarin-forms/app-fundamentals/effects/index.md) qui peuvent être utilisés pour influencer le rendu natif pour chaque plateforme.

#### <a name="the-logicalvisual-tree"></a>Arborescence logique/d’éléments visuels

Il n’existe aucune API exposée pour parcourir l’arborescence logique dans Xamarin. Forms, mais vous pouvez utiliser la réflexion pour récupérer les mêmes informations. Par exemple, [Voici une méthode qui peut énumérer des enfants logiques](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) avec réflexion.

## <a name="graphics"></a>Graphiques

Xamarin. Forms comprend un système graphique pour le dessin de primitives, appelées formes. Pour plus d’informations sur les formes, consultez [formes Xamarin. Forms](~/xamarin-forms/user-interface/shapes/index.md). En outre, vous pouvez inclure des bibliothèques tierces telles que [SkiaSharp](~/xamarin-forms/user-interface/graphics/index.md) pour accéder au dessin 2D multiplateforme.

## <a name="resources"></a>Ressources

WPF et Xamarin. Forms ont tous deux le concept de ressources et de dictionnaires de ressources. Vous pouvez placer n’importe quel type d’objet dans un `ResourceDictionary` avec une clé, puis le Rechercher avec `{StaticResource}` pour des éléments qui ne changent pas ou `{DynamicResource}` pour des éléments qui peuvent changer dans le dictionnaire au moment de l’exécution. L’utilisation et la mécanique sont les mêmes, à une différence près : Xamarin. Forms nécessite que vous définissiez le `ResourceDictionary` à assigner à la `Resources` propriété alors que WPF en crée un et l’assigne à votre demande.

Par exemple, consultez la définition ci-dessous :

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

Si vous ne définissez pas la `ResourceDictionary` , une erreur d’exécution est générée.

## <a name="styles"></a>Styles

Les styles sont également entièrement pris en charge dans Xamarin. Forms et peuvent être utilisés pour créer un thème des éléments Xamarin. Forms qui composent l’interface utilisateur. Ils prennent en charge les déclencheurs (propriété, événement et données), l’héritage via `BasedOn` et les recherches de ressources pour les valeurs. Les styles sont appliqués aux éléments de manière explicite par le biais de la `Style` propriété ou implicitement en ne fournissant pas de clé de ressource, comme WPF.

### <a name="device-styles"></a>Styles d’appareils

WPF possède un ensemble de propriétés prédéfinies (stockées sous forme de valeurs statiques sur un ensemble de classes statiques telles que `SystemColors` ) qui dictent les couleurs système, les polices et les métriques sous la forme de valeurs et de clés de ressources. Xamarin. Forms est similaire, mais définit un ensemble de [styles d’appareil](~/xamarin-forms/user-interface/styles/device.md) pour représenter les mêmes choses. Ces styles sont fournis par l’infrastructure et définis sur des valeurs basées sur l’environnement d’exécution (par exemple, l’accessibilité).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
